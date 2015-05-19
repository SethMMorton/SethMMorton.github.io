---
layout: post
title: "Getting doctest to play nice with C extensions"
description: "Getting doctest to play nice with C extensions"
category: How To
tags:
- python
- testing
---
{% include JB/setup %}

(*Check out [the original Stack Overflow question](http://stackoverflow.com/questions/25131630/doctests-fail-with-unicodedecodeerror-on-c-extension-and-python3).*)

[tl;dr - a working solution in script form](#script)

Python's [doctest](https://docs.python.org/3.4/library/doctest.html) module is a
must-have (must-use?) for anyone documenting their API and including examples.
The last thing that you want is for the example(s) in your documentation to
have a syntax error, or even worse, show the wrong results! For this reason,
it is very important to run your documentation through
[doctest](https://docs.python.org/3.4/library/doctest.html) as a gross reality
check that you are not misleading your users with bad documentation.

The problem arises when you write a C extension module for Python, at which point
you will find that [doctest](https://docs.python.org/3.4/library/doctest.html)
fails to work on Python 3.  It turns out that this occurs for three reasons, all
of which are simply related to the fact that the
[doctest](https://docs.python.org/3.4/library/doctest.html) author made the
(reasonable) assumption that [doctest](https://docs.python.org/3.4/library/doctest.html)
would only be run on .py files. The three reasons are

  1. A file is assumed to be an importable module *only* if it ends in ".py".
  1. On Python 3, extension modules are [ABI version tagged](https://www.python.org/dev/peps/pep-3149/).
  1. [inspect.function](https://docs.python.org/3/library/inspect.html#inspect.isfunction)
     is used to look for functions, but functions in an extension module
     look like a builtin to the Python interpreter, so 
     [inspect.builtin](https://docs.python.org/3/library/inspect.html#inspect.isbuiltin)
     is needed to identify them.

The mitigation for these three reasons are detailed below.

### Telling `doctest` to import ".so" files

The first issue is that [doctest](https://docs.python.org/3.4/library/doctest.html) only
tries to import a file if it ends with ".py", but extension modules end in ".so". The
behavior of [doctest](https://docs.python.org/3.4/library/doctest.html) is to read a
file as text if it is not importable (this is so files like README.txt can be checked).
On Python 2, reading the ".so" file as text works because it handles the bytes/unicode
boundary gracefully. On Python 3, reading the ".so" as text results in a `UnicodeDecodeError`
(because all the binary data can't be decoded) so this strategy does not work.

Fixing this issue is just a matter of redirecting code to the "import" branch if the
file extension is ".so", which is just replacing

{% highlight python %}
if filename.endswith(".py"):
{% endhighlight %}
with
{% highlight python %}
if filename.endswith(".py") or filename.endswith(".so"):
{% endhighlight %}

### Accounting for ABI version tagging

In Python 3, C extension modules can have extra information in the suffix; for
example, a file might be named `myextension.cpython-34m.so` and not
`myextension.so` (check out [PEP 3149](https://www.python.org/dev/peps/pep-3149/)
for the rationale).

To compensate for this, we just have to replace

{% highlight python %}
m = __import__(filename[:-3])
{% endhighlight %}
with
{% highlight python %}
from sysconfig import get_config_var
m = __import__(filename[:-3] if filename.endswith(".py") else filename.replace(get_config_var("EXT_SUFFIX"), ""))
{% endhighlight %}

### Helping `inspect` identify that your function is a function

The [inspect](https://docs.python.org/3/library/inspect.html) module is extremely useful
for performing introspection at runtime. [doctest](https://docs.python.org/3.4/library/doctest.html)
uses [inspect.function](https://docs.python.org/3/library/inspect.html#inspect.isfunction)
to identify if an object is a function, but this function does not identify functions in
a C extension as a function, because these are considered by the interpreter to be a builtin.
Because of this, [inspect.builtin](https://docs.python.org/3/library/inspect.html#inspect.isbuiltin)
is needed to identify functions in a C extension.

To handle this requires adding a call to [inspect.builtin](https://docs.python.org/3/library/inspect.html#inspect.isbuiltin)
in [doctest](https://docs.python.org/3.4/library/doctest.html) by replacing

{% highlight python %}
if ((inspect.isfunction(val) or inspect.isclass(val)) and
{% endhighlight %}
with
{% highlight python %}
if ((inspect.isfunction(val) or inspect.isbuiltin(val) or inspect.isclass(val)) and
{% endhighlight %}

### <a name="script"></a> A doctest patch script

The three above solutions are included in the following script, and the script
can be used to create a patched version of
[doctest](https://docs.python.org/3.4/library/doctest.html) that work for C extensions
on Python 3. You can download the script from
[this Gist](https://gist.github.com/SethMMorton/dff367d7258107d4bd23).

{% highlight python %}
"""\
Copies doctest.py from the stdlib to the current directory,
and modifies it so that

  a) It will load "*.so" files as modules just like a "*.py" file
  b) It recognizes functions defined in "*.so" files
  c) Remove the configuration extension from the "*.so" files (on Python3)

With these enhancements, doctest can be run on a C python extension module.
"""
from __future__ import print_function

import sys
import inspect
import doctest

# Get the source file location
dt_location = inspect.getsourcefile(doctest)

# Read the module into memory
with open(dt_location) as fl:
    doctest_str = fl.read()

# Add a search for the .so extension when inspecting the input files
# so that extension modules will be loaded properly.
doctest_str = doctest_str.replace(
    'if filename.endswith(".py"):',
    'if filename.endswith(".py") or filename.endswith(".so"):'
)

# inspect.isfunction does not work for functions written in C,
# so we have to replace that with an inspect.isbuiltin check when
# looking for functions with docstrings.
doctest_str = doctest_str.replace(
    'if ((inspect.isfunction(val) or inspect.isclass(val)) and',
    'if ((inspect.isfunction(val) or inspect.isbuiltin(val) or inspect.isclass(val)) and'
)

# Replace the configuration extension with nothing on Python3
if sys.version[0] == '3':
    doctest_str = doctest_str.replace(
        'm = __import__(filename[:-3])',
        'm = __import__(filename[:-3] if filename.endswith(".py") else filename.replace(get_config_var("EXT_SUFFIX"), ""))'
    )
    # We need to import the get_config_var variable.
    doctest_str = doctest_str.replace(
        'def _test():',
        'from sysconfig import get_config_var\ndef _test():'
    )


# Open up the new output file and write the modified input to it.
with open('doctest.py', 'w') as fl:
    print(doctest_str, file=fl, end='')
{% endhighlight %}

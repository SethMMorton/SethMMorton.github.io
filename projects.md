---
layout: page
title: "Projects"
description: "My Projects"
---
{% include JB/setup %}

## Python Libraries

### natsort

[Documentation](http://pythonhosted.org/natsort/index.html) | [Code](https://github.com/SethMMorton/natsort) | [Download](https://pypi.python.org/pypi/natsort)

When you try to sort a list of strings that contain numbers, the normal python sort algorithm sorts lexicographically, so you might not get the results that you expect:

{% highlight python %}
>>> a = ['a2', 'a9', 'a1', 'a4', 'a10']
>>> sorted(a)
['a1', 'a10', 'a2', 'a4', 'a9']
{% endhighlight %}

Notice that it has the order ('1', '10', '2') - this is because the list is being sorted in lexicographical order, which sorts numbers like you would letters (i.e. 'b', 'ba', 'c').

natsort provides a function natsorted that helps sort lists "naturally", either as real numbers (i.e. signed/unsigned floats or ints), or as versions. Using natsorted is simple: 

{% highlight python %}
>>> from natsort import natsorted
>>> a = ['a2', 'a9', 'a1', 'a4', 'a10']
>>> natsorted(a)
['a1', 'a2', 'a4', 'a9', 'a10']
{% endhighlight %}

natsorted identifies real numbers anywhere in a string and sorts them naturally.

### fastnumbers

[Documentation](http://pythonhosted.org/fastnumbers/index.html) | [Code](https://github.com/SethMMorton/fastnumbers) | [Download](https://pypi.python.org/pypi/fastnumbers)

This module is a Python C extension that will convert strings to numbers much faster than can be done using pure Python. Additionally, if the string cannot be converted, instead of a ValueError the return value can be either the input as-is or a default value. To achieve this, the module makes some assumptions about the input type (input is int (or long), float, or str (or unicode)), and otherwise a TypeError is raised. Check out [these timing results](http://pythonhosted.org//fastnumbers/timing.html) for just how much faster it can be.

### input_reader

[Documentation](http://pythonhosted.org/input_reader/index.html) | [Code](https://pypi.python.org/pypi/input_reader) | [Download](https://pypi.python.org/pypi/input_reader)

input_reader is a python module that provides tools to help read the contents of a block-type input file.
If you have ever written a custom parser for an input file, you will know that there are many
non-obvious challenges that must be overcome, mostly in the realm of error handling.
The challenges include dealing with unknown or missing keys, incorrect input (str vs float),
data storage, and parse ordering. Handling these issues can make a seemingly easy task become
a complicated mess that obscures the intent of your program.

The input_reader module was designed with a balance of conciseness and clarity in mind, as well
as a strong resemblance to the argparse module in the python standard library. With input_reader,
you can define how to read your input files in a relatively small number of lines (~1-2 per keyword)
that are (for the most part) easy to read.

## Scientific Projects

### RAPID

[Code](https://github.com/jensengrouppsu/rapid)

Simulate IR and Raman peaks with fast exchange.  This is only useful if you
are a vibrational spectroscopist.

### NPSpec

[API Documentation](http://sethmmorton.github.io/NPSpec) | [Code](https://github.com/SethMMorton/NPSpec)

The NPSpec library aids in solving the optical properties of nanoparticles. 
NPSpec provides interfaces for four languages: C, C++, Fortran, and Python.

### NPVol

[Code](https://github.com/jensengrouppsu/npvol)

This program is designed to find the volume of a nanoparticle using Monte Carlo
integration. The volume of your nanoparticle is a nice thing to know in certain
cases, because you can use it to

 * apply a size-correction factor to your dielectric constant to account for
   quantum-size effects
 * determine the absorption/scattering/extinction cross-section of your nanoparticle
 * determine the density of your nanoparticle
 
I'm sure there are many other wonderful uses, but these are why I am interested
in the volume.

## Other Projects

### CMake Fortran Template

[Code](https://github.com/SethMMorton/cmake_fortran_template)

A template directory structure for a Fortran project using CMake as the build system.

---
layout: page
title: "Projects"
description: "My Projects"
---
{% include JB/setup %}

## Python Libraries

### natsort

[Documentation](http://pythonhosted.org/natsort/index.html) | [Code](https://github.com/SethMMorton/natsort) | [Download](https://pypi.python.org/pypi/natsort)

The natsort package provides a key helps sorts lists "naturally"; that is it sorts alphabetically
and numerically, and not lexicographically. It provides support for ints and floats
(including negatives and exponential notation) as well as version numbers (i.e. 1.2.3).

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

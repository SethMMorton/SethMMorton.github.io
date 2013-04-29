---
layout: post
title: "Using CMake to find OpenMP flags for Fortran compilers"
description: "Using CMake to find OpenMP flags for Fortran compilers"
category: How To
tags: 
- cmake
- fortran
- parallelization
---
{% include JB/setup %}

[CMake](http://www.cmake.org/) is a wonderful tool for configuring your build system.
It has built-in support for C, C++, and Fortran projects.  The support for C and C++ is
excellent.  The support for Fortran is quite good, but it there is some missing functionality
in some areas.  For me, the most obvious deficiency was OpenMP support for Fortran.
This can be easily fixed with a little detective work and elbow grease.

### A little background

Pretty much all of the CMake functions are actually implemented as scripts in
`$SOMEPATH/share/cmake/Modules`, where for me `$SOMEPATH=/usr/local` but your mileage may
very (they will likely be in your install path on a Windows machine).  All the packages that
are known to the `FIND_PACKAGE` function must be implemented as one of these scripts with
the name `FindPackage.cmake`, where "Package" is the package that is searched for. 

If you take a look in `$SOMEPATH/share/cmake/Modules/FindOpenMP.cmake`, you will notice
that it returns the variables `OPENMP_FOUND`, `OpenMP_C_FLAGS`, `OpenMP_CXX_FLAGS`; notice
that there is no `OpenMP_Fortran_FLAGS`.  This is OK if the C compiler that CMake found
is the same vendor as your Fortran compiler (i.e. gcc & gfortran or icc & ifort) so that
you can just use the C flags for the Fortran flags.  However, this is not a robust solution;
for example, what if the openmp flags between the C and Fortran compilers of a vendor don't
match?  Or, what if you are using a gcc C compiler with an intel Fortran compiler (as I was)?
This obviously won't work.  The best solution is to tell CMake how to look for Fortran OpenMP
flags.

#### How does CMake determine the Fortran OpenMP flags?

I'm not going to go into details, but basically all known OpenMP compiler
flags are implemented.  Here is an excerpt from the `FindOpenMP.cmake` file:

{% highlight cmake %}
set(OpenMP_FLAG_CANDIDATES
  #GNU
  "-fopenmp"
  #Microsoft Visual Studio
  "/openmp"
  #Intel windows
  "-Qopenmp"
  #PathScale, Intel
  "-openmp"
  #Empty, if compiler automatically accepts openmp
  " "
  #Sun
  "-xopenmp"
  #HP
  "+Oopenmp"
  #IBM XL C/c++
  "-qsmp"
  #Portland Group, MIPSpro
  "-mp"
)
{% endhighlight %}

It then uses a simple C file that will fail without OpenMP to check for the correct flag:

{% highlight c++ %}
#include <omp.h>
int main() {
#ifdef _OPENMP
  return 0;
#else
  breaks_on_purpose
#endif
}
{% endhighlight %}

For each flag that is given in the above list, CMake attempts to compile the C snippet
using the `check_c_source_compiles` function that comes with CMake.
The first flag that does not cause a compiler error is considered the correct flag.

### Adding Fortran support for OpenMP

Note:  You can download the file `FindOpenMP_Fortran.cmake` at my [cmake_fortran_template 
GitHub page](https://github.com/SethMMorton/cmake_fortran_template/blob/master/cmake/Modules/FindOpenMP_Fortran.cmake).

Instead of adding Fortran to `FindOpenMP.cmake`, I decided to make a new 
`FindOpenMP_Fortran.cmake` file.  Essentially, I made a copy of the original
`FindOpenMP.cmake` and placed it in `${CMAKE_SOURCE_DIR}/cmake/Modules/FindOpenMP_Fortran.cmake`, 
where `${CMAKE_SOURCE_DIR}` is the top level directory of my Fortran project.

I decided that in addition to simply finding the Fortran flag, I wanted my function
to also determine the max number of threads that OpenMP can use on the current system
and return that as well.  To do that, I used the following Fortran snippet:

{% highlight fortran %}
program TestOpenMP
 use omp_lib
 write(*,'(I2)',ADVANCE='NO') omp_get_num_procs()
end program TestOpenMP
{% endhighlight %}

Interestingly, there is no `check_fortran_source_compiles` (someone should request this)
so all the testing had to be done manually.  The business end of this code is given
below:

{% highlight cmake %}
TRY_RUN (OpenMP_RUN_FAILED OpenMP_FLAG_DETECTED ${CMAKE_BINARY_DIR}
    ${CMAKE_BINARY_DIR}${CMAKE_FILES_DIRECTORY}/CMakeTmp/testFortranOpenMP.f90
    COMPILE_DEFINITIONS ${CMAKE_REQUIRED_DEFINITIONS}
    CMAKE_FLAGS -DCOMPILE_DEFINITIONS:STRING=${MACRO_CHECK_FUNCTION_DEFINITIONS}
    COMPILE_OUTPUT_VARIABLE OUTPUT
    RUN_OUTPUT_VARIABLE OMP_NUM_PROCS_INTERNAL)
{% endhighlight %}

The correct flag is given in `OpenMp_Fortran_FLAGS`, and the number of OpenMP
threads is given in `OMP_NUM_PROCS`.

Using this is as simple as:

{% highlight cmake %}
SET(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/Modules/")
FIND_PACKAGE(OpenMP_Fortran)
SET_TARGET_PROPERTIES(${EXECUTABLE_NAME} PROPERTIES
                      COMPILE_FLAGS "${OpenMP_Fortran_FLAGS}"
                      LINK_FLAGS "${OpenMP_Fortran_FLAGS}")
{% endhighlight %}

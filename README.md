# FibExtensionModule
Just a short guide on writing Python C++ extension modules with SWIG

For this module the C++ code was compiled with Mingw64 (i686-w64-mingw32-g++)

The first step is installing Mingw64 and SWIG after doing so the Mingw /bin/ files (.exe) and the SWIG (.exe) files should be set to your environment variables so they can be called from the command line.

First step is to actually write and debug the C++ function you want to interface with Python.

Because I am using approximation and not recursion I did the calculation in C++
```c++
// fib.cpp
#include <iostream>
#include <cmath> 

double Fibonacci(int n) {
	static const double phi = (1 + sqrt(5))*0.5;
	double fib = (pow(phi, n) - pow(1 - phi, n)) / sqrt(5);
	return round(fib);
}
```
Next we build our SWIG template file.
```
/* File: fib.i */
%module fib
%{
extern double Fibonacci(int n);
%}
extern double Fibonacci(int n);
```
Using Swig we prep our Module.
```
cmd$> swig -c++ -python fib.i
```
This generates a fib.py file as well as a fib_wrapper.cxx (C++ file).
Next we need to compile our C++ code (Mingw64) and then generate the phython DLL file so we can load our module.
We have to be sure to include the python Include header files!
```
i686-w64-mingw32-g++ -c fib.cpp -I C:\Python27\include
```
Then Compile the wrapper code.
```
i686-w64-mingw32-g++ -c fib_wrap.cxx -I C:\Python27\include
```
This leaves us with two compiled O files.
```
fib.o & fib_wrap.o
```
Lastly, we need to create a python DLL linker to our Compiled files in order to load the Module in python.
```
i686-w64-mingw32-g++ -shared -I C:\Python27\include -L C:\Python27\libs fib.o fib_wrap.o -o _fib.pyd -lpython27

Note: If you get an error with a definition you need to browse to the location to rename the string since some of the modules get renamed to "_filename" during the compilation.
Example:
C:/Program Files (x86)/mingw-w64/i686-7.3.0-posix-dwarf-rt_v5-rev0/mingw32/lib/gcc/i686-w64-mingw32/7.3.0/include/c++/cmath:1136:11: error: '::hypot' has not been declared
   using ::hypot;
 Browsing to the file: "C:/Program Files (x86)/mingw-w64/i686-7.3.0-posix-dwarf-rt_v5-rev0/mingw32/lib/gcc/i686-w64-mingw32/7.3.0/include/c++/cmath"
 #define hypot was renamed to _hypot to fix this error this is due to the way some of the links are renamed during compilation.
```

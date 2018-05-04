# FibExtensionModule
Just a short guide on writing Python C++ extension modules with SWIG

For this module the C++ code was compiled with Mingw64 (i686-w64-mingw32-g++)

The first step is installing Mingw64 and SWIG after doing so the Mingw /bin/ files (.exe) and the SWIG (.exe) files should be set to your environment variables so they can be called from the command line.

First step is to actually write and debug the C++ function you want to interface with Python.

```c++
#include <iostream>
#include <cmath> 

double Fibonacci(int n) {
	static const double phi = (1 + sqrt(5))*0.5;
	double fib = (pow(phi, n) - pow(1 - phi, n)) / sqrt(5);
	return round(fib);
}
```

# FibExtensionModule
Just a short guide on writing C++ extension modules with SWIG for Python.

For this module the C++ code can be easily compiled using the Builtin Distutils module from Python.

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
Next we need to compile our C++ code using distutils. We Create a "setup.py" file that links to our pre-compiled files.
Note that our pre-compiled code and the wrapper python file are linked to in the setup portion.
```python
# setup.py file

from distutils.core import setup, Extension

ext_module = Extension('_fib',
                           sources=['fib_wrap.cxx', 'fib.cpp'],
                           )

setup (name = 'fib_calculator',
       version = '0.1',
       author      = "TD4B",
       description = """Fibbonacci Calculator""",
       ext_modules = [ext_module],
       py_modules = ["fib"],
       )
```
After creating the setup file we can compile our code into a python extension module using Distutils.
```
cmd$> python setup.py build_ext --inplace
```
After we have successfully compiled the code and created the python "pyd" DLL we are all set for loading and running our C++
extension module.

Example:
```python
# file: runme.py

import fib

arr = []
for i in range(0,100):
	arr.append('{:0.3e}'.format(fib.Fibonacci(i)))
print arr
```
Results from Calculation:

```
C:\Examples\python\fib>python2 runme.py
['0.000e+00', '1.000e+00', '1.000e+00', '2.000e+00', '3.000e+00', '5.000e+00', '8.000e+00', '1.300e+01', '2.100e+01', '3.400e+01', '5.500e+01', '8.900e+01', '1.440e+02', '2.330e+02', '3.770e+02', '6.100e+02', '9.870e+02', '1.597e+03', '2.584e+03', '4.181e+03', '6.765e+03', '1.095e+04', '1.771e+04', '2.866e+04', '4.637e+04', '7.502e+04', '1.214e+05', '1.964e+05', '3.178e+05', '5.142e+05', '8.320e+05', '1.346e+06', '2.178e+06', '3.525e+06', '5.703e+06', '9.227e+06', '1.493e+07', '2.416e+07', '3.909e+07', '6.325e+07', '1.023e+08', '1.656e+08', '2.679e+08', '4.335e+08', '7.014e+08', '1.135e+09', '1.836e+09', '2.971e+09', '4.808e+09', '7.779e+09', '1.259e+10', '2.037e+10', '3.295e+10', '5.332e+10', '8.627e+10', '1.396e+11', '2.259e+11', '3.654e+11', '5.913e+11', '9.567e+11', '1.548e+12', '2.505e+12', '4.053e+12', '6.557e+12', '1.061e+13', '1.717e+13', '2.778e+13', '4.495e+13', '7.272e+13', '1.177e+14', '1.904e+14', '3.081e+14', '4.985e+14', '8.065e+14', '1.305e+15', '2.111e+15', '3.416e+15', '5.528e+15', '8.944e+15', '1.447e+16', '2.342e+16', '3.789e+16', '6.131e+16', '9.919e+16', '1.605e+17', '2.597e+17', '4.202e+17', '6.799e+17', '1.100e+18', '1.780e+18', '2.880e+18', '4.660e+18', '7.540e+18', '1.220e+19', '1.974e+19', '3.194e+19', '5.168e+19', '8.362e+19', '1.353e+20', '2.189e+20']
```

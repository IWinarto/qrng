﻿# qrng

## Description

This POSIX-compliant Shell script sends HTTP GET requests to [ANU's QRNG](https://qrng.anu.edu.au/) to get random
non-negative integers printed out as numbers in some user-specified base. 

ANU is The Australian National University, and QRNG stands for Quantum Random Number Generator.

## What problems does this script solve?

### Problem 1

The [QRNG@ANU JSON API](https://qrng.anu.edu.au/API/api-demo.php) returns random non-negative numbers in either one of 
the following ranges only per request:

* ![](https://latex.codecogs.com/gif.latex?\left[0,&space;255&space;\right&space;])
* ![](https://latex.codecogs.com/gif.latex?\left[0,&space;65535&space;\right&space;])
* ![](https://latex.codecogs.com/gif.latex?$\left[0,16^{2\times block\_size}-1&space;\right&space;]$)

where, ![](https://latex.codecogs.com/gif.latex?$1&space;\leq&space;block\_size&space;\leq&space;1024$). This 
means the user cannot request a custom range of random non-negative numbers.

This script enables the user to specify a custom range of random non-negative numbers up to the QRNG limit 
![](https://latex.codecogs.com/gif.latex?$16^{2048}&space;-&space;1$) with one very important property:

> the custom range that the user specified is still uniformly random, i.e. no bias!

Details of the algorithm that preserves uniform randomness of custom ranges will be provided in this document in the
future. If you want to read about it now, a detailed although incomplete explanation of the algorithm is provided under
the shell function `wrap_numbers`.

### Problem 2

The same API also does not provide the capability of specifying the base of the random numbers other than base-10 and
base-16. This script extends the number of bases available by providing base-2 up to base-16.

### Problem 3

POSIX.1-2017 defines the maximum limit of positive integer of any expression to be the maximum of ISO C `signed long`
type, which is at least ![](https://latex.codecogs.com/gif.latex?$2^{31}-1$). However, QRNG can provide random numbers 
that are as large as $16^2048 - 1$. This script utilizes POSIX `bc` to do arbitrary precision calculations to overcome 
the the limit.


## Usage:

`qrng [-h | -b <base>] [minimum possible number] <maximum possible number> <amount of random numbers>`

* The -h option prints out this usage.

* If the -b option is given, the random numbers printed out will be in the given base. The specified base must be an 
integer at least equal to 2 and at most 16. If this option is not specified, the default base i s 10.

* [minimum possible number] is the minimum possible value of any random number requested. It must at least be equal to
\0. If this is not specified, 0 is assumed. 

* \<maximum possible number> argument is the maximum possible value of any random number requested. It must not exceed
QRNG's limit ![](https://latex.codecogs.com/gif.latex?$16^{2048}&space;-&space;1$), and must at least be equal to 1.

* The final argument is the amount of random numbers to be requested (at least 0). If this argument is 0,  then this
script will not make requests at all, and the exit status is set to 0.                          

* The minimum possible number, maximum possible number, and amount of random numbers to request can be given as
hexadecimal numbers and/or decimal numbers. A hexadecimal argument must be prefixed by the characters 0x (in that
order) with the hexadecimal number following the character x immediately. Otherwise, the argument is expected to be a
decimal integer.

## Non-POSIX Requirement

For POSIX-compliant systems with the usual POSIX programs installed, only `curl` has to be installed before this script
can be run.

## Known POSIX Compatibility Issues

For some unknown reason, when running `yash` shell in the "posixly-correct" mode, the `g` flag of the substitute command
of `sed` is ignored. This causes the script to not work properly. This issue does not appear when running `sed --posix
-E` on `bash` with the option `set -o posix` specified.

Please open issues if you discover other compatibility problems.

## THIS DOC IS INCOMPLETE - MORE TO FOLLOW

# random

## Problem

This challenge requires a correct key to obtain the flag. Looking at the source code in *random.c*, we need (key ^ random) == 0xcafebabe, where random is a random value generated using rand().

## Approach

The C standard library function rand() is a pseudorandom number generator, meaning values produced are not truly random. One common mistake when using this function is not specifying a see with the function srand(). If no seed is specified, the default seed (1) will be used, meaning the sequence of values produced is predictable.

I made a copy of the random.c file in the tmp/ directory on the server. I did this instead of copying the file to my own machine because the implemenation of rand() is specific to that machine (notice there was no stdlib.h include).

By editing the program to print out the current value of random, we can see it is the same value each time: 0x6b8b4567. Now that we know *random* remains constant over each run, we only need to determine the value of *key* such that (key ^ random) == 0xcafebabe.

# Heap 0

## Problem

This challenge gives us a binary, source program, and connection to an instance of the executable. Running the executable, we see that the goal is to overflow the heap to modify the value at a certain memory address. 

The program gives the user a few options: to view the heap, write a value to the heap, and check if safe_var has been modified.

## Approach

Let's take a look at the source for the program. We know that there are two variables allocated on the heap: the user input and safe_var. By examining the source, we see that each are allocated 5 bytes of memory.


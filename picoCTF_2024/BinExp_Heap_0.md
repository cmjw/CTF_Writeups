# Heap 0

## Problem

This challenge gives us a binary, source program, and connection to an instance of the executable. Running the executable, we see that the goal is to overflow the heap to modify the value at a certain memory address. 

The program gives the user a few options: to view the heap, write a value to the heap, and check if safe_var has been modified.

## Approach

Let's take a look at the source for the program. We know that there are two variables allocated on the heap: the user input and safe_var. By examining the source, we see that each are allocated 5 bytes of memory.

Both variables are allocated using malloc(). This means the two 5 byte memory chunks on the heap are not guaranteed to be contiguous.

When we run the executable, the addresses of the two variables are given to us. For example, in one instance the user variable is located at 0x557bbc31f6b0, and safe_var is located at 0x557bbc31f6d0. This is a difference of 0x20, or 0d32.

Thus, by writing at least 32 characters into the heap, we can overwrite the data at the location of safe_var.

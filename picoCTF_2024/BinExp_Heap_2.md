# Heap 2

## Problem

This problem is similar to Heap 0 and Heap 1, where we need to allocate a value on the heap to overwrite the value of another location. In this iteration of the problem, we need to call a certain function to obtain the flag. (The value at safe_var is used as a pointer, and we want it to point to this function.)

## Approarch

First, let's examine the source code. The check_win() function calls another function using function pointers. We want to call win(), which will give us the flag.

Thus, our approach should be to overflow the heap by overwriting the value of safe_var with the address of the win() function. So, how do we determine the address of win()?

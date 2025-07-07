# Heap 2

## Problem

This problem is similar to Heap 0 and Heap 1, where we need to allocate a value on the heap to overwrite the value of another location. In this iteration of the problem, we need to call a certain function to obtain the flag. (The value at safe_var is used as a pointer, and we want it to point to this function.)

## Approarch

First, let's examine the source code. The check_win() function calls another function using function pointers. We want to call win(), which will give us the flag.

Thus, our approach should be to overflow the heap by overwriting the value of safe_var with the address of the win() function. So, how do we determine the address of win()? I used objdump to search for the function win, and its address, which is 0x4011a0.

So our value to input is: {32 characters} 0x4011a0. Actually, we need to remember that addresses are handled in little endian here. So: {32 characters} 0xa01140.

To input hex characters more easily, I used a Python2 script to pass the string '1'*32 + '\xa0\x11\x40\x00' as the value, which will give us the flag.

To recap: we wanted to store a pointer to the win() function at the location of the variable x. This variable is then dereferenced as a function pointer, which executes the win() function, giving us the flag. 

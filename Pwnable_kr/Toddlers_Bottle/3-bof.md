# bof

## Problem

* We can execute the binary using $nc 0 9000
* The program requests an input value.
* TO obtain the flag, the *key* variable must be equal to 0xcafebabe.
* Examining the source code, we see the input is obtained using gets() into the buffer overflowme.
* The gets() has a known vulnerability that it fails to detect a buffer overflow.
* Since the buffer is a char array of 32, we need to overflow the buffer past the 32 characters expected.
* By simply overflowing the buffer past this point, we get a new message: "stack smashing detected".


Thus...
* We need to remember that the buffer *overflowme* and the variable *key* are not guaranteed to be contiguous in memory.
* Thus, overflowing the input buffer with 32 arbitrary characters plus "cafebabe" does not guarantee the overflow portion will overwrite *key*.
* In the bof.c source code given, we see main() calls func() with the value 0xdeadbeef, which becomes the value of key.
* We can trace this value to determine where *key* is located.

Using gdb
* I first disassembled the binary, paying particular attention to main() and func().
* In main(), the value 0xdeadbeef is pushed onto the stack.
* In func(), the line *comp 0xcafebabe, 0x8(%ebp)* compares the key to the expected value.
* We can set a breakpoint at gets() to check the value at this address during runtime.
* Using the command *x $ebp+8*, we can print the hexadecimal value at the address %ebp+8.
* This value is 0xdeadbeef, located at address 0xffffcf00.

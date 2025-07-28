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

Finding the address of key
* I first disassembled the binary, paying particular attention to main() and func().
* In main(), the value 0xdeadbeef is pushed onto the stack.
* In func(), the line *comp 0xcafebabe, 0x8(%ebp)* compares the key to the expected value.
* We can set a breakpoint at gets() to check the value at this address during runtime.
* Using the command *x $ebp+8*, we can print the hexadecimal value at the address %ebp+8.
* This value is 0xdeadbeef, located at address 0xffffcf00 during runtime when I checked.

Finding the address of overflowme
* Similarly, I used gdb.
* Before the call to gets() in func(), there is a LEA instruction that stores an address in %eax, which is then pushed to the stack.
* We can infer this is the address of the buffer overflowme.
* I set a breakpoint at gets(), and checked the value of %eax.
* As expected, it contains a pointer to the value entered to gets().
* In the same run as above, the location was 0xffffcecc.

Putting it all together
* So we have the key located at, say 0xffffcf00 and the buffer at 0xffffcecc for example.
* The difference between the two addresses is 0x34, or 0d52.
* Thus, now we know there are 52 bytes in between the start of the buffer and key.
* So we need to provide the program with a value of 52 arbitrary bytes, plus the value 0xcafebabe (in little endian)!

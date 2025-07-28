# bof

## Problem

* We can execute the binary using $nc 0 9000
* The program requests an input value.
* TO obtain the flag, the *key* variable must be equal to 0xcafebabe.
* Examining the source code, we see the input is obtained using gets() into the buffer overflowme.
* The gets() has a known vulnerability that it fails to detect a buffer overflow.
* Since the buffer is a char array of 32, we need to overflow the buffer past the 32 characters expected.
* By simply overflowing the buffer past this point, we get a new message: "stack smashing detected".

* We need to remember that the buffer *overflowme* and the variable *key* are not guaranteed to be contiguous in memory.
* Thus, overflowing the input buffer with 32 arbitrary characters plus "cafebabe" does not guarantee the overflow portion will overwrite *key*.

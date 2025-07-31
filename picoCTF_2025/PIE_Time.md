# PIE Time

## Problem

For this challenge, we are given an instance running on a server, along with the binary and its source code. Examining the source code, the program prints the address of main(), and asks the user to input an address. If the address is that of the win() function, we will get a flag.

## Approach

We need to identify the address of win(). Using *objdump* on the binary given to us, we see the address of main() is 0x133d, and the address of win() is 0x12a7. However, when we connect to the instance running on the server, we can see the address of main is printed, and is a different value.

As hinted in the challenge name, the binary is a PIE (position independent executable) file (we can verify this using *file vuln*). This means it uses ASLR (address space layout randomization) to determine the location of functions and data in memory. Thus, the addresses we have found of main() and win() in the disassembled *vuln* binary will not necessarily be the same addresses during runtime. The offset between the two functions, however, will remain constant, independent of the base offset.

To locate win() on the server instance, we can calculate the offset between the local and server main(), and add the local address of win(). Thus we need to calculate *server main() address - local main() address + local win() address*.

For example, if the server address of main() is 0x632fb8a152a7, then the server address of win is 0x632fb8a152a7 - 0x133d + 0x12a7 = 0x632fb8a152a7.

## Resources

* https://www.redhat.com/en/blog/position-independent-executables-pie

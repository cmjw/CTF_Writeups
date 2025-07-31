# PIE Time

## Problem

For this challenge, we are given an instance running on a server, along with the binary and its source code. Examining the source code, the program prints the address of main(), and asks the user to input an address. If the address is that of the win() function, we will get a flag.

## Approach

We need to identify the address of win(). Using *objdump* on the binary given to us, we see the address of main() is 0x133d, and the address of win() is 0x12a7. However, when we connect to the instance running on the server, we can see the address of main is printed, and is a different value.

To locate win() on the server instance, we can calculate the offset between the local and server main(), and add the local address of win().

Thus we need to calculate *server main() address - local main() address + local win() address*.

For example, if the server address of main() is 0x632fb8a152a7, then the server address of win is 0x632fb8a152a7 - 0x133d + 0x12a7 = 0x632fb8a152a7.

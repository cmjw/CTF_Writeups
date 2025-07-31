# PIE Time 2

## Problem

We are given an instance running on a server. The program asks for a name, then an address to jump to. Similarly to PIE Time (1), we need to provide the address of a function, win(), that will print the flag. The binary given is a PIE executable, similar to the previous problem.

Unlike the first PIE Time, we are not given the address of main() during runtime. The program also asks for a name, so perhaps this can be used in some way.

## Approach

In the local binary, main() is located as 0x1400 and win() at 0x136a.

We know the binary running is a PIE executable, so it uses ASLR (address space layout randomization).

## Resources

* https://www.redhat.com/en/blog/position-independent-executables-pie
* https://pollevanhoof.be/nuggets/buffer_overflow_linux/5_PIE_bypass

# 0x41haz

## Problem

We are given a program that asks for a password. The user is tasked with finding this password, which will give us a flag once inputted.

The description notes the author has used anti-reverse engineering techniques to make analysis more challenging.

## Approach

An initial search using the *strings* function does not reveal much. Using the *file* command, the format of the binary is unknown. It recognizes it an an ELF 64-bit with MSB encoding, but unknown architecture. This makes disassembly challenging. Similarly, *objdump* does not recognize the format.

The challenge mentioned it is using anti-reverse-engineering measures, so the author may have edited the ELF header. I next consulted the ELF documentation regarding the header, which indicates the file format: https://man7.org/linux/man-pages/man5/elf.5.html.

- The 5th bit indicates 32-bit (0x1) or 64-bit (0x2).
- The 6th bit determines little endian (0x2) or big endian (0x1).

Changing the 5th bit to 32-bit does not help *objdump* or *file* recognize the format. Changing the 6th bit to little endian does! The file is now recognized at an ELF 64-bit pie executable in x86-64.

Now we can try disassembing using Ghidra. Examining the entry point, we can see a call to a pointer containing some function address. Before this, we see the address of RUN_00101165 loaded into RDI. Examining the decompiled code of this function, we see the familiar text asking for and verifying the password. After getting user input, the program first checks if it is the correct length. Next, it checks each character of the input against the correct password, stored in local_le. Going back the beginning of this function, we can see the variable local_le is set using strncpy to a string value, which is the correct password!

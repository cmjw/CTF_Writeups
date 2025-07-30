# pwnable.kr - 1. fd

After connecting to the server, I first checked permissions of the file *flag*. We can see we do not have permission to read, write, or execute; notice that the executable *fd* is in the same group (fd_pwn) with read permissions.

Next I analyzed *fd.c*. This program takes in an input, and reads from a file at the file descriptor *fd* into a buffer. The file descriptor is the user input minus 0x1234. If the string read from this buffer is "LETMEIN", the program will read the contents of *flag*.

With a bit of research, I found that by default the first file descriptor (*fd*=0) points to stdin. If we can read from file descriptor 0, we can read from stdin via the command line, and provide this string, "LETMEIN", to read out the flag!

The final step is to provide an input of 0 minus 0x1234 to read from fd=0 (stdin), and provide the correct string.

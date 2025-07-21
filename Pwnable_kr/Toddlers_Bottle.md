# pwnable.kr - Toddler's Bottle

## 1. fd

* Check permissions of flag. Don't have perms. Need to be fd_pwn to r/w/x. Notice the executable fd is in this group.
* Analyze fd.c.
* Take in input; fd := input as a number - 0x1234.
* Then read the file at this fd into buffer buf.
* If string read from buffer is equal to "LETMEWIN", should read the flag from the file.
* 

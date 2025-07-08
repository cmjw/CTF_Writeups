# Reversing ELF

This room focusing on reverse engineering ELF, which is a common executable file format for Linux and Unix-based operating systems.

## Part One

Simply changing file permissions and executing prints out the flag.

## Part Two

Upon running the executable we are prompted for a password. Using the _strings_ command, we can view the string values in the executable file. 

By viewing the strings, we can easily determine which one is the password.

## Part Three

We are prompted for a password again. Using the _strings_ program, we can see a string with a "==" suffix (indicating it is encoded in base64 format). Decoding this string will give us the password.

## Part Four

This time _strings_ does not reveal any passcode, encoded or otherwise. We know that the program must compare the user input to the password string at some point.

We can examine the functions defined in the program using gdb (info functions). We see there is a strcmp() function, which is likely used to check against the password.

Next, we can set a breakpoint at the address of this function. Once the program reaches this point, we can examine the register values. Examining the contents of each register as a string, register %rax holds the value of the password.

## Part Five

Similarly to Part Four, we can use gdb to view register values. Again we are targeting the input values to a call to strcmp(). This time, the user input is in %rax, and the password is in %rbx.

## Part Six

This time, I examined the executable's assembly instructions using _objdump_. There are two functions of note: compare_pwd() and my_secure_test(). It seems this time the author has hidden the password value more carefully.

Next, I disassembled the program using Ghidra. We can see from the code generated that main() calls compare_pwd(), which calls my_secure_test(). 

my_secure_test() compares the user input to an 8-character value, one character at a time. Putting these characters together as a string gives us the password.

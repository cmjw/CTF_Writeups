# passcode

## Problem

We are told to investigate a password system to obtain the flag. The description gives a hint that there may be some unsafe usage of C library functions.

Examining the contents of the passcode/ directory, we have three files: *flag*, *passcode*, and *passcode.c*. 

The program prompts the user for their name, and then password1. It then checks this first password and the second password, password2.

It seems we have to somehow use the password1 input to overwrite password2.


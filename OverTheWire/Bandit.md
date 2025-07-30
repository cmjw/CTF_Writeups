# Bandit

## Level 0

Connect to the server specified in the Level 0 description. To connect to an ssh server, use the command $ssh <user>@<domain> -p<port_number>.

To find the password for Level 1, read the file "readme". For example, use the *cat* command.

## Level 1

We need to research how to open (or reference) a file with the filename "-", which is treated as a special character.

One possible way to view the contents of the file is to reference it as *./-*.

## Level 2

Similar to the last level, we need to reference a file with special characters in the filename - this time containing spaces.

One possibility is to surround the filename in double quotes: "./spaces in this filename".

## Level 3

For this level, we need to access a hidden director, *inhere*. Using ls -al, we can see the hidden directory, and navigate to it using $cd ./inhere. 

Once in the hidden folder, we can list its hidden contents using *ls -al*, and cat out the hidden file "*... Hiding-From-You*".

## Level 4

Again, we need to navigate to the hidden directory *inhere*. Within the directory, there are nine files: -file00 through -file09. Viewing the contents of these files (using the filename in quotes, since they begin with the special character "-"), we see most contain gibberish. One file's contents contains no special characters, so we can assume this is the key for the next challenge.

## Level 5

In the directory *inhere*, we are tasked with finding a file with the following characteristics: 1) it is human readable, 2) it is 1033 B, and 3) it is not executable. 

There are a few methods to find such a file. First, I used the command *$du -ab .* to list the size of all sub-directories and files. We can see most files have a unique size. One method would be to comb through this list to find any file 1033 B large.

Another method to search by file size is to use the *find* command, for example *find . -size 1033c*. This search returns one file. Using the *file* command, we see the file is ASCII text, meaning it is not executable. And viewing its contents, we see it is human-readable. So we can conclude we have found the next key.

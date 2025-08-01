# Bandit

Located at https://overthewire.org/wargames/bandit/

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

## Level 6

We need to find a file that is 1) owned by user *bandit7*, 2) owned by group *bandit6*, and 3) 33 B in size. We can use the *find* command, and specify the user and group. Since the key may be anywhere on the server, we need to specify the entire server as the search directory. I used the command *$find . -user bandit7 -group bandit6 -size 33c*, which returned one file.

## Level 7

For this level, we are given a file, *data.txt*, with several lines in the format of <keyword> <key>. We are told the correct key is preceded by the keyword "millionth".

We can use *grep* to search for a matching string: *$grep "millionth" data.txt*.

## Level 8

Similar to the previous level, we are given a large file *data.txt* that contains several possible keys. Our hint is that the correct key only occurs once in the file. So, we need to filter the contents of the file to find the line that only occurs once.

We can use *uniq* with the *-u* option to list unique (non-repeated) lines. However, *uniq* only detects *adjacent* repeated lines, so we need to sort the contents of *data.txt* before providing them to *uniq*, for example using the *sort* command.

Combining all of this: we can pipe the sorted contents of *data.txt* to *uniq*, returning only the unique line(s). I used the command *$sort data.txt | uniq -u*.

## Level 9 

We are given a hint that the key is preceded by several "=" characters. Investing *data.txt*, this time it is in binary format. To search a binary file as if it was text, we can use the *grep* "-a" option. I searched for the "=" character using the following command: *$grep -a "=" data.txt*.

## Level 10

This time the key is encoded, specifically in base64 format. We can use the *base64* program to decode *data.txt* as follows: *$base64 -d data.txt*.

## Level 11

We are given a hint that the key is encoded using the ROT13 cipher. This cipher applied to alphabetic text shifts each character by 13. Since there are 26 letters in the alphabet, we can apply another round of ROT to "undo" the encoding.

Thus, we need to shift alphabetic characters (uppercase and lowercase) 13 characters, wrapping back around from Z to A. In other words, we need to map A-Z to N-ZA-M, and a-z to n-za-m. We could reverse this encoding by using an already-built tool, but why not try it ourselves? I solved this challenge using the following command: *$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'*.

## Level 12

I first copied *data.txt* into a temporary directory, renaming it *hexdump*. I then reverse the hexdump, putting the contents in a file called *reversed* (using *xxd -r hexdump reversed*).

Since we know the hexdump comes from a compressed file, we can examine the header to determine which compression format was used. The first two bytes are "1F 8B", which matches the signature for the *gzip* format (determined using this [list](https://en.wikipedia.org/wiki/List_of_file_signatures) of signatures). 

If we attempt to decompress this data as-is, we get a warning from *gzip*: "unknown suffix". We need to rename the file with a .gz suffix, then we can decompress it as *data1*.

This file is still compressed. A hexdump of *data1* reveals this file is compressed in the bzip2 format. We know this because the signature matches this format. 

Again, the result file is compressed. This time it is gzip format. Again, we can rename the file and decompress it.

This time we have an archive. We can rename with the .tar extension and extract to data5.bin. (Use *xxd <> | head* to dump the header.) Again we can extract to data6.bin.

Once again we have a bzip2 file to decompress. Then an archive to extract. Next, a gzip compressed file.

Finally, the file is in human-readable format, and we have our next key!

## Level 13

This time, we are given a private SSH key to use to connect to Level 14. We need to configure this private key correctly on our host machine to access the server at bandit13@bandit.labs.overthewire.org.

I copied the private key from *sshkey.private* on bandit13 to my ~/.ssh directory as *id_rsa*. To make sure no other user has r/w/x permission, we can execute *chmod 600 id_rsa*. Now when we ssh into bandit14, we are not asked for a password, and are connected via the ssh key.

## Level 14

Aftering sshing into the server as the bandit14 user, the next key is located at /etc/bandit_pass/bandit14. We need to send this key (the bandit14 password) to port 30000 on localhost to obtain the next key.

We can use *nc* (netcat) to connect to the port specified to send the password: *nc 127.0.0.1 30000*. When connected, enter the password for bandit14, and the response will contain the password for bandit15.

## Level 15

This time we need to connect to port 30001 on localhost using SSL/TLS encryption. The program *ncat* expands *nc*, for example adding support for SSL-encrypted connections. 

I used the following command: *ncat --ssl 127.0.0.1 30001*, and entered the passcode to bandit15.

## Level 16

We need to submit the bandit16 password to a port on localhost in the range 31000 to 32000 - but we do not know which one. The correct port will have a server listening on it, and will use SSL/TLS.

We can use *nmap* to scan for open ports. In this case, we are looking for ports on localhost (127.0.0.1) from 31000 to 32000 that use SSL/TLS. I used the command *nmap -sT -v --script ssl-cert 127.0.0.1 -p31000-32000* to target these criteria. *nmap* returns two listening ports: 31518 and 31790. 

I connected to each port using *ncat --ssl 127.0.0.1 <port>*, and upon entering the bandit16 password, port 31790 responds with a private ssh key.

## Level 17

Similar to Level 13, we need to configure a private SSH key. I copied the contents of the private key to ~/.ssh/id_rsa_bandit17, modifying permissions with *chmod 600*. 

I then modified *~/.ssh/config*, adding a section for this key: 

```
Host bandit.labs.overthewire.org
HostName bandit.labs.overthewire.org
IdentityFile ~/.ssh/id_rsa_bandit17
User bandit17
IdentitiesOnly yes
```

Now we can SSH into bandit17.

As the description hints, the password for the next level can be obtained using the *password.new* and *password.old*. The next password is the line that differs in *password.new* compared to *password.old*. 

We can use the *diff* program to compare the two files: *diff password.new password.old*.

## Level 18

Using the password obtained in the previous level to SSH into bandit18, we notice we are immediately logged out. As the description states, .bashrc has been modified to log the user (us) out when we log in.

We know the password is stored in a file *readme* on the server. If we use the command *ssh bandit18@bandit.labs.overthewire.org -p2220 ls -al*, we receive back the result of the *ls -al* command, confirming the *readme* file exists, without establishing a shell ssh connection. Similarly to cat out the flag, we can execute the following command: *ssh bandit18@bandit.labs.overthewire.org -p2220 cat readme*, bypassing the modifications to *.bashrc*.
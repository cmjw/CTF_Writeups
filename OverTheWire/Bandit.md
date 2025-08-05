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

Another method to search by file size is to use the *find* command, for example:

```
$find . -size 1033c
```

This search returns one file. Using the *file* command, we see the file is ASCII text, meaning it is not executable. And viewing its contents, we see it is human-readable. So we can conclude we have found the next key.

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

We know the password is stored in a file *readme* on the server. If we use the command 

```
$ ssh bandit18@bandit.labs.overthewire.org -p2220 ls -al
```

we receive back the result of the *ls -al* command, confirming the *readme* file exists, without establishing a shell ssh connection. Similarly to cat out the flag, we can execute the following command, bypassing the modifications to *.bashrc*.

```
$ ssh bandit18@bandit.labs.overthewire.org -p2220 cat readme
```

## Level 19

For this level, we are given a binary, *bandit20-do*, that uses *setuid* to execute a subsequent command as another user- bandit20. As stated in the description, we know the password for bandit20 is located at /etc/bandit_pass/bandit20.

We can see the file is read-only for the user *bandit20*:

```
-r--------   1 bandit20 bandit20    33 Jul 28 19:03 bandit20
```

Using the *bandit20-do* binary, we can *cat* out the contents of the file by executing the command as the bandit20 user:

```
$ ./bandit20-do cat /etc/bandit_pass/bandit20
```

I found the following site useful for learning more about *setuid*: https://www.liquidweb.com/blog/how-do-i-set-up-setuid-setgid-and-sticky-bits-on-linux/.

## Level 20

We are given a setuid binary (*suconnect*) that connects to localhost at the port specified as an argument. The description notes we must use this binary to send the password for bandit20 that we obtained in the previous level.

To determine which port to connect to, I used nmap to scan for open ports on localhost:

```
$ nmap -sT -v 127.0.0.1
```

This scan discovered 8 open ports. I tried them one by one until I found interesting behavior from post 3000. Connecting to port 3000 using the *suconnect* binary, it read the password I had copied to my clipboard, and displayed a message:

```
Password matches, sending next password
```

However, the connection closes before the next password is displayed.

The description encourages us to connect to our own network daemon, to "verify it works as expected".

I used netcat to listen to an arbitrary port:
```
nc -lnvp 3001
```

Then I connected to the same port using the setuid binary.

I then was able to receieve a connection on that port, send the bandit20 password, and receive the next password:
```
$ nc -lvnp 3001
Listening on 0.0.0.0 3001
Connection received on 127.0.0.1 45580
<bandit20 password>
<bandit21 password>
```

## Level 21

We are told there is a program running automatically via *cron*, specified in /tmp/cron.d.

There is a job, /tmp/cron.d/cronjob_bandit22, with the following contents:
```
bandit21@bandit:~$ cat /etc/cron.d/cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

So, it is executing the script at /usr/bin/cronjob_bandit22.sh:
```
bandit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

We can see this script copies the bandit22 password into a file in the /tmp directory, /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv.

## Level 22

Similar to the last level, we need to investigate a cron job, this time at /tmp/cron.d/cronjob_bandit23, which executes the following script:
```
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh 
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

We can see the script computes the value of the variable *mytarget*, then copies the bandit22 password to /tmp/$mytarget.

The computation of *mytarget* begins with echoing the string "I am user $myname". We know from the previous line that *myname* resolves to "bandit22". Next, this output is piped to the *md5sum* function, which calculates the MD5 checksum of the string. Finally, the output is trimmed using the *cut* command, specifically to remove the portion after the whitespace generated by md5sum.

We can compute this output for ourselves using the command line, then cat out its contents, which are the password for level 22 (the CURRENT level). Instead, the /usr/bin/cronjob_bandit23.sh script has execute permissions, so running the script would give the same output.

There is only one issue: the final /tmp/$mytarget file receives the password for the current level, bandit22. To obtain the password for bandit23, we can assume we need to perform this computation, but for user bandit23 instead. We might think at first we need to run the script as user bandit23, but there is a simpler way. We can simply generate the $mytarget value by performing the same series of computations with the literal string "bandit23":
```
$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
```



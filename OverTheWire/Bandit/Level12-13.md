# Bandit: Level12 -> Level13

## Challenge Description

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)

### Tools Used

- cat
- tr

### Steps

1. Use the following command to log into the machine;
   `ssh bandit10@bandit.labs.overthewire.org -p 2220`
2. Create a temporary directory using `mkdir /tmp/<dir_name>`
3. Copy the file to the temporary directory using `cp data.txt /tmp/<dir_name>`
4. Use `xxd -r data.txt > data1` to reverse the hexdump to get the file.
5. Check the filetype using `file data1`. We can see that the file is a gzip compressed file.
6. Use `gunzip -c data1 > data2` to decompress the file.
7. Check the filetype again and use the following commands to decompress the file according to the filetype. Continue till you get a ASCII text file.

    | Filetype | Command |
    |----------|---------|
    | gzip compressd | `gunzip -c <filename> <new_filename>` |
    | bzip2 compressed | `bzip2 -dk <filename>` |
    | POSIX tar | `tar -xf <filename>` |

8. Print the final file to get the password

### Solution

Password for Level11: 'FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn'

### Lessons Learned

- Getting the original file from a hexdump.
- Learning how to know and decompress files with different compression methods.

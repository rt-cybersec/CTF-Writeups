# Bandit: Level5 -> Level6

## Challenge Description

The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

human-readable
1033 bytes in size
not executable

### Tools Used

- cd
- ls
- cat
- file
- du

### Steps

1. Use the following command to log into the machine;
   `ssh bandit5@bandit.labs.overthewire.org -p 2220`
2. Use `cd inhere` command to go into the inhere directory.
3. Use `ls -a` to view all the files in the directory.
4. We can use `file */{.,}* | grep ASCII` to list all the files that are human readable.
5. We can use `du -b -a | grep 1033` command to list all files of size 1033 bytes.
6. We find that ./maybehere07/.file2 has come up in both lists.
7. We then use `cat` to print the contents of the file.

### Solution

Password for Level6: 'HWasnPhtq9AVKe0dmk45nxy20cvUa6EG'

### Lessons Learned

- Learned how to filter files by types using the grep command in conjecture with file.
- Learned how to filter files by size by combining the grep and du commands
- Discovered the importance of cross-referencing results from different commands to narrow down files of interest.

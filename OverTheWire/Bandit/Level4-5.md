# Bandit: Level4 -> Level5

## Challenge Description

The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.

### Tools Used

- ls
- cat

### Steps

1. Use the following command to log into the machine;
   `ssh bandit4@bandit.labs.overthewire.org -p 2220`
2. Use `cd inhere` command to go into the inhere directory.
3. Use `ls -a` to view all the files in the directory.
4. Use `file ./-file0{0..9}` command to check the type of each file.
5. As we can see the only **-file07** is ASCII text, we use `cat ./-file07` to view it's content.

### Solution

Password for Level1: '4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw'

### Lessons Learned

- Learned how to check the type of file
- Learned how to use `..` to run a single command on multiple files with incrementing names.

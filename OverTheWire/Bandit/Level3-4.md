# Bandit: Level3 -> Level4

## Challenge Description

The password for the next level is stored in a hidden file in the inhere directory.

### Tools Used

- cat
- ls
- cd

### Steps

1. Use the following command to log into the machine;
   `ssh bandit3@bandit.labs.overthewire.org -p 2220`
2. Use `cd inhere` command to go into the inhere directory.
3. Use `ls -a` to view all the files in the directory. The -a option specifies that all the files should be shown including hidden files.
4. Use `cat ...Hiding-From-You` to view the contents.

### Solution

Password for Level3: '2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ'

### Lessons Learned

- Learned how to traverse into a directory
- Learned how to view hidden files in a directory.

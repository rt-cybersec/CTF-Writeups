# Bandit: Level1 -> Level2

## Challenge Description

The password for the next level is stored in a file called - located in the home directory.

### Tools Used

- cat

### Steps

1. Use the following command to log into the machine;
   `ssh bandit1@bandit.labs.overthewire.org -p 2220`
2. Use `cat ./-` command to view the content. Here `./` represents current directory hence it refers the file explicitly.

### Solution

Password for Level1: '263JGJPfgU6LtdEvgfWU1XP5yac29mFx'

### Lessons Learned

- Learned how to use the current directory for filenames that might be special characters that could cause the shell to interpret it wrong.

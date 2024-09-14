# Bandit: Level2 -> Level3

## Challenge Description

The password for the next level is stored in a file called spaces in this filename located in the home directory.

### Tools Used

- cat

### Steps

1. Use the following command to log into the machine;
   `ssh bandit2@bandit.labs.overthewire.org -p 2220`
2. Use `cat "spaces in this filename"` command to view the content.

### Solution

Password for Level3: 'MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx'

### Lessons Learned

- Learned how handle spaces in a filename by enclosing it within quotes ("").

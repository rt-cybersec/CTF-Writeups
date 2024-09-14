# Bandit: Level0 -> Level1

## Challenge Description

The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.

### Tools Used

- ls
- cat

### Steps

1. Use the following command to log into the machine;
   `ssh bandit0@bandit.labs.overthewire.org -p 2220`
2. Use the `ls` command to see the name of the file.
3. Use `cat readme` to print the content of the file containing the password to the next level on the screen.

### Solution

Password for Level1: 'ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If'

### Lessons Learned

- Learned how to dump the contents of a file to the standard output

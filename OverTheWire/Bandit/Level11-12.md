# Bandit: Level11 -> Level12

## Challenge Description

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

### Tools Used

- cat
- tr

### Steps

1. Use the following command to log into the machine;
   `ssh bandit10@bandit.labs.overthewire.org -p 2220`
2. Use `cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'` to print the password.

### Solution

Password for Level11: '7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4'

### Lessons Learned

- Learned how to decode rotational cyphers.

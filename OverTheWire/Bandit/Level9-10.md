# Bandit: Level9 -> Level10

## Challenge Description

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

### Tools Used

- cd
- sort
- uniq

### Steps

1. Use the following command to log into the machine;
   `ssh bandit9@bandit.labs.overthewire.org -p 2220`
2. Use `strings data.txt | grep ===` to print the unique line in the file.

### Solution

Password for Level10: 'FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey'

### Lessons Learned

- Learned how to search in a binary file by combining strings and grep commands.

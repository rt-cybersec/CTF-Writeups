# Bandit: Level8 -> Level9

## Challenge Description

The password for the next level is stored in the file data.txt and is the only line of text that occurs only once

### Tools Used

- cd
- sort
- uniq

### Steps

1. Use the following command to log into the machine;
   `ssh bandit9@bandit.labs.overthewire.org -p 2220`
2. Use `sort data.txt | uniq -u` to print the unique line in the file.

### Solution

Password for Level9: '4CKMh1JI91bUIZZPXDqGanal4xvAg0JM'

### Lessons Learned

- Learned how to search for unique lines in a file by combining sort and uniq commands.

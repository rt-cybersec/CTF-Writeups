# Bandit: Level7 -> Level8

## Challenge Description

The password for the next level is stored in the file data.txt next to the word millionth

### Tools Used

- cd
- grep

### Steps

1. Use the following command to log into the machine;
   `ssh bandit6@bandit.labs.overthewire.org -p 2220`
2. Use `grep "millionth" data.txt` to print the line containing millionth.

### Solution

Password for Level8: 'dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc'

### Lessons Learned

- Learned how to use the grep command to search in files

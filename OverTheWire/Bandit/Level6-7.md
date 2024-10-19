# Bandit: Level6 -> Level7

## Challenge Description

The password for the next level is stored somewhere on the server and has all of the following properties:

owned by user bandit7
owned by group bandit6
33 bytes in size

### Tools Used

- cd
- find
- cat

### Steps

1. Use the following command to log into the machine;
   `ssh bandit6@bandit.labs.overthewire.org -p 2220`
2. Use `cd /` command to go into the main directory.
3. Use `find . -user bandit7 -group bandit6 -size 33c` to filter the file which satisfies our conditions.
4. We can use `cat` to list the contents of the file to get the password to the next level.

### Solution

Password for Level7: 'morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj'

### Lessons Learned

- Learned how to use the find command and its arguments to search for a file.
- Learned how to filter files using multiple conditions like ownership, group ownership and size.

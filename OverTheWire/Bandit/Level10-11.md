# Bandit: Level10 -> Level11

## Challenge Description

The password for the next level is stored in the file data.txt, which contains base64 encoded data.

### Tools Used

- base64

### Steps

1. Use the following command to log into the machine;
   `ssh bandit10@bandit.labs.overthewire.org -p 2220`
2. Use `base64 -d data.txt` to print the password.

### Solution

Password for Level11: 'dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr'

### Lessons Learned

- Learned how to decode base65.

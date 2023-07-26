# hijacking

## Challenge

Getting root access can allow you to read the flag. Luckily there is a python file that you might like to play with.

## Solution

Using `ls -a`, we see there is a python file. However, the challenge can be solved while completely ignoring this file.

We attempt running a command as root, such as `sudo python3`, but see `Sorry, user picoctf is not allowed to execute '/usr/bin/python3' as root on challenge.`

We run `sudo -l` to see which commands we can execute as root:

```
User picoctf may run the following commands on challenge:
    (ALL) /usr/bin/vi
    (root) NOPASSWD: /usr/bin/python3 /home/picoctf/.server.py
```

We can use vi as `root` and edit any file. We will edit `/etc/sudoers.d/picoctf`, which controls which commands we can run with `sudo`.

We run `sudo vi /etc/sudoers.d/picoctf` and edit the line `picoctf  ALL=(ALL) /usr/bin/vi` to `picoctf ALL=(ALL:ALL) ALL`. We overwrite the readonly file with `:w!` and quit. Now we can execute any command as root.

The flag is located at `/root/.flag.txt`, which we now have sufficient permissions to view.

 `picoCTF{pYth0nn_libraryH!j@CK!n9_f56dbed6}`



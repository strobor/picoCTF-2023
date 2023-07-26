# VNE

## Challenge

We've got a binary that can list directories as root, try it out !!

## Solution

Logging in, we see our home directory contains only the executable `bin`. Attempting to run it, we get the output `Error: SECRET_DIR environment variable is not set`.

We try setting `SECRET_DIR="/root"` and running the command again. This time, it outputs:

```
Listing the content of /root as root:
flag.txt
```

We cannot directly view the flag because we don't have sufficient permissions. We try to exploit the binary using the `SECRET_DIR` variable. Running `SECRET_DIR="; cat /root/flag.txt"`and `./bin` outputs the flag.

`picoCTF{Power_t0_man!pul4t3_3nv_3f693329}`

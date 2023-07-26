# two-sum

## Challenge

Can you solve this?
What two positive numbers can make this possible: `n1 > n1 + n2 OR n2 > n1 + n2`
[Source](https://artifacts.picoctf.net/c/456/flag.c)

## Solution

Looking in the provided source, we can see that the numbers are stored as 32 bit integers.

If an integer attains a value greater than `INT_MAX`, which is `2147483647`, then it will overflow and become negative. So, any two positive numbers whose sum is greater than INT_MAX will satisfy the condition. One possibility is `2147483647` and `1`.

Entering these two values into the server outputs the flag:
`picoCTF{Tw0_Sum_Integer_Bu773R_0v3rfl0w_ccd078bd}`
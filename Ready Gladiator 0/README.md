# Ready Gladiator 0

## Challenge

Can you make a CoreWars warrior that always loses, no ties? Your opponent is the Imp. The source is available [here](https://artifacts.picoctf.net/c/311/imp.red).

## Solution

[Core War](https://en.wikipedia.org/wiki/Core_War) is a computer game where computer programs, written in [Redcode](https://vyznev.net/corewar/guide.html), fight against each other.

The goal of this challenge is to write a program that always loses. We see from the Redcode instruction set that the `DAT` is used to kill processes. So we create a program that only executes this instruction:

```
;redcode
;assert 1
DAT #0, #0
end
```

This program loses all 100 games and we get the flag.

`picoCTF{h3r0_t0_z3r0_4m1r1gh7_e476d4cf}`

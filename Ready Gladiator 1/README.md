# Ready Gladiator 1

## Challenge

Can you make a CoreWars warrior that wins? Your opponent is the Imp. The source is available [here](https://artifacts.picoctf.net/c/311/imp.red).

## Solution

[Core War](https://en.wikipedia.org/wiki/Core_War) is a computer game where computer programs, written in [Redcode](https://vyznev.net/corewar/guide.html), fight against each other.

This challenge is a more lenient version of Ready Gladiator 2, so the same solution there can be used here.

Alternatively, we can try an example warrior, like the Dwarf.

```
;redcode
;assert 1
ADD #4, 3
MOV 2, @2
JMP -2
DAT #0, #0
end
```

This program wins some games and we get the flag.

`picoCTF{1mp_1n_7h3_cr055h41r5_ec57a42e}`

# Ready Gladiator 2

## Challenge

Can you make a CoreWars warrior that wins every single round? Your opponent is the Imp. The source is available [here](https://artifacts.picoctf.net/c/311/imp.red).

## Solution

[Core War](https://en.wikipedia.org/wiki/Core_War) is a computer game where computer programs, written in [Redcode](https://vyznev.net/corewar/guide.html), fight against each other. It's noted that certain types of warriors are counters to other types, like in a game of rock, paper, scissors.

Searching online for imp counters, we see that the following program will always win:

```
;redcode
;assert 1
JMP 0, <-5
end
```

We use this program, winning all games, and get the flag.

`picoCTF{d3m0n_3xpung3r_fc41524e}`

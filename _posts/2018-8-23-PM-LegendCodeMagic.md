---
layout: post
title: Legend of Code and Magic Post Mortem
---
___

Ended up: xxxxxxxx, second time I reach legend \o/

# Intro
Post mortem for the contest held by codingame.com: Legend of Code and Magic (Locam). First 4 weeks long contest on the platform, around a collectible card game with mechanisms similar to The Elder Scroll: Legend.

A lot of people to thank: **aCat** and **radekmie** and the **CG team** for the game. **aCat** (again) and **reCurse** for the twitch lives, I made great progress in terms of coding and understanding the game a little better.
French and general chat (and discord) had super interesting conversation about meta, strategies, etc (**Naab** for organizing my card scoring function better, **AIDevOOps** for the quick optimization discussion that gave good ideas to test, and many more!).

Very happy about the format, it gave me enough time to implement every idea I had and improve upon them methodically (testing one feature at a time!).

I regret the lack of "AI" elements in my draft part of the game, I used hand tuned scores for each card. Most of them were defined by mimicking the players... The small depth for the simulations (depth 1) and the high branching factor allowed me to spend time on code optimization, hash tables...

# The Bot

## Draft
Hand made static scores for each card. No blue items.

## Trade simulations
I had a DFS considering:
- First the cards I can play from my hand.
- Then the attacks my creatures can do.
- Finally, I ended up with an approximation for simulating the opponent. I assume the opponent will play each card in order to minimize my score: for his N cards: test the change of score for each action, apply the best one => 3 `for` loops.

It turns out that fully simulating the opponent turn was worse than my approximation (and sometimes timeouting). Probably because simulating the opponent is an approximation too and certainly because my evaluation function wasn't good enough.

To avoid time out or using a break on time:
- I used hash tables to store the combinations already tested both summoning and attacking equivalent combinations (Summon A then B is the same as Summon B then A, same for attacking player unless you have a fine treatment for runes). The hash is a very crude one, it produces a string for each card and concatenate everything. Still a lot to learn!
- Only my guards are allowed to stay idle. This goes against board control in situations where you break opponent's runes by inflicting high damage allowing him to draft several cards and regaining control.

## Evaluation function
- Global score: players hp difference
- Then sum card's evaluation, for one card:
   - attack or lethal * x
   - defense + ward * x
   - linear combination of attack score, defense score and sqrt(attack * defense)
   - no bonus for guard

Different attempt to take drain and breakthrough into account without much success. In the end I did unfortunate attempts to have a board control evaluation: consider runes and card draw vs card in hands/board.

# Outro

Fun game, it feels like it started last week and not 4 weeks ago.

# Resources

- [Codingame](www.codingame.com)
- [Post Mortems on codingame](https://www.codingame.com/forum/t/legends-of-code-magic-cc05-feedback-strategies/50996/5)
- [reCurse stream](https://www.youtube.com/watch?v=BU9b445CpaM)
- [aCat and Radekmie stream](https://www.youtube.com/watch?v=GQPCvs12R64&list=PLarKb0MFLwmjussHoTGnzQNV22drlgA6A)
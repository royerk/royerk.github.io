---
layout: post
title: Wondev Woman Post Mortem
---
___
# K.I.S.S.

Wondev Woman AI challenge was held from June 25th to July 3rd on [Codingame](www.codingame.com).

First time I really managed to Keep It Simple Stupid! I initially thought that I would have plenty of time for this challenge. I was being naive.

## Out of Wood 3 league
Read actions, pick one at random.

## Out of Wood 2 league
Pick action that brings a unit uphill, score points if possible.

## Out of Wood 1 league
Trickier and messier, for each units (my units only) compute a score:
- -2 for each blocked neighboring cell
- +5/+2 for each neighboring cell with height == 3/2
- -100 if unit is blocked
- -2/+10 if going down/up
- +100 for a point

Later I added a bonus for pushing an enemy downhill.

## Bronze to Gold league
After many tries I ended up with a simple evaluation function. For each unit (including visible enemy units (with a *-1* factor):
- +2 for a point scored
- count accessible cells with a decreasing factor: distance == 1 -> +1, distance == 2 -> +1/2...
- unit height x 3

No trees, no function to detect where the enemy might be.

# K.I.S.S. drawbacks
Making a simple and efficient AI is awesome, it seems cool and elegant but... I couldn't go beyond it. Minimax attempts timed out, probably because my actions generator was too simple and time consuming.

# Some links
- From Bob (codingame active member): [Be Lazy and Keep It Simple](https://www.codingame.com/blog/lazy-keep-simple/)
- Voronoi diagrams: [tech.io/voronoi](https://tech.io/playgrounds/243/voronoi-diagrams)

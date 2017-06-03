---
layout: post
title: Coders of the Caribean - Post Mortem
---
___
# Pirates, rum, cannonball and a pinch of Monte Carlo
[Codingame.com](https://codingame.com)


Codingame hosted another of their intense 10 days Artificial Intelligence competition. This time it was about guiding pirate ships to outlive. How to you outlive a pirate ? Easy: have more rum!

## Rules

As always CG deployed their league system:
* Wood 3: one ship
* Wood 2: one ship, mines here and there, canon available !
* Wood 1: up to 3 ships
* Bronze to Legend: manual steering available

## From Wood 3 to Bronze league
As always I started with a rather simple AI to grasp the game. I would find the nearest barrel of rum and move towards it. In wood 2 I would fire at an opponent if he happens to be in range and if my speed was > 0 (I wasn't dodging canon balls so better keep moving).

I had a hardtime in Bronze because I wouldn't let go my simple bot. I changed the aiming code, tweaked the nearest barrel search (taking into account my next position) and so on. With little to no results.

## Pseudo Monte Carlo
After some sleep I decided to refactor the whole thing. I'm a big fan of Monte Carlo methods. I already have a quite good connect four AI based on Monte Carlo process and I played with Neural Networks as branch factor limitator in Monte Carlo Tree Search (lots of fun).

As a ~~good~~ lazy person I tried something I've read about Code vs Zombies which was easy to implement and brought quite good results.

Monte Carlo is about applying one move and then simulating randoms moves afterward. Either you simulate the game until the end (possible in Connect Four) or you evaluate your solution with an heuristic at some depth. Then you would keep the move that has the best win-rate or best sum of evaluation.

I did a pseudo Monte Carlo: pick a random series of order (5 to 6 orders), evaluate the status after all the moves and keep the best serie.

#### 1. First step
Each ship act as if it was the only ship on the map. Given an ammount of time it would generate a serie of orders: SLOWER, FASTER, PORT, STARBOARD, WAIT.
The goal is to reach a specific destination which leads to the following evaluation function:
* if goal is reached: return rum cost (initially 0, -25 if a mine is touched, -25/-50 if the ship is hit by a canonball)
* if goal is not reached: retun rum cost + distance to destination

Firing was a post process when the order is WAIT: compute target position assuming that it would apply WAIT-WAIT-WAIT...

This brought in Bronze top 100 (when Bronze was the higher league) and then to Silver. This version was doing amazing considering the small effort I put in it. Mines were dodged easily as well as canon balls! Very nice result.

#### 2. Stop swaying!
At some point a ship would sway left and rigth. This can happen if the destination is behind the ship, then moving foward would increase the distance towards the destination.

I added some control over the order generation function:
* if speed = 0 then STARBOARD-PORT or PORT-STARBOARD is not valid
* if the ship moves out of the map: set speed to 0 and do not apply the foward movement

With this I reached Gold.

#### 3. Staying in Gold top 100 (after Legend's league opening)
Few effective modifications, those had some results:
* compute collision for the first order 2 orders (assuming WAIT for the other)
* if collision then FIRE
* ship with too few rum to survive -> hunt the opponent
* ship with sufficient rum -> run to survive
* check "shadow" positions for mines or barrels: when turning 2 cells are touched (after moving foward but before applyng the rotation) but not visible on the replay

## Conclusion
Not considering the enemy reached its limit but held an excellent result. Going for a too simple idea was a bad idea for this contest, I didn't had enough time during the last days of the contest to rewrite a simulation engine.

Coding in Go was fun, I started 3 months ago and I kinda like it. Attending a Codinghub was nice: meeting fellow coders and discussing ideas.

I ended 186 / 3600 (top 5%), my best score so far :-)

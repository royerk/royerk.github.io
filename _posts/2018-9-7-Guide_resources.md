---
layout: post
title: Guide and resources
---
___
# Where to start? - Intro

I started AI contests somewhere in late 2012 (I think).
At the time I was an active player on [Warlight](https://www.warzone.com/) (now *Warzone*) a risk-like game you can play in a browser.
One day there was a pop-up to advertise a partnership with [TheAiGames](http://theaigames.com/) which started an AI contest using the same game.
So for my first contest I was lucky to play a game at which I was already very good.
This helped a lot, I knew what was important in the game in terms of resources, timing of attacks, etc.

Later I started playing [LeekWars](https://leekwars.com/), leeks fighting each other with guns and laser.
The game proposed high level function such as line of sight, path finding, etc. and have (had?) a nice learning curve.
You had to get better to earn credits, allowing you to buy weapons your leeks could you in a match.
Also you started with one leek, later you could have a team.
Once again, by design a player would knew where to start and could progressively improve.

**My point**: the way I discovered AI contests never had me ask *Where do I start?* and for that I have to thank the platforms I cited.
And all my friends with whom I played tabletop games :) (a good way to discover and understand new game mechanics).

So now, what if you land on AI contest without much experience.
Where do you start when everybody is talking about GA, DFS, sims and eval function like it's small talk?
I'll try to offer some clues, mostly citing better coders/teachers than myself!

# First step: get something working

Create a working code in the language you are the most familiar with.
An extra simple code. Like **very** simple.
I had friends trying to do a killer AI from the start of a contest, they gave up after a couple of hours debugging...

Start with a working random bot.
Then upgrade it to make a not too random, eg:
* [Ultimate Tic Tac Toe](https://www.codingame.com/multiplayer/bot-programming/tic-tac-toe):
  * v0: playing randomly to check that I could handle the inputs.
  * v1: out of the possible moves I tried to find a move that would win a small tictactoe grid if any, otherwise play randomly.
* [Coder Strike Back](https://www.codingame.com/multiplayer/bot-programming/coders-strike-back), a pod race:
  * v0: max thrust towards next checkpoint.
  * v1: slow before passing a checkpoint to help the pod making a sharper turn.
* [Code of Kutulu](https://www.codingame.com/multiplayer/bot-programming/code-of-kutulu), stay sane in a labyrinth:
  * v0: move random,
  * v1: for each accessible cell go to the one minimizing the distance to another player (your character would lose less sanity when close to another character).
* [Coder of the Carribean](https://www.codingame.com/multiplayer/bot-programming/coders-of-the-caribbean), navigate your boats, drink rum and sink your opponent:
  * v0: go to first rum barrel given in the inputs.
  * v1: go to the nearest one (smart right?).

Same thing on other platforms, start super simple, walk before you run.

# Second step: find a simple goal

In all game there is always a metric to start with: have a lot of HP, be in front of others, earn more gold...
This metric is often referred as the *evaluation function*, it tells you how well your bot is doing.
This can be your first goal: make a decision maximizing this evaluation function for you, given the state of the game (state can be created from the inputs to start) and a move:
```
def score(game_state):
    my_metric = ...
    return my_metric

def evaluate(game_state, a_move):
    new_game_state = game_state.apply(a_move)  # simulate your move
    return score(new_game_state)
```
With this you have a starting point!
If it's easy to list the legal moves, test all of them and play the best one.
Otherwise make a list of some decent moves.
In Coder Strike Back you can do a lot of different moves every turn (turn your pod (from -18° to +18°, apply thrust 0 to 200), to start it's possible to consider 5 rotations and 5 thrusts values).
See how far you can go with this approach.

If it's possible take your opponent into account.
To do this you need to assign him a behavior, a good starting point is to consider he is waiting (no move, nothing, eventually reacting to the game or taking damage):
```
def score(game_state):
    score = my_metric
    score -= opponent_metric  # exact same metric but applied to the opponent
    return score
```

To quote MadKnight: *"tu fais howClosePlayerIsToWin(toi) - howClosePlayerIsToWin(ennemie)"*.

On codingame this approach is easy to try with the league system.
For a given game during the first 3 leagues (Wood leagues) you have access to a simplified version of the game.
Perfect for starting simple and experimenting.
After Wood leagues come Bronze league, with all the rules.

Don't over think it and have fun.

# Next?

Making an always better evaluation function with combinations of factors.
On codingame after each contest participants share how they approach the game and provide some insight on their evaluation function.
It's always worth reading.

Having a good evaluation function is of paramount importance.
I proposed to score the game state by applying one move, how about applying several moves before evaluating?
If you've asked yourself this question then you probably never ask *where do I start?* again ;)


# Resources

## Must read

* [Keep It Simple Stupid](https://www.codingame.com/blog/lazy-keep-simple/) by Bob, when you have little time to invest and want to make the most out of it

## Search algorithms

### Genetic Algorithm

* Applied to CSoder Strike Back [here](https://docs.google.com/document/d/1vI-wB9KxLoVjm70Pbwss27c5-ewFKoypCbdcWANysBA/edit) and [here](https://docs.google.com/document/d/143-gL23RGp1S_7pAyXKYY19NTJyWAMgTWJrAdl1xXws/edit)
* Tuto where you build a GA step by step (in your browser!) [tech.io]( https://tech.io/playgrounds/334/genetic-algorithms/history)


## AI contests platforms

* [Codingame](www.codingame.com)
* [TheAiGames](theaigames.com)
* [riddles.io](riddles.io)
* [Leekwars](www.Leekwars.com)
* Russian competitions but I don't understand it.

## Physics
* Magus (CG player) pseudo code to handle collisions [magus site](http://files.magusgeek.com/csb/csb_en.html)
* Visual effects of collisions [here](https://www.myphysicslab.com/engine2D/collision-en.html) where you can play with elasticity, mass, etc.
*

## Other
* reCurse stream code org (and C++ stuff)

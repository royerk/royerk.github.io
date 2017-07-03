---
layout: post
title: Applying Neural Networks to Ultimate Tic Tac Toe
---
___
# Introduction

## An AI challenge adapted to machine learning

When Ultimate Tic Tac Toe (*UT3*) first came out on [theAiGames](https://theaigames.com) I played a game against a friend on a piece of paper. I never played another one. Why ? I didn't find it very interesting. Some midgame moves were completely random and in the end it became quite hard to keep track of more than a few moves ahead.

Which can be interpreted as:
* a big decision tree,
* a branching factor that can go through the roof (max = 81)
* and a simple evaluation determines the winner.

This was a perfect game for an AI challenge!

## Inspired by AlphaGo
2016 saw the victory of AlphaGo against human best players and that was both fascinating and inspiring. So I wanted to try implementing an AI on *UT3* that would use some of the ideas implemented in AlphaGo.

As I understand, AlphaGo works like this:
1. it first uses a neural network (*NN*) to determine which move a good player would look at, that's the **policy network**,
2. then it simulates games using a **Monte Carlo Search Tree** (*MCTS*)
3. and finally it evaluates the possible resulting boards through a second *NN* called the **value network**.

For a (much) better description of AlphaGo please refer to [Dan Maas' blog](http://www.dcine.com/2016/01/28/alphago/) which provides more details and figures or DeepMind [paper itself](https://www.scribd.com/doc/302719734/AlphaGo-Paper).

## The basic ideas for the bot
I decided to use a Monte Carlo Tree search (*MCTS*) as my main framework (as opposed to alpha beta pruning or expert AI (aka heuristics)), I had already developed it in java for [theAiGames Four in a row](http://theaigames.com/competitions/four-in-a-row).  
To try something new I wanted to reduce the branching factor (if I have to evaluate 8 moves from a position I have a branching factor of 8). That would be the role of my Neural Network (*NN*): to determine which move could be interesting in order to reduce the branching factor and study the selected moves more deeply. And from the selected moves I would build the *MCTS*, assuming that both me and my opponent would play those (apparently) good moves.  
That was the plan.

___
# Assembling stuff for the Bot
## 1. Gathering data
As anyone who played with machine learning knows too well: before anything you need data, like a lot of it.
First I downloaded games from the top 100 players for the past months. That was great to start with. After hours of building the nicest json parser I could I had a nice dataset (a python pandas DataFrame to be specific). In a *.csv* file with a board on each line (like the board given by the game engine) with 2s for the player that played the last move and a column to tell who won the game.  
Afterwards I had a raspberry-pi gather the data for the best players.

## 2. Building and training the neural network
### First approach: evaluate all the moves

From the games I had in my dataset:

    X: a vector representing both players boards before and after the move
    y: 1 if the player that moved won the game else -1 (values are not important here, it represents 2 classes)

The NN acts as a classifier ("Is this move a win ?"), 2 hidden layers (400/200), relu activations, softmax output, no dropout, binary cross entropy, a few epochs. When expanding a node in MCTS I would evaluate the odds for each possible move. I would keep only the promising nodes (win probability > 50%). If no nodes are good I would fall back to a classic MCTS and try them all.

Three weeks before lock down I discovered that my dataset had a serious flaw, it couldn't be fixed. I was able to recreate only 40% of it. At first the NN had a 70% accuracy, by adding more data it reached 75% of accuracy.

The MCTS-NN bot won about 30/35% of the games against my MCTS bot.

I also tried adding the winning probability to the MCTS UCT function and keeping all the nodes in the tree (win ratio + acoefficient x NN prediction + explorationfactor x sqrt(...)). Not a big success neither.

### Second approach: predict the proper cell to play in

Here I tried to guess the best cell to play in (when it's limited to a 3x3 board, not when it can play anywhere on the 9x9 board). About the same NN architecture (with categorical cross entropy). I kept the winning moves to build the dataset:

    X: a vector representing both players boards before and after the move
    y: cell position in a 3x3 board

At best I had a 40% accuracy, but I didn't have time to check in which condition the NN was predicting the proper cell (if it has 5 free cells to play in, a random bot would have a 20% chance of making the proper move and then a 40% accuracy isn't that good). But by selecting the top 4 probabilities (for 9 potential moves) I had about 80/85% chance of studying the actual best move, which is good for reducing the branching factor of the MCTS. Unfortunately I couldn't implement it and test it before the lock down.

## 3. More stuff added to the bot
### Openings' book

Because consuming the time bank at the start of a game is a waste of time I ran tons of games (with Monte Carlo) to determine the win ratio of each opening move.

- If **id = 1** then according to my simulations playing in the middle (x = 4, y = 4) is the best move, about 52 % chance of winning.
- If **id = 2**, the best response to any opening move came as a moves' matrix. If the opening is (3, 1) then the best response is ... and so on.


### End game
I launched series of matchs between a bot that uses the *NN* to reduce the *MCTS* branching factor *versus* a bot that doesn't prune the *MCTS*. Turns out the bot with the *NN* wasn't that good... Especially at the end of the game, it wouldn't consider moves that actually lead to a victory.  
So when a few microboards are won the bot stops using the *NN* and considers all possible moves (classic MCTS).

___
# Conclusion
First time I'm using neural network for an AI, it was fun ! As always building a proper dataset is the main issue, the simplicity of the game made this possible.  
Next step would have been an optimized *field* class to enable deeper tree search.

# Counterfactual regret minimization

Counterfactual regret minimization (CFR) is a technique that converges to a Nash equilibrium for imperfect information games.
CFR learns a Nash equilibrium through in self-play.
It adjusts its strategy by evaluating the payoff of the action it chose against the payoffs it could have obtained from alternative actions, thus acknowledging and minimizing the regret associated with not selecting the most advantageous action.
This repository contains implementations of CFR programs from http://modelai.gettysburg.edu/2013/cfr/cfr.pdf

## Rock Paper Scissors
In the classic game of Rock Paper Scissors, each player selects one of three actions (rock, paper or scissors).
Each player does not know what action the other player took, thus making the game an imperfect information game. Below is the score matrix where the scores are seen from the row player's perspective.
| | Rock  | Paper | Scissors |
| - | - | - | - |
| Rock  | 0/0 | -1 | +1 |
| Paper  | +1 | 0/0 | -1 |
| Scissors  | -1 | +1 | 0/0 |

### Notebook
The optimal unexploitable strategy is found through self play.
When playing against a fixed exploitable strategy, the maximally exploitable strategy is found. (This exploiting strategy is itself also exploitable. So cross your fingers and hope that the opponent does not change strategy)

## Blotto game
Each player has S soldiers, and each soldier can be assigned to one of N < S battlefields.
Any number of soldiers can be allocated to each battlefield.
A player claims a battlefield if they send more soldiers to the battlefield than their opponent.
The player’s job is to break down his pool of soldiers into groups to which he assigned to each battlefield.
The winning player is the one who claims the most battlefields.

### Example
- S = 6
- N = 4
- Player 1 = (2, 1, 1, 3)
- Player 2 = (1, 4, 1, 0)
  
Player 1 wins the game as the outcome from player 1's perspective is positive (Win=1, Loss=-1, Draw=0, Win=1) = 1

### Notebook
The optimal strategy is found for any (S, N). An exploiting strategy is found when playing against an opponent with a suboptimal/exploitable strategy.

## Kuhn Poker
Kuhn Poker is a simplified poker game with only two players and three cards (Jack, Queen and King in this version).  
Each player ante 1 chip, i.e. forced to bet 1 chip into the pot before the cards are dealt. The three cards are
shuffled, and one card is dealt to each player and held as private information. Play alternates starting
with player 1. On a turn, a player may either pass or bet. A player that bets places an additional chip
into the pot. When a player passes after a bet, the opponent takes all chips in the pot. When there
are two successive passes or two successive bets, both players reveal their cards, and the player with
the higher card takes all chips in the pot.

Here is a summary of possible play sequences with the resulting chip payoffs:

| Player 1  | Player 2 | Player 1 | Payoff |
| - | - | - | - |
| pass | pass | | +1 to player with higher card
| pass | bet | pass | +1 to player 2
| pass | bet | bet | +2 to player with higher card
| bet | pass | | +1 to player 1
| bet | bet | | +2 to player with higher card

### Notebook
A Nash equilibrium is found (one of multiple Nash equilibria).

# Counterfactual regret minimization

Counterfactual regret minimization (CFR) is a technique that converges to a Nash equilibrium for imperfect information games.
CFR learns a Nash equilibrium through in self-play.
It adjusts its strategy by evaluating the payoff of the action it chose against the payoffs it could have obtained from alternative actions, thus acknowledging and minimizing the regret associated with not selecting the most advantageous action.
This repository mostly contains implementations of CFR programs from http://modelai.gettysburg.edu/2013/cfr/cfr.pdf

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
- Player 1 = [2 1 1 3]
- Player 2 = [1 4 1 0]
  
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

## Dudo
In this simplified Dudo game, only 2 players play against eachother.
Each player rolls their dice/die and keeps the roll as private information.
The players then takes turns making increasingly stronger claims about the total strength of all the dice.
A claim consists of an amount of ranks (face of the die) where higher ranks are stronger and higher amounts of ranks are even stronger.
There is one exception; dice with rank 1 count towards the other ranks aswell, thus making a claim involving an amount of 1s half as likely as other claims of the same amount of ranks.
Therefore claims involving 1s are ranked higher than claims involving 6s.

Below is a table of claims and strengths for a game with 2 dice
| Strength | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 |
| - | - | - | - | - | - | - | - | - | - | - | - | - |
| Claim (amount × rank) | 1 × 2 | 1 × 3 | 1 × 4 | 1 × 5 | 1 × 6 | 1 × 1 | 2 × 2 | 2 × 3 | 2 × 4 | 2 × 5 | 2 × 6 | 2 × 1 |


The claim has to be stronger than the previous claim to be valid. If the player does not want to make a claim but instead test the opponents claim, they can call Dudo.
When a player calls Dudo both players has to reveal their original roll.
The player calling Dudo loses if the rank count exceeds or matches the claim and wins otherwise if the rank count is less than the claim.

For example:
- Player 1 rolls 4
- Player 2 rolls 1
- Player 1 claims 1 x 4
- Player 2 claims 1 x 6
- Player 1 calls Dudo and loses since the rank count of the claim (1 x 6) matches the total rank count (as 1 also counts towards the rank count of 6s)

### Notebook
A Dudo model is trained.
The model can play against itself and you can also play against the model.

## Liar Die
A player begins by rolling a die and keeps the result as secret information.
The player then claims a roll rank.
The opponent then has two possible actions:
- Doubt: The opponent doubts the claim, the roll is revealed and compared against the rank
claimed. If the roll rank is greater than or equal to the claim rank, the player wins. Otherwise,
the opponent wins.
- Accept: The opponent accepts the claim. Without revealing the prior roll rank, the die is given
to the opponent, who now takes on the same role as the initial player with one exception. After
the die is again secretly rolled and observed, one must make a claim that is higher than the
previous claim. Thus, players will take turns secretly rolling the die and making successively
higher claims until one player doubts their opponent’s claim, and the roll is checked to see who
wins the game.

### Notebook
This Liar Die implementation is using Fixed-Strategy Iteration Counterfactual Regret Minimization (FSICFR).

CFR traverses extensive game subtrees, recursing forward with reach probabilities that
each player will play to each node (i.e. information set) while maintaining history, and backpropagating
utilities used to update parent node action regrets and thus future strategy.

Fixed-Strategy Iteration CFR (FSICFR) divides the recursive CFR algorithm into two iterative
passes, one forward and one backward, through a directed acyclic graph of nodes. On the forward pass, visit counts and
reach probabilities of each player are accumulated, yet all strategies remain fixed. (By contrast, in CFR,
the strategy at a node is updated with each CFR visit.) After all visits are counted and probabilities
are accumulated, a backward pass computes utilities and updates regrets. FSICFR computational
time complexity is proportional to the number of nodes times the average node outdegree, whereas
CFR complexity is proportional to the number of node visits times the average node outdegree. Since
the number of node visits grows exponentially in such abstracted problems, FSICFR has exponential
savings in computational time per training iteration relative to CFR.

The model learns the optimal strategy for what die rank to claim and whether to doubt or accept the opponents claim

## Blackjack
The player makes a bet and is dealt two cards face up. The dealer is dealt one card face up and one card face down.

The objective of the game is to have a hand that sums to more than the dealer's hand, but does not sum to more than 21.

Cards 2 through 10 has values corresponding to their number. Face cards (jack, queen, king) are all valued as 10.
Aces can either take the value 11 or 1.

In this game of Blackjack, the player can take 4 actions.
- Hit: The player is dealt an additional card
- Stand: The player does not want any additional cards and the game proceeds to showdown i.e. the dealer reveals their card and deals additional cards to themselves until their hand sums to 17 or more.
- Double Down: The player doubles their initial bet and receives a single additional card. The game proceeds to showdown.
- Split: This action can only be taken if the player is dealt two cards of the same rank. The player splits the hand into two hands and is dealt another card for each hand. The player bets an additional amount the size of the initial bet, so that there is a bet on each hand.

If the dealer is dealt blackjack (21), then the player takes money back if they also got dealt blackjack, otherwise the player loses with no opportunity to take actions.
If the player is dealt blackjack and the dealer is not, then the player receives 2.5 times their bet in return.
If the player wins they receive their double their bet, and if they lose they receive nothing and the dealer takes their bet.

### Notebook
There are two versions of the blackjack model: The first does not count cards and the second does.

The model that does not count cards has a negative expected value when fully trained. This is as expected as the house has the edge in this game. Or does it...

The second model counts the cards using the Wong-Halves card counting method.
The idea of card counting is to have a running count that is added to or subtracted from based on the dealt cards.
The player should bet big when the count is high i.e. there are more high cards in the deck which is favourable to the player.
In the implementation, the player bets big when the count is greater than or equal to 4.
I have experimented with learning the optimal count to bet big on, using a seperate CFR algorithm for the count only.
This led to instability in the learning and did not reliably produce a positive expected value.
Therefore, I have simply hardcoded the big bet count to 4.

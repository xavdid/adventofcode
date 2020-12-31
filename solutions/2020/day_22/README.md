# Day 22 (2020)

`Crab Combat` ([prompt](https://adventofcode.com/2020/day/22))

## Part 1

Part 1 is... unexpectedly straightforward for a day 22. Honestly the tricky bit is just the repetitive code, but it's nothing we can't handle.

We'll be tracking a `list` of `int`s, but we need to do a couple of special operations. We'll need to remove the first element (top card) of the list for the losing deck and rotate the winning deck (remove the top element, put it on the bottom). As luck would have it, Python has the exact data structure for this: the `Deque`.

Pronounced just like "deck" and called so because it's a "double-ended queue". It can add and remove from either end of the list, plus it's got rotation built in. Let's build a `Deck` to hold our `deque`:

```py
class Deck:
    def __init__(self, deck: str) -> None:
        self.cards = deque(map(int, deck.split("\n")[1:]))
```

It takes one of the two input decks and handles all the parsing. We'll also add some simple utility methods that will help play the game:

```py
class Deck:
    ...

    @property
    def top(self) -> int:
        return self.cards[0]

    @property
    def lost(self) -> bool:
        return len(self.cards) == 0

    def win_round(self, won_card: int) -> None:
        self.cards.rotate(-1)
        self.cards.append(won_card)
```

Now we play! There's not much to it. We've got two variables for the decks themselves and some pointers, `round_winner` and `round_loser` that we point at each:

```py
blocks = self.input.split("\n\n")

player_1 = Deck(blocks[0])
player_2 = Deck(blocks[1])
winner: Optional[Deck] = None

while winner is None:
    if player_1.top > player_2.top:
        round_winner = player_1
        round_loser = player_2
    else:
        round_winner = player_2
        round_loser = player_1

    round_winner.win_round(round_loser.cards.popleft())

    if round_loser.lost:
        winner = round_winner
```

Last is the scoring, which uses the index to get the "value" of a card:

```py
class Deck:
    ...

    def score(self) -> int:
        max_size = len(self.cards)

        return sum(card * (max_size - index) for index, card in enumerate(self.cards))

...

return winner.score()
```

## Part 2
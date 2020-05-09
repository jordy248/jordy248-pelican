Title: 2020-05-09 Riddler
Date: 2020-05-09 14:42
Category: Riddler
Tags: FiveThirtyEight, Riddler, R, probability, simulation, binomial
Slug: riddler-2020-05-09
Cover: theme/assets_images/tatiana-rodriguez-XEn-Pvif5Q0-unsplash.jpg
Cover_Credits: Tatiana Rodriguez/Unsplash
Authors: Jordy Nelson
Summary: Six marks the dot. 
Comments: True

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
 "HTML-CSS": { linebreaks: { automatic: true } },
         SVG: { linebreaks: { automatic: true } }
});
</script>

The newest [Riddler Express](https://fivethirtyeight.com/features/can-you-eat-an-apple-like-a-toddler/) is a simple probability problem duo:

> From Dee Harley comes a devilish matter of dominos:

> In a set of dominos, each tile has two sides with a number of dots on each side: zero, one, two, three, four, five or six. There are 28 total tiles, with each number of dots appearing alongside each other number (including itself) on a single tile.

> Question 1: What is the probability of drawing a "double" from a set of dominoes &ndash; that is, a tile with the same number on both sides?

> Question 2: Now you pick a random tile from the set and uncover only one side, revealing that it has six dots. What’s the probability that this tile is a double, with six on both sides?

With 7 dot options for each side of each domino tile, there are $\frac{{7 \choose 1}}{{7 \choose 1} + {7 \choose 2}}$ ways of drawing a double &ndash; or 25%, which intuitively makes sense given that there are 7 doubles out of 28 total tiles. 

For fun, let's simulate:

```{python}
import itertools
import numpy as np
from random import choice, seed
from scipy.stats import binom
from scipy.special import comb

seed(42)

class Dominoes:
    """
    Initiates a Dominoes() class 
    with range(0, n) dots per tile side
    and r sides per domino tile.
    
    gen_dominoes() generates the tiles.
    
    draw_domino() draws a random tile and removes it from the pool.
    """
    def __init__(self, faces: list, sides: int):
        self.faces = faces
        self.sides = sides
    
    def gen_dominoes(self):
        dominoes = list(itertools.combinations(iterable = self.faces, 
                                               r        = self.sides))
        for face in self.faces:
            ditto_domino = tuple(np.repeat(face, self.sides))
            dominoes.append(ditto_domino)
        self.dominoes = dominoes
        
    def draw_domino(self):
        domino = choice(self.dominoes)
        self.dominoes.remove(domino)
        return domino

# Q1
dupe_draws = 0
n = 100000
for i in range(0, n):
    d = Dominoes(faces = range(0, 7),
             sides = 2)
    d.gen_dominoes()
    draw = d.draw_domino()
    if draw[0] == draw[1]:
        dupe_draws += 1

dupe_draws / n
```
Which gives us `0.25144`.

If we first peek at one of the sides and notice a 6 after drawing a domino, there is 1 way for a 6 to be on the other side and ${6 \choose 1}$ ways for it to be a number other than 6. In other words, $\frac{{1 \choose 1}}{{6 \choose 1} + {1 \choose 2}}$ &ndash; or about 14.3%. 

```{python}
# Q2
six_six = 0
n = 100000
d = Dominoes(faces = range(0, 7),
             sides = 2)
d.gen_dominoes()
dom_six = [v for v in d.dominoes if v[0] == 6 or v[1] == 6]

for i in range(0, n):
    draw = choice(dom_six)
    if draw[0] == draw[1]:
        six_six += 1

six_six / n
```
Which gives us `0.14254`.

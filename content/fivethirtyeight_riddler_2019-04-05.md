Title: 2019-04-05 Riddler
Date: 2019-04-07 08:22
Category: Riddler
Tags: FiveThirtyEight, Riddler, R, probability, combinatorics, simulation
Slug: riddler-2019-04-05
Cover: theme/assets_images/karl-fredrickson-35017-unsplash.jpg
Cover_Credits: Karl Frederickson/Unsplash
Authors: Jordy Nelson
Summary: 2019-04-05 Riddler

This past weekend's [Riddler Classic](https://fivethirtyeight.com/features/does-your-gift-card-still-have-free-drinks-on-it/) is a neat little combinatorics problem.

> From Michael Branicky, your card has been declined:

> Lucky you! You’ve won two gift cards, each loaded with 50 free drinks from your favorite coffee shop, Riddler Caffei-Nation. The cards look identical, and because you’re not one for record-keeping, you randomly pick one of the cards to pay with each time you get a drink. One day, the clerk tells you that he can’t accept the card you presented to him because it doesn’t have any drink credits left on it.

> What is the probability that the other card still has free drinks on it? How many free drinks can you expect are still available?

There are two scenarios that end this puzzle:

1. We make a coffee run, but *both cards are empty.*

2. We make a coffee run, and the card we choose is empty, but the other card has at least one drink remaining.

In both cases, we don't need to worry about the order of the cards. 

The first is relatively straightforward: If both cards are empty, this means we've already ordered exactly 100 coffees. 50 of them were on the card we tried using, and 50 were on the other card. The probability for this scenario happening is `((1/2) ^ (100)) * choose(100, 50)` &dash; or just about 8%. So the probability that this isn't the case (i.e., that the card we didn't has at least one drink remaining) is about 92%.



Let's confirm with an R simulation:

```R
# riddler
# 2019-04-05

##### ------- #####
##### ------- #####
##### EXPRESS ##### -----------------------------------------------------------
##### ------- #####
##### ------- #####

if(F) {
        '
        Riddler Express
        From Tom Hanrahan, a probability puzzle; or, a mini-lesson in 
        surprising results:
        
        You are playing your first ever game of "Ticket to Ride," 
        a board game in which players compete to lay down railroad
        while getting so competitive they risk ruining their marriages.
        At the start of the game, you are randomly dealt a set of three D
        estination Tickets out of a deck of 30 different tickets. Each reveals
        the two terminals you must connect with a railroad to receive points. 
        During the game, you eventually pick up another set of three 
        Destination Tickets, so you have now seen six of the 30 tickets in the 
        game.
        
        Later, because you enjoyed it so much, you and your friends play a 
        second game. The ticket cards are all returned and reshuffled. Again,
        you are dealt a set of three tickets to begin play. Which is more 
        likely: that you had seen at least one of these three tickets before, 
        or that they were all new to you?
        '
}

### ---- ###
### init ### ------------------------------------------------------------------
### ---- ###
rm(list=ls())
gc()

### --------- ###
### load libs ###
### --------- ###
library(progress)
library(tidyverse)

### ------ ###
### params ###
### ------ ###
set.seed <- 42
terminals <- 1:30

### ----------------------- ###
### fun to simulate dealing ### -----------------------------------------------
### ----------------------- ###
deal <- function(deck,
                 n_cards) {
        
        hand <- sample(x       = deck,
                       size    = n_cards,
                       replace = F)
        
        deck <- deck[-hand]
        
        # return
        res <- list(hand = hand,
                    deck = deck)
        return(res)
}

### ----------------------------- ###
### simulate ticket to ride games ### -----------------------------------------
### ----------------------------- ###

# init vector to hold flags for games with dupe terminals
dupe_terminals <- rep(0L,
                      1e6)

# init progress bar
pb <- progress_bar$new(
        format = paste0('simulating hands',
                        '(:spin) [:bar] :percent eta: :eta'),
        total = length(dupe_terminals), clear = FALSE, width= 80)

# simulate two games and update dupe_terminals for matches
for(i in 1:length(dupe_terminals)) {
        
        # simulate games
        game_1 <- deal(deck      = terminals, 
                       n_cards = 6)
        
        game_2 <- deal(deck      = terminals, 
                       n_cards = 3)
        
        # get hands from simulated games
        hand_game_1 <- game_1$hand
        
        hand_game_2 <- game_2$hand
        
        # check for dupes and update dupe_terminals
        if(sum(hand_game_1 %in% hand_game_2) > 0) {
                dupe_terminals[i] <- 1L
        }
        
        # init progress
        pb$tick()
        
}

### -------- ###
### calc res ### --------------------------------------------------------------
### -------- ###

# second games that included at least one card that was seen in game 1
sum(dupe_terminals)/length(dupe_terminals)
```

```R
[1] 0.501665
```

Over the course of a million simulations, we roughly match our computed result above. And we can watch the simulations converge on this result:

```R
### -------- ###
### plot res ### --------------------------------------------------------------
### -------- ###
df <- data.frame(samples = 1:length(dupe_terminals),
                 dat     = dupe_terminals) %>%
        mutate(running_rate = cumsum(dat) / samples)

df %>%
        ggplot() +
        geom_line(aes(x = log(samples),
                      y = running_rate))

```

![Riddler Express 2019-03-29 Plot](../../theme/assets_images/riddler_express_2019-03-29.png "Riddler Express 2019-03-29 Plot")

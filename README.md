# PaaFCSP
Pokémon as a Fuzzy Constraint Satisfaction Problem. Work in progress

This repository/project is NOT a:

- Tool for breaking any terms of use of any product.
- Perfect representation of Pokémon scenarios
- "One size fits all" solution to team building

This repository/project IS a:

- work in progress (do not trust any outputs without doing your own validation)
- tool to help determine how to train your Pokémon, based on community termed "Effort values", or EVs.
- research project as an application of fuzzy constraint satisfaction problems

# Contribution guide

So you want to help out! Fantastic! There are likely issues/feature requests/ bugs in the issues tab. Feel free to make a branch and start taking a crack at it.

PRs are reviewed as I get time, I still have a full time job and this is a side project, but help in reviewing PRs is always appreciated.

This readme is VERY loose intentionally, if you want to add something to make this file (or any others) more readable, make a PR and chances are it'll get in.


# Overview

## What is a Constraint Satisfaction Problem (CSP)?

In one sentence: The modeling of a set of conditions or "constraints" such that an interrogated input resolves as true for all constraints.

Example 1: The N-queens problem - place N queens on an NxN chess board such that no 2 queens can capture each other in 1 move. See more on this problem [from Google](https://developers.google.com/optimization/cp/queens)

Example 2: Linear optimization - given a set of variable constraints, maximize or minimize a function on aforementioned variables. See an example of this problem [from Google](https://developers.google.com/optimization/introduction/python#example)

Further reading:
- [Google's OR-tools](https://developers.google.com/optimization/cp)
- [Ole reliable: wikipedia](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem)

## What is a "fuzzy" CSP?

In a typical CSP the solution _must_ satisfy all constraints entirely (e.g. the solution to a linear optimization must leave all variables in their respective domains: x = 3 cannot be in the solution if a constraint is x < 2). Fuzzy CSPs introduce a degree of "fuzziness" - a bit of wiggle room - to accept that not all constraints have to be met and that some constraints may be partially satisfied, replacing the boolean output for each constraint with the unit interval [0,1]. These outputs can then be assessed as a weighted sum of the outputs (here on referred to as a weighted FCSP), or in a flat sum.

Further reading:
- [Ruttkay's publication](https://ieeexplore.ieee.org/document/343640)

Aside: Other flexible CSPs
- MAX-CSP: A variation on CSPs that involve accepting not all constraints can be met, and selects a solution that maximizes the target function while also satisfying as many constraints as possible. This is not desirable for this application since if our constraint set is damage calculations, the uneven number of physical/special attackers/walls makes this not a good representation of how a Pokémon will perform.

- Weighted CSP: A variation on MAX-CSP where there are manual weights added to constraints to select a preference on constraints, and in maximizing that over the cardinality of constraints satisfied. This is not immediately desirable as it does not account for calculations where the result of the damage roll is not discrete (e.g. there can be a 6% chance a move will faint an opponent after N attacks)

## Pokemon damage calculations

Game freak does some **goofy** math in game, including their own flavor of rounding down on *.5, read DaWoblefet's article below if you really want to go down that rabbit hole. To make matters worse, several decently reputable sources online have differences in the formula. DaWoblefet's formula is consistent with most online calculators, so we will use it. 

[/todo: add link to forward/backward script/]: ()

Further reading:
- [DaWoblefet's damage dissertation](https://www.trainertower.com/dawoblefets-damage-dissertation/)
 
## Pokémon to constraint translation and encoding

### Contextualizing complex EV spreads
This assumes the reader is familiar with Effort Values/EVs. If you are not, please see some of these resources:

- [Bulbapedia](https://bulbapedia.bulbagarden.net/wiki/Effort_values)
- [Serebii](https://www.serebii.net/games/evs.shtml)

There are a few different ways a trainer may want to train their Pokémon, and each of these archetypes influence the weights of our weighted FCSP roughly as follows:

- Defensive wall: These Pokémon are trained to live the most hits, and stay on the field for as long as possible. Prominent current examples are female Indeedee and Garganacl, as they allow for significant support or chip damage.
- Bulky attacker: These Pokémon are trained to deal lots of damage, but in sacrificing some of their offensive pressure (generally either in speed or their primary attacking stat) they target bulk to survive opposing offensive threats, allowing them to stay on the field and deal more damage over time. This can also apply to very strong but frail Pokémon, such as Flutter Mane, where some speed can be given in exchange for living to common physical attacks despite it's abyssmal phsyical defense.
- Hyper-offense: These Pokémon typically are maximized in two stats, their attack and either health or speed. Speed tiers are the most important part in play here, so the goal is to put just enough speed to get at least a trade on the field, fainting an opponent mon before potentially being knocked out in one hit (OHKO'd). Defenses are generally either not considered or primarily focused on surviving specific meta threats with just enough HP to get out another attack. 

These all involve optimizing for different scenarios in the meta, with respect to speed and damage output. **Speed control moves like tailwind and trick room are not considered here**, so if a certain spread of EVs cannot outspeed without them, speed will be sacrificed for either more bulk or offensive pressure.

### Constraint definition

The two types of constraints - offensive and defensive - are mathematical representations of the damage either done or received going against Pokémon present. The output/evaluation of each constraint will be tested to be a combination of the following metrics:
- Offensively:
  - Minimum damage done (0 = no effect, 1 = guaranteed OHKO)
  - Chances of OHKO (0 = gauranteed to survive with at least 1HP guaranteed OHKO, 1 = guaranteed OHKO)
- Defensively*:
  - Most damage received (0 = Gauranteed OHKO, 1 = no effect)
  - Chances of OHKO inverted (0=guaranteed OHKO, 1 = gauranteed to survive with at least 1HP)
  
*Defensive outputs have to be inverted since 1 is the fully satisfied constraint, with positive connotation so that these both can be used in the same problem.

These constraint combinations will have to be assessed for the various archetypes, and can be different for each (for the end user that just means a switch based on what archetype they're building for)
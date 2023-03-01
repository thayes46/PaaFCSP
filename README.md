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

In a typical CSP the solution _must_ satisfy all constraints entirely (e.g. the solution to a linear optimization must leave all variables in their respective domains: x = 3 cannot be in the solution if a constraint is x < 2). Fuzzy CSPs introduce a degree of "fuzziness" - a bit of wiggle room - to accept that not all constraints have to be met and that some constraints may be partially satisfied, replacing the boolean output for each constraint with the unit interval [0,1]. These outputs can then be assessed as a weighted sum of the outputs, or in a flat sum.

Further reading:
- [Ruttkay's publication](https://ieeexplore.ieee.org/document/343640)

Aside: Other flexible CSPs
- MAX-CSP: A variation on CSPs that involve accepting not all constraints can be met, and selects a solution that maximizes the target function while also satisfying as many constraints as possible. This is not desirable for this application since if our constraint set is damage calculations, the uneven number of physical/special attackers/walls makes this not a good representation of how a Pokémon will perform.

- Weighted CSP: A variation on MAX-CSP where there are manual weights added to constraints to select a preference on constraints, and in maximizing that over the cardinality of constraints satisfied. This is not immediately desirable as it does not account for calculations where the result of the damage roll is not discrete (e.g. there can be a 6% chance a move will faint an opponent after N attacks)

## Pokemon damage calculations

## Pokémon to constraint translation

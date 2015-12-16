---
layout: project
title: Artificial Intelligence
date: Spring Quarter 2015
image: mancala.png
---

##Overview
Here are some assignments from our Intro to Artificial Intelligence class. While the code to display the game boards was provided for us, we wrote the implementation of the algorithms ourselves. Unfortunately the code cannot be provided as assignments are recycled for future classes.  

### Mancala Player (April 18)

* Implements the alpha-beta pruning search algorithm
* Evaluates the strength of the move through a game board evaluator
* Has been optimized to beat our own game playing strategies

### Sudoku Solver (May 2)

Our Sudoku solver is capable of solving 4x4, 9x9, 16x16, and 25x25 Suodoku boards. Even the largest board can be solved in less than a second with the right combination of forward checking heuristics.

* Backtracking - to keep track of solved variables
* Forward Checking - chooses which unsolved variable to solve next, can either choose the next unsolved variable or use one (or a combination) of the following heuristics to decide which variable to solve next.
   * MRV (minimum remaining values) Heuristic – choose the variable with the
fewest values left
   * MCV (most constrained variable - degree) Heuristic – choose the variable that is
involved in the largest number of constraints with unassigned variables
   * LCV (least constraining value) Heuristic – choose the value that rules out the
fewest choices for other unassigned variables

### Sentiment Analysis

Training data

* Creates two dictionaries (one positive and one negative) from a list of training files
* Saves the dicionaries into a pickle file so that they don't need to be recalculated

Testing data

* Naive Bayes Classifier
  * Calculates the conditional probability or each word in a document
  * Takes prior probability into account
  * Classifies a document or phrase as either positive, negative or neutral
* Created a dynamic ignore list to find and ignore words commonly found in both the positive and negative dictionaries

Evaluation

* Uses 10 fold cross validation to evaluate the classifier
   * Training data was not included in testing data
* Classifier was evaluated based on precision, recall, and f-measure


## Sketch Recognition

* Implement the Viterbi algorithm to determine whether or not an individual pen stroke is a part of a drawing or a letter
* Uses 10 fold cross validation to evaluate the classifier

# Exercise 5 of module 2 (Challenger)

## What is good
- Analysis process is clear for reader to understand.
- The model idea is good. It assumes each O-ring is independent, with a failure probability that changes with temperature, and uses logistic regression to estimate it.


## What is wrong
1. Only used 7 flights with at least one failure. Other information are also important to plot and analysis. For example good data can be use for compare with problem data to prove what is really matter to it.
2. The lowest temperature of chosen data is 53°F, this cannot explain the cases in the report that when was 31°F. 
3. After selecting data and plot it, the influence of temperature is not obvious. But it does not mean it has no influence.
4. The uncertainty of the estimate was not shown.

## Improved version
See `exo5_en.ipynb`

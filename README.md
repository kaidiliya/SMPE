# SMPE

## Week 2

This folder contains the homework for Week 2, including:

- `exo5_en.ipynb`: Jupyter Notebook for Exercise 5 (Challenger).
- `shuttle.csv`: Dataset used in the exercise.
- `report_exo5_en.md`: Report discussing the Challenger exercise.
- `figure_analysis.pdf`: Critical analysis of figures and proposed improvements.

## Quicksort

Homework on the Parallel Quicksort project (folder `ParallelQuicksort/`): re-run a colleague's multithreaded quicksort, improve the experimental design (sizes, repetitions, order), repeat it on other machines, and make better figures and analysis with `ggplot2`.

* `ParallelQuicksortNote.md`: Laboratory notebook. It covers the setup, the weaknesses of the original design, seven modified experiments, a comparison on three computers, the improved analysis (confidence intervals, continuous trend, extrapolation) and the conclusion.
* `fig1_original.png`: Original design (5 sizes, 5 repetitions, fixed order).
* `fig2_more_sizes.png`: More sizes (10 sizes, 5 repetitions, fixed order).
* `fig3_more_reps.png`: More repetitions (5 sizes, 30 repetitions, fixed order).
* `fig4_random_order.png`: Random order (5 sizes, 5 repetitions, random order).
* `fig5_all_changes.png`: All changes (10 sizes, 30 repetitions, random order).
* `fig_ci_dots.png`: Every repetition with the mean and its 95% confidence interval (1,000,000 integers).
* `fig6_big_sizes.png`: Bigger sizes (up to 10,000,000 integers, 5 repetitions, fixed order, 2 runs).
* `fig7_big_all_changes.png`: Bigger sizes with more repetitions and random order (up to 10,000,000 integers, 30 repetitions).
* `fig8_huge_sizes.png`: Up to 100,000,000 integers (5 repetitions, fixed order).
* `analysis_output.txt`, `analysis_big_output.txt`: Output of the R analysis (means, ratios, confidence intervals).
* `liya_2026-11-10-DataFromTomCode/`: Tom's code and benchmark script run on my laptop (raw data and figures).
* `verona_2026-11-10-DatafromOther/`: My code run on a friend's laptop (raw data).

The scripts, the other raw data (`.txt`, `.csv`) and the R analysis (`scripts/analyze.R` and `scripts/analyze_big.R`) are in my fork of the project: https://github.com/kaidiliya/M2R-ParallelQuicksort

Teammate: Tom Laucournet's repository: https://github.com/tomlcn/M2R-ParallelQuicksort

Main result: 
On Tom's laptop and on a friend's laptop, the parallel version is slower for small arrays and faster for big arrays (around 10,000,000 integers), so the size is important when designing the experiment.

On my laptop (i5-1340P, 16 logical CPUs, gcc 15.2) the parallel version was slower than the sequential one at every size tested, up to 100,000,000 integers, with my code and with Tom's code. So the result also depends on the computer. 

The extrapolation shows that on my laptop the parallel version would need an array much bigger than my memory to become faster.
# SMPE

## Week 2

This folder contains the homework for Week 2, including:

- `exo5_en.ipynb`: Jupyter Notebook for Exercise 5 (Challenger).
- `shuttle.csv`: Dataset used in the exercise.
- `report_exo5_en.md`: Report discussing the Challenger exercise.
- `figure_analysis.pdf`: Critical analysis of figures and proposed improvements.

## Quicksort

Homework on the Parallel Quicksort project (folder `ParallelQuicksort/`):

- `ParallelQuicksortNote.md`: Laboratory notebook. It covers the setup, the weaknesses of the original design, the four modified experiments, the results with confidence intervals, and the conclusion.
- `fig1_original.png`: Original design (5 sizes, 5 repetitions, fixed order).
- `fig2_more_sizes.png`: More sizes (10 sizes, 5 repetitions, fixed order).
- `fig3_more_reps.png`: More repetitions (5 sizes, 30 repetitions, fixed order).
- `fig4_random_order.png`: Random order (5 sizes, 5 repetitions, random order).
- `fig5_all_changes.png`: All changes (10 sizes, 30 repetitions, random order).
- `fig_ci_dots.png`: Every repetition with the mean and its 95% confidence interval (1,000,000 integers).

The scripts, raw data (`.txt`, `.csv`) and the R analysis (`scripts/analyze.R`) are in my fork of the project: https://github.com/kaidiliya/M2R-ParallelQuicksort

Main result: on my laptop (i5-1340P, 16 logical CPUs, gcc 15.2, `-O0`) the parallel version was slower than the sequential one at every size tested, about 3 times slower at 1,000,000 integers.
# Parallel Quicksort: laboratory notebook

## Setup

- Machine: personal laptop
- CPU: 13th Gen Intel Core i5-1340P, 16 logical CPUs (`nproc` = 16)
- OS: Ubuntu, Linux kernel 7.0.0-34-generic, x86_64
- Compiler: gcc 15.2.0
- Compile flags: original Makefile, `-O0` (no optimization), `-pthread -std=c99`

## First single run

First I ran the code once: `./src/parallelQuicksort 1000000`

I got:

```
Sequential: 0.259 s
Parallel:   0.805 s   <- 3x slower
Built-in:   0.274 s
```

But a single run can not prove the result yet.

## The original design and its weaknesses

After reading the author's journal and code, this is what the original code does:

- 5 sizes: 100 to 1,000,000
- 5 repetitions each
- runs them in a fixed order, so all the 100s first, then all the 1000s, and so on
- appends the output to a text file, which a Perl script turns into CSV, then plotted

I think the weaknesses of the design are: few repetitions, fixed order, only 5 sizes, and no confidence interval.

## Baseline: running the original script

I ran the experiment script as it is: `bash scripts/run_benchmarking.sh`

Rough numbers of this first run (rounded by eye, the exact numbers are in `data/liya_2026-11-04/analysis_output.txt`):

| size | Sequential | Parallel |
|---|---|---|
| 100 | 0.00002 s | 0.007 s |
| 10,000 | 0.0025 s | 0.05 s |
| 1,000,000 | 0.26 s | 0.79 s |

The author's journal says that at 1,000,000 elements parallel takes 0.16 s and sequential 0.23 s, so parallel is faster, and he writes that parallel is worth it for arrays larger than 400,000.

On my laptop at 1,000,000 elements parallel takes 0.81 s and sequential 0.27 s, so parallel is 3 times slower.

So the author's claim is not valid on my laptop.

Possible reasons: different hardware, gcc and Linux versions

## Experiments

Based on the weaknesses above, I designed 4 experiments. I change one factor at a time, and then all factors together. I call them Experiment 1 to 4 here. In the figures, "Test 1, 2, 3" means the three runs of the same experiment.

### Experiment 1: change the sizes

Main change in the code: 10 sizes

```bash
touch $OUTPUT_FILE
for i in 100 300 1000 3000 10000 30000 100000 300000 1000000 3000000 ; do
    for rep in `seq 1 5`; do
        echo "Size: $i" >> $OUTPUT_FILE;
        ./src/parallelQuicksort $i >> $OUTPUT_FILE;
    done ;
done
```

### Experiment 2: change the repetitions

Main change in the code: 30 repetitions

```bash
touch $OUTPUT_FILE
for i in 100 1000 10000 100000 1000000; do
    for rep in `seq 1 30`; do
        echo "Size: $i" >> $OUTPUT_FILE;
        ./src/parallelQuicksort $i >> $OUTPUT_FILE;
    done ;
done
```

### Experiment 3: change the order to random

Main change in the code:

```bash
touch $OUTPUT_FILE
for i in 100 1000 10000 100000 1000000; do
for rep in `seq 1 5`; do
echo $i
done
done | shuf | while read i; do
echo "Size: $i" >> $OUTPUT_FILE;
./src/parallelQuicksort $i >> $OUTPUT_FILE;
done
```

### Experiment 4: change all conditions

Main change in the code:

```bash
touch $OUTPUT_FILE
for i in 100 300 1000 3000 10000 30000 100000 300000 1000000 3000000; do
for rep in `seq 1 30`; do
echo $i
done
done | shuf | while read i; do
echo "Size: $i" >> $OUTPUT_FILE;
./src/parallelQuicksort $i >> $OUTPUT_FILE;
done
```

### Running and converting

I ran every script three times and the original script too, and changed the output from txt to csv files with the Perl script given by the author:

```bash
cd ~/M2R-ParallelQuicksort/data/liya_2026-11-04
for f in *.txt; do
  perl ../../scripts/csv_quicksort_extractor.pl < "$f" > "${f%.txt}.csv"
done
ls
cd ../..
```

Then I analysed everything with `scripts/analyze.R` and plotted it with ggplot:

```bash
Rscript scripts/analyze.R > data/liya_2026-11-04/analysis_output.txt 2>&1
```

| Experiment | Script | Sizes | Reps | Order |
|---|---|---|---|---|
| Original | run_benchmarking.sh | 5 | 5 | fixed |
| More sizes | run_benchmarking_size.sh | 10 | 5 | fixed |
| More repetitions | run_benchmarking_reps.sh | 5 | 30 | fixed |
| Random order | run_benchmarking_random.sh | 5 | 5 | random |
| All changes | run_benchmarking_all.sh | 10 | 30 | random |

Data are in `data/liya_2026-11-04`, the analysis script is `scripts/analyze.R`.

## Results

In every figure below: x axis = array size (number of integers to sort), y axis = time to sort in seconds, both in log scale. Blue = sequential (1 thread), orange = parallel (several threads), dark red = built-in C `qsort`. Test 1, 2, 3 = the three runs of the same script.

### Original (5 sizes, 5 repetitions, fixed order)

![original](data/liya_2026-11-04/fig1_original.png)

### More sizes (10 sizes, 5 repetitions, fixed order)

![more sizes](data/liya_2026-11-04/fig2_more_sizes.png)

### More repetitions (5 sizes, 30 repetitions, fixed order)

![more repetitions](data/liya_2026-11-04/fig3_more_reps.png)

### Random order (5 sizes, 5 repetitions, random order)

![random order](data/liya_2026-11-04/fig4_random_order.png)

### All changes (10 sizes, 30 repetitions, random order)

![all changes](data/liya_2026-11-04/fig5_all_changes.png)

### Parallel time / sequential time (from analysis_output.txt)

- 100 integers: about 380-490 times slower
- 1,000,000 integers: about 2.8-3.5 times slower
- 3,000,000 integers (the experiments with 10 sizes): about 2.8-3.2 times slower

The parallel program was never faster. The three tests of each experiment agree.

## Confidence intervals

At first the confidence intervals were not visible in my plots, because they are too small on a log scale. So I made one more figure that shows them: only 1,000,000 integers, Test 1 of each experiment.

![confidence interval](data/liya_2026-11-04/fig_ci_dots.png)

Figure: every repetition (grey dot), the mean (red diamond) and its 95% confidence interval (red bar), for 1,000,000 integers, Test 1 of each experiment. With 5 repetitions a single slow run can stretch the interval a lot (for example the built-in qsort in "More sizes"). With 30 repetitions the interval is much shorter, so the mean is more reliable. Each row has its own y axis, so compare bar lengths only within a row.

Average width of the 95% CI (% of the mean), from analysis_output.txt:

| Experiment | Width |
|---|---|
| Original | 9.9 |
| More sizes | 11.6 |
| More repetitions | 4.4 |
| Random order | 12.5 |
| All changes | 5.0 |

With 30 repetitions the interval is about half as wide as with 5.

## Observations

- Random order: no clear difference with 5 repetitions, the ratios are similar to Original (for example about 3.1-3.4 at 1,000,000 integers). It does not show that fixed order is harmless, 5 repetitions may be too few to see an effect.
- More sizes: the ratio does not jump, it drops gradually from about 400 at 100 integers to about 3 at 100,000 integers, then stays around 3. This looks like a fixed cost (probably creating threads) that is spread over bigger arrays, but I did not test it.
- More repetitions and All changes have much narrower confidence intervals than the experiments with 5 repetitions.

## Conclusion and limitations

On my laptop (i5-1340P, 16 logical CPUs, gcc 15.2, -O0) the claim that the parallel version saves time is not supported: it was slower at every size I tested, up to 3,000,000 integers. The journal found the parallel version faster at 1,000,000 integers, so the result depends on the machine.

Limitations: only one machine, only `-O0`, only sizes up to 3,000,000.

Next: repeat on my groupmate's (Tom Laucournet) machine 


## Bigger sizes (after discussion with my groupmate)

After discuss with groupmate, he thinks that my design of experiments needs bigger size, because so far parallel version has no advantages. Maybe the parallel version only wins when the array is much bigger. So I will try with bigger size.

### Experiment 5: much bigger sizes (run_benchmarking_bigsize.sh)

Main change in the code: sizes up to 10,000,000, 5 repetitions, fixed order. I ran this script two times (Test 1, Test 2).

```bash
touch $OUTPUT_FILE
for i in 100 1000 10000 100000 1000000 10000000 ; do
for rep in `seq 1 5`; do
echo "Size: $i" >> $OUTPUT_FILE;
./src/parallelQuicksort $i >> $OUTPUT_FILE;
done ;
done
```

### Experiment 6: much bigger sizes with more reps and random order (run_benchmarking_bigsizewithall.sh)

Main change in the code: sizes up to 10,000,000, 30 repetitions, random order. I ran this script one time.

```bash
touch $OUTPUT_FILE
for i in 100 1000 10000 100000 1000000 10000000 ; do
for rep in `seq 1 30`; do
echo $i
done
done | shuf | while read i; do
echo "Size: $i" >> $OUTPUT_FILE;
./src/parallelQuicksort $i >> $OUTPUT_FILE;
done
```

### Experiment 7: 100,000,000 integers (run_benchmarking_bigbigsize.sh)

Main change in the code: one more size, 100,000,000 integers (400 MB for one array), 5 repetitions, fixed order. I ran this script one time. I did not do 30 repetitions here, because 30 repetitions would take too long.

```bash
touch $OUTPUT_FILE
for i in 100 1000 10000 100000 1000000 10000000 100000000 ; do
for rep in `seq 1 5`; do
echo "Size: $i" >> $OUTPUT_FILE;
./src/parallelQuicksort $i >> $OUTPUT_FILE;
done ;
done
```

The data are in `data/liya_2026-11-04_big`, the analysis script is `scripts/analyze_big.R` same figures as before: three programs in one panel, time in seconds, log scales.

### Results of the bigger sizes

Experiment 5 (up to 10M, 5 repetitions, fixed order):

![bigger sizes](data/liya_2026-11-04_big/fig6_big_sizes.png)

Experiment 6 (up to 10M, 30 repetitions, random order):

![bigger sizes all changes](data/liya_2026-11-04_big/fig7_big_all_changes.png)

Experiment 7 (up to 100M, 5 repetitions, fixed order):

![huge sizes](data/liya_2026-11-04_big/fig8_huge_sizes.png)

Parallel time / sequential time (from analysis_big_output.txt):

| size | Exp 5 Test 1 | Exp 5 Test 2 | Exp 6 | Exp 7 |
|---|---|---|---|---|
| 100 | 352 | 375 | 459 | 434 |
| 1,000 | 115 | 110 | 119 | 122 |
| 10,000 | 20.5 | 20.2 | 20.3 | 19.4 |
| 100,000 | 4.0 | 3.6 | 3.3 | 3.5 |
| 1,000,000 | 2.4 | 3.2 | 3.3 | 3.4 |
| 10,000,000 | 2.5 | 2.9 | 3.0 | 3.0 |
| 100,000,000 | - | - | - | 2.3 |

Average width of the 95% CI (% of the mean): Experiment 5 12.4, Experiment 6 3.8, Experiment 7 9.3.

### Observations of the bigger sizes

- The parallel version is still slower at every size, also at 10,000,000 and at 100,000,000. So bigger size alone does not make the parallel version faster on my laptop.
- The ratio becomes smaller when the size is bigger: about 3.3 at 1,000,000, about 3.0 at 10,000,000 and 2.3 at 100,000,000. But the 100,000,000 point is only one run with 5 repetitions, and the sequential times of the 5 repetitions are quite different. So I think it is a trend, but not a proof.

- My groupmate (Tom) looked at my curves and said that after about 10^6 the sequential and the parallel curves become parallel, so it seems that my computer stops gaining parallel performance, while on his machine the two curves are the same as mine until 10^6, but after that his parallel curve continues to improve and gets closer to the sequential one. On a log-log figure two parallel lines mean that the ratio is constant, so his remark is consistent with my numbers. I do not know why yet. Possible reasons (not tested):
  - the parallel version uses only a few cores on my laptop,
  - the memory (bandwidth, cache) is the limit and not the CPU,
  - his machine has a different CPU or different compile flags.


## Conclusion and limitations

On my laptop the claim that the parallel version saves time is not supported: it was slower at every size I tested, even up to 100,000,000 integers. 

The ratio parallel / sequential goes down slowly with the size, about 3.3 at 1M, 3.0 at 10M, 2.3 at 100M, so I cannot say that the parallel version will never win at bigger sizes, but I did not test it.

Limitations: only sizes up to 100,000,000 and only one run and 5 repetitions at this biggest size.



# p-CSAT experiment artifact

This repository contains a statically linked p-CSAT executable for Linux x86-64
and 40 compressed AIG instances. No compilation is required.

| Directory | Instances | Experiment |
| --- | --- | --- |
| `ins/` | `ins1.aig.gz`–`ins20.aig.gz` | Mapped circuit SAT |
| `b/` | `b1.aig.gz`–`b20.aig.gz` | Reconstructed uniform instances with SAT sweeping |

Both suites use instance numbers 1–20, matching the corresponding table rows.

## Prepare

Run these commands from the repository root. They require `gzip` and GNU `timeout`.

```sh
chmod +x p-CSAT
gzip -dk ins/*.aig.gz b/*.aig.gz
```

The executable reads the extracted `.aig` files. The compressed originals are kept.

## Experiment commands

These commands use the experiment solver settings, with default-valued options
omitted. The timeout settings are given below. Replace the instance number to
run another case.

### Mapped circuit SAT (`ins/`)

Parallel: 32 threads, 1000-second cutoff.

```sh
timeout 1000 ./p-CSAT ins/ins1.aig --mode=0
```

Sequential: 1 thread, 5000-second cutoff.

```sh
timeout 5000 ./p-CSAT ins/ins1.aig --mode=0 -t 1
```

The parallel experiment used an external 1000-second timeout; the solver's
internal cutoff remained at its 5000-second default.

### SAT sweeping (`b/`)

Parallel: 32 threads, 5000-second cutoff.

```sh
timeout 5000 ./p-CSAT b/b1.aig --mode=0 --sat_sweeping=1
```

Sequential: 1 thread, 5000-second cutoff.

```sh
timeout 5000 ./p-CSAT b/b1.aig --mode=0 --sat_sweeping=1 -t 1
```

The solver's internal cutoff, evaluation cutoff, and external timeout are all
5000 seconds.

The original sweeping runs also used `taskset` to assign each concurrent case
its own CPU set. To pin a run, insert `taskset -c CPU_LIST` between the timeout
duration and `./p-CSAT`, choosing 32 available logical CPUs for parallel runs
or one for sequential runs.

## Defaults and results

The default configuration uses 32 threads, a 5000-second internal cutoff,
SAT sweeping disabled, and partitioning, worker diversity, and clause sharing
enabled. Sweeping defaults are 8 rounds, `sweep_bv_bits=17`, and seed 0.
`-t 1` selects one thread. `--mode=0` is a required argument in this executable
and must be supplied even though the selected mode is 0.

The solver prints `s SATISFIABLE` or `s UNSATISFIABLE` and exits with code 10
or 20, respectively. These nonzero codes indicate a solved instance.
GNU `timeout` returns 124 when its time limit is reached.

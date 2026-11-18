# p-CSAT experiment artifact

This repository contains a statically linked p-CSAT executable for Linux x86-64
and 40 compressed AIG instances.

| Directory | Instances | Experiment |
| --- | --- | --- |
| `ins/` | `ins1.aig.gz`–`ins20.aig.gz` | circuit SAT |
| `b/` | `b1.aig.gz`–`b20.aig.gz` |instances with SAT sweeping |

Both suites use instance numbers 1–20.

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

### circuit SAT (`ins/`)

Parallel: 32 threads, 1000-second cutoff.

```sh
timeout 1000 ./p-CSAT ins/ins1.aig --mode=0
```

Sequential: 1 thread, 5000-second cutoff.

```sh
timeout 5000 ./p-CSAT ins/ins1.aig --mode=0 -t 1
```

### SAT sweeping (`b/`)

Parallel: 32 threads, 5000-second cutoff.

```sh
timeout 5000 ./p-CSAT b/b1.aig --mode=0 --sat_sweeping=1
```

Sequential: 1 thread, 5000-second cutoff.

```sh
timeout 5000 ./p-CSAT b/b1.aig --mode=0 --sat_sweeping=1 -t 1
```

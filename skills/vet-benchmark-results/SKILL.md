---
name: vet-benchmark-results
description: Vet a performance measurement before reporting or acting on it. Use when running a benchmark, comparing the performance of options, or reporting a speedup or regression you measured.
---

# Vet Benchmark Results

Answer each question below with evidence from a run, not a guess from reading code.

For a quick ballpark the user asked for, one run is enough. Still check questions 4 and 7, and say it is one run. A choice between options is never a ballpark.

## Before running

- Write down the claim you expect to make, e.g. "export is 30% faster at p50 on the 60k-row dataset". The questions test that sentence.
- Read the measurement script. Note what it times, counts, and ignores.
- Check machine load and core count. If the machine is busy and you can't stop the load, alternate the sides so both see the same noise, and say so.

## Questions

1. **Why not double?** Name the limiter: a core, a lock, the disk, the network, or the load generator itself. Find it with a profiler or system counters in a separate run you don't report, then map it to source. If a change didn't move the number, the limiter explains why.
2. **Was it tuned?** Run every side the way it runs in production: release builds, production flags, warm or cold caches as production sees them, the same versions and data. If one side runs on defaults, you compared configurations, not implementations. Don't pick a winner from an untuned run.
3. **Did it break limits?** Do the arithmetic against disk and network bandwidth and core count. Removing a piece that takes 10% of the run makes it at most about 11% faster. A result past a limit measured something else, such as a cache, a no-op, or a bug.
4. **Did it error?** Count failures and check outputs are correct, not just present. Rejections are fast, timeouts are slow. If the script doesn't count errors, add the count.
5. **Does it reproduce?** Run each side at least 5 times, alternating A and B. Report the median and range. A gap smaller than run-to-run variation is no measurable difference.
6. **Does it matter?** Next to a micro result, measure the end-to-end path a user waits on, and report the micro result as a share of it.
7. **Did it even happen?** Confirm the work ran inside the timed region: the request arrived, the rows were written, the result was used. Lazy code, unawaited promises, and timeouts produce numbers for work that never happened.

## Report

- Lead with the verdict: faster, slower, no measurable difference, or inconclusive.
- Give the number with its unit, the run count, the range, and the limiter, e.g. "p50 41 ms → 33 ms, median of 7 runs per side, range 32 to 35 ms after, bound by JSON parsing on one core".
- Call it inconclusive when you can't name the limiter, a side ran untuned, or you couldn't check questions 4 and 7. Name the gap.

## Location and plan

Blegdamsvej 3B, Copenhagen

Lectures: 17-18 November 2025, 10.00 – 16.00

## Exercises

### Login

TBD

### Software installations

Install apptainer, conda, and omnibenchmark using conda following [https://docs.omnibenchmark.org/latest/howto/](ob's instructions.

### The clustering example (clustbench)

Plot clustbench's topology.

Dry-run the clustbench using conda.

Dry-run the clustbench using apptainer.

Run the clustbench using conda or apptainer.

What are methods producing? Is it a vector of partitions? Is it a 2D matrix? 

### Add a new module to the clustering example

We aim to modify the `Clustering_conda.yml` manifest to add a method that assigns random clusters to the observations. This aims to be a baseline method. Pin the random seed and/or pass it as an argument to the method.

1. Start a new git repository
2. 

Write a proper environment file.

Test the method locally.

Fork `clustering_example` and update the YAML to incorporate the new method.

Run the updated benchmark.

#### Common pitfalls

1. Starting too big:
   - Mistake: Running full benchmark immediately
   - Solution: Start with dry-run, then small subset

2. Ignoring resource requirements:
   - Mistake: Running on laptop with insufficient RAM
   - Solution: Check requirements, use HPC if needed

3. Not checking architecture:
   - Mistake: Trying to run on arm64 when conda does not offer packages for that architecture
   - Solution: Verify the software environments match your system's architecture

4. Hardcoded paths in modules:
   - Mistake: `/home/user/data/file.csv` in code
   - Solution: Use command-line arguments and relative paths

5. Missing dependency versions:
   - Mistake: `numpy` instead of `numpy==1.23.5`
   - Solution: Pin all versions

6. Not testing locally:
   - Mistake: `ob run benchmark`  untested module
   - Solution: Test locally with toy data first

7. Ignoring random seeds:
   - Mistake: Stochastic methods without seed control
   - Solution: Accept and use random seed parameter

8. Poor error handling:
   - Mistake: Silent failures
   - Solution: Catch exceptions, log errors, return error codes


#### Debugging

### Plan your own omnibenchmark

#### Input/output formats

#### Software environments

#### Stages vs modules

#### Running locally or on HPC

#### Sharing results

#### Collaborative coding

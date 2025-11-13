## Location and plan

Blegdamsvej 3B, Copenhagen

Lectures: 17-18 November 2025, 10.00 – 16.00

## Exercises

### Login

TBD

### Software installations

[Install apptainer, conda, and omnibenchmark using conda](https://docs.omnibenchmark.org/latest/howto/).


Mind the exact omnibenchmark version:  `pip install omnibenchmark==0.3.1`.

Test these:

```bash

conda --version
apptainer --version
ob --version
```

### The clustering example (clustbench)

Get the clustbench manifest by cloning the clustbench repository at https://github.com/omnibenchmark/clustering_example .

```bash
cd
mkdir benchmarks
cd $_
git clone git@github.com:omnibenchmark/clustering_example.git
cd clustering_example
```

Plot clustbench's topology.

```bash
ob info topology -b Clustering_conda.yml
```

And the computational topology.

```bash
ob info computational -b Clustering_conda.yml | dot -Tpng -Gdpi=300 -o plot.png
## visualize plot.png
```

How are software stacks defined?

```bash
## conda envs are recipes using conda-forge and bioconda
grep conda Clustering_conda.yml
ls -l envs
head -100 envs/*yml

## apptainer images are downloaded from a registry, but recipes are available
grep apptainer Clustering_oras.yml
head envs/build_singularity.sh
head envs/*def
```

Dry-run the `Clustering_conda.yml`. 

```bash
## local-storage means results will be stored locally
ob run benchmark -b Clustering_conda.yml --dry-run --local-storage
```

Dry-run the `Clustering_oras.yml`.

```bash
ob run benchmark -b Clustering_oras.yml --dry-run --local-storage
```

Run the clustbench using conda or apptainer.

```bash
ob run benchmark -b Clustering_conda.yml --dry-run --local-storage
```

What are methods producing? Is it a vector of partitions? Is it a 2D matrix? 

```bash

```

Run a single module using `ob run module`.

```bash

```

Run a single module without omnibenchmark's CLI, just using the right interpreter, script name, and arguments. Plan to activate the software backend.

```bash

```

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

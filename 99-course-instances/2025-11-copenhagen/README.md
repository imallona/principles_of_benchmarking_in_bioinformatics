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
## list benchmark results
## notice provenance is explicit and described by folder nesting

tree -a out

## inspect the output of method agglomerative/linkage-average for dataset fcps/atom
zcat out/data/clustbench/dataset_generator-fcps_dataset_name-atom/clustering/agglomerative/linkage-average/clustbench_ks_range.labels.gz  | head

## inspect the output of all methods for that dataset (fcps/atom)
##   find ... -exec sh -c '...' sh {} +: runs a shell loop over all found files.
##   echo "$f": prints the filename.
##   zcat "$f" | head: shows the first 10 lines of that file.
##   echo: adds a blank line between files for readability.
find out/data/clustbench/dataset_generator-fcps_dataset_name-atom/clustering/*/*/ -name 'clustbench_ks_range.labels.gz' \
     -exec sh -c 'for f; do echo "$f"; zcat "$f" | head; echo; done' sh {} +
## so a 2D matrix. `k` means the true number of clusters
```

Run a single module using `ob run module`.

```bash
ob run module -b Clustering_conda.yml  -m fastcluster --input_dir out/data/clustbench/dataset_generator-fcps_dataset_name-atom/
```

Run a single module without omnibenchmark's CLI, just using the right interpreter, script name, and arguments. Plan to activate the software backend.

```bash
## create the environment independently; also helps to update it if needed
conda env create -f envs/clustbench.yml

## activate the environment (it's nested under omnibenchmark's)
conda activate clustbench
echo $PATH

## get (git clone in this case) the repository to try
##  you might want to change the working directory
old_path=$(pwd)
cd
git clone git@github.com:imallona/clustbench_fastcluster.git
cd clustbench_fastcluster

## the method already has a help listing its arguments
python run_fastcluster.py

## create output folder
mkdir -p manual_run_output

## fill the arguments pointing to appropriate inputs
python run_fastcluster.py --data.matrix ~/benchmarks/clustering_example/out/data/clustbench/dataset_generator-fcps_dataset_name-atom/clustbench.data.gz \
       --data.true_labels ~/benchmarks/clustering_example/out/data/clustbench/dataset_generator-fcps_dataset_name-atom/clustbench.labels0.gz \
       --output_dir manual_run_output \
       --name test \
       --linkage complete

## inspect results
zcat manual_run_output/*gz

## go back to the old working directory
cd $old_path

## deactivate the current environment (careful, if repeated this could deactivate the omnibenchmark env)
conda deactivate
echo $PATH
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

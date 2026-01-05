# Location and plan

Blegdamsvej 3B, Copenhagen

Lectures: 17-18 November 2025, 10.00 – 16.00

# Philosophy

Please ask questions. If something feels broken it might be: let us know so we can fix it.

You can compute in a dedicated Linux server but also in your laptop *if it runs a Linux on amd64*. (Our instructions assume `apt`-based distributions.)

We aim to go through these exercises while also running a basic recap session on files, workflows and software management strategies in benchmarking of bioinformatics tools; plus Omnibenchmark. [This repository](https://github.com/imallona/principles_of_benchmarking_in_bioinformatics) contains longer units with extra exercises and slides about these; running these exercises is not mandatory (it might be interesting if you get bored during lectures).

The main documentation is https://docs.omnibenchmark.org/latest/.

Please fill three short feedback forms:
- [17th Nov](https://forms.gle/KCUr9f1RF5KqYNQJ6)
- [18th Nov](https://forms.gle/k7ZDMvVWY8FaM2SZ9)
- end of the course

## Clustbench resources

To understand the exercises' clustering benchmark by Marek Gagolewski's:
- [clustbench documentation (omnibenchmark-free)](https://clustering-benchmarks.gagolewski.com/index.html)
- [Datasets](https://clustering-benchmarks.gagolewski.com/weave/data-v1.html#wut/circles)
- [Gagolewski M., A framework for benchmarking clustering algorithms, SoftwareX 20, 2022, 101270](linkinghub.elsevier.com/retrieve/pii/S2352711022001881)

# Exercises

## Login details

Info shared elsewhere: in short, `ssh`-able Linux server on amd64 architecture.

Recommendation: `ssh-copy-id` your key to ease log in, and use `tmux` to run long-ish benchmarks, installations or tests.

Please monitor resource usage with `top`, `vmstat`, `free`, `df` etc.

## Config

Create a `tmp` folder within your home, and export the `TMPDIR` accordingly.

```
mkdir -p ~/tmp
export TMPDIR=~/tmp

## make changes permanent; caution tilde does not expand to $HOME if noninteractive
echo 'export TMPDIR=$HOME/tmp' >> ~/.bashrc

# load changes
source ~/.bashrc
```

## Software installations

[Install conda, and omnibenchmark using conda](https://docs.omnibenchmark.org/latest/howto/). Apptainer is already installed.

Mind installing the exact omnibenchmark version:  **`pip install omnibenchmark==0.3.2`**. **Also install the storage-related requirements with `pip install 'omnibenchmark[s3]'`**.

Short instructions to install:

```bash
mkdir -p soft
cd soft

# download installation script
# this assumes our server; if not, select another script from https://conda-forge.org/miniforge/
wget https://github.com/conda-forge/miniforge/releases/download/25.3.1-0/Miniforge3-25.3.1-0-Linux-x86_64.sh

chmod +x *sh
./Miniforge3-25.3.1-0-Linux-x86_64.sh
## read and accept the EULA, install with defaults and, ideally, do not modify your .bashrc to activate conda base on login

# activate conda base environment
#  (unless you decided to have it activated by default and your prompt start with (base))
source ~/miniforge3/bin/activate
```

Now, install omnibenchmark

```bash

curl -sSL https://raw.githubusercontent.com/omnibenchmark/omnibenchmark/main/omni-environment.yml -o omni-environment.yml

conda create -n omnibenchmark python=3.12 -y
conda activate omnibenchmark
conda env update -f omni-environment.yml

pip install 'omnibenchmark[s3]' ## this is to make sure the object storage dependencies are installed

```

Test these:

```bash

conda --version
apptainer --version
ob --version
```

## The clustering example (clustbench)

### Clone

Get the clustbench manifest by cloning the clustbench repository at https://github.com/omnibenchmark/clustering_example .

```bash
cd  # this goes to your $HOME
mkdir -p benchmarks
cd benchmarks
git clone https://github.com/omnibenchmark/clustering_example
cd clustering_example
```

### Plot topology

Print clustbench's topology (in [mermaid](https://mermaid.js.org/) format) and visualize it with a mermaid renderer.

```bash
ob info topology -b Clustering_conda.yml
```


<!-- ### Plot computational topology  -->
<!-- And the computational topology. -->

<!-- ```bash -->
<!-- ob info computational -b Clustering_conda.yml | dot -Tpng -Gdpi=300 -o plot.png -->
<!-- ## visualize plot.png -->
<!-- ``` -->


### Inspect software environment specifications

```bash
## conda envs are recipes using conda-forge and bioconda
##  question: when to use nodefaults, conda-forge, and defaults?
grep conda Clustering_conda.yml
ls -l envs
head -100 envs/*yml

## apptainer images are downloaded from a registry, but recipes are available
###  question: what is an ORAS registry?
grep apptainer Clustering_oras.yml
head envs/build_singularity.sh
head envs/*def
```

### Dry run benchmark

Dry-run the full `Clustering_conda.yml` benchmark. 

```bash
## local-storage means results will be stored locally
##  no it really doesn't matter, because this is a dry run, but for actual runs
##  we don't want to store results in a S3 bucket (yet)
##  hence the argument
ob run benchmark -b Clustering_conda.yml --dry-run --local-storage
```

Dry-run the `Clustering_oras.yml`.

```bash
ob run benchmark -b Clustering_oras.yml --dry-run --local-storage
```

### Run benchmark

Run the clustbench using conda or apptainer.

```bash
ob run benchmark -b Clustering_conda.yml --cores 1 --local-storage
```

What is omnibenchmark producing? What is the output (`out` folder) structure? Are methods results a vector of partitions? or 2D matrices? why?

```bash
## list benchmark results
## notice provenance is explicit and described by folder nesting

tree -a out

## inspect the output of method agglomerative/linkage-average for dataset fcps/atom
zcat out/data/clustbench/dataset_generator-fcps_dataset_name-atom/clustering/agglomerative/linkage-average/clustbench_ks_range.labels.gz  | head

## inspect the output of all methods for that dataset (fcps/atom)
##   find ... -exec sh -c '...' sh {} +: runs a shell loop over all found files.
##     also noticed the asterisk in the directory path - a way to search all methods run on a given dataset
##   echo "$f": prints the filename.
##   zcat "$f" | head: shows the first 10 lines of that file.
##   echo: adds a blank line between files for readability.
find out/data/clustbench/dataset_generator-fcps_dataset_name-atom/clustering/*/*/ -name 'clustbench_ks_range.labels.gz' \
     -exec sh -c 'for f; do echo "$f"; zcat "$f" | head; echo; done' sh {} +
## so a 2D matrix. `k` means the true number of clusters
```

### Run module with `ob run module`

Run a single module using `ob run module`.

```bash
ob run module -b Clustering_conda.yml  -m fastcluster --input_dir out/data/clustbench/dataset_generator-fcps_dataset_name-atom/
```

### Run module manually (ideal for development)

Run a single module without omnibenchmark's CLI, just using the right interpreter, script name, and arguments. Plan to activate the software backend.

```bash
## please do not copy, paste and run as a chunk, but line by line instead

## create the environment independently; also helps to update it if needed
conda env create -f envs/clustbench.yml

## activate the environment (it's nested under omnibenchmark's)
conda activate clustbench

## make sure the environment has been activated (appended to the left of the PATH)
echo $PATH

## get (git clone in this case) the repository to try
##  you might want to change the working directory
old_wd=$(pwd)
cd
git clone https://github.com/imallona/clustbench_fastcluster
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
cd $old_wd

## make sure clustbench is still leftmost
echo $PATH

## deactivate the current environment (mindfully, if repeated this could deactivate the omnibenchmark env)
conda deactivate

## make sure the omnibenchmark environment is still there (leftmost)
echo $PATH
```

### Un-comment some modules from the clustering example

Uncomment some of the modules (enabling them), save the YAML, and re-evaluate the benchmark scope (that is, `ob run benchmark` with a dry run and not an actual run. Are the new modules incorporated in the new plan? are some of the results cached? why?

```bash
## uncomment (i.e., effectively add) some modules to the `Clustering_conda.yml`
## then, dry run

ob run benchmark -b Clustering_conda.yml --local-storage --dry-run

```

### Add a new module to the clustering example

We aim to modify the `Clustering_conda.yml` manifest to add a method that assigns random clusters to the observations. This aims to be a baseline method. Pin the random seed and/or pass it as an argument to the method.

1. Start a new git repository
2. Implement (in your favourite language) a method that parses, from the commandline, the required arguments for a stage 'clustering' module. That is:

```
--data.matrix       ## the actual data to cluster (this is specific to this `stage` contract)
--data.true_labels  ## the true partitioning  (this is specific to this `stage` contract)
--output_dir        ## the output dir (all modules need this argument)
--name              ## the name of the module (all modules need this argument)
```

Please consider also adding a `--seed` argument to set the random seed.

3. Write a conda YAML (or singularity container recipe, and build it) with the required dependencies.
4. Test the method locally.
5. Fork `clustering_example` and update the YAML to incorporate the new method.
6. Dry run the updated benchmark.

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

### Plan your own omnibenchmark

Lecture and groups discussion on benchmark topologies *plus workflow constraints*:

1. Stages contain modules sharing input/output file formats and shapes (in syntax but also in semantics)
2. Modules might group several methods if they share software environments; or could be fully atomic (one module == one repository == one method)
3. Using Slurm
4. Using S3 object storage
5. How to collaboratively code with other users

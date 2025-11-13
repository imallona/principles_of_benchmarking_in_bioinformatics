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

#### Debugging

### Plan your own omnibenchmark

#### Input/output formats

#### Software environments

#### Stages vs modules

#### Running locally or on HPC

#### Sharing results

#### Collaborative coding

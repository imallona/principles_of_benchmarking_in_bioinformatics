# Omnibenchmark - Exercises

## Questions and Answers

### Section 1: Omnibenchmark overview

#### Q1.1: What is Omnibenchmark and how does it differ from traditional benchmark workflows?

Answer:
Omnibenchmark is a platform for continuous, collaborative benchmarking in bioinformatics.

Key differences from traditional benchmarks:

Traditional benchmarks:
- One-time publication with fixed results
- Centralized, monolithic codebase
- Difficult to extend with new methods
- Results become outdated quickly
- Limited community involvement

Omnibenchmark:
- Continuous: Automatically updates when methods or data change
- Modular: Distributed modules in separate repositories
- Collaborative: Community can contribute new modules
- Reproducible: Full provenance tracking with Renku
- Automated: GitLab CI orchestrates execution
- Living: Always reflects current state of the field

Think of it as "continuous integration for benchmarking."

#### Q1.2 What backends does Omnibenchmark support for managing software environments?

Answer:
Omnibenchmark supports three backends:

1. Conda: Python package management (default)
   - Best for: Local development, standard Python packages
   - Configuration: `Clustering.yaml`
   - Pros: Easy to use, widely supported
   - Cons: Can be slow, conflicts possible

2. Apptainer/Singularity: Container system
   - Best for: HPC systems, complex dependencies
   - Configuration: `Clustering_apptainer.yaml`
   - Pros: Complete isolation, reproducible
   - Cons: Requires Apptainer/Singularity installation

3. Easybuild/Lmod: Environment modules
   - Best for: HPC with existing module systems
   - Configuration: `Clustering_easybuild.yaml`
   - Pros: Fast, leverages HPC infrastructure
   - Cons: Requires building (or pre-built) modules

See `envs/README.md` in the repository for specific configuration details.

### Section 2: The Clustering Example

#### Q2.1: What is the clustering_example benchmark and what does it include?

Answer:
Repository: `https://github.com/omnibenchmark/clustering_example`

A comprehensive benchmark for evaluating clustering methods using the clustbench dataset collection (originally by Marek Gagolewski).

Components:

Data (79+ datasets):
- MNIST (fashion, digits)
- UCI datasets (iris, wine, etc.)
- Synthetic datasets (various shapes and difficulties)
- Sipu datasets (spatial patterns)
- WUT datasets (Warsaw University synthetic)

Methods (20+ across 5 families):
- Hierarchical: fastcluster, sklearn, genieclust
- Partitioning: k-means, PAM, Clara
- Density-based: HDBSCAN variants
- Model-based: Gaussian mixture
- Specialized: FCPS methods (Minimax, MinEnergy, etc.)

Metrics:
- Adjusted Rand Index (ARI)
- Mutual Information variants (MI, NMI, AMI)
- Fowlkes-Mallows score
- Normalized clustering accuracy
- Pair sets index

Important: Only runs on amd64 architecture (not arm64/M1 Macs).

#### Q2.2: What is the k±2 strategy and why is it used?

Answer:
The k±2 strategy: For each dataset with true number of clusters k, test methods with k-2, k-1, k, k+1, k+2 clusters.

Special case: When k=3 (minimum), use k = 2, 2, 3, 5, 6 (fill with k=2 as needed).

Why use this strategy:

1. Fair comparison: All datasets get same number of runs
2. Robustness testing: Evaluates method sensitivity to cluster number
3. Realistic scenario: In practice, true k is often unknown
4. Comparable runtimes: Same computational effort per dataset

Trade-off: Some values are recomputed (e.g., k=2 twice when true k=3), but this ensures comparable execution times across datasets regardless of their true cluster count.

This better reflects real-world usage where the true number of clusters must be determined as part of the analysis.

#### Q2.3: What are the main method families included in the clustering example?

Answer:
Five method families with 20+ implementations:

1. fastcluster (`github.com/imallona/clustbench_fastcluster`)
   - Fast hierarchical clustering
   - Linkages: complete, ward, average, weighted, median, centroid

2. sklearn (`github.com/imallona/clustbench_sklearn`)
   - Scikit-learn implementations
   - Methods: BIRCH, k-means, spectral clustering, Gaussian mixture

3. genieclust (`github.com/imallona/clustbench_genieclust`)
   - Genie hierarchical clustering family
   - Variants: genie, GIC, ICA with different thresholds

4. FCPS (`github.com/imallona/clustbench_fcps`)
   - Fundamental Clustering Problems Suite
   - Methods: Minimax, MinEnergy, HDBSCAN (multiple minPts), Diana, Fanny, Hardcl, Softcl, Clara, PAM

5. agglomerative (`github.com/imallona/clustbench_agglomerative`)
   - Additional agglomerative implementations
   - Linkages: average, complete, ward

Each method family is in its own repository with standardized interface.

### Section 3: Installation and Setup

#### Q3.1: How do you install Omnibenchmark and its dependencies?

Answer:
```bash
# Option 1: Using pip
pip install omnibenchmark

# Verify installation
ob --version
```

Full documentation: `https://docs.omnibenchmark.org/latest/howto/#install-omnibenchmark`

#### Q3.2: How do you clone and explore the clustering example?

Answer:

```bash
# Clone the repository
# Option 1: SSH (if you have SSH keys set up)
git clone git@github.com:omnibenchmark/clustering_example.git

# Option 2: HTTPS
git clone https://github.com/omnibenchmark/clustering_example.git

# Enter the repository
cd clustering_example

# Explore the structure
ls -la
# Key files:
# - Clustering.yaml: Main configuration (conda backend)
# - Clustering_apptainer.yaml: Apptainer backend config
# - Clustering_easybuild.yaml: Easybuild backend config
# - envs/: Environment specifications
# - README.md: Documentation

# View the README
cat README.md

# Check the first 50 lines of a benchmark manifesto (conda backend)
head -50 Clustering.yaml
```

#### Q3.3: What should you check before running the clustering example?

Answer:

1. Architecture: Verify you're on amd64 (not arm64)
   ```bash
   uname -m  # Should show x86_64, not aarch64 or arm64
   ```

2. Available resources:
   ```bash
   # Check CPU cores
   nproc
   
   # Check available RAM
   free -h
   
   # Check available disk space
   df -h .
   ```

3. Software dependencies:
   ```bash
   # Check omnibenchmark
   ob --version
   
   # Check conda (if using conda backend)
   conda --version
   
   # Check git
   git --version
   ```

4. Dry run: Test without actually running, and using conda
   ```bash
   ob run benchmark -b Clustering_conda.yaml --dry-run
   ```

5. Expected runtime: Plan for 5 mins with 2 cores.

6. Internet connection: First run downloads data and creates environments

### Section 4: Running the Benchmark

#### Q4.1: What is the basic command to run the clustering benchmark?

Answer:

```bash
# Basic command with 2 cores
ob run benchmark -b Clustering_conda.yaml --local-storage --cores 2

# Command breakdown:
# ob run benchmark: Omnibenchmark command to run a benchmark
# -b Clustering.yaml: Specify the benchmarking manifest file
# --local-storage: Store results locally, not S3
# --cores 2: Run at most 2 concurrent tasks

# Always start with a dry run first:
ob run benchmark -b Clustering_conda.yaml --dry-run --local-storage

# This shows what would be executed without actually running
```

Important: This is computationally intensive. Consider starting with a subset of datasets for testing.

#### Q4.2: How do you monitor the execution and check for errors?

Answer:
Monitoring progress:

```bash
# Watch the console output
# Snakemake shows:
# - Jobs completed (e.g., [50/200] 25%)
# - Currently running jobs
# - Estimated time remaining


# Monitor resource usage
top   # Standard process monitor

# Check intermediate results
ls -lhR out
du -sh out  # Check disk usage
```

Handling errors:

```bash
# If execution fails:
# Check error message in console

# Common issues:
# - Out of memory: Reduce --cores or exclude large datasets
# - Missing dependencies: Check backend installation
# - Network issues: Retry after connection is stable

# Resume after fixing (Snakemake resumes automatically)
ob run benchmark -b Clustering_conda.yaml --local-storage --cores 2
```

#### Q4.3: What outputs does the benchmark produce?

### Section 5: Understanding the Configuration

#### Q5.1: Explain the structure of the Clustering.yaml configuration file.

todo

Key concepts:
- Each section (data, methods, metrics) depict benchmarking stages and lists repositories carrying out benchmarking steps that share input and output formats
- Parameters modify the behaviour of modules

#### Q5.2: How would you modify the configuration to run a subset of the benchmark?


#### Q5.3: What information is typically in a module's entrypoint?


### Section 6: Designing benchmark manifest-compatible modules

#### Q6.1: What are the steps to contribute a new method module to a benchmark?

Answer:
Steps to create and contribute a method module:

1. Create repository structure:
   ```bash
   mkdir my_clustering_method
   cd my_clustering_method
   git init
   ```

2. Implement standardized interface:
   ```
   my_clustering_method/
   ├── config.yaml           # Omnibenchmark config
   ├── environment.yaml      # Conda environment
   ├── src/
   │   ├── run.py           # Main script
   │   └── utils.py         # Helper functions
   ├── tests/
   │   └── test_method.py   # Unit tests
   ├── README.md            # Documentation
   └── LICENSE
   ```

3. Implement run script with standard interface:
   ```python
   # src/run.py
   import argparse
   import pandas as pd
   
   def main():
       parser = argparse.ArgumentParser()
       parser.add_argument('--input', required=True)
       parser.add_argument('--output', required=True)
       parser.add_argument('--n_clusters', type=int, required=True)
       args = parser.parse_args()
       
       # Load, process, save
       data = pd.read_csv(args.input)
       labels = my_clustering_method(data, args.n_clusters)
       pd.DataFrame(labels).to_csv(args.output, index=False)
   ```

4. Test locally:
   ```bash
   # Create test environment
   conda env create -f environment.yaml
   conda activate my_method
   
   # Test with toy data
   python src/run.py --input test_data.csv --output test_output.csv --n_clusters 3
   ```

5. Push to a Git remote:
   ```bash
   git add .
   git commit -m "Initial implementation"
   git push origin main
   ```

6. Submit PR:
   Add your module to the benchmark YAML and submit a pull request to the benchmark repository.

#### Q6.2: What makes a good Omnibenchmark module?

#### Q6.3: How do you test a module before contributing it?

### Section 7: Best Practices and Troubleshooting

#### Q7.1: What are common pitfalls when using Omnibenchmark?

Answer:
Common mistakes and solutions:

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
   - Mistake: Submitting untested module
   - Solution: Test locally with toy data first

7. Ignoring random seeds:
   - Mistake: Stochastic methods without seed control
   - Solution: Accept and use random seed parameter

8. Poor error handling:
   - Mistake: Silent failures
   - Solution: Catch exceptions, log errors, return error codes

#### Q7.2: How do you debug a failing module in Omnibenchmark?

#### Q7.3: What should you do if the full benchmark takes too long?

## Hands-on Exercises

### Exercise 1: Clone the clustering example


```bash
# Clone the repository
git clone https://github.com/omnibenchmark/clustering_example.git
cd clustering_example

# Explore the structure
ls -la
cat README.md
head -50 Clustering_conda.yaml
```

Questions:
1. How many data modules are defined in Clustering.yaml?
2. Which method families are included?
3. What metrics are computed?

### Exercise 2: Dry run

Task: Run a dry-run and analyze what would be executed.

```bash
ob run benchmark -b Clustering_conda.yaml --dry-run --local-storage > dryrun.txt
```

Questions:
1. How many total jobs would be executed?
2. What's the first job that would run?
3. What's the dependency chain for one method-dataset pair?

### Exercise 3: Implement a Method Module

Task: Create a simple method module that wraps sklearn's DBSCAN.

Requirements:
- Standard command-line interface
- Conda environment file
- README with usage instructions
- Test with toy data

### Exercise 4: Debug a Failure

Scenario: A method module fails with error "ValueError: n_clusters must be >= 2"

Task: 
1. Identify the likely cause
2. Propose a fix
3. Explain how to test the fix
4. What should the module do differently?

## Additional Resources

### Documentation
- Omnibenchmark docs: https://docs.omnibenchmark.org
- Clustering example: https://github.com/omnibenchmark/clustering_example

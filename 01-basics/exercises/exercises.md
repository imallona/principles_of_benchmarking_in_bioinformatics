# Basics - Exercises

## Questions and answers

### Section 1: Text and executable Files

#### Q1.1: What is the difference between a text file and an executable file?

Answer:
- Text files are human-readable files, typically encoded in ASCII or UTF-8 format. They can be opened and edited with any text editor. Examples include `.txt`, `.csv`, `.yaml`, and `.md` files.
- Executable files are binary files containing machine-readable instructions that can be directly executed by the operating system. **They are platform-specific and require execute permissions in Linux**. Examples include compiled programs and shell scripts with execute permissions. So executables for arm64 won't work on amd64 processors.

#### Q1.2: How do you make a file executable in Linux?

Answer:
Use the `chmod` command:

```bash
chmod +x filename.sh
```

You can verify the permissions with:

```bash
ls -l filename.sh
```

The output should show `x` in the permissions (e.g., `-rwxr-xr-x`).

#### Q1.3: How can you check if a file is text or binary?

Answer:
Use the `file` command:

```bash
file myfile.txt
# Output: myfile.txt: ASCII text

file program.exe
# Output: program.exe: ELF 64-bit LSB executable, x86-64
```

You can also try viewing the first few lines with `head`:

```bash
head -n 5 myfile.txt  # Works for text files
```

#### Q1.4: What is the $PATH environment variable and why is it important?

Answer:
The `$PATH` is an environment variable that tells Linux **where to look for executable programs**. When you type a command, Linux searches through directories listed in `$PATH` (in order) to find the program.

View your PATH:

```bash
echo $PATH
# Output: /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
```

Key concepts:
- Directories are separated by colons (`:`)
- **Order matters and left has priorioty over right** - first match is executed
- This is where software "lives" on your system
- Can be modified to add new software locations

Find program location:

```bash
which python
# Output: /usr/bin/python
```

Add directory to PATH:
```bash
export PATH="/my/custom/bin:$PATH"
```

> [!IMPORTANT]
> This concept is crucial for understanding how conda and apptainer manage software.

---

### Section 2: Git version control

#### Q2.1: What is the difference between a local and remote Git repository?

Answer:
- Local repository: Your personal copy of the project on your computer
  - Contains all history and changes
  - You can work offline
  - Changes are private (as private as your machine is) until you push

- Remote repository: Shared copy on a server (BitBucket, GitHub, GitLab, etc.)
  - Means of centralization for collaboration (better than emailing tgz-ed git repositories)
  - Accessible to team members
  - Backup of your work

Key point: You have full control over your local repository, but need to explicitly sync with the remote using `push` and `pull`.

#### Q2.2: What do `git add` and `git commit` do, and where do they operate?

Answer:
Both commands operate on your local repository:

`git add` - Stages files

```bash
git add myfile.txt    # Stage specific file
git add .             # Stage all changes
```
- Marks files to be included in next commit
- Does NOT save changes yet
- Think of it as "preparing" changes

`git commit` - Saves changes locally

```bash
git commit -m "Add analysis script"
```
- Creates a snapshot in your local repository
- Changes are saved but still only on your computer
- Others cannot see these changes yet

#### Q2.3: What do `git push` and `git pull` do, and how do they differ from add/commit?

Answer:
These commands sync between local and remote repositories:

`git push` - Upload to remote
```bash
git push origin main
```
- Uploads your local commits to the remote repository
- Makes your changes visible to others
- Requires internet connection

`git pull` - Download from remote
```bash
git pull origin main
```
- Downloads commits from remote to your local repository
- Updates your local copy with others' changes
- Keeps you synchronized with the team

Key difference: `add`/`commit` are local operations, `push`/`pull` are remote operations.

#### Q2.4: Describe a typical Git workflow from editing a file to sharing it.

Answer:
Complete workflow:

```bash
# 1. Edit your files
emacs script.py # you can use vim (!), nano, or any other editor

# 2. Check what changed
git status

# 3. Stage the changes (local)
git add script.py

# 4. Commit to local repository (local)
git commit -m "Fix bug in data processing"

# 5. Push to remote repository (remote)
git push origin main

# 6. Later, get others' changes (remote)
git pull origin main
```

This way, changes flow like this: Working directory → Staging (add) → Local repo (commit) → Remote repo (push)

#### Q2.5: Why is Git important for reproducible research?

Answer:

Git enables reproducibility by:
1. Version history: Every change is tracked with who, what, when
2. Exact snapshots: You can recreate any previous state
3. Documentation: Commit messages explain why changes were made
4. Collaboration: Multiple people can contribute safely
5. Sharing: Others can reproduce your exact code version

> [!IMPORTANT]
> For benchmarking and research, this means:
> - Exact code version can be cited
> - Results can be traced to specific code
> - Changes can be reviewed and audited
> - Experiments can be reproduced

---

### Section 3: YAML files

#### Q3.1: What is YAML and why is it important?

Answer:
YAML (YAML Ain't Markup Language) is a human-readable data serialization format commonly used for configuration files. It is important because:
- Human-friendly but also computer parseable
- Widely used: Configuration for Snakemake, conda, Omnibenchmark, and many other tools
- Structure: Supports nested data structures, lists, and key-value pairs
- No special characters: Uses indentation for structure instead of brackets or tags
- It's plain text

#### Q3.2: What are the critical rules for YAML indentation?

Answer:
1. Use spaces, never tabs! Tabs will cause syntax errors
2. Consistent indentation: Usually 2 spaces per level
3. Alignment matters: Items at the same level must have the same indentation
4. Colons: Key-value pairs use `key: value` format

Example:

```yaml
# Correct
config:
  threads: 4
  memory: 8GB

# Wrong - inconsistent indentation
config:
  threads: 4
    memory: 8GB  # Error: wrong indentation
```

#### Q3.3: How do you create a list in YAML?

Answer:
Lists use dashes (`-`) with consistent indentation:

```yaml
# Simple list
dependencies:
  - python
  - numpy
  - pandas

# List of dictionaries
samples:
  - name: sample1
    type: control
  - name: sample2
    type: treatment
```

#### Q3.4: What are the common YAML data types?

Answer:
1. Strings: `name: "myproject"` or `name: myproject`
2. Numbers: `threads: 4` or `alpha: 0.5`
3. Booleans: `debug: true` or `debug: false`
4. Lists: Items with dashes
5. Dictionaries/mappings: Nested key-value pairs
6. Multi-line strings: Using `|` or `>`

```yaml
description: |
  This is a multi-line
  string with line breaks
```

#### Q3.5: Write a simple YAML configuration file for an analysis project.


A first draft could include project name, version, parameters, inputs, and outputs:

```yaml
project:
  name: my-analysis
  version: 1.0
  
parameters:
  threads: 8
  memory: 16GB
  
input_files:
  - data/sample1.txt
  - data/sample2.txt
  
output:
  directory: results/
  format: csv
```

---

### Section 4: Snakemake workflows

#### Q4.1: What is Snakemake and why is it useful for bioinformatics?

Answer:

Snakemake is a workflow management system that helps create reproducible and scalable data analysis pipelines. It provides:
- Reproducibility: Workflows are defined as code, ensuring analyses can be repeated
- Automation: Automatically determines which steps need to be run
- Parallelization: Runs independent tasks in parallel
- Dependency tracking: Only reruns necessary steps when input data changes
- Integration: Works seamlessly with Conda and Singularity for environment management
- Friendliness: It's friendly to Python developers.

#### Q4.2: What are the main components of a Snakemake rule?

Answer:

A Snakemake rule typically contains:
1. Rule name: Identifier for the rule
2. input: Files required for the rule to execute
3. output: Files that will be produced by the rule
4. shell (or script, run): Commands or code to execute
5. Optional components: `params`, `log`, `threads`, `resources`, `conda`, `singularity`

Example:

```python
rule process_data:
    input:
        data="data/sample.txt",
        config="config/params.yaml"
    output:
        "results/sample_processed.txt"
    shell:
        "python scripts/process.py {input.data} --config {input.config} -o {output}"
```

#### Q4.3: How do wildcards work in Snakemake?

Answer:
Wildcards allow you to process multiple files with the same rule. They act as placeholders that are replaced with actual values:

```python
rule process_data:
    input:
        data="data/{sample}.txt"
    output:
        "results/{sample}_processed.txt"
    shell:
        "python scripts/process.py {input.data} -o {output}"
```

The `{sample}` wildcard will be replaced with actual sample names. To process multiple samples:

```python
rule all:
    input:
        expand("results/{sample}_processed.txt", sample=["sample1", "sample2", "sample3"])
```

> [!TIP]
> Wildcards are also used for Omnibenchmark. Omnibenchmark largely reuses Snakemake for workflow management.


#### Q4.4: What commands would you use to test a Snakemake workflow without actually running it?

Answer:

```bash
# Dry run - shows what would be executed
snakemake -n

# Same, with a full argument
snakemake --dry-run

# Create a visual DAG (Directed Acyclic Graph of the workflow)
snakemake --dag | dot -Tpng > workflow.png

# Print the reason for each rule execution
snakemake -n -r
```

#### Q4.5: Write a simple Snakemake rule that processes text files and counts their number of lines with `wc`.

Answer:

```python
rule count_lines:
    input:
        "data/{sample}.txt"
    output:
        "results/{sample}_count.txt"
    shell:
        "wc -l {input} > {output}"
```

This rule uses `wc -l` to count lines in the input file and writes the count to an output file.

---

### Section 5: Conda package management

#### Q5.1: What is Conda and why is it important for data analysis?

Answer:

Conda is a package and environment management system that allows users to:
- Install software without root privileges
- Manage multiple versions of the same software
- Create isolated environments for different projects
- Automatically resolve and install dependencies
- Ensure reproducibility by documenting exact software versions

Conda is especially important for data analysis because:
- Provides access to thousands of scientific packages
- It simplifies installation of complex tools with many dependencies
- Environments can be shared as YAML files for reproducibility
- Works across different operating systems


> [!NOTE]  
> Conda modifies the PATH to point to the "right" software versions


#### Q5.2: What is the difference between `conda create` and `conda install`?

Answer:

- `conda create`: Creates a new conda environment with specified packages
  ```bash
  conda create -n myenv python=3.10 numpy
  ```
  This creates a new, isolated environment named "myenv" using python 3.10.

- `conda install`: Installs packages into an existing (usually activated) environment
  ```bash
  conda activate myenv
  conda install pandas
  ```
  This adds packages to the current environment.

#### Q5.3: How do you create a reproducible Ccnda environment using a YAML file?

Answer:
1. Create an `environment.yml` (YAML) file:

```yaml
name: myanalysis
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.10
  - numpy=1.24
  - pandas=2.0
  - snakemake-minimal=7.32
  - matplotlib=3.7
```

2. Create the environment from the file:

```bash
conda env create -f environment.yml
```


#### Q5.4: What are the main conda channels and what is their purpose?

Answer:
1. conda-forge: Community-maintained packages (Python libraries, scientific tools)
2. defaults: Official Anaconda repository (base packages). Caution read the EULA: you might have to pay.
3. bioconda: Bioinformatics-specific software (optional, for specialized tools)
4. nodefaults: To avoid defaults, mainly for $$ reasons.

Priority order matters: first has priority over last.

To add channels to an environment:

```bash
conda config --add channels nodefaults
conda config --add channels conda-forge
```

#### Q5.5: How does Conda modify the $PATH and why does this matter?

Answer:
When you activate a Conda environment, it prepends the environment's `bin/` directory to your `$PATH`. This ensures software from that environment is found first.

Example:
```bash
# Before activation
echo $PATH
# /usr/local/bin:/usr/bin:/bin

# Activate environment
conda activate myenv

# After activation - conda's bin directory is first
echo $PATH
# /home/user/conda/envs/myenv/bin:/usr/local/bin:/usr/bin:/bin

# Programs from the environment are now found
which python
# /home/user/conda/envs/myenv/bin/python
```


Why this matters:
- Each environment has isolated software
- No conflicts between different versions
- Software "lives" in the environment's directory
- Deactivating restores the original PATH

> [!WARNING]  
> This is extremely important. Always deactivate conda environments, and avoid nesting them.


#### Q5.6: How do you integrate conda environments with Snakemake?

Answer:
Specify a conda environment YAML file in your Snakemake rule:

```python
rule process_data:
    input:
        data="data/{sample}.txt"
    output:
        "results/{sample}_processed.txt"
    conda:
        "envs/processing.yaml"
    shell:
        "python scripts/process.py {input.data} -o {output}"
```

Run Snakemake with conda integration:

```bash
snakemake --use-conda --cores 8
```

Snakemake will automatically create and activate the specified environment for each rule.

---

### Section 6: Singularity containers

#### Q6.1: What is Singularity and how does it differ from Docker?

Answer:
Singularity is a container platform designed for HPC and scientific computing.

Key differences from Docker:
- No root daemon: Singularity doesn't require a background service with root privileges
- User permissions: Users inside the container are the same as outside (important for shared systems)
- HPC-friendly: Designed for multi-user HPC environments
- Compatibility: Can run Docker images directly
- Single file: Singularity containers are single `.sif` files (easier to distribute)

Why Singularity for scientific computing:
- Provides complete reproducibility (entire software stack)
- Works on HPC clusters without root access
- Is portable across different Linux systems

#### Q6.2: How do you pull and run a Docker image using Singularity?

Answer:

```bash
# Pull a Docker image and convert to Singularity format - just a version of python, not a full OS
singularity pull docker://python:3.10-slim

# This creates a file: python_3.10-slim.sif

# Execute a command in the container
singularity exec python_3.10-slim.sif python script.py

# Run interactively
singularity shell python_3.10-slim.sif

# Run the container's default command
singularity run python_3.10-slim.sif
```

#### Q6.3: What are the main sections of a Singularity definition file?

Answer:
A Singularity definition file (`.def`) contains several sections:

1. Bootstrap/From: Base image source
   ```
   Bootstrap: docker
   From: ubuntu:22.04
   ```

2. %post: Commands to run during build (install software)
   ```
   %post
       apt-get update
       apt-get install -y python3 python3-pip
       pip3 install numpy pandas
   ```

3. %environment: Environment variables
   ```
   %environment
       export PATH=/usr/local/bin:$PATH
   ```

4. %runscript: Default command when container is run
   ```
   %runscript
       exec python3 "$@"
   ```

5. %labels: Metadata
   ```
   %labels
       Author yourname@email.com
       Version v1.0
   ```

#### Q6.4: How do you integrate Singularity with Snakemake workflows?

Answer:

Add a `singularity:` directive to your Snakemake rule:

```python
rule process_data:
    input:
        data="data/{sample}.txt"
    output:
        "results/{sample}_processed.txt"
    singularity:
        "docker://python:3.10-slim"
    shell:
        "python scripts/process.py {input.data} -o {output}"
```

Run with Singularity enabled:

```bash
snakemake --use-singularity --cores 8
```

Snakemake will automatically pull the container (if needed) and execute the rule inside it.

> [!TIP]
> Indeed Singularity is now called Apptainer, but the Snakemake stanza is called `singularity:`


#### Q6.5: How does Singularity handle $PATH and software isolation?

Answer:
Singularity containers have their own isolated `$PATH` environment, completely separate from the host system.

Inside vs. outside container:
```bash
# On host system
which python
# /home/user/conda/envs/myenv/bin/python

# Inside Singularity container
singularity exec myimage.sif which python
# /usr/bin/python (container's python)

# Check PATH inside container
singularity exec myimage.sif echo $PATH
# /usr/local/bin:/usr/bin:/bin (container's PATH)
```

#### Q6.6: What are the advantages of using containers for benchmarking?

Answer:
1. Complete reproducibility: Exact same software environment across runs
2. Version control: Software versions are locked
3. Portability: Same container works on different systems - if they share their microarchitecture (see multiarch builds)
4. Isolation: No interference from system libraries
5. Consistency: Eliminates "works on my machine" problems
6. Documentation: Container definition serves as software documentation

This is especially important for benchmarking because:
- Ensures fair comparisons between methods
- Results are reproducible by others
- Eliminates confounding factors from different software versions
- Enables long-term reproducibility (containers can be archived)


> [!NOTE]  
> Containers built for Apple silicon won't work for amd64, and vice-versa

---

### Section 7: Omnibenchmark

#### Q7.1: How do you install Omnibenchmark + conda?

Answer:

If u sing Miniforge (conda with conda-forge and a minimal installation):

```bash
# Download and install Miniforge
wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh
bash Miniforge3-Linux-x86_64.sh -b -p $HOME/miniforge3

# Initialize
source ~/miniforge3/bin/activate

## download the Omnibenchmark conda recipe
curl -sSL https://raw.githubusercontent.com/omnibenchmark/omnibenchmark/main/omni-environment.yml -o omni-environment.yml

## inspect it
cat omni-environment.yml

## create a new environment named `omnibenchmark` 
conda create -n omnibenchmark python=3.12 -y
conda activate omnibenchmark
conda env update -f omni-environment.yml

## Check the CLI: it's named `ob`, simply
ob --version
```


#### Q7.2: What is Omnibenchmark and why is it useful?

#### Q7.3: What is the role of the benchmarking manifest file in Omnibenchmark?

#### Q7.4: How do Omnibenchmark-compatible software modules look like?

#### Q7.5: How does Omnibenchmark integrate Snakemake, Conda, and Singularity?

#### Q7.6: How do Omnibenchmark modules connect to each other?

#### Q7.7: Create a simple Omnibenchmark YAML manifest


---

## Extra hands-on exercises

### Exercise 1: Write a YAML Configuration File
Create a YAML file for an analysis project that includes:
1. Project metadata (name, version)
2. A list of input files
3. Parameters with default values
4. Output directory configuration

### Exercise 2: Set up a conda environment
1. Create a conda environment with Python 3.10, NumPy, and Pandas
2. Export the environment to a YAML file
3. Delete the environment and recreate it from the YAML file
4. Verify all packages are correctly installed

### Exercise 3: Work with Singularity
1. Pull a Python container from Docker Hub
2. Run a simple command (e.g., check version) inside the container
3. Mount a local directory and access files from within the container

---

## Additional resources

- YAML: https://yaml.org/spec/
- Snakemake: https://snakemake.readthedocs.io/en/stable/tutorial/tutorial.html
- Conda Cheat Sheet: https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html
- Apptainer: https://sylabs.io/guides/latest/user-guide/
- Omnibenchmark: https://docs.omnibenchmark.org/latest/

---

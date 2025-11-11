# Basics

This teaching unit provides an introduction to fundamental concepts in benchmarking in bioinformatics, specifically targeting Linux amd64 environments.

## Contents

### Slides 

To download the slides:
- The slides are automatically compiled via GitHub Actions on pull requests
- Compiled PDFs are available as artifacts in the Actions tab
- You can also trigger a manual build using the "Build LaTeX Slides" workflow

To compile them locally (using LaTeX):

```bash
cd slides
pdflatex basics.tex
pdflatex basics.tex  # Run twice for proper references
```

Requirements for local compilation:
- `pdflatex` (from TeX Live or similar LaTeX distribution)
- `beamer`
- `listings` (code highlighting)

### Exercises (`exercises/exercises.md`)

A structured Q&A document with 25+ questions covering all topics from the slides:

- Section 1: Text and executable Files (5 questions)
- Section 2: Snakemake Workflows (5 questions)
- Section 3: Conda package management (5 questions)
- Section 4: Singularity containers (5 questions)
- Section 5: Integration and best practices (5 questions)
- Practical exercises
- Additional resources and tips

## Prerequisites

- Basic Linux command line experience
- Access to a Linux system (preferably amd64 architecture)

## Duration

This unit is designed to be covered in approximately:
- 90-120 minutes for lecture (slides)
- 120-180 minutes for exercises and hands-on practice

## Additional Resources

- Snakemake: https://snakemake.readthedocs.io
- Conda: https://docs.conda.io
- Bioconda: https://bioconda.github.io
- Singularity: https://sylabs.io/docs
- BioContainers: https://biocontainers.pro

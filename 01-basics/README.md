# Basics

This teaching unit provides an introduction to fundamental concepts in benchmarking in bioinformatics, specifically targeting Linux amd64 environments.

## Contents

### Slides 

To download the slides:
- The slides are automatically compiled via GitHub Actions on pull requests
- PDF slides are available at https://github.com/imallona/principles_of_benchmarking_in_bioinformatics/releases

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

A structured Q&A document with 25+ questions covering all topics from the slides. Including extra hands-on exercises, and additional resources and tips

## Prerequisites

- Basic Linux command line experience
- Access to a Linux system (preferably amd64 architecture)

## Additional Resources

- Snakemake: https://snakemake.readthedocs.io
- Conda: https://docs.conda.io
- Bioconda: https://bioconda.github.io
- Singularity: https://sylabs.io/docs
- BioContainers: https://biocontainers.pro

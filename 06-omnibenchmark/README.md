# Omnibenchmark

This teaching unit provides an introduction to fundamental concepts in benchmarking in bioinformatics, specifically targeting Linux amd64 environments.

## Contents

### Slides 

To download the slides:
- The slides are automatically compiled via GitHub Actions on pull requests
- PDF slides are available at https://github.com/imallona/principles_of_benchmarking_in_bioinformatics/releases

To compile them locally (using LaTeX):

```bash
cd slides
pdflatex omnibenchmark.tex
pdflatex omnibenchmark.tex  # Run twice for proper references
```

Requirements for local compilation:
- `pdflatex` (from TeX Live or similar LaTeX distribution)
- `beamer`
- `listings` (code highlighting)

### Exercises (`exercises/exercises.md`)

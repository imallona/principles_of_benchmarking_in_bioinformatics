# Principles of benchmarking in bioinformatics

A hands-on training resource for students covering fundamental concepts and best practices in bioinformatics.

Disclaimer: we cover common benchmarking systems, including OpenEBench, Open Problems, and Omnibenchmark. But authors (Izaskun Mallona) are developers of Omnibenchmark.


## Repository Structure

```
.
├── 01-basics/                   # First teaching unit
│   ├── slides/                  # LaTeX beamer presentations
│   │   └── basics.tex
│   ├── exercises/               # Q&A exercises
│   │   └── exercises.md
│   └── README.md               # Unit documentation
└── .github/workflows/           # CI/CD automation
    └── build-slides.yml         # Automatic slide compilation
```

## Units

### 01-basics

Introduction to fundamental bioinformatics concepts:

- Text and executable files
- The PATH
- Workflow management
- Software management: conda and apptainer

See [basics/README.md](1-basics/README.md) for details.

## Getting the slides

### Option 1: download the PDFs

Compiled PDF slides are automatically built via GitHub Actions and available as artifacts:
1. Go to the [Actions tab](../../actions)
2. Click on the latest "Build LaTeX Slides" workflow run
3. Download the `basics-slides-pdf` artifact

### Option 2: pdflatex locally

Requires LaTeX (pdflatex):

```bash
cd 01-basics/slides
pdflatex basics.tex
pdflatex basics.tex  # Run twice for proper references
```

## CI/CD

This repository uses GitHub Actions to automatically:
- Compile LaTeX slides on pull requests
- Make PDFs available as downloadable artifacts

## Contributing

This is an active project not yet open for contributions.

## Contact

izaskun.mallona at gmail.com

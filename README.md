# Principles of benchmarking in bioinformatics

A hands-on training resource covering concepts and practices in benchmarking in bioinformatics. Includes exercises and slides, structured in independent but perhaps sequential learning units.

Bias disclaimer: authors are developers of Omnibenchmark (since inception, 2019) and GNU/Linux advocates (since 2002).

## TL/DR

For Denmark 17-18 Nov 2025: [exercises](99-course-instances/2025-11-copenhagen/README.md).

## Repository structure

Self-contained units (from `01-basics` to `06-omnibenchmark`) plus workshop instances of various lengths (`99-course-instances`).

```
.
├── 01-basics/                   # First teaching unit
│   ├── slides/                  # LaTeX beamer presentations
│   │   └── basics.tex
│   ├── exercises/               # Q&A exercises
│   │   └── exercises.md
│   └── README.md                # Unit documentation
├── 02-benchmark-design
│   └── TO-DO.md
├── 03-ethical-benchmarking
│   └── TO-DO.md
├── 04-openproblems
│   └── TO-DO.md
├── 05-openebench
│   └── TO-DO.md
├── 06-omnibenchmark             # Omnibenchmark and clustbench
│   ├── exercises
│   │   ├── exercises.md
├── 99-course-instances          # Workshops 
│   └── 2025-11-copenhagen       # 2-day workshop in Copenhagen, DK
│       └── README.md
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

See [01-basics/README.md](01-basics/README.md) for details.

Compiled PDF slides are automatically built via GitHub Actions and available as artifacts:
1. Go to the [Actions tab](../../actions)
2. Click on the latest "Build LaTeX Slides" workflow run
3. Download the `basics-slides-pdf` artifact

Slides can be built using LaTeX (pdflatex):

```bash
cd 01-basics/slides
pdflatex basics.tex
pdflatex basics.tex  # Run twice for proper references
```

### 06-omnibenchmark

Solo benchmarking with omnibenchmark.

See [06-omnibenchmark/README.md](06-omnibenchmark/README.md) for details.

Compiled PDF slides are automatically built via GitHub Actions and available as artifacts:
1. Go to the [Actions tab](../../actions)
2. Click on the latest "Build LaTeX Slides" workflow run
3. Download the `omnibenchmark-slides-pdf` artifact

Slides can be built using LaTeX (pdflatex):

```bash
cd 06-omnibenchmark/slides
pdflatex omnibenchmark.tex
pdflatex omnibenchmark.tex  # Run twice for proper references
```


## CI/CD

This repository uses GitHub Actions to automatically:
- Compile LaTeX slides on pull requests
- Make PDFs available as downloadable artifacts

## License

CC-BY-NC-ND Izaskun Mallona (that is, reusable with Attribution-NonCommercial-NoDerivatives 4.0 International)

## Contributing

This is an active project not yet open for contributions.

## Contact

izaskun.mallona at gmail.com

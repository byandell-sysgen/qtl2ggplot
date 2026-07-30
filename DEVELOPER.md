# Developer Guide for qtl2ggplot

Welcome to the developer guide for **qtl2ggplot** (v1.2.8), an R package providing `ggplot2`-based visualization tools for quantitative trait loci (QTL) experiments and genome scans within the [`R/qtl2`](https://kbroman.org/qtl2/) ecosystem.

This document outlines the package architecture, data models, developer setup, coding conventions, S3 plotting methods, quality assurance protocols, and release workflows for maintainers and contributors.

---

## 1. Ecosystem & Package Architecture

`qtl2ggplot` serves as the primary modern graphics engine for high-dimensional QTL analysis, replacing or extending base R graphics from `qtl2plot` with `ggplot2` components.

### Package Ecosystem

| Package / Repository | Role & Details |
| :--- | :--- |
| [`qtl2`](https://kbroman.org/qtl2/) | Core statistical engine for genome scans, kinship calculation, and genotype probability mapping |
| [`qtl2ggplot`](ReadMe.md) | Modern `ggplot2` visualizations for genome scans, coefficients, SNP associations, and gene annotations |
| [`qtl2pattern`](https://github.com/byandell-sysgen/qtl2pattern) | Allele pattern support and contrast analysis for `R/qtl2` |
| [`qtl2shiny`](https://github.com/byandell-sysgen/qtl2shiny) | Interactive Shiny web application integrating `qtl2ggplot` visualizations |

### Directory Structure

```
qtl2ggplot/
├── R/               # R source functions (ggplot_scan1, ggplot_coef, S3 autoplot methods)
├── src/             # C++ source code compiled via Rcpp for fast matrix processing
├── inst/            # Package assets and supplementary data
├── man/             # Roxygen2 generated Rd documentation files
├── tests/           # Unit test suite using testthat (test-arrange_genes.R, test-map_to_pos.R)
├── vignettes/       # RMarkdown vignettes (qtl2ggplot.Rmd, devel_guide/)
├── DESCRIPTION      # Package metadata, versioning, and dependencies
└── NAMESPACE        # Exported functions and registered S3 methods
```

---

## 2. Developer Environment & Setup

### R Runtime & Dependencies

- **R Version**: `>= 3.1.0` (R `>= 4.2.0` recommended)
- **Roxygen2 Version**: `>= 7.3.3`
- **Core Dependencies**: Listed in [DESCRIPTION](DESCRIPTION) (`ggplot2`, `qtl2`, `ggrepel`, `dplyr`, `purrr`, `tidyr`, `rlang`, `assertthat`, `RColorBrewer`, `Rcpp`).

### Local Development Setup

To set up your local development session:

```r
# 1. Install development utilities if needed
install.packages(c("devtools", "roxygen2", "testthat", "knitr", "rmarkdown"))

# 2. Load the package interactively into your active session
devtools::load_all()

# 3. Regenerate documentation headers and NAMESPACE
devtools::document()
```

---

## 3. Data Models & S3 Visual Architecture

### S3 Autoplot Methods

`qtl2ggplot` integrates seamlessly with `ggplot2` by providing S3 `autoplot()` methods for core `qtl2` data structures:

| Class | Function / S3 Method | Description |
| :--- | :--- | :--- |
| `scan1` | `autoplot.scan1()` / `ggplot_scan1()` | Genome-wide LOD score profile curves across chromosomes |
| `scan1coef` | `autoplot.scan1coef()` / `ggplot_coef()` | Founder allele effect curves and QTL coefficient curves |
| `listof_scan1coef` | `autoplot.listof_scan1coef()` / `ggplot_listof_scan1coef()` | Multi-model or multi-region coefficient comparisons |
| `genes` | `autoplot.genes()` / `ggplot_genes()` | Genomic gene exon track visualization |

### Specialized Plotting Functions

- **`ggplot_coef_and_lod()`**: Aligns founder allele effect curves above LOD score tracks into dual-panel figures.
- **`ggplot_snpasso()`**: Manhattan-style plots for high-density SNP association scans.
- **`ggplot_snpasso_and_genes()`**: Combined visualization aligning SNP association peaks directly above gene locus maps.
- **`ggplot_pxg()`**: Phenotype-by-genotype distributions showing individual observations grouped by founder genotype or pattern.
- **`ggplot_onegeno()`**: Genotype probability profile along chromosomes for a single individual.
- **`ggplot_peaks()`**: Summary plot displaying significant LOD peaks across multiple traits and chromosomes.

---

## 4. R Language & Coding Conventions

When contributing code to `qtl2ggplot`, strictly adhere to these standards:

### Vector Subsetting & Line Filtering Safety

When filtering lines or strings in R (e.g. parsing comments, headers, or annotations), **NEVER** invert `grep()` directly (e.g. `!grep(...)`). Inverting integer vectors evaluates `!2` as `FALSE`, wiping out entire vectors into `character(0)`.

```r
# INCORRECT - Risk of vector wipeout to character(0)
# clean_lines <- lines[!grep("^\\s*#'", lines)]

# CORRECT - Safe boolean indexing using grepl
clean_lines <- lines[!grepl("^\\s*#'", lines)]

# ALTERNATIVE - Safe index inversion with grep
clean_lines <- lines[grep("^\\s*#'", lines, invert = TRUE)]
```

### Explicit Package Namespacing

Always use explicit package prefixes (`pkg::func()`) in exported functions and internal helper modules to prevent namespace collisions across loaded packages.

```r
# INCORRECT - Implicit namespace lookup
plot_scan <- function(scan_data) {
  ggplot(scan_data, aes(x = pos, y = lod)) + geom_line()
}

# CORRECT - Explicit package namespacing
plot_scan <- function(scan_data) {
  ggplot2::ggplot(scan_data, ggplot2::aes(x = pos, y = lod)) + 
    ggplot2::geom_line()
}
```

### Roxygen Comment Stripping for WebAssembly (WASM)

When inlining R code blocks into WebAssembly or Shinylive blocks (`{shinylive-r}`), roxygen lines starting with `^#'` must be stripped to prevent Pandoc JSON string serialization errors.

```r
# Utility function to strip roxygen comments for inline WASM execution
strip_roxygen_for_wasm <- function(code_lines) {
  code_lines[!grepl("^\\s*#'", code_lines)]
}
```

### Vectorized Operations Over Loops

Prefer vectorized transformations (`ifelse()`, `lapply()`, `purrr::map()`) over explicit `for` loops when manipulating genomic position data frames or genotype probability matrices.

---

## 5. Documentation & Technical Writing Standards

### Single H1 Title Constraint

Every markdown (`.md`), R Markdown (`.Rmd`), or Quarto (`.qmd`) document in this repository **MUST contain exactly one `# Title` tag at the top of the file**, followed by structured `##` and `###` heading levels.

### Relative Path Hygiene

Always use valid relative file paths when linking to repository assets:
- Root files: `[README.md](ReadMe.md)` or `[DESCRIPTION](DESCRIPTION)`
- Relative navigation: `[Org Developer Guide](../github_org/DEVELOPER.md)`

### GitHub Pages & Jekyll Security (`.nojekyll`)

When rendering pkgdown articles or Quarto reports for deployment to GitHub Pages (`gh-pages`), ensure an empty `.nojekyll` file exists in the deployment directory:

```bash
touch docs/.nojekyll
```

---

## 6. Verification & Quality Assurance

Before submitting pull requests or merging changes in `qtl2ggplot`:

1. **Run R Package Checks**:

   ```r
   devtools::check()
   ```

2. **Execute Unit Tests**:

   ```r
   devtools::test()
   ```

3. **Synchronize Roxygen Documentation**:

   ```r
   devtools::document()
   ```

4. **Empirical Verification**: Perform concrete local test runs of `ggplot_scan1()`, `ggplot_coef()`, and `ggplot_snpasso()` with sample `qtl2` datasets (`sample_data`) to verify visual layout clean execution.

---

## 7. Version Control Governance

- **No Automatic Git Operations**: Maintainers and AI assistants **must never** execute `git commit` or `git push` automatically. All staging, committing, and pushing must be performed manually.
- **Commit Prefixes**: Use informative commit message conventions:
  - `feat:` New plotting capability or S3 autoplot method.
  - `fix:` Bug fix in coordinate alignment or plot layout.
  - `docs:` Documentation update or vignette clarification.
  - `refactor:` Code simplification without behavioral changes.
  - `test:` Additional unit tests for gene or SNP parsing.

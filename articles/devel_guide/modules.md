# Function Index & S3 Class System Breakdown

## Function Index & S3 Class System Breakdown

`qtl2ggplot` organizes its source functions across 24 R source files and
C++ routines in `R/` and `src/`. Functions are grouped into five core
functional categories:

------------------------------------------------------------------------

### 1. S3 Autoplot Methods & Dispatch

`qtl2ggplot` integrates with
[`ggplot2::autoplot()`](https://ggplot2.tidyverse.org/reference/autoplot.html)
to provide unified plotting interfaces for `qtl2` data structures:

| Method | S3 Target Class | Source File | Description |
|:---|:---|:---|:---|
| [`autoplot.scan1()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_scan1.md) | `scan1` | [`R/ggplot_scan1.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_scan1.R) | Dispatch method for genome-wide LOD score profile curves |
| [`autoplot.scan1coef()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_coef.md) | `scan1coef` | [`R/ggplot_coef.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_coef.R) | Dispatch method for founder allele effect curves along chromosomes |
| [`autoplot.listof_scan1coef()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_listof_scan1coef.md) | `listof_scan1coef` | [`R/ggplot_listof_scan1coef.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_listof_scan1coef.R) | Dispatch method for multi-model or multi-region coefficient comparisons |
| [`autoplot.genes()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_genes.md) | `genes` | [`R/ggplot_genes.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_genes.R) | Dispatch method for gene exon track visualization |

------------------------------------------------------------------------

### 2. Specialized Plot Generators & Multi-Panel Layouts

| Function | Primary Role | Source File |
|:---|:---|:---|
| [`ggplot_scan1()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_scan1.md) | Plots LOD score profiles across single or multiple phenotypes | [`R/ggplot_scan1.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_scan1.R) |
| [`ggplot_coef()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_coef.md) / [`ggplot_coefCC()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_coef.md) | Plots QTL effect curves, founder allele estimates, and BLUPs | [`R/ggplot_coef.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_coef.R) |
| `ggplot_coef_and_lod()` | Combines coefficient curves and LOD scores into a dual-panel figure | [`R/ggplot_coef_and_lod.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_coef_and_lod.R) |
| [`ggplot_snpasso()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_snpasso.md) | Manhattan-style plot of high-density SNP association results | [`R/ggplot_snpasso.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_snpasso.R) |
| [`ggplot_genes()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_genes.md) | Renders gene locus structures and exon/intron tracks | [`R/ggplot_genes.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_genes.R) |
| `ggplot_snpasso_and_genes()` | Aligns SNP association peaks directly above gene locus maps | [`R/ggplot_snpasso_and_genes.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_snpasso_and_genes.R) |
| [`ggplot_pxg()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_pxg.md) / [`mean_pxg()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_pxg.md) | Phenotype-by-genotype scatter plots and cell mean summaries | [`R/ggplot_pxg.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_pxg.R) |
| [`ggplot_onegeno()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_onegeno.md) | Visualizes chromosome genotype probabilities for a single subject | [`R/ggplot_onegeno.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_onegeno.R) |
| [`ggplot_peaks()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_peaks.md) | Overview plot summarizing LOD peak locations across chromosomes | [`R/ggplot_peaks.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/ggplot_peaks.R) |

------------------------------------------------------------------------

### 3. Summary & Object Manipulation Methods

| Function / Method | S3 Class | Description |
|:---|:---|:---|
| [`summary.scan1()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/summary_scan1.md) / [`summary_scan1()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/summary_scan1.md) | `scan1` | Extracts peak LOD locations, positions, and chromosome tags |
| [`summary.scan1coef()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/listof_scan1coef.md) / [`summary_scan1coef()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/listof_scan1coef.md) | `scan1coef` | Summarizes maximum allele effect positions and contrast estimates |
| [`summary.listof_scan1coef()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/listof_scan1coef.md) | `listof_scan1coef` | Summarizes peak locations across list of coefficient objects |
| [`subset.listof_scan1coef()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/listof_scan1coef.md) / `[.listof_scan1coef` | `listof_scan1coef` | Subsets list-of-scan1coef objects by chromosome or model index |

------------------------------------------------------------------------

### 4. Map Alignment & SNP Utility Helpers

- [`align_scan1_map()`](https://rdrr.io/pkg/qtl2/man/qtl2-internal.html):
  Aligns physical (Mbp) or genetic (cM) maps with `scan1` LOD matrices
  ([`R/align_scan1_map.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/align_scan1_map.R)).
- `color_patterns()`: Manages color palettes for strain patterns and
  allele contrasts
  ([`R/color_patterns.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/color_patterns.R)).
- [`sdp_to_pattern()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/sdp_to_pattern.md):
  Converts Strain Distribution Patterns (SDP) into strain pattern
  strings
  ([`R/sdp_to_pattern.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/sdp_to_pattern.R)).
- `snpinfo_to_map()` / `snpinfo_to_haplos()`: Extracts genetic maps and
  haplotype structures from SNP info objects
  ([`R/snpinfo_to_map.R`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/R/snpinfo_to_map.R)).

------------------------------------------------------------------------

### 5. High-Performance C++ Extensions

- `arrange_genes()`: C++ function compiled via `Rcpp`
  ([`src/arrange_genes.cpp`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/src/arrange_genes.cpp))
  that computes vertical layout rows for gene locus tracks, ensuring
  gene names and exon blocks never overlap visually.

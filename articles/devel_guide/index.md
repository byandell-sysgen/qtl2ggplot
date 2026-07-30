# qtl2ggplot Developer Guide Overview & Architecture

## qtl2ggplot Developer Guide Overview & Architecture

### Package Purpose & Ecosystem

**qtl2ggplot** provides `ggplot2`-based visualization tools for
quantitative trait loci (QTL) experiments and genome scans within the
[`R/qtl2`](https://kbroman.org/qtl2/) ecosystem. It modernizes and
extends graphics routines from `qtl2plot`, offering flexible
customization, faceting, and interactive integration.

- **Authors:** Brian S. Yandell (<brian.yandell@wisc.edu>), Karl W.
  Broman (<kbroman@biostat.wisc.edu>)
- **License:** GPL-3
- **Minimum R Version:** ≥ 3.1.0 (≥ 4.2.0 recommended)

Ecosystem relationships:

- **`qtl2`**: Core statistical engine for genome scans, kinship
  matrices, and genotype probability mapping.
- **`qtl2ggplot`**: Modern `ggplot2` visualization layer for genome
  scans, coefficient tracks, SNP associations, and gene locus
  annotations.
- **`qtl2pattern`**: Allele pattern support, top SNP filtering, and
  contrast calculations for `R/qtl2`.
- **`qtl2shiny`**: Interactive Shiny application leveraging `qtl2ggplot`
  for interactive data exploration.

------------------------------------------------------------------------

### High-Level Architecture & Visual Data Flow

`qtl2ggplot` maps statistical output objects from `qtl2` into structured
`ggplot2` graphics layers:

``` mermaid
flowchart TD
    scan1Obj["qtl2 scan1 Object (LOD scores & map)"]
    scan1coefObj["qtl2 scan1coef Object (founder allele effects)"]
    snpInfoObj["snpinfo Data Frame (SNPs & positions)"]
    genesObj["genes Data Frame (gene annotations & exons)"]

    autoplotScan1["autoplot.scan1() / ggplot_scan1()"]
    autoplotCoef["autoplot.scan1coef() / ggplot_coef()"]
    ggplotSnpasso["ggplot_snpasso()"]
    autoplotGenes["autoplot.genes() / ggplot_genes()"]

    ggplotCoefLod["ggplot_coef_and_lod() (Dual Panel)"]
    ggplotSnpGenes["ggplot_snpasso_and_genes() (Stacked Track)"]
    ggplotPxG["ggplot_pxg() (Phenotype by Genotype)"]

    ggplotOut["ggplot2 Output Object"]

    scan1Obj --> autoplotScan1
    scan1coefObj --> autoplotCoef
    snpInfoObj --> ggplotSnpasso
    genesObj --> autoplotGenes

    autoplotScan1 --> ggplotCoefLod
    autoplotCoef --> ggplotCoefLod
    ggplotSnpasso --> ggplotSnpGenes
    autoplotGenes --> ggplotSnpGenes

    autoplotScan1 --> ggplotOut
    autoplotCoef --> ggplotOut
    ggplotCoefLod --> ggplotOut
    ggplotSnpGenes --> ggplotOut
    ggplotPxG --> ggplotOut

    classDef input fill:#1f77b4,stroke:#333,stroke-width:2px,color:#fff
    classDef s3 fill:#ff7f0e,stroke:#333,stroke-width:2px,color:#fff
    classDef combo fill:#2ca02c,stroke:#333,stroke-width:2px,color:#fff
    classDef output fill:#d62728,stroke:#333,stroke-width:2px,color:#fff

    class scan1Obj,scan1coefObj,snpInfoObj,genesObj input
    class autoplotScan1,autoplotCoef,ggplotSnpasso,autoplotGenes s3
    class ggplotCoefLod,ggplotSnpGenes,ggplotPxG combo
    class ggplotOut output
```

------------------------------------------------------------------------

### Developer Quick Start

#### Local Development Workflow

``` r

# 1. Load local package sources dynamically
devtools::load_all()

# 2. Re-generate documentation & NAMESPACE
devtools::document()

# 3. Run R package checks
devtools::check(vignettes = FALSE)
```

------------------------------------------------------------------------

### Navigating the Guide

Explore the sub-guides for detailed breakdowns of exported S3 generics,
plotting functions, and underlying data transformations:

- **[Function Index & S3 Class System
  Breakdown](https://byandell-sysgen.github.io/qtl2ggplot/articles/devel_guide/modules.md)**:
  Comprehensive list of exported functions, S3 methods, helper routines,
  and C++ extensions.
- **[Data Pipeline & Plotting
  Architecture](https://byandell-sysgen.github.io/qtl2ggplot/articles/devel_guide/data_flow.md)**:
  Mathematical and structural workflow detailing alignment of
  physical/genetic maps, SNP association expansion, gene track layout,
  and dual-panel composition.

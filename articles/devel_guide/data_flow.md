# Data Pipeline & Plotting Architecture

## Data Pipeline & Plotting Architecture

`qtl2ggplot` translates matrix outputs, position maps, and annotation
tables from `qtl2` into structured `ggplot2` layers. This document
details the underlying data transformations, position mapping, and
multi-panel composition pipelines.

------------------------------------------------------------------------

### Data Pipeline Flowchart

``` mermaid
flowchart TD
    scan1Data["scan1 Object (Matrix: positions x phenotypes)"]
    mapData["Map Object (Physical Mbp or Genetic cM)"]
    snpData["snpinfo Table (SNP IDs, SDP, positions)"]
    geneData["genes Table (Name, start, stop, strand)"]

    alignMap["align_scan1_map() (Rescaling & Intersecting)"]
    expandSnp["expand_snp_results() (Mapping Top SNPs)"]
    cppArrange["arrange_genes() [C++] (Vertical Row Layout)"]

    tidyScan["Tidy Data Frame (chr, pos, lod, pheno)"]
    tidySnp["Tidy SNP Table (chr, pos, lod, sdp, pattern)"]
    tidyGene["Tidy Gene Table (gene, start, stop, row)"]

    geomLine["ggplot2::geom_line() (LOD Profiles)"]
    geomPoint["ggplot2::geom_point() / ggrepel (SNP Association)"]
    geomRect["ggplot2::geom_rect() / geom_text (Gene Tracks)"]

    finalPlot["ggplot2 Object"]

    scan1Data --> alignMap
    mapData --> alignMap
    alignMap --> tidyScan

    snpData --> expandSnp
    expandSnp --> tidySnp

    geneData --> cppArrange
    cppArrange --> tidyGene

    tidyScan --> geomLine
    tidySnp --> geomPoint
    tidyGene --> geomRect

    geomLine --> finalPlot
    geomPoint --> finalPlot
    geomRect --> finalPlot

    classDef input fill:#1f77b4,stroke:#333,stroke-width:2px,color:#fff
    classDef transform fill:#ff7f0e,stroke:#333,stroke-width:2px,color:#fff
    classDef tidy fill:#2ca02c,stroke:#333,stroke-width:2px,color:#fff
    classDef geom fill:#d62728,stroke:#333,stroke-width:2px,color:#fff

    class scan1Data,mapData,snpData,geneData input
    class alignMap,expandSnp,cppArrange transform
    class tidyScan,tidySnp,tidyGene tidy
    class geomLine,geomPoint,geomRect,finalPlot geom
```

------------------------------------------------------------------------

### 1. Map Alignment & Positional Rescaling

`qtl2` genome scans output numeric matrices where rows correspond to
pseudomarkers/markers and columns correspond to phenotypes.
[`align_scan1_map()`](https://rdrr.io/pkg/qtl2/man/qtl2-internal.html)
extracts positional coordinates from the associated map list:

1.  **Map Harmonization**: Verifies matching marker names between
    `scan1` row attributes and map vectors.
2.  **Chromosome Boundaries**: Computes cumulative genomic offsets for
    multi-chromosome genome-wide scans.
3.  **Tidy Reshaping**: Converts internal matrices into long data frames
    containing `chr`, `pos`, `lod`, and `pheno` columns suitable for
    `ggplot2::aes(x = pos, y = lod, color = pheno)`.

------------------------------------------------------------------------

### 2. Founder Allele Effect & Coefficient Scaling

When plotting founder allele coefficients (`scan1coef`), `qtl2ggplot`
handles 8 Collaborative Cross (CC) founder lines (`AJ`, `B6`, `129`,
`NOD`, `NZO`, `CAST`, `PWK`, `WSB`):

- **Color Mapping**: Uses Okabe-Ito CC color palettes from `CCcolors`.
- **Baseline Centering**: Adjusts coefficient curves to reflect relative
  strain contributions or intercept-sum constraints.
- **BLUP Processing**: Supports Best Linear Unbiased Predictor (BLUP)
  matrix structures from
  [`qtl2::scan1blup()`](https://rdrr.io/pkg/qtl2/man/scan1blup.html).

------------------------------------------------------------------------

### 3. High-Density SNP Association & Gene Track Layout

#### SNP Expansion

[`ggplot_snpasso()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/ggplot_snpasso.md)
processes top SNP association matrices from
[`qtl2::top_snps()`](https://rdrr.io/pkg/qtl2/man/top_snps.html): -
Converts Strain Distribution Patterns (SDP) into strain pattern strings
via
[`sdp_to_pattern()`](https://byandell-sysgen.github.io/qtl2ggplot/reference/sdp_to_pattern.md). -
Maps SNP LOD scores against physical genomic coordinates (Mbp). -
Highlights top associate variants and applies repelling text labels
using
[`ggrepel::geom_text_repel()`](https://ggrepel.slowkow.com/reference/geom_text_repel.html).

#### Non-Overlapping Gene Layout C++ Algorithm

When plotting gene annotations in region scans, multiple genes often
overlap within identical base pair windows. `qtl2ggplot` delegates
vertical row assignment to a fast C++ routine `arrange_genes()`
([`src/arrange_genes.cpp`](https://github.com/byandell-sysgen/qtl2ggplot/blob/main/src/arrange_genes.cpp)):

``` cpp
// C++ algorithm snippet for vertical gene track assignment
IntegerVector arrange_genes(const NumericVector& start, const NumericVector& end) {
    int n = start.size();
    IntegerVector row(n);
    // Greedily assigns non-overlapping vertical track rows
    ...
    return row;
}
```

This guarantees clean visual separation of gene exons
([`geom_rect()`](https://ggplot2.tidyverse.org/reference/geom_tile.html))
and gene symbol labels
([`geom_text()`](https://ggplot2.tidyverse.org/reference/geom_text.html))
without label collisions.

------------------------------------------------------------------------

### 4. Multi-Panel Composite Figures

`qtl2ggplot` provides compound layout generators that stack related
genomic tracks:

- **`ggplot_coef_and_lod()`**: Stacks founder coefficient curves
  directly above LOD score profiles, sharing identical X-axis genomic
  coordinate limits (`ggplot2::coord_cartesian(xlim = ...)`).
- **`ggplot_snpasso_and_genes()`**: Aligns SNP association Manhattan
  plots above gene track layouts, allowing immediate identification of
  candidate genes harboring peak SNPs.

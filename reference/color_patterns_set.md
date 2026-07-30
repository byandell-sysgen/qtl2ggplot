# Set up colors for patterns or points

Set up colors for patterns or points

## Usage

``` r
color_patterns_set(
  scan1output,
  snpinfo,
  patterns,
  col,
  pattern,
  show_all_snps,
  col_hilit,
  drop_hilit,
  maxlod
)
```

## Arguments

- scan1output:

  output of linear mixed model for `phename` (see
  [`scan1`](https://rdrr.io/pkg/qtl2/man/scan1.html))

- snpinfo:

  Data frame with snp information

- patterns:

  Connect SDP patterns: one of `c("none","all","hilit")`.

- col:

  Color of other points, or colors for patterns

- pattern:

  allele pattern as of form `AB:CDEFGH`

- show_all_snps:

  show all SNPs if `TRUE`

- col_hilit:

  Color of highlighted points

- drop_hilit:

  SNPs with LOD score within this amount of the maximum SNP association
  will be highlighted.

- maxlod:

  Maximum LOD for drop of `drop_hilit`

## Value

list of `col` and `pattern`.

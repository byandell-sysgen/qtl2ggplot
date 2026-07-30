# Set up col, pattern, shape and group for plotting.

Set up col, pattern, shape and group for plotting.

## Usage

``` r
color_patterns_pheno(
  scan1ggdata,
  lod,
  pattern,
  col,
  shape,
  patterns,
  facet = NULL
)
```

## Arguments

- scan1ggdata:

  data frame to be used for plotting

- lod:

  matrix of LOD scores by position and pheno

- pattern:

  allele pattern of form `AB:CDEFGH`

- col:

  Color for `color` column in `scan1ggdata`

- shape:

  Shape for `shape` column in `scan1ggdata`

- patterns:

  Connect SDP patterns: one of `c("none","all","hilit")`

- facet:

  use
  [`facet_wrap`](https://ggplot2.tidyverse.org/reference/facet_wrap.html)
  if not `NULL`

## Value

data frame `scan1ggdata` with additional objects.

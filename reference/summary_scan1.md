# Summary of scan1 object

Summary of scan1 object

## Usage

``` r
summary_scan1(
  object,
  map,
  snpinfo = NULL,
  lodcolumn = seq_len(ncol(object)),
  chr = names(map),
  sum_type = c("common", "best"),
  drop = 1.5,
  show_all_snps = TRUE,
  ...
)

# S3 method for class 'scan1'
summary(object, ...)
```

## Arguments

- object:

  object from [`scan1`](https://rdrr.io/pkg/qtl2/man/scan1.html)

- map:

  A list of vectors of marker positions, as produced by
  [`insert_pseudomarkers`](https://rdrr.io/pkg/qtl2/man/insert_pseudomarkers.html).

- snpinfo:

  Data frame with SNP information with the following columns (the last
  three are generally derived from with
  [`index_snps`](https://rdrr.io/pkg/qtl2/man/index_snps.html)):

  - `chr` - Character string or factor with chromosome

  - `pos` - Position (in same units as in the `"map"` attribute in
    `genoprobs`.

  - `sdp` - Strain distribution pattern: an integer, between 1 and
    \\2^n - 2\\ where \\n\\ is the number of strains, whose binary
    encoding indicates the founder genotypes

  - `snp` - Character string with SNP identifier (if missing, the
    rownames are used).

  - `index` - Indices that indicate equivalent groups of SNPs.

  - `intervals` - Indexes that indicate which marker intervals the SNPs
    reside.

  - `on_map` - Indicate whether SNP coincides with a marker in the
    `genoprobs`

- lodcolumn:

  one or more lod columns

- chr:

  one or more chromosome IDs

- sum_type:

  type of summary

- drop:

  LOD drop from maximum

- show_all_snps:

  show all SNPs if `TRUE`

- ...:

  other arguments not used

## Value

tbl summary

## Author

Brian S Yandell, <brian.yandell@wisc.edu>

## Examples

``` r
# read data
iron <- qtl2::read_cross2(system.file("extdata", "iron.zip", package="qtl2"))
# insert pseudomarkers into map
map <- qtl2::insert_pseudomarkers(iron$gmap, step=1)

# calculate genotype probabilities
probs <- qtl2::calc_genoprob(iron, map, error_prob=0.002)

# grab phenotypes and covariates; ensure that covariates have names attribute
pheno <- iron$pheno
covar <- match(iron$covar$sex, c("f", "m")) # make numeric
names(covar) <- rownames(iron$covar)
Xcovar <- qtl2::get_x_covar(iron)

# perform genome scan
out <- qtl2::scan1(probs, pheno, addcovar=covar, Xcovar=Xcovar)

# summary
summary(out, map)
#> # A tibble: 40 × 5
#>    pheno  chr     pos marker       lod
#>    <chr>  <fct> <dbl> <chr>      <dbl>
#>  1 liver  1      90.3 c1.loc90   2.53 
#>  2 spleen 1      27.3 D1Mit18    0.949
#>  3 liver  2      56.8 D2Mit17    4.86 
#>  4 spleen 2      55.3 c2.loc55   1.83 
#>  5 liver  3      25.1 D3Mit22    1.08 
#>  6 spleen 3      42.1 c3.loc42   0.526
#>  7 liver  4      10.9 D4Mit2     2.74 
#>  8 spleen 4      53.6 D4Mit352   1.71 
#>  9 liver  5      55.5 c5.loc55.5 0.790
#> 10 spleen 5      62.3 D5Mit30    1.99 
#> # ℹ 30 more rows
```

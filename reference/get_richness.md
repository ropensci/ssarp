# Create a species richness dataframe for a given occurrence record dataframe

Use a dataframe output by
[`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md)
to determine how many species occur on each island by creating a species
richness dataframe.

## Usage

``` r
get_richness(occs)
```

## Arguments

- occs:

  The dataframe output by
  [`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md),
  or if using a custom dataframe, ensure that it has the following named
  columns:

  - "areas" containing the areas associated with the land masses of
    interest

  - "specificEpithet" containing the names of the species living on
    those islands

## Value

A dataframe with two columns: the first containing island areas and the
second containing the associated species richness (number of unique
species)

## Details

The output of this function can be used directly with [the sars R
package](https://txm676.github.io/sars/articles/sars-r-package.html) to
fit additional SAR models that `ssarp` does not create itself.

## Examples

``` r
# The GBIF key for the Anolis genus is 8782549
# Read in example dataset filtered from:
#  dat <- rgbif::occ_search(taxonKey = 8782549,
#                           hasCoordinate = TRUE,
#                           limit = 10000)
dat <- read.csv(system.file("extdata",
                            "ssarp_Example_Dat.csv",
                            package = "ssarp"))
land <- find_land(occurrences = dat)
areas <- find_areas(occs = land)
#> ℹ Recording island names...
#> ℹ Assembling island dictionary...
#> ℹ Adding areas to final dataframe...
richness <- get_richness(occs = areas)
```

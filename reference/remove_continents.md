# Remove continents from area dataframe.

Reference a list of continental areas to remove them from the dataframe
output by
[`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md).

## Usage

``` r
remove_continents(occs)
```

## Arguments

- occs:

  The dataframe that is returned by
  [`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md).
  I do not recommend using a custom dataframe for this function because
  it references areas given by the area database used in
  [`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md).
  If you must use a custom dataframe, please ensure that the landmass
  areas are in a column called "areas"

## Value

A dataframe of the species name, island name, and island area (without
continents)

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
occs <- find_land(occurrences = dat)
areas <- find_areas(occs = occs)
#> ℹ Recording island names...
#> ℹ Assembling island dictionary...
#> ℹ Adding areas to final dataframe...
new_areas <- remove_continents(areas)
```

# Create a presence-absence dataframe for a given occurrence record dataframe

Use a dataframe output by
[`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md)
to determine which species occur on specific islands by creating a
presence-absence dataframe. A 1 represents presence and a 0 represents
absence.

## Usage

``` r
get_presence_absence(occs)
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

  - "first" containing locality information. In the ssarp workflow, this
    column contains the country name

  - "second" containing locality information. In the ssarp workflow,
    this column contains a province or island name

  - "third" containing locality information. In the ssarp workflow, this
    column contains the island name if the 7th column does not contain
    the island name

## Value

A dataframe with a row for each island in the given occurrence record
dataframe and a column for each species. Within each species column, a 1
represents the presence of that species on the island corresponding to
the given row, and a 0 represents the absence of that species on the
island corresponding to the given row.

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
pres_abs <- get_presence_absence(areas)
```

# Gather sources from GBIF data for citation

When using data obtained via
[`rgbif::occ_search()`](https://docs.ropensci.org/rgbif/reference/occ_search.html)
and filtered with
[`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md)
for a publication, you must keep a record of the datasets used in your
analysis. This function assists in creating the dataframe necessary to
follow GBIF's citation guidelines (see References).

## Usage

``` r
get_sources(occs)
```

## Arguments

- occs:

  The occurrence record dataframe returned by
  [`rgbif::occ_search()`](https://docs.ropensci.org/rgbif/reference/occ_search.html)
  or
  [`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md).

## Value

A dataframe of dataset keys and the number of occurrence records
associated with each key that were gathered with
[`rgbif::occ_search()`](https://docs.ropensci.org/rgbif/reference/occ_search.html)
and/or filtered with
[`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md).

## References

- [GBIF citation guidelines](https://www.gbif.org/citation-guidelines)

- Data obtained via
  [`rgbif::occ_search()`](https://docs.ropensci.org/rgbif/reference/occ_search.html)
  and filtered with
  [`ssarp::find_areas()`](https://docs.ropensci.org/ssarp/reference/find_areas.md)
  falls under [the derived datasets
  distinction](https://www.gbif.org/derived-dataset/about)

- [More information about creating derived
  datasets](https://data-blog.gbif.org/post/derived-datasets/)

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
source_df <- get_sources(occs = dat)
```

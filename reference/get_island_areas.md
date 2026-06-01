# Access properly encoded island_area object

In order to address an R CMD check warning about non-ASCII characters in
the island_area object, these characters in the island names had to be
converted to an ASCII format. The non-ASCII accents in the island names
are important for the functionality of the ssarp package, so this
function provides the user with a dataframe including the original,
un-converted island names.

## Usage

``` r
get_island_areas()
```

## Value

An edited version of the
[`ssarp::island_areas`](https://docs.ropensci.org/ssarp/reference/island_areas.md)
object, which is a dataframe including the names, areas, and maximum
elevations of islands from across the globe.

## Examples

``` r
island_df <- get_island_areas()
```

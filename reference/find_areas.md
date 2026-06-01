# Find areas of land masses.

Find the areas of the land masses relevant to the taxon of interest with
two options: a database of island names and areas, or a user-provided
shapefile.

## Usage

``` r
find_areas(occs, area_custom = NULL, shapefile = NULL, names = NULL)
```

## Arguments

- occs:

  The dataframe that is returned by
  [`ssarp::find_land()`](https://docs.ropensci.org/ssarp/reference/find_land.md).
  If using a custom occurrence record dataframe, ensure that it has the
  following columns: "genericName", "specificEpithet",
  "decimalLongitude", "decimalLatitude", "first", "second", "third",
  "datasetKey". The "datasetKey" column is important for GBIF records
  and identifies the dataset to which the occurrence record belongs.
  Custom dataframes without this style of data organization should fill
  the column with placeholder values.

- area_custom:

  A dataframe including names of land masses and their associated areas.
  This dataframe should be provided when the user would like to bypass
  using the built-in database of island names and areas. Please ensure
  that the custom dataframe includes the land mass's area in a column
  called "AREA" and the name in a column called "Name". (Optional)

- shapefile:

  A shapefile (.shp) containing spatial information for the geographic
  locations of interest. (Optional)

- names:

  If the user would like to restrict which polygons in the shapefile are
  included in the returned occurrence record dataframe, they can be
  specified here as a vector. If the user does not provide a vector, all
  of the non-NA names in the shapefile will be included (as found in
  shapefile\$name). (Optional)

## Value

A dataframe of the species name, island name, and island area

## Details

The first method is to reference a built-in dataset of island names and
areas to find the areas of the landmasses relevant to the taxon of
interest. The user may also decide to input their own custom dataframe
including names of relevant land masses and their associated areas to
bypass using *ssarp*'s built-in dataset.

The second method is to reference a user-supplied shapefile containing
spatial information for the landmasses of interest in order to determine
their areas.

While the word "landmasses" was used heavily in this documentation,
users supplying their own custom area dataframe or shapefile are
encouraged to use this function in the *ssarp* workflow to create
species- and speciation- area relationships for island-like systems such
as lakes, fragmented habitat, and mountain peaks.

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
```

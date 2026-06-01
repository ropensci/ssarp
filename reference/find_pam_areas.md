# Find areas of islands using a presence-absence matrix (PAM)

Use a presence-absense matrix (PAM) to create a dataframe that can be
used to generate a species-area relationship (SAR) or speciation-area
relationship (SpAR) in the `ssarp` pipeline.

## Usage

``` r
find_pam_areas(pam, area_custom = NULL)
```

## Arguments

- pam:

  A presence-absence matrix (PAM), saved as a dataframe. Please ensure
  that the PAM has species names (include both generic name and specific
  epithet, with an underscore separating them) as the column names, with
  the exception of the first column that designates locations, which
  must be named "Island".

- area_custom:

  A dataframe including names of land masses and their associated areas.
  This dataframe should be provided when the user would like to bypass
  using the built-in database of island names and areas. Please ensure
  that the custom dataframe includes the land mass's area in a column
  called "AREA" and the name in a column called "Name". (Optional)

## Value

A dataframe of species names, island names, and island areas

## Details

PAMs summarize the occurrence of species across different geographic
locations. The column names of a PAM are species names, with the
exception of the first column, which specifies the name of locations.
For each cell corresponding to a species/location pair, either a 1
(presence) or a 0 (absence) is input depending on whether the species
can be found at that location or not.

Using a PAM, this function will find the areas of the land masses
relevant to the taxon of interest with two options: a built-in database
of island names and areas, or a user-provided list of island names and
areas.

The default method is to reference a built-in dataset of island names
and areas to find the areas of the landmasses relevant to the taxon of
interest. The user may also decide to input their own custom dataframe
including names of relevant land masses and their associated areas to
bypass using `ssarp`'s built-in dataset.

While the word "landmasses" was used heavily in this documentation,
users supplying their own custom area dataframe or shapefile are
encouraged to use this function in the `ssarp` workflow to create
species- and speciation- area relationships for island-like systems such
as lakes, fragmented habitat, and mountain peaks.

## Examples

``` r
pam <- read.csv(system.file("extdata",
                            "example_pam.csv",
                            package = "ssarp"))
areas <- find_pam_areas(pam = pam)
#> ℹ Recording island names...
#> ℹ Assembling island dictionary...
#> ℹ Adding areas to final dataframe...
```

# Get speciation rates following equation 4 in Magallón and Sanderson (2001)

Use methodology from Magallón and Sanderson (2001) to estimate
speciation rates using a user-provided phylogeny and output a dataframe
for use in ssarp's speciation-area relationship pipeline. This method
also removes any species rows without rates (this is most likely to
occur when the tree does not have all of the species included in the
occurrence record dataframe)

## Usage

``` r
estimate_ms(tree, label_type = "binomial", occurrences)

estimate_MS(tree, label_type = "binomial", occurrences)
```

## Arguments

- tree:

  The dated phylogenetic tree that corresponds with the taxa to be
  included in a speciation-area relationship

- label_type:

  Either "epithet" or "binomial" (default): describes the type of tip
  label in the provided tree. If "epithet," only the species epithet
  will be used when interacting with the tree. If "binomial," the full
  species name (including genus) will be used when interacting with the
  tree.

- occurrences:

  The occurrence record dataframe output from the ssarp pipeline. If you
  would like to use a custom dataframe, please make sure that there are
  columns titled "specificEpithet", "genericName", and "areas"

## Value

A dataframe that includes speciation rates for each island in the
user-provided occurrence record dataframe.

## References

- Magallón, S. & Sanderson, M.J. (2001). Absolute Diversification Rates
  in Angiosperm Clades. Evolution, 55(9): 1762-1780.

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

# Read tree from Patton et al. (2021), trimmed to Caribbean species
tree <- ape::read.tree(system.file("extdata",
                                   "Patton_Anolis_Trimmed.tree",
                                   package = "ssarp"))

occ_speciation <- estimate_ms(tree = tree,
                              label_type = "epithet",
                              occurrences = areas)
```

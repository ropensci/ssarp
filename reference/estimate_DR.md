# Get speciation rates using the DR statistic (Jetz et al. 2012)

DR stands for “diversification rate,” but it is ultimately a better
estimation of speciation rate than net diversification (Belmaker and
Jetz 2015; Quintero and Jetz 2018) and returns results similar to BAMM’s
tip speciation rate estimations (Title and Rabosky 2019).

## Usage

``` r
estimate_dr(tree, label_type = "binomial", occurrences)

estimate_DR(tree, label_type = "binomial", occurrences)
```

## Arguments

- tree:

  The dated phylogenetic tree that corresponds with the taxa to be
  included in a speciation-area relationship

- label_type:

  Either "epithet" or "binomial" (default): describes the type of tip
  label in the provided tree. If "epithet," only the species epithet
  will be used to match speciation rates to tips in the returned
  occurrence dataframe. If "binomial," the full species name (including
  genus) will be used to match speciation rates to tips in the returned
  occurrence dataframe.

- occurrences:

  The occurrence record dataframe output from the ssarp pipeline. If you
  would like to use a custom dataframe, please make sure that there are
  columns titled "genericName" and "specificEpithet"

## Value

A dataframe that includes speciation rates for each species in the
occurrence record dataframe

## Details

This function uses methodology from Sun and Folk (2020) to calculate the
DR statistic for each tip of a given tree and output a dataframe for use
in ssarp's speciation-area relationship pipeline. This method also
removes any species rows without rates (this is most likely to occur
when the tree does not have all of the species included in the
occurrence record dataframe). The tree read in the examples below is
modified from Patton et al. (2021).

## References

- Belmaker, J., & Jetz, W. (2015). Relative roles of ecological and
  energetic constraints, diversification rates and region history on
  global species richness gradients. Ecology Letters, 18: 563–571.

- Jetz, W., Thomas, G.H, Joy, J.B., Harmann, K., & Mooers, A.O. (2012).
  The global diversity of birds in space and time. Nature, 491: 444-448.

- Patton, A.H., Harmon, L.J., del Rosario Castañeda, M., Frank, H.K.,
  Donihue, C.M., Herrel, A., & Losos, J.B. (2021). When adaptive
  radiations collide: Different evolutionary trajectories between and
  within island and mainland lizard clades. PNAS, 118(42): e2024451118.

- Quintero, I., & Jetz, W. (2018). Global elevational diversity and
  diversification of birds. Nature, 555, 246–250.

- Sun, M. & Folk, R.A. (2020). Cactusolo/rosid_NCOMMS-19-37964-T: Code
  and data for rosid_NCOMMS-19-37964 (Version V.1.0). Zenodo.
  http://doi.org/10.5281/zenodo.3843441

- Title P.O. & Rabosky D.L. (2019). Tip rates, phylogenies and
  diversification: What are we estimating, and how good are the
  estimates? Methods in Ecology and Evolution. 10: 821–834.

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

occ_speciation <- estimate_dr(tree = tree,
                              label_type = "epithet",
                              occurrences = areas)
```

# Get tip speciation rates from BAMM (Rabosky 2014) analysis

Use the BAMMtools package (Rabosky et al. 2014) to extract tip
speciation rates from user-supplied BAMM analysis objects.

## Usage

``` r
estimate_bamm(label_type = "binomial", occurrences, edata)

estimate_BAMM(label_type = "binomial", occurrences, edata)
```

## Arguments

- label_type:

  Either "epithet" or "binomial" (default): describes the type of tip
  label in the tree used for the BAMM analysis. If "epithet," only the
  species epithet will be used to match speciation rates to tips in the
  returned occurrence dataframe. If "binomial," the full species name
  (including genus) will be used to match speciation rates to tips in
  the returned occurrence dataframe.

- occurrences:

  The occurrence record dataframe output from the ssarp pipeline. If you
  would like to use a custom dataframe, please make sure that there are
  columns titled "genericName" and "specificEpithet"

- edata:

  The eventdata object created by using the
  [`BAMMtools::getEventData()`](https://rdrr.io/pkg/BAMMtools/man/getEventData.html)
  function

## Value

A dataframe that includes speciation rates for each species in the
occurrence record dataframe

## References

- Rabosky, D.L. (2014). Automatic Detection of Key Innovations, Rate
  Shifts, and Diversity-Dependence on Phylogenetic Trees. PLOS ONE,
  9(2): e89543.

- Rabosky, D.L., Grundler, M., Anderson, C., Title, P., Shi, J.J.,
  Brown, J.W., Huang, H., & Larson, J.G. (2014), BAMMtools: an R package
  for the analysis of evolutionary dynamics on phylogenetic trees.
  Methods in Ecology and Evolution, 5: 701-707.

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

# Event data file from an external BAMM run
event_data <- system.file("extdata",
                          "event_data_Patton_Anolis.txt",
                           package = "ssarp")

edata <- BAMMtools::getEventData(phy = tree, eventdata = event_data)
#> Reading event datafile:  /home/runner/work/_temp/Library/ssarp/extdata/event_data_Patton_Anolis.txt 
#>      ...........
#> Read a total of 1000 samples from posterior
#> 
#> Discarded as burnin: GENERATIONS <  0
#> Analyzing  1000  samples from posterior
#> 
#> Setting recursive sequence on tree...
#> 
#> Done with recursive sequence
#> 

occ_speciation <- estimate_bamm(label_type = "epithet",
                                 occurrences = areas ,
                                 edata = edata)
```

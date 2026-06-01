# Construct a Speciation-Area Relationship with ssarp

## Introduction

A speciation-area relationship (SpAR) plots speciation rates against the
area of the island on which the associated species live. This vignette,
which covers how to create a SpAR using the *ssarp* package, uses
knowledge and data generated from [the vignette describing the creation
of species-area
relationships](https://kmartinet.github.io/ssarp/articles/Create_SAR.html).
Code relevant to generating the necessary data for this SpAR example
from the species-area relationship (SAR) vignette will be included here,
but the reader is encouraged to read [the SAR
vignette](https://kmartinet.github.io/ssarp/articles/Create_SAR.html)
for additional details.

In this vignette, we will create a SpAR for the lizard genus *Anolis*,
as a continuation of the SAR vignette. We will focus on using the
[`ssarp::estimate_ms()`](https://docs.ropensci.org/ssarp/reference/estimate_MS.md)
function to estimate per-island speciation rates using the lambda
calculation for crown groups from Magallón and Sanderson (2001) for the
main example. Additional short examples using
[`ssarp::estimate_dr()`](https://docs.ropensci.org/ssarp/reference/estimate_DR.md)
and
[`ssarp::estimate_bamm()`](https://docs.ropensci.org/ssarp/reference/estimate_BAMM.md)
for speciation rate estimations are included after the main example.

## Generate Occurrence Data

First, we will generate the “nocont_dat” object from the SAR vignette,
which includes occurrence records and their associated island names and
areas for *Anolis* lizards that live on Caribbean islands.

``` r

library(rgbif)
library(ssarp)
library(ape)

# Get the GBIF key for the Anolis genus
query <- "Anolis"
rank <- "Genus"

suggestions <- name_suggest(q = query, rank = rank)

key <- as.numeric(suggestions$data[1,1])

# Get data for Anolis from GBIF from islands in the Caribbean
limit <- 10000
occurrences <- occ_search(taxonKey = key, 
                          hasCoordinate = TRUE, 
                          limit = limit,
                          geometry = 'POLYGON((-84.8 23.9, -84.7 16.4, -65.2 13.9, -63.1 11.0, -56.9 15.5, -60.5 21.9, -79.3 27.8, -79.8 24.8, -84.8 23.9))')

dat <- occurrences$data

# Find land mass names
land_dat <- find_land(occurrences = dat)

# Use the land mass names to get their areas
area_dat <- find_areas(occs = land_dat)

# Remove continents from the filtered occurrence record dataset
nocont_dat <- remove_continents(occs = area_dat)
```

## Estimate Speciation Rates

The “nocont_dat” object created above can be used with a phylogenetic
tree to create a SpAR. This step in the *ssarp* workflow enables the
user to determine whether the breakpoint in the SAR corresponds with a
threshold for island size at which in situ speciation occurs (see Losos
and Schluter 2000).

The phylogenetic tree for *Anolis* that we will use in this example is a
trimmed version of the tree used by Patton et al. (2021). This trimmed
tree only includes anoles found on islands in the Caribbean. In order to
read the tree, we must use the *ape* R package.

``` r


tree <- read.tree(system.file("extdata", 
                              "Patton_Anolis_trimmed.tree", 
                              package = "ssarp"))
```

![Phylogenetic tree from Patton et al. (2021) trimmed to include only
anoles found on islands in the Caribbean.](anolis_tree.png)

Phylogenetic tree from Patton et al. (2021) trimmed to include only
anoles found on islands in the Caribbean.

Now that we have a phylogenetic tree, we can estimate tip speciation
rates for use in our speciation-area relationship. *ssarp* includes
three methods for estimating tip speciation rates: BAMM (Rabosky 2014),
the lambda calculation for crown groups from Magallón and Sanderson
(2001), and DR (Jetz et al. 2012).

The
[`ssarp::estimate_bamm()`](https://docs.ropensci.org/ssarp/reference/estimate_BAMM.md)
function requires a `bammdata` object as input, which must be created
using the `BAMMtools` package (Rabosky et al. 2014) after the user
completes a BAMM analysis. This object includes tip speciation rates by
default in the “meanTipLambda” list element, which `ssarp` accesses to
add the appropriate tip speciation rates for each species to the
occurrence record dataframe.

DR stands for “diversification rate,” but it is ultimately a better
estimation of speciation rate than net diversification (Belmaker and
Jetz 2015; Quintero and Jetz 2018) and returns results similar to BAMM’s
tip speciation rate estimations (Title and Rabosky 2019). The
[`ssarp::estimate_dr()`](https://docs.ropensci.org/ssarp/reference/estimate_DR.md)
function returns the values obtained from running an adapted version of
the “DR_statistic” function from Sun and Folk (2020).

In addition to tip speciation rates, `ssarp` includes a function for
calculating the speciation rate for a clade from Magallón and Sanderson
(2001). The
[`ssarp::estimate_ms()`](https://docs.ropensci.org/ssarp/reference/estimate_MS.md)
function uses the
[`ape::subtrees`](https://rdrr.io/pkg/ape/man/subtrees.html) function
(Paradis and Schliep 2019) to generate all possible subtrees from the
user-provided phylogenetic tree that corresponds with the taxa of
interest for the SpAR. Then, species in the provided occurrence records
generated from previous steps in the `ssarp` workflow are grouped by
island. For each group of species that comprise an island, the number of
subtrees that represent that group of species and the root age of each
subtree is recorded, along with the name and area of the island. The
speciation rate for each subtree is then calculated following Equation 4
in Magallón and Sanderson (2001). If an island includes multiple
subtrees, the island speciation rate is the average of the calculated
speciation rates. This average is calculated when the SpAR is plotted.

In this example, we will use the lambda calculation for crown groups
from Magallón and Sanderson (2001) through the
[`ssarp::estimate_ms()`](https://docs.ropensci.org/ssarp/reference/estimate_MS.md)
function. The “label_type” parameter in this function tells *ssarp*
whether the tip labels on the given tree include the full species name
(binomial) or just the specific epithet (epithet).

``` r

# Calculate tip speciation rates using the lambda calculation for crown groups from Magallón and Sanderson (2001)
speciation_occurrences <- estimate_ms(tree = tree, label_type = "epithet", occurrences = nocont_dat)
```

The “speciation_occurrences” object is a dataframe containing island
areas with their corresponding speciation rate as estimated by the
[`ssarp::estimate_ms()`](https://docs.ropensci.org/ssarp/reference/estimate_MS.md)
function.

## Create Speciation-Area Relationship

Next, we will use the “speciation_occurrences” object with the
[`ssarp::create_spar()`](https://docs.ropensci.org/ssarp/reference/create_SpAR.md)
function to create a SpAR (can also be run with
[`create_SpAR()`](https://docs.ropensci.org/ssarp/reference/create_SpAR.md)).
Just like the
[`ssarp::create_sar()`](https://docs.ropensci.org/ssarp/reference/create_SAR.md)
function, the
[`ssarp::create_spar()`](https://docs.ropensci.org/ssarp/reference/create_SpAR.md)
function creates multiple regression objects with breakpoints up to the
user-specified “npsi” parameter. For example, if “npsi” is two,
[`ssarp::create_spar()`](https://docs.ropensci.org/ssarp/reference/create_SpAR.md)
will generate regression objects with zero (linear regression), one, and
two breakpoints. The function will then return the regression object
with the lowest AIC score. The “npsi” parameter will be set to one in
this example. Note that if linear regression (zero breakpoints) is
better-supported than segmented regression with one breakpoint, the
linear regression will be returned instead. We set the “visualize”
parameter to `TRUE` so that the function outputs the plot of the SAR.

``` r

create_spar(occurrences = speciation_occurrences, npsi = 1, visualize = TRUE)
```

    ## 
    ##  ***Regression Model with Segmented Relationship(s)***
    ## 
    ## Call: 
    ## segmented.lm(obj = linear, seg.Z = ~x, npsi = 1, control = segmented::seg.control(display = FALSE))
    ## 
    ## Estimated Break-Point(s):
    ##           Est. St.Err
    ## psi1.x 19.924  1.036
    ## 
    ## Coefficients of the linear terms:
    ##               Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)  1.364e-12  2.233e-02   0.000        1
    ## x           -8.298e-14  1.227e-03   0.000        1
    ## U1.x         5.712e-03  2.150e-03   2.656       NA
    ## 
    ## Residual standard error: 0.01069 on 32 degrees of freedom
    ## Multiple R-Squared: 0.3968,  Adjusted R-squared: 0.3403 
    ## 
    ## Boot restarting based on 6 samples. Last fit:
    ## Convergence attained in 2 iterations (rel. change 2.5337e-14)

![This is the SpAR for Anolis including island-based occurrences within
a polygon around Caribbean islands from the first 10000 records for the
genus in GBIF! The best-fit model was a segmented regression with one
breakpoint.](anolis_SpAR-1.png)

This is the SpAR for *Anolis* including island-based occurrences within
a polygon around Caribbean islands from the first 10000 records for the
genus in GBIF! The best-fit model was a segmented regression with one
breakpoint.

You will notice that two of the largest islands have a speciation rate
of zero in this example. This very likely occurred because the
calculation for speciation rate in Magallón and Sanderson (2001) that
[`ssarp::estimate_ms()`](https://docs.ropensci.org/ssarp/reference/estimate_MS.md)
uses is based on monophyly, which can be disrupted on islands with
non-native species occurrence records. When using the
[`ssarp::estimate_ms()`](https://docs.ropensci.org/ssarp/reference/estimate_MS.md)
function to estimate speciation rates for a SpAR, it is incredibly
important to manually filter the returned occurrence records to remove
non-native species.

The
[`ssarp::create_spar()`](https://docs.ropensci.org/ssarp/reference/create_SpAR.md)
function will also output the summary for the best-fit model for the
data (displayed above). This summary includes a few important pieces of
information to highlight. First, the `Est` column of `psi1.x` displays
the location of the breakpoint on the x-axis. Next, the table of
`Coefficients of the linear terms` includes estimates (located in the
`Estimate` column) for the following terms: the y-intercept
(`(Intercept)`), the slope before the breakpoint (`x`), and the slope
after the breakpoint (`U1.x`). In this example, to three signifcant
figures, we would report that the breakpoint is at 19.9, the slope
before the breakpoint is -8.30e-14, and the slope after the breakpoint
is 5.71e-03.

## Estimating Speciation Rates with BAMM and DR

While the above example focused on using the
[`ssarp::estimate_ms()`](https://docs.ropensci.org/ssarp/reference/estimate_MS.md)
function for estimating per-island speciation rates, *ssarp* also
provides functionality for BAMM (Rabosky 2014) and DR (Jetz et
al. 2012). These methods are described in detail in the “Estimate
Speciation Rates” section above, so this section will focus on providing
clear examples for their use in inferring SpARs.

### Estimating Speciation Rates with BAMM

The
[`ssarp::estimate_bamm()`](https://docs.ropensci.org/ssarp/reference/estimate_BAMM.md)
function allows users to integrate their `eventdata` results from a
`BAMM` run into *ssarp*’s SpAR workflow. More information about
`BAMM`(Bayesian Analysis of Macroevolutionary Mixtures) [can be found on
their documentation website](http://bamm-project.org/). The output from
[`BAMMtools::getEventData()`](https://rdrr.io/pkg/BAMMtools/man/getEventData.html)
is a required parameter of
[`ssarp::estimate_bamm()`](https://docs.ropensci.org/ssarp/reference/estimate_BAMM.md).
The quickstart guide for `BAMMtools` [can also be found on their
documentation website](http://bamm-project.org/postprocess.md).

The steps for using
[`ssarp::estimate_bamm()`](https://docs.ropensci.org/ssarp/reference/estimate_BAMM.md)
as part of the SpAR workflow are as follows:

- Run a `BAMM` analysis using the phylogeny of interest (this requires
  the `BAMM` application, which is external to R)
- Read in the event data file from the `BAMM` analysis into R using the
  `BAMMtools` R package
- Run
  [`ssarp::estimate_bamm()`](https://docs.ropensci.org/ssarp/reference/estimate_BAMM.md)
  with your occurrence record dataset and eventdata object

In this short example, we will use the `nocont_dat` object created in
the main example above as the occurrence record input.

``` r

# Read in event data file from an external BAMM run
event_data <- system.file("extdata",
                          "event_data_Patton_Anolis.txt",
                           package = "ssarp")

# Run getEventData to import event data
edata <- BAMMtools::getEventData(phy = tree, eventdata = event_data)

# Run estimate_bamm to associate tip speciation rates with occurrence records
speciation_occurrences <- estimate_bamm(label_type = "epithet", 
                                        occurrences = nocont_dat,
                                        edata = edata)
                                        
# Infer SpAR
create_spar(occurrences = speciation_occurrences, npsi = 1, visualize = FALSE)
```

    ## 
    ##  ***Regression Model with Segmented Relationship(s)***
    ## 
    ## Call: 
    ## segmented.lm(obj = linear, seg.Z = ~x, npsi = 1, control = segmented::seg.control(display = FALSE))
    ## 
    ## Estimated Break-Point(s):
    ##           Est. St.Err
    ## psi1.x 25.048  0.215
    ## 
    ## Coefficients of the linear terms:
    ##               Estimate Std. Error t value Pr(>|t|)
    ## (Intercept) -3.3941891  0.0124859 -271.842   <2e-16 ***
    ## x           -0.0007567  0.0006448   -1.174    0.256  
    ## U1.x         0.0465122  0.0264435    1.759       NA 
    ## ---
    ## Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    ## 
    ## Residual standard error: 0.007177 on 18 degrees of freedom
    ## Multiple R-Squared: 0.2821,  Adjusted R-squared: 0.1625 
    ## 
    ## Boot restarting based on 6 samples. Last fit:
    ## Convergence attained in 3 iterations (rel. change 8.3049e-09)

Note how different the breakpoint and slopes are when `BAMM` tip rates
are used to infer the SpAR compared to the first example using
methodology from Magallón and Sanderson (2001): 25.05 here versus 19.92
previously for the breakpoint, -7.6x10e-4 versus -8.30e-14 previously
for the slope before the breakpoint, and 0.047 versus 5.712e-03
previously for the slope after the breakpoint. The difference between
how tip speciation rates are estimated in `BAMM` and how crown group
speciation rates are calculated using Equation 4 from Magallón and
Sanderson (2001) results in vastly different estimates for the
regression parameters.

### Estimating Speciation Rates with DR

The
[`ssarp::estimate_dr()`](https://docs.ropensci.org/ssarp/reference/estimate_DR.md)
function returns tip speciation rate values obtained from running an
adapted version of the “DR_statistic” function from Sun and Folk (2020).
This function only requires a phylogenetic tree and occurrence records
to estimate tip speciation rates. In this short example, we will use the
`nocont_dat` and `tree` objects created during the first example in this
vignette.

``` r

# Run estimate_dr to associate tip speciation rates with occurrence records
speciation_occurrences <- estimate_dr(tree = tree,
                                      label_type = "epithet",
                                      occurrences = nocont_dat)

# Infer SpAR
create_spar(occurrences = speciation_occurrences, npsi = 1, visualize = FALSE)
```

    ## Call: 
    ## stats::lm(formula = y ~ x, data = dat)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -0.6146 -0.2271  0.1438  0.2055  0.2298 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) -2.635986   0.382664  -6.889 1.08e-06 ***
    ## x           -0.005784   0.019150  -0.302    0.766    
    ## ---
    ## Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    ## 
    ## Residual standard error: 0.2656 on 20 degrees of freedom
    ## Multiple R-squared:  0.004541,   Adjusted R-squared:  -0.04523 
    ## F-statistic: 0.09124 on 1 and 20 DF,  p-value: 0.7657

Note that when using DR to estimate tip speciation rates, the best-fit
model for the SpAR is a linear regression. This result is entirely
different compared to when
[`ssarp::estimate_ms()`](https://docs.ropensci.org/ssarp/reference/estimate_MS.md)
and
[`ssarp::estimate_bamm()`](https://docs.ropensci.org/ssarp/reference/estimate_BAMM.md)
are used, which highlights the importance of considering which method
for estimating speciation rates most aligns with the evolutionary
history of your system.

### Literature Cited

- Belmaker, J., & Jetz, W. (2015). Relative roles of ecological and
  energetic constraints, diversification rates and region history on
  global species richness gradients. Ecology Letters, 18: 563–571.
- Jetz, W., Thomas, G.H., Joy, J.B., Hartmann, K., & Mooers, A.O.
  (2012). The global diversity of birds in space and time. Nature, 491:
  444-448.
- Losos, J.B. & Schluter, D. (2000). Analysis of an evolutionary
  species-area relationship. Nature, 408: 847-850.
- Magallón, S. & Sanderson, M.J. (2001). Absolute Diversification Rates
  in Angiosperm Clades. Evolution, 55(9): 1762-1780.
- Paradis, E. & Schliep, K. (2019). ape 5.0: an environment for modern
  phylogenetics and evolutionary analyses in R. Bioinformatics, 35:
  526-528.
- Patton, A.H., Harmon, L.J., del Rosario Castañeda, M., Frank, H.K.,
  Donihue, C.M., Herrel, A., & Losos, J.B. (2021). When adaptive
  radiations collide: Different evolutionary trajectories between and
  within island and mainland lizard clades. PNAS, 118(42): e2024451118.
- Quintero, I., & Jetz, W. (2018). Global elevational diversity and
  diversification of birds. Nature, 555, 246–250.
- Rabosky, D.L. (2014). Automatic Detection of Key Innovations, Rate
  Shifts, and Diversity-Dependence on Phylogenetic Trees. PLOS ONE,
  9(2): e89543.
- Rabosky, D.L., Grundler, M., Anderson, C., Title, P., Shi, J.J.,
  Brown, J.W., Huang, H., & Larson, J.G. (2014), BAMMtools: an R package
  for the analysis of evolutionary dynamics on phylogenetic trees.
  Methods in Ecology and Evolution, 5: 701-707.
- Sun, M. & Folk, R.A. (2020). Cactusolo/rosid_NCOMMS-19-37964-T: Code
  and data for rosid_NCOMMS-19-37964 (Version V.1.0). Zenodo.
  <http://doi.org/10.5281/zenodo.3843441>
- Title P.O. & Rabosky D.L. (2019). Tip rates, phylogenies and
  diversification: What are we estimating, and how good are the
  estimates? Methods in Ecology and Evolution. 10: 821–834.

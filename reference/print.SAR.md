# Print species-area relationship summary

Function for printing the summary of species-area relationship objects
from the
[`ssarp::create_SAR()`](https://docs.ropensci.org/ssarp/reference/create_SAR.md)
function

## Usage

``` r
# S3 method for class 'SAR'
print(x, printlen = NULL, ...)
```

## Arguments

- x:

  The SAR object of interest

- printlen:

  Should always be NULL

- ...:

  Parameters to pass to print()

## Value

The summary of your species-area relationship

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
seg <- create_SAR(areas, npsi = 0)
print(seg)
#> 
#> Call:
#> stats::lm(formula = y ~ x, data = dat)
#> 
#> Residuals:
#>      Min       1Q   Median       3Q      Max 
#> -1.16312 -0.50377 -0.06522  0.48846  1.16484 
#> 
#> Coefficients:
#>             Estimate Std. Error t value Pr(>|t|)    
#> (Intercept) -4.58604    1.07072  -4.283 0.000239 ***
#> x            0.27512    0.05372   5.122 2.72e-05 ***
#> ---
#> Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
#> 
#> Residual standard error: 0.6664 on 25 degrees of freedom
#> Multiple R-squared:  0.512,  Adjusted R-squared:  0.4925 
#> F-statistic: 26.23 on 1 and 25 DF,  p-value: 2.721e-05
#> 
```

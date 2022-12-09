
<!-- README.md is generated from README.Rmd. Please edit that file -->

# snapKrig

<!-- badges: start -->
<!-- badges: end -->

snapKrig is a computationally lean implementation of a 2-dimensional
spatial correlation model for gridded data. By restricting to models
with (separable) Kronecker covariance, the package speeds computations
on certain likelihood and kriging problems (orders of magnitude
difference) compared to alternatives like `gstat`, `fields`, `geoR`,
`spatial`, and `LatticeKrig`.

## Installation

You can install the development version of snapKrig from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("deankoch/snapKrig")
```

A CRAN release is expected soon.

## Example

Define an empty grid to get started

``` r
library(snapKrig)

# simulate data on a rectangular grid
g_empty = sk(c(100, 200))
g_empty
#> 100 x 200 empty
```

Generate some random auto-correlated data for this grid

``` r
# simulate data on a square grid and plot
pars = sk_pars(g_empty)
g_sim = sk_sim(g_empty, pars)
plot(g_sim, main='snapKrig simulation')
```

<img src="man/figures/README-intro_sim-1.png" width="75%" style="display: block; margin: auto;" />

``` r
g_sim
#> 100 x 200 complete
```

Downscale and filter noise by simple kriging

``` r
g_down = sk_rescale(g_sim, down=10)
g_pred = sk_cmean(g_down, pars, X=0)
#> 100 x 200 complete sub-grid detected
plot(g_pred, main='snapKrig prediction at 10X resolution')
```

<img src="man/figures/README-intro_down-1.png" width="75%" style="display: block; margin: auto;" />

``` r
summary(g_pred)
#> complete sk grid
#> 1973081 points
#> range [-1.97, 2.41]
#> ..............................
#> dimensions : 991 x 1991
#> resolution : 0.1 x 0.1
#>     extent : [0, 99] x [0, 199]
```

# Technical Features

snapKrig’s computational efficiency makes it useful in situations where
interpolation or down-scaling would pose a problem due to long
computation times and/or high memory demands. Such problems are common
when working with geo-referenced data in earth sciences. Features
include:

- anisotropic Gaussian processes on 2-dimensional regular grids for a
  choice of covariance kernels
- optimized computation of the likelihood function, generalized least
  squares, and kriging predictor/variance
- fast computations with missing data problems, and even faster in the
  complete data case
- automated maximum likelihood model fitting and support for sample
  semi-variograms
- user friendly helper functions for raster down-scaling and point
  interpolation

`snapKrig` depends only on packages included by default in R (like
`graphics` and `stats`), but supports raster and geometry classes from
`sf` and `terra`.

# History

An earlier incarnation of snapKrig was called
[pkern](https://github.com/deankoch/pkern). snapKrig is a redesigned
version that uses a more user-friendly S3 grid object class.

pkern was an R implementation of some methods I developed in [my
thesis](https://doi.org/10.7939/r3-91zn-v276) for speeding up
geostatistical computations involving large covariance matrices. The
central idea is to model spatial dependence using a separable
2-dimensional covariance kernel, defined as the product of two
(1-dimensional) univariate covariance kernels. This introduces special
symmetries and structure in the covariance matrix, which are exploited
in this package for fast and memory-efficient computations.

I developed snapKrig to support a project to interpolate weather data,
but the methods underlying snapKrig are applicable more generally. See
also \[[1](https://doi.org/10.7939/r3-g6qb-bq70)\], where I use product
kernels to study directions of anisotropy in a non-stationary random
fields, and \[[2](https://doi.org/10.1007/s11538-021-00899-z),
[3](https://doi.org/10.1098/rsif.2020.0434)\], where I apply it to fit a
covariance structure, and to speed up calculations of dispersal kernel
convolutions.

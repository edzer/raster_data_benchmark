# Raster Data Benchmarks for R

[Edzer Pebesma](https://github.com/edzer/), 
[Lauren O'Brien](https://twitter.com/obrl_soil),
[Michael Sumner](https://github.com/mdsumner/), and
[Michael Dorman](https://github.com/michaeldorman)

## Summary

Most spatial data belongs to one of two types: vector data (points,
lines, polygons) and raster data (imagery, grids). For vector data a
clear set of standards exists both for data models, serialisations,
and for operations on geometries. For raster data, this is much
less the case: although a formal standard for serving raster data
("web coverage service") and a de-facto (open source software)
standard for reading and writing raster data (GDAL) exist, a set
of raster operations is not standardised.

In R, packages for raster analysis have existed for a long time,
starting with `spatstat` (2002), `rgdal` (2003), `sp` (2005) and
`raster` (2010), and several packages have been building on those to
add functionality (specific analysis or visualisation), like
`exactextractr` and `fasterize`. Currently, we see a broadening of
raster packages, including `stars` and `terra` with different data
represenations, and several packages aiming at handling particular
data types (e.g.  `ecmwfr` for ERA-5 weather data, `MODIStsp`
for MODIS or `sen2r` for Sentinel-2 data) or at visualising data
(`tmap`, `ggplot2`, `mapview`).

This proposal will create an R package that explains, compares
and shows the correspondences and differences of R packages for
raster analysis, expressed in terms of numerical results, speed of
computation, scalability to large rasters, and visualisation. This
will be made accessible in R vignettes that can be recreated when
one of the packages compared is updated. With this package, users can
make better informed decisions about which package to use for what.

## The problem

Raster data analysis is less standardised than vector data analysis,
and different R packages have different naming and capabilities
to carry out raster analysis, perform differently, have different
ways to handle (or not handle) very larger rasters, and provide
different ways to visualise raster data. From a user perspective
this variety is complicated, and makes it hard to choose which
package to use for what.

During the geospatial discussion panel at the "Why R?" conference
in September 2021 this led Ahmadou Dicko to ask the questions
[_As much as many analysis on vector data now use `sf`,
what's the future for a more unified raster analysis data in
R?_](https://www.youtube.com/watch?v=_HBpzbbUVgc&feature=youtu.be&t=1588)
Given the broadening of R packages for raster analysis and
visualisation we observe, this question is not easily answerable
and becomes more relevant.

### Existing work

Existing packages for reading, writing and analysing raster data include:

* [rgdal](https://CRAN.R-project.org/package=rgdal), lets you
read and write raster data, possibly piece-by-piece, in one of over 150
different file formats,
* [raster](https://CRAN.R-project.org/package=raster) allows users
to work with raster maps or stacks of them, where a stack can
either refer to different bands (color) or different time steps,
but not both. Package raster can iterate functions over large
files on disc, and takes care of the caching, using either
rgdal or [ncdf4](https://CRAN.R-project.org/package=ncdf4).
* [terra](https://cran.r-project.org/package=terra) a successor of
`raster`, mostly written in C++,
* [stars](https:://cran.r-project.org/package=stars) a package strongly
connected to `sf`, addressing raster data but also multidimensional
data cubes
* [fasterize](https://cran.r-project.org/package=fasterize) provides
a replacement for rasterize() from the `raster` package that takes
`sf`-type objects, and is much faster
* [exactextractr](https://cran.r-project.org/package=exactextractr)
provides a replacement for the 'extract' function from the 'raster'
package that is suitable for extracting raster values using 'sf'
polygons.

Other package for reading multidimensional arrays, are

* [RNetCDF](https://cran.r-project.org/package=RNetCDF) and
* [ncdf4](https://CRAN.R-project.org/package=ncdf4).

Among the many packages that build upon on `raster` and/or `stars` we
can list [RStoolbox](https://CRAN.R-project.org/package=RStoolbox),
[MODIS](https://CRAN.R-project.org/package=MODIS),
[MODIStsp](https://CRAN.R-project.org/package=MODIStsp),
[sen2r](https://CRAN.R-project.org/package=sen2r),
[anglr](https://cran.r-project.org/package=anglr),
[ceramic](https://cran.r-project.org/package=ceramic),
[lazyraster](https://cran.r-project.org/package=lazyraster),
[starsExtra](https://cran.r-project.org/package=starsExtra),
[shadow](https://cran.r-project.org/package=shadow),
[tmap](https://cran.r-project.org/package=tmap),
[landsat](https://CRAN.R-project.org/package=landsat), and
[hsdar](https://CRAN.R-project.org/package=hsdar).

Package `stars` contains a
[vignette](https://r-spatial.github.io/stars/articles/stars6.html)
that explains how `raster` functions and methods map into equivalent
functions and methods in `stars`; this has been a joint effort,
with several contributors. Examples are failing, and potential
differences in outcomes or performance are not revealed.

Many issues, e.g. how to create RGB (red-green-blue) composites from
raster data, how to handle color tables of categorical raster maps,
or how to rasterize polygons or polygonize rasters are discussed
in scattered places, e.g. the issues of particular packages. These
issues have often been closed, and are hard to find and are not
indexed in some place.  Collecting and organising the reproducible
examples of these issues in an easily accessible way will help
users decide where the functionality they need can be found.

Finally, bringing differences in functionality, computed results,
performance and visualisation options into the light is expected
to have a positive effect on package development, as it will make
differences clear in a simple, comprehensive and standardised way.
This way, it is an easier basis for discussion in user and developer
communities, and may incentivise further collaboration, improvement
of existing packages, or the development of new packages.

## The Plan: 

To inform users about the capabilities of different packages for
handling, analysing and visualising raster data, we propose here to
develop an "R raster benchmark", in the form of an R package that
shows how particular operations (read, write, analyse, visualise)
can be done with different packages, and compares

* whether results from different packages are identical
* the speed of these packages
* their ability to handle very large rasters
* their ability to properly visualise the results

The main output will be a set of vignettes that makes the analysis of
the comparisons easily readabable and findable. The vignettes should
be easy to recreate when one of the packages compared is updated.

The packages to be compared includes but is not limited to: `raster`,
`terra`, `stars`, `fasterize`, `exactextractr`, `mapview`, `tmap`,
and `anglr`. The operations to be compared includes those listed
in the vignette comparing the stars and raster package, as well as
plotting routines, and includes cropping, resampling and plotting
at reduced resolution, polygonizing rasters, rasterizing polygons,
cell-based operations, extraction at point/line/polygon sets. 

The package developed will only depend on CRAN packages and possibly
on a data-only package (such as the off-CRAN `starsdata` package)
that contains a set of data sets too large for a CRAN package. The
packages involved however have a number of system dependencies. To
better control for these system dependencies (and make it easier
e.g.  to run the benchmarks with new or upcoming GDAL releases),
one or more docker files will be provided to containerise the
installation of R, all packages involved, and carry out the
benchmarking.

The work breaks down in the following work packages:

* comparison of computed results
* performance comparison
* scalability comparison (handling very large rasters)
* visualisation comparison
* containerisation of benchmark

## How Can The ISC Help: 

We will use the funding to develop the R package, develop the docker
files, carry out and publish the benchmarks, and write related
publications (blog posts, vignettes). Total costs will be 10,000 USD.

## Dissemination: 

We will regularly post blogs about the project on [r-spatial.org](http://r-spatial.org/), use twitter, post to [r-sig-geo](https://stat.ethz.ch/mailman/listinfo/r-sig-geo), write answers to stackoverflow, and communicate through github issues and discussions. The project will live on GitHub, in the [r-spatial](https://github.com/r-spatial/) organisation. We will work under a permissive open source license (Apache-2, and CC-0 for documentation). Pull requests will be encouraged. R consortium blogs will be provided upon request. A publication, preferably in _the R Journal_, is anticipated.

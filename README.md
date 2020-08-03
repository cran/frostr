
<!-- README.md is generated from README.Rmd. Please edit that file -->

# frostr

<!-- badges: start -->

[![license](https://img.shields.io/github/license/mashape/apistatus.svg?maxAge=2592000)](https://github.com/imangR/frostr/blob/master/LICENSE)
<!-- badges: end -->

## Overview

frostr is an unofficial R API to MET Norway’s Frost API. The package
provides functions that retrieves data from resources in the Frost API
to R as data frames.

The functions are

  - `get_observations()` to get weather data
  - `get_sources()` to get the station IDs
  - `get_locations()` to get location names
  - `get_elements()` to get climate and weather elements
  - `get_available_timeseries()` to get available time series
  - `get_available_qualitycodes()` to get available quality codes
  - `get_element_codetables()` to get code tables for climate and
    weather elements

Please visit the [Frost API website](https://frost.met.no/index.html) to
get a client ID and learn more about how to properly use the API
service. Here are some starters:

  - [How to use Frost](https://frost.met.no/howto.html)
  - [API reference](https://frost.met.no/api.html)
  - [Terms of use](https://frost.met.no/termsofuse2.html)

The data is from the The Norwegian Meteorological Institute (abbreviated
as MET Norway). The data and products are distributed under the
[Norwegian License for Open Data 2.0
(NLOD)](https://data.norge.no/nlod/en/2.0) and [Creative
Commons 4.0](https://creativecommons.org/licenses/by/4.0/).

## Installation

You can install the released version of frostr from
[CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("frostr")
```

### Development version

Alternatively, you can install a development version of frostr from
GitHub to get bug fixes or new features before the next package version
is released on CRAN. To install the development version, you can use
devtools to install frostr from GitHub.

``` r
# install.packages("devtools")
devtools::install_github("imangR/frostr")
```

## Example

Say that I want to retrieve daily mean temperature, sum precipitation,
and mean wind speed data for the first five months of 2019 as registered
by the data source at Blindern. Then, I can utilize frostr to get that
data as follows.

``` r
# Attach packages
library(frostr)
library(dplyr)
library(stringr)

# Set your client ID
frost_client_id <- "<YOUR CLIENT ID>"

# Find the source ID for Blindern held by MET.NO
sources <- get_sources(client_id = frost_client_id)

blindern_id <- sources %>%
  filter(str_detect(name, "OSLO - BLINDERN") & stationHolders == "MET.NO") %>% 
  select(id)

# Find the name of the climate and weather elements of interest
elements <- get_elements(client_id = frost_client_id)

View(elements)
#> id                            name                                      units
#> ...                           ...                                       ...
#> mean(air_temperature P1D)     Mean air temperature (24 h)               degC
#> sum(precipitation_amount P1D) Precipitation (24 h)                      mm
#> mean(wind_speed P1D)          Average of wind speed of main obs. (24 h) m/s
#> ...                           ...                                       ...

elements <- c("mean(air temperature P1D)",
                   "sum(precipitation_amount P1D)",
                   "mean(wind_speed P1D)")

# Declare the time range for which you want to retrieve observations
reference_time <- "2019-01-01/2019-05-31"

# Get the weather observations specified
observations_df <- get_observations(client_id      = frost_client_id,
                                    sources        = blindern_id,
                                    elements       = elements,
                                    reference_time = reference_time)
```

## Stability

frostr can break if MET Norway make changes to the Frost API. If you
discover that a function does not work, then raise an issue by
submitting a reproducible example on
[GitHub](https://github.com/imangR/frostr/issues), so that I can
identify and fix the issue.

## File an issue or suggest an improvement

If you want to report a discovered bug, raise some other issue, or
suggest an improvement to frostr, then please file an issue on
[GitHub](https://github.com/imangR/frostr/issues). For bugs, please file
a minimal reproducible example.

## Known issues

The *sources* resource in the Frost API returns country names in
Norwegian. Unfortunately, at the time of writing (2019-06-09), a
language parameter cannot be set for this resource to return country
names in English.

-----

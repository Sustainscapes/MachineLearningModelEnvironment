
<!-- README.md is generated from README.Rmd. Please edit that file -->

# MachineLearningModelEnvironment

<!-- badges: start -->
<!-- badges: end -->

The goal of MachineLearningModelEnvironment is to use the Novana
Dataset, together with climatic and soil data in order to predict the
potential Species Richness, one star species richness and the Arstcore
for all of Denmark assuming that the whole country is tranformed into
one of the major habitats.

## Generation of the training dataset

In order to arrange this we will need the following packages:

``` r
library(tidyverse)
library(sf)
library(DT)
library(terra)
library(knitr)
```

First we will use points from the NOVANA dataset

``` r
Habs <- read_rds("AllData4.rds") %>%
  dplyr::filter(Dataset == "Novana") %>%
  separate(col = "ID", into = c("ID", "plot")) %>%
  dplyr::select("plot", "ID", "Dataset","habtype",
                "MajorHab") %>% dplyr::filter(!is.na(MajorHab)) 
#> Warning: Expected 2 pieces. Additional pieces discarded in 1 rows [1].
```

There are 44532 plots, in the following table we can se the number of
plots per each type of habitat:

| MajorHab | number\_of\_plots |
|:---------|------------------:|
| 62       |              9460 |
| 21       |              9377 |
| 72       |              6604 |
| 40       |              5611 |
| 13       |              4313 |
| 71       |              3568 |
| 64       |              2868 |
| 23       |               895 |
| 91       |               397 |
| 22       |               297 |
| 99       |               289 |
| 51       |               236 |
| 61       |               187 |
| 12       |               185 |
| 82       |               183 |
| 11       |                25 |
| 31       |                21 |
| 70       |                15 |
| 32       |                 1 |

Number of plots per major habitat in descending order

``` r
Species <- read_csv("Novana/alledata-abiotiske2.csv") %>% dplyr::select("site", "plot", "year", "antalarter", "antalstjernearter", "antaltostjernearter",
  "antalenaarigearter", "meanscore", "andelstjerne", "andeltostjerne") %>%
  dplyr::filter(plot %in% Habs$plot) %>%
  mutate(plot = as.character(plot), site = as.character(site))
#> Warning: One or more parsing issues, see `problems()` for details
#> Rows: 97560 Columns: 97
#> ── Column specification ────────────────────────────────────────────────────────
#> Delimiter: ","
#> chr (76): sekhabtype, terhabtype, pHjord, pHvand, Cijord, Nijord, CNratio, F...
#> dbl (21): site, plot, year, UTMx, UTMy, fEL, fER, fEN, fAnnualsGrime, fchmax...
#> 
#> ℹ Use `spec()` to retrieve the full column specification for this data.
#> ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.


Habs <- Habs %>% left_join(Species)
#> Joining, by = "plot"

Habs <- Habs %>%
    drop_na()
```

## Reproducibility ticket:

    #> ─ Session info ───────────────────────────────────────────────────────────────
    #>  setting  value                       
    #>  version  R version 4.1.2 (2021-11-01)
    #>  os       Ubuntu 18.04.6 LTS          
    #>  system   x86_64, linux-gnu           
    #>  ui       X11                         
    #>  language (EN)                        
    #>  collate  en_US.UTF-8                 
    #>  ctype    en_US.UTF-8                 
    #>  tz       Europe/Copenhagen           
    #>  date     2022-03-10                  
    #> 
    #> ─ Packages ───────────────────────────────────────────────────────────────────
    #>  package     * version date       lib source        
    #>  assertthat    0.2.1   2019-03-21 [1] CRAN (R 4.1.0)
    #>  backports     1.4.1   2021-12-13 [1] CRAN (R 4.1.2)
    #>  brio          1.1.3   2021-11-30 [1] CRAN (R 4.1.2)
    #>  broom         0.7.11  2022-01-03 [1] CRAN (R 4.1.2)
    #>  cachem        1.0.6   2021-08-19 [1] CRAN (R 4.1.1)
    #>  callr         3.7.0   2021-04-20 [1] CRAN (R 4.1.0)
    #>  cellranger    1.1.0   2016-07-27 [1] CRAN (R 4.1.0)
    #>  class         7.3-19  2021-05-03 [1] CRAN (R 4.1.0)
    #>  classInt      0.4-3   2020-04-07 [1] CRAN (R 4.1.0)
    #>  cli           3.2.0   2022-02-14 [1] CRAN (R 4.1.2)
    #>  codetools     0.2-18  2020-11-04 [1] CRAN (R 4.1.0)
    #>  colorspace    2.0-3   2022-02-21 [1] CRAN (R 4.1.2)
    #>  crayon        1.5.0   2022-02-14 [1] CRAN (R 4.1.2)
    #>  DBI           1.1.2   2021-12-20 [1] CRAN (R 4.1.2)
    #>  dbplyr        2.1.1   2021-04-06 [1] CRAN (R 4.1.0)
    #>  desc          1.4.0   2021-09-28 [1] CRAN (R 4.1.2)
    #>  devtools      2.4.3   2021-11-30 [1] CRAN (R 4.1.2)
    #>  digest        0.6.29  2021-12-01 [1] CRAN (R 4.1.2)
    #>  dplyr       * 1.0.8   2022-02-08 [1] CRAN (R 4.1.2)
    #>  DT          * 0.20    2021-11-15 [1] CRAN (R 4.1.2)
    #>  e1071         1.7-9   2021-09-16 [1] CRAN (R 4.1.1)
    #>  ellipsis      0.3.2   2021-04-29 [1] CRAN (R 4.1.0)
    #>  evaluate      0.15    2022-02-18 [1] CRAN (R 4.1.2)
    #>  fansi         1.0.2   2022-01-14 [1] CRAN (R 4.1.2)
    #>  fastmap       1.1.0   2021-01-25 [1] CRAN (R 4.1.0)
    #>  forcats     * 0.5.1   2021-01-27 [1] CRAN (R 4.1.0)
    #>  fs            1.5.2   2021-12-08 [1] CRAN (R 4.1.2)
    #>  generics      0.1.2   2022-01-31 [1] CRAN (R 4.1.2)
    #>  ggplot2     * 3.3.5   2021-06-25 [1] CRAN (R 4.1.0)
    #>  glue          1.6.2   2022-02-24 [1] CRAN (R 4.1.2)
    #>  gtable        0.3.0   2019-03-25 [1] CRAN (R 4.1.0)
    #>  haven         2.4.3   2021-08-04 [1] CRAN (R 4.1.1)
    #>  highr         0.9     2021-04-16 [1] CRAN (R 4.1.0)
    #>  hms           1.1.1   2021-09-26 [1] CRAN (R 4.1.1)
    #>  htmltools     0.5.2   2021-08-25 [1] CRAN (R 4.1.1)
    #>  htmlwidgets   1.5.4   2021-09-08 [1] CRAN (R 4.1.1)
    #>  httr          1.4.2   2020-07-20 [1] CRAN (R 4.1.0)
    #>  jsonlite      1.7.3   2022-01-17 [1] CRAN (R 4.1.2)
    #>  KernSmooth    2.23-20 2021-05-03 [1] CRAN (R 4.1.0)
    #>  knitr       * 1.37    2021-12-16 [1] CRAN (R 4.1.2)
    #>  lifecycle     1.0.1   2021-09-24 [1] CRAN (R 4.1.1)
    #>  lubridate     1.8.0   2021-10-07 [1] CRAN (R 4.1.2)
    #>  magrittr      2.0.2   2022-01-26 [1] CRAN (R 4.1.2)
    #>  memoise       2.0.0   2021-01-26 [1] CRAN (R 4.1.0)
    #>  modelr        0.1.8   2020-05-19 [1] CRAN (R 4.1.0)
    #>  munsell       0.5.0   2018-06-12 [1] CRAN (R 4.1.0)
    #>  pillar        1.7.0   2022-02-01 [1] CRAN (R 4.1.2)
    #>  pkgbuild      1.2.0   2020-12-15 [1] CRAN (R 4.1.0)
    #>  pkgconfig     2.0.3   2019-09-22 [1] CRAN (R 4.1.0)
    #>  pkgload       1.2.4   2021-11-30 [1] CRAN (R 4.1.2)
    #>  prettyunits   1.1.1   2020-01-24 [1] CRAN (R 4.1.0)
    #>  processx      3.5.2   2021-04-30 [1] CRAN (R 4.1.0)
    #>  proxy         0.4-26  2021-06-07 [1] CRAN (R 4.1.1)
    #>  ps            1.6.0   2021-02-28 [1] CRAN (R 4.1.0)
    #>  purrr       * 0.3.4   2020-04-17 [1] CRAN (R 4.1.0)
    #>  R6            2.5.1   2021-08-19 [1] CRAN (R 4.1.1)
    #>  Rcpp          1.0.8   2022-01-13 [1] CRAN (R 4.1.2)
    #>  readr       * 2.1.2   2022-01-30 [1] CRAN (R 4.1.2)
    #>  readxl        1.3.1   2019-03-13 [1] CRAN (R 4.1.0)
    #>  remotes       2.4.2   2021-11-30 [1] CRAN (R 4.1.2)
    #>  reprex        2.0.0   2021-04-02 [1] CRAN (R 4.1.0)
    #>  rlang         1.0.2   2022-03-04 [1] CRAN (R 4.1.2)
    #>  rmarkdown     2.11    2021-09-14 [1] CRAN (R 4.1.2)
    #>  rprojroot     2.0.2   2020-11-15 [1] CRAN (R 4.1.0)
    #>  rstudioapi    0.13    2020-11-12 [1] CRAN (R 4.1.0)
    #>  rvest         1.0.2   2021-10-16 [1] CRAN (R 4.1.2)
    #>  scales        1.1.1   2020-05-11 [1] CRAN (R 4.1.0)
    #>  sessioninfo   1.1.1   2018-11-05 [1] CRAN (R 4.1.0)
    #>  sf          * 1.0-6   2022-02-04 [1] CRAN (R 4.1.2)
    #>  stringi       1.7.6   2021-11-29 [1] CRAN (R 4.1.2)
    #>  stringr     * 1.4.0   2019-02-10 [1] CRAN (R 4.1.0)
    #>  terra       * 1.5-21  2022-02-17 [1] CRAN (R 4.1.2)
    #>  testthat      3.1.2   2022-01-20 [1] CRAN (R 4.1.2)
    #>  tibble      * 3.1.6   2021-11-07 [1] CRAN (R 4.1.2)
    #>  tidyr       * 1.2.0   2022-02-01 [1] CRAN (R 4.1.2)
    #>  tidyselect    1.1.2   2022-02-21 [1] CRAN (R 4.1.2)
    #>  tidyverse   * 1.3.1   2021-04-15 [1] CRAN (R 4.1.0)
    #>  tzdb          0.2.0   2021-10-27 [1] CRAN (R 4.1.2)
    #>  units         0.8-0   2022-02-05 [1] CRAN (R 4.1.2)
    #>  usethis       2.1.5   2021-12-09 [1] CRAN (R 4.1.2)
    #>  utf8          1.2.2   2021-07-24 [1] CRAN (R 4.1.1)
    #>  vctrs         0.3.8   2021-04-29 [1] CRAN (R 4.1.0)
    #>  withr         2.5.0   2022-03-03 [1] CRAN (R 4.1.2)
    #>  xfun          0.29    2021-12-14 [1] CRAN (R 4.1.2)
    #>  xml2          1.3.2   2020-04-23 [1] CRAN (R 4.1.0)
    #>  yaml          2.3.5   2022-02-21 [1] CRAN (R 4.1.2)
    #> 
    #> [1] /home/derek/R/x86_64-pc-linux-gnu-library/4.1
    #> [2] /usr/local/lib/R/site-library
    #> [3] /usr/lib/R/site-library
    #> [4] /usr/lib/R/library

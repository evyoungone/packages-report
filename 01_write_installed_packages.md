01_write_installed_packages.r
================
Evan Young
2022-07-25

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "/Library/Frameworks/R.framework/Versions/4.1/Resources/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "/Library/Frameworks/R.framework/Resources/library"

``` r
identical(.Library, .libPaths()[2])
```

    ## [1] FALSE

``` r
## Huh? Maybe this is an symbolic link issue?
library(fs)
identical(path_real(.Library), path_real(.libPaths()[2]))
```

    ## [1] FALSE

Installed packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.5     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   2.0.1     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 378

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 3 × 3
    ##   LibPath                                                       Priority       n
    ##   <chr>                                                         <chr>      <int>
    ## 1 /Library/Frameworks/R.framework/Versions/4.1/Resources/libra… base          14
    ## 2 /Library/Frameworks/R.framework/Versions/4.1/Resources/libra… recommend…    15
    ## 3 /Library/Frameworks/R.framework/Versions/4.1/Resources/libra… <NA>         349

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 × 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                 202 0.534 
    ## 2 yes                169 0.447 
    ## 3 <NA>                 7 0.0185

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 × 3
    ##   Built     n   prop
    ##   <chr> <int>  <dbl>
    ## 1 4.1.0   353 0.934 
    ## 2 4.1.2    25 0.0661

Reflections

``` r
## reflect on ^^ and make a few notes to yourself; inspiration
##   * does the number of base + recommended packages make sense to you?
##   * how does the result of .libPaths() relate to the result of .Library?
```

Going further

``` r
## if you have time to do more ...

## is every package in .Library either base or recommended?
all_default_pkgs <- list.files(.Library)

all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)

setdiff(all_default_pkgs, all_br_pkgs)
```

    ##   [1] "abind"                "antiword"             "ash"                 
    ##   [4] "askpass"              "assertive"            "assertive.base"      
    ##   [7] "assertive.code"       "assertive.data"       "assertive.data.uk"   
    ##  [10] "assertive.data.us"    "assertive.datetimes"  "assertive.files"     
    ##  [13] "assertive.matrices"   "assertive.models"     "assertive.numbers"   
    ##  [16] "assertive.properties" "assertive.reflection" "assertive.sets"      
    ##  [19] "assertive.strings"    "assertive.types"      "assertthat"          
    ##  [22] "backports"            "base64enc"            "bayestestR"          
    ##  [25] "BH"                   "bit"                  "bit64"               
    ##  [28] "bitops"               "blob"                 "brew"                
    ##  [31] "brio"                 "broom"                "bslib"               
    ##  [34] "cachem"               "callr"                "car"                 
    ##  [37] "carData"              "caret"                "cellranger"          
    ##  [40] "checkmate"            "classInt"             "cli"                 
    ##  [43] "clipr"                "coda"                 "colorspace"          
    ##  [46] "commonmark"           "corrplot"             "cowplot"             
    ##  [49] "cpp11"                "crayon"               "credentials"         
    ##  [52] "crosstalk"            "curl"                 "data.table"          
    ##  [55] "datawizard"           "datetime"             "DBI"                 
    ##  [58] "dbplyr"               "dbscan"               "dendextend"          
    ##  [61] "desc"                 "devtools"             "diffobj"             
    ##  [64] "digest"               "dplyr"                "DT"                  
    ##  [67] "dtplyr"               "dtt"                  "e1071"               
    ##  [70] "effectsize"           "ellipse"              "ellipsis"            
    ##  [73] "english"              "estimatr"             "evaluate"            
    ##  [76] "expss"                "extrafont"            "extrafontdb"         
    ##  [79] "factoextra"           "FactoMineR"           "fansi"               
    ##  [82] "farver"               "fastmap"              "fastmatch"           
    ##  [85] "flashClust"           "flexdashboard"        "fontawesome"         
    ##  [88] "forcats"              "foreach"              "formattable"         
    ##  [91] "Formula"              "fs"                   "future"              
    ##  [94] "future.apply"         "gargle"               "gdtools"             
    ##  [97] "generics"             "gert"                 "ggalluvial"          
    ## [100] "GGally"               "ggalt"                "gganimate"           
    ## [103] "ggcorrplot"           "ggeasy"               "ggiraph"             
    ## [106] "ggplot2"              "ggpubr"               "ggrepel"             
    ## [109] "ggridges"             "ggsci"                "ggsignif"            
    ## [112] "ggthemes"             "gh"                   "gifski"              
    ## [115] "gitcreds"             "glmnet"               "globals"             
    ## [118] "glue"                 "googledrive"          "googlesheets4"       
    ## [121] "gower"                "gridBase"             "gridExtra"           
    ## [124] "gtable"               "hardhat"              "haven"               
    ## [127] "here"                 "highr"                "hms"                 
    ## [130] "hrbrthemes"           "htmlTable"            "htmltools"           
    ## [133] "htmlwidgets"          "httpuv"               "httr"                
    ## [136] "hunspell"             "ids"                  "igraph"              
    ## [139] "ini"                  "insight"              "ipred"               
    ## [142] "isoband"              "ISOcodes"             "iterators"           
    ## [145] "janeaustenr"          "jquerylib"            "jsonlite"            
    ## [148] "kableExtra"           "knitr"                "labeling"            
    ## [151] "lars"                 "later"                "lava"                
    ## [154] "lazyeval"             "lda"                  "LDAvis"              
    ## [157] "leaps"                "lexicon"              "lifecycle"           
    ## [160] "listenv"              "lme4"                 "lmtest"              
    ## [163] "lpSolve"              "lubridate"            "magrittr"            
    ## [166] "maps"                 "maptools"             "markdown"            
    ## [169] "MatrixModels"         "matrixStats"          "memoise"             
    ## [172] "mgsub"                "mime"                 "minqa"               
    ## [175] "mitools"              "mnormt"               "ModelMetrics"        
    ## [178] "modelr"               "modeltools"           "moments"             
    ## [181] "munsell"              "naivebayes"           "ndjson"              
    ## [184] "network"              "networkD3"            "ngramrr"             
    ## [187] "nloptr"               "NLP"                  "numDeriv"            
    ## [190] "nycflights13"         "nytimes"              "openssl"             
    ## [193] "packcircles"          "packrat"              "pander"              
    ## [196] "parallelDist"         "parallelly"           "parameters"          
    ## [199] "pbkrtest"             "pdftools"             "performance"         
    ## [202] "pillar"               "pkgbuild"             "pkgconfig"           
    ## [205] "pkgload"              "plotly"               "plyr"                
    ## [208] "png"                  "polynom"              "praise"              
    ## [211] "prettyunits"          "pROC"                 "processx"            
    ## [214] "prodlim"              "progress"             "progressr"           
    ## [217] "proj4"                "promises"             "proxy"               
    ## [220] "ps"                   "psych"                "purrr"               
    ## [223] "qdapDictionaries"     "qdapRegex"            "qpdf"                
    ## [226] "quadprog"             "quanteda"             "quanteda.textplots"  
    ## [229] "quantreg"             "R6"                   "randomForest"        
    ## [232] "rappdirs"             "rcmdcheck"            "RColorBrewer"        
    ## [235] "Rcpp"                 "RcppArmadillo"        "RcppEigen"           
    ## [238] "RcppParallel"         "RCurl"                "readODS"             
    ## [241] "readr"                "readtext"             "readxl"              
    ## [244] "rebus"                "rebus.base"           "rebus.datetimes"     
    ## [247] "rebus.numbers"        "rebus.unicode"        "recipes"             
    ## [250] "rematch"              "rematch2"             "remotes"             
    ## [253] "reprex"               "reshape"              "reshape2"            
    ## [256] "rgeos"                "rjson"                "RJSONIO"             
    ## [259] "rlang"                "rmarkdown"            "rnaturalearth"       
    ## [262] "rnaturalearthdata"    "roxygen2"             "rprojroot"           
    ## [265] "rsconnect"            "RSocrata"             "rstatix"             
    ## [268] "rstudioapi"           "Rttf2pt1"             "rversions"           
    ## [271] "rvest"                "s2"                   "sandwich"            
    ## [274] "sass"                 "scales"               "scatterplot3d"       
    ## [277] "see"                  "selectr"              "SentimentAnalysis"   
    ## [280] "sentimentr"           "sessioninfo"          "sf"                  
    ## [283] "shape"                "shiny"                "shinydashboard"      
    ## [286] "slam"                 "sna"                  "SnowballC"           
    ## [289] "sourcetools"          "sp"                   "SparseM"             
    ## [292] "spikeslab"            "SQUAREM"              "stargazer"           
    ## [295] "statnet.common"       "stm"                  "stopwords"           
    ## [298] "streamR"              "stringdist"           "stringi"             
    ## [301] "stringr"              "striprtf"             "survey"              
    ## [304] "svglite"              "sys"                  "systemfonts"         
    ## [307] "syuzhet"              "tau"                  "testthat"            
    ## [310] "textclean"            "textshape"            "tibble"              
    ## [313] "tidyr"                "tidyselect"           "tidytext"            
    ## [316] "tidyverse"            "timeDate"             "tinytex"             
    ## [319] "tm"                   "tmvnsim"              "tokenizers"          
    ## [322] "topicmodels"          "transformr"           "translations"        
    ## [325] "treemap"              "tweenr"               "tzdb"                
    ## [328] "units"                "usethis"              "utf8"                
    ## [331] "uuid"                 "vader"                "vctrs"               
    ## [334] "viridis"              "viridisLite"          "vroom"               
    ## [337] "waldo"                "webshot"              "wesanderson"         
    ## [340] "whisker"              "withr"                "wk"                  
    ## [343] "writexl"              "xfun"                 "xml2"                
    ## [346] "xopen"                "xtable"               "yaml"                
    ## [349] "zip"                  "zoo"

``` r
## study package naming style (all lower case, contains '.', etc

## use `fields` argument to installed.packages() to get more info and use it!
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()

ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 × 3
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 FALSE    155 0.410
    ## 2 TRUE     223 0.590

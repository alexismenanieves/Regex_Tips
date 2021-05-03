Regex Tips
================

## Case 1. Reading logs

You have received a log file, which contains non-structured data:

``` r
logs <- c("27-May-19 16:57:58.1244 (6628 6452) Module1.exe:Programg.cpp,v:854: ERROR: group 5 failed with error = 0x8008000f",
"27-May-19 16:57:58.1254 (6628 6452) Module1.exe:Programg.cpp,v:880: ERROR: group 5 failed on its 3 retry", 
"27-May-19 17:00:04.8385 ( -1 1376 13900) Module2.exe:Execute:803: Information - Executing command 1", 
"27-May-19 17:00:08.5115 ( -1 1376 13900) Module2.exe:Execute:603: Information - command 1 completed.", 
"27-May-19 17:00:09.5209 ( -1 1376 13900) Module2.exe:Execute:803: Information - Executing command 2")
```

You are required to convert the log into a data frame.

``` r
# Extract date and time, join by a white space
# The trick here is to separate the string and grab just the first two substrings
date_time <- sapply(strsplit(logs,' '), '[', 1:2)
date_time <- apply(date_time, 2, paste, collapse = " ")

# Suppress date and time to extract content inside parenthesis
# This regex captures a non-space character (one or more) until it finds a parenthesis
mod_x <- sub("^[^ ]+ [^(]+", "", logs) 
parenthesis <- sub("(\\([^)]+\\)).*","\\1", mod_x)

# Extract text forward on
# This regex captures all non closing parenthesis 
detail <- sub("[^)]+\\)","", mod_x)
detail <- sub("^\\s","", detail)

# Join data into a dataframe
logs_data_frame <- data.frame(date_time, parenthesis, detail)
```

Let’s see the dataframe:

``` r
logs_data_frame
```

    ##                 date_time      parenthesis
    ## 1 27-May-19 16:57:58.1244      (6628 6452)
    ## 2 27-May-19 16:57:58.1254      (6628 6452)
    ## 3 27-May-19 17:00:04.8385 ( -1 1376 13900)
    ## 4 27-May-19 17:00:08.5115 ( -1 1376 13900)
    ## 5 27-May-19 17:00:09.5209 ( -1 1376 13900)
    ##                                                                          detail
    ## 1 Module1.exe:Programg.cpp,v:854: ERROR: group 5 failed with error = 0x8008000f
    ## 2          Module1.exe:Programg.cpp,v:880: ERROR: group 5 failed on its 3 retry
    ## 3                    Module2.exe:Execute:803: Information - Executing command 1
    ## 4                   Module2.exe:Execute:603: Information - command 1 completed.
    ## 5                    Module2.exe:Execute:803: Information - Executing command 2

## Case 2. Separate by capitalized letters

You have musicians names which have the first letter capitalized but are
joined:

``` r
musicians <- c("AlanisMorisette","ACDC","PearlJam",
               "will.i.am","CreedenceClearwaterRevival")
```

You are required to separate the text by the capitalized letter:

``` r
# Let's detect a non-capitalized and a capitalized letter, and create groups 
musicians <- gsub('([a-z])([A-Z])', '\\1 \\2', musicians, perl = TRUE)
```

Let’s see the result:

``` r
# Sure I prefer Pearl Jam and Creedence Clearwater Revival
musicians
```

    ## [1] "Alanis Morisette"             "ACDC"                        
    ## [3] "Pearl Jam"                    "will.i.am"                   
    ## [5] "Creedence Clearwater Revival"

## Case 3. Match a specific ingredient pattern

You have a product description and you want to capture the ingredient,
which have an specific pattern:

``` r
description <- c("Bakery: Desdemona (Desserts; A9B-APO) 7th Avenue")
```

All the ingredients have the following structure: three alphanumeric
characters, an hyphen and three alphanumeric characters. So we want to
capture this pattern:

``` r
# We will use the stringr library to extract our pattern.
ingredient <- str_extract(description, '([a-zA-Z0-9]{3}-[a-zA-Z0-9]{3})')
ingredient 
```

    ## [1] "A9B-APO"

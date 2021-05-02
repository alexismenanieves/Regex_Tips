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
fec_hor <- sapply(strsplit(logs,' '), '[', 1:2)
fec_hor <- apply(fec_hor, 2, paste, collapse = " ")

# Suppress date and time to extract content inside parenthesis
mod_x <- sub("^[^ ]+ [^(]+", "", logs) 
parentesis <- sub("(\\([^)]+\\)).*","\\1", mod_x)

# Extract text forward on
detalle <- sub("[^)]+\\)","", mod_x)
detalle <- sub("^\\s","", detalle)

# Join data into a dataframe
logs_data_frame <- data.frame(fec_hor, parentesis, detalle)
```

Let’s see the dataframe:

``` r
logs_data_frame
```

    ##                   fec_hor       parentesis
    ## 1 27-May-19 16:57:58.1244      (6628 6452)
    ## 2 27-May-19 16:57:58.1254      (6628 6452)
    ## 3 27-May-19 17:00:04.8385 ( -1 1376 13900)
    ## 4 27-May-19 17:00:08.5115 ( -1 1376 13900)
    ## 5 27-May-19 17:00:09.5209 ( -1 1376 13900)
    ##                                                                         detalle
    ## 1 Module1.exe:Programg.cpp,v:854: ERROR: group 5 failed with error = 0x8008000f
    ## 2          Module1.exe:Programg.cpp,v:880: ERROR: group 5 failed on its 3 retry
    ## 3                    Module2.exe:Execute:803: Information - Executing command 1
    ## 4                   Module2.exe:Execute:603: Information - command 1 completed.
    ## 5                    Module2.exe:Execute:803: Information - Executing command 2
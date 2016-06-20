To read in Websites we can use the `rvest` package.

    library(rvest) # use install.packages(rvest) if the package isn't installed yet

    ## Loading required package: xml2

Within ´rvest´ we can use `read_html` to fetch the HTML Code of an URL.
In this example we read the Wikipedia-Page of Bern, Switzerland to get
information on the local climate.

    url <- "https://de.wikipedia.org/wiki/Bern"
    raw<-read_html(url)

Within the HTML source code we now look for everything that is a table:

    html_table(roh,fill=TRUE,header=TRUE,dec=",")

We can see, that our climate table is the sixth table.

    climate_table <- html_table(raw,fill=TRUE,header=TRUE,dec=",")[[6]]

However, the read in data looks very messy, so we need to clean up:

    climate_table <- climate_table[1:2,1:13] # only keep relevant range

    library(reshape2)
    library(dplyr) # load dplyr and reshape2 for data wrangling functions

    ## 
    ## Attaching package: 'dplyr'

    ## Die folgenden Objekte sind maskiert von 'package:stats':
    ## 
    ##     filter, lag

    ## Die folgenden Objekte sind maskiert von 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

    # adjust column names
    names(climate_table)[1]<-"t"

    # reshape table to have observations being rows and variables being columns
    climate_table <- melt(climate_table)

    ## Using t as id variables

    # nicer labels
    climate_table$t <- substr(climate_table$t,1,3)

    # "t" contains two different information but tidy data demands us to only carry one information per column:
    climate_table <- dcast(climate_table,...~t)

    # clean up column labels
    names(climate_table)[1] <- "Month"

    # Names of month corresponding to systems locale
    climate_table$Month<-format(ISOdate(2000, 1:12, 1), "%B")

    climate_table

    ##        Month  Max  Min
    ## 1     Januar  2.8 -3.6
    ## 2    Februar  4.7 -3.1
    ## 3       März  9.5  0.2
    ## 4      April 13.4  3.0
    ## 5        Mai 18.2  7.4
    ## 6       Juni 21.6 10.5
    ## 7       Juli 24.3 12.5
    ## 8     August 23.7 12.3
    ## 9  September 19.1  8.9
    ## 10   Oktober 13.8  5.4
    ## 11  November  7.3  0.4
    ## 12  Dezember  3.5 -2.3



















# HowTo Get data from the EUROSTAT
## Author: A. Blejec

















```
## Warning: package 'Hmisc' was built under R version 3.0.3
## Warning: package 'lattice' was built under R version 3.0.3
```

## Introduction

To make reproducible reports from the EUROSTAT data, I will explore
the possibility to download data from their repository. They provide data in different formats, with footnotes and labels included or not included. The most promising for automatic retreival is the so called \bold{TSV} format which is in fact tab separated file. This file is included in the \file{.zip} or \file{.gz} file.

## Finding the file name

We will explore it later.

## Get the data

Asumming that we have the desired file code, we can get it by composing the
URL:


```r
fcode <- "tps00001"
lfn <- paste(fcode, ".tsv", sep = "")
upre <- "http://epp.eurostat.ec.europa.eu/NavTree_prod/everybody/BulkDownloadListing?file=data/"
upost <- ".tsv.gz"
furl <- paste(upre, fcode, upost, sep = "")
furl
```

```
## [1] "http://epp.eurostat.ec.europa.eu/NavTree_prod/everybody/BulkDownloadListing?file=data/tps00001.tsv.gz"
```


```r
temp <- tempfile()
download.file(furl, temp)
data <- read.table(gzfile(temp), sep = "\t", header = TRUE, row.names = 1, 
    na.strings = ": ")
unlink(temp)
```

Show the data structure


```r
str(data)
```

```
## 'data.frame':	43 obs. of  12 variables:
##  $ X2003: Factor w/ 43 levels "10142362 ","10192649 ",..: 14 41 20 3 40 38 37 2 42 31 ...
##  $ X2004: Factor w/ 43 levels "10116742 ","10195347 ",..: 14 41 20 3 40 38 37 2 42 31 ...
##  $ X2005: Factor w/ 43 levels "10097549 ","10198855 ",..: 14 41 20 3 40 38 37 2 42 31 ...
##  $ X2006: num  3149143 8254298 3842650 10511382 7718750 ...
##  $ X2007: Factor w/ 43 levels "10066158 ","10254233 ",..: 14 42 20 4 39 38 40 2 41 31 ...
##  $ X2008: Factor w/ 43 levels "10045401 ","10343422 ",..: 14 42 20 4 38 39 40 2 41 31 ...
##  $ X2009: num  3184701 8335003 3843998 10753080 7467119 ...
##  $ X2010: Factor w/ 42 levels "10014324 ","10462088 ",..: NA 41 19 4 37 38 40 2 39 30 ...
##  $ X2011: Factor w/ 43 levels "10486731 ","10572721 ",..: 12 40 18 3 36 38 41 1 39 30 ...
##  $ X2012: Factor w/ 42 levels "10505445 ","10542398 ",..: NA 39 17 3 35 37 40 1 38 29 ...
##  $ X2013: Factor w/ 41 levels "10487289 ","10516125 ",..: 11 38 NA 4 34 36 39 2 37 28 ...
##  $ X2014: Factor w/ 42 levels "10427301 e","10512419 ",..: 11 39 17 4 35 38 40 2 37 29 ...
```

First few lines


```r
head(data)
```

```
##            X2003     X2004     X2005    X2006     X2007     X2008
## JAN,AL  3102781   3119548   3134975   3149143  3152625   3170050 
## JAN,AT  8100273   8142573   8201359   8254298  8282984   8307989 
## JAN,BA 3830349 e 3837414 e 3842532 e  3842650  3844017   3843846 
## JAN,BE 10355844  10396421  10445852  10511382 10584534  10666866 
## JAN,BG  7845841   7801273   7761049   7718750 7572673 b  7518002 
## JAN,CH  7313853   7364148   7415102   7459128  7508739   7593494 
##           X2009     X2010      X2011     X2012     X2013      X2014
## JAN,AL  3184701      <NA>   2831741       <NA>  2898782   2895947 e
## JAN,AT  8335003  8351643    8375164   8408121   8451860    8507786 
## JAN,BA  3843998  3844046    3843183  3839265 p      <NA>  3830911 e
## JAN,BE 10753080 10839905  11000638 b 11094850  11161642  11203992 p
## JAN,BG  7467119  7421766    7369431   7327224   7284552    7245677 
## JAN,CH  7701856  7785806   7870134 b  7954662   8039060   8136689 p
```

## Clean the data

Some data contain labels and can be suffixed with a space. So we have to clean the data.


```r
X <- data
X <- as.data.frame(apply(X, 2, function(x) as.numeric(sapply(x, FUN = function(u) gsub("[[:alpha:][:blank:]]", 
    "", u)))))
dimnames(X)[[1]] <- dimnames(data)[[1]]
```

Show the structure

```r
str(X)
```

```
## 'data.frame':	43 obs. of  12 variables:
##  $ X2003: num  3102781 8100273 3830349 10355844 7845841 ...
##  $ X2004: num  3119548 8142573 3837414 10396421 7801273 ...
##  $ X2005: num  3134975 8201359 3842532 10445852 7761049 ...
##  $ X2006: num  3149143 8254298 3842650 10511382 7718750 ...
##  $ X2007: num  3152625 8282984 3844017 10584534 7572673 ...
##  $ X2008: num  3170050 8307989 3843846 10666866 7518002 ...
##  $ X2009: num  3184701 8335003 3843998 10753080 7467119 ...
##  $ X2010: num  NA 8351643 3844046 10839905 7421766 ...
##  $ X2011: num  2831741 8375164 3843183 11000638 7369431 ...
##  $ X2012: num  NA 8408121 3839265 11094850 7327224 ...
##  $ X2013: num  2898782 8451860 NA 11161642 7284552 ...
##  $ X2014: num  2895947 8507786 3830911 11203992 7245677 ...
```

and first few lines


```r
head(X)
```

```
##           X2003    X2004    X2005    X2006    X2007    X2008    X2009
## JAN,AL  3102781  3119548  3134975  3149143  3152625  3170050  3184701
## JAN,AT  8100273  8142573  8201359  8254298  8282984  8307989  8335003
## JAN,BA  3830349  3837414  3842532  3842650  3844017  3843846  3843998
## JAN,BE 10355844 10396421 10445852 10511382 10584534 10666866 10753080
## JAN,BG  7845841  7801273  7761049  7718750  7572673  7518002  7467119
## JAN,CH  7313853  7364148  7415102  7459128  7508739  7593494  7701856
##           X2010    X2011    X2012    X2013    X2014
## JAN,AL       NA  2831741       NA  2898782  2895947
## JAN,AT  8351643  8375164  8408121  8451860  8507786
## JAN,BA  3844046  3843183  3839265       NA  3830911
## JAN,BE 10839905 11000638 11094850 11161642 11203992
## JAN,BG  7421766  7369431  7327224  7284552  7245677
## JAN,CH  7785806  7870134  7954662  8039060  8136689
```
















## SessionInfo
{
Windows 7 x64 (build 7601) Service Pack 1 
R version 3.0.2 (2013-09-25)
Platform: x86_64-w64-mingw32/x64 (64-bit)

locale:
[1] LC_COLLATE=Slovenian_Slovenia.1250 
[2] LC_CTYPE=Slovenian_Slovenia.1250   
[3] LC_MONETARY=Slovenian_Slovenia.1250
[4] LC_NUMERIC=C                       
[5] LC_TIME=Slovenian_Slovenia.1250    

attached base packages:
[1] splines   grid      methods   stats     graphics  datasets 
[7] utils     grDevices base     

other attached packages:
[1] Hmisc_3.14-4    Formula_1.1-1   survival_2.37-7 lattice_0.20-27
[5] knitr_1.6      

loaded via a namespace (and not attached):
[1] cluster_1.14.4      evaluate_0.5.5      formatR_0.10       
[4] latticeExtra_0.6-26 RColorBrewer_1.0-5  stringr_0.6.2      
[7] tools_3.0.2        
Project path:\verb' D:/_Y/R/Rome '\\
Main file :\verb' ../doc/getEurostat.Rnw '

### View as vignette
Project files can be viewed by pasting this code to **`R`** console:\\
> projectName <-"Rome";  mainFile <-"getEurostat"


```r
commandArgs()
library(tkWidgets)
openPDF(file.path(dirname(getwd()),"doc",
paste(mainFile,"PDF",sep=".")))
viewVignette("viewVignette", projectName, #
file.path("../doc",paste(mainFile,"Rnw",sep=".")))
#
```



`Git Revision: ` gitCommitterUnixDate \gitAbbrevHash{} (\gitCommitterDate)} \hfill \copyright A. Blejec\\
` ` gitReferences} \hfill \verb'../doc/getEurostat.Rnw'\\

}






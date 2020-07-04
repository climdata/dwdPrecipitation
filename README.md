---
title: "Dwd Precipitation"
author: "Kmicha71"
date: "19 8 2019"
output:
  html_document: 
    keep_md: true
  pdf_document: default
---



## DWD Precipitation

Download the precipitation data from dwd (monthly & regional summary)



```sh

for month in "01" "02" "03" "04" "05" "06" "07" "08" "09" "10" "11" "12"
do
 file="regional_averages_rr_$month.txt"
 echo "Download file: $file"
 [ -f ./download/$file ] && mv -f ./download/$file ./download/$file.bck
 wget -q -P download https://opendata.dwd.de/climate_environment/CDC/regional_averages_DE/monthly/precipitation/$file
 ## Remove first line !!
 tail -n +2 ./download/$file > ./download/$file.tmp && mv ./download/$file.tmp ./download/$file
done
```

```
## Download file: regional_averages_rr_01.txt
## Download file: regional_averages_rr_02.txt
## Download file: regional_averages_rr_03.txt
## Download file: regional_averages_rr_04.txt
## Download file: regional_averages_rr_05.txt
## Download file: regional_averages_rr_06.txt
## Download file: regional_averages_rr_07.txt
## Download file: regional_averages_rr_08.txt
## Download file: regional_averages_rr_09.txt
## Download file: regional_averages_rr_10.txt
## Download file: regional_averages_rr_11.txt
## Download file: regional_averages_rr_12.txt
```



```r
prec <- read.csv("./download/regional_averages_rr_01.txt", sep=";")

for (month in c("02","03","04","05","06","07","08","09","10","11","12")){
  file <- paste("./download/regional_averages_rr_", month, ".txt", sep="")
  print(paste("Reading file:", file))
  tmp <- read.csv(file, sep=";")
  prec <- rbind(prec, tmp)
}
```

```
## [1] "Reading file: ./download/regional_averages_rr_02.txt"
## [1] "Reading file: ./download/regional_averages_rr_03.txt"
## [1] "Reading file: ./download/regional_averages_rr_04.txt"
## [1] "Reading file: ./download/regional_averages_rr_05.txt"
## [1] "Reading file: ./download/regional_averages_rr_06.txt"
## [1] "Reading file: ./download/regional_averages_rr_07.txt"
## [1] "Reading file: ./download/regional_averages_rr_08.txt"
## [1] "Reading file: ./download/regional_averages_rr_09.txt"
## [1] "Reading file: ./download/regional_averages_rr_10.txt"
## [1] "Reading file: ./download/regional_averages_rr_11.txt"
## [1] "Reading file: ./download/regional_averages_rr_12.txt"
```

```r
prec <- prec[order(prec$Jahr, prec$Monat),]
names(prec)[names(prec) == "Jahr"] <- "year"
names(prec)[names(prec) == "Monat"] <- "month"
prec$ts <- signif(prec$year + (prec$month-0.5)/12, digits=6)
prec$time <- paste(prec$year,prec$month, '15 00:00:00', sep='-')
prec$X <- NULL

write.table(prec, file = "csv/monthly_precipitation_de.csv", append = FALSE, quote = TRUE, sep = ",",
            eol = "\n", na = "NA", dec = ".", row.names = FALSE,
            col.names = TRUE, qmethod = "escape", fileEncoding = "UTF-8")
```


## Plot Precipitation Data


```r
require("ggplot2")
```

```
## Loading required package: ggplot2
```

```r
mp <- ggplot() +
      geom_line(aes(y=prec$Deutschland, x=prec$ts), color="blue") +
      xlab("Year") + ylab("Precipitation [mm]")
mp
```

![](README_files/figure-html/plot-1.png)<!-- -->

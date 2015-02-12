---
title: "hw2"
output: html_document
---

Find all HCV gene expression data using the Illumina platform submitted by an investigator at Yale. 

```r
library(GEOmetadb)
```

```
## Loading required package: GEOquery
## Setting options('download.file.method.GEOquery'='auto')
```

```r
geo_con <- dbConnect(SQLite(),'GEOmetadb.sqlite')
description1<-dbGetQuery(geo_con, "SELECT gse.title, gse.gse, gpl.gpl, gpl.manufacturer, gpl.title FROM (gse JOIN gse_gpl ON gse.gse=gse_gpl.gse) j JOIN gpl ON j.gpl=gpl.gpl WHERE gse.Title LIKE '%HCV%' AND gse.contact LIKE '%Yale%' AND gpl.manufacturer LIKE '%Illumina%';")
description1
```

```
##                                                            gse.title
## 1 The blood transcriptional signature of chronic HCV [Illumina data]
## 2                 The blood transcriptional signature of chronic HCV
##    gse.gse  gpl.gpl gpl.manufacturer
## 1 GSE40223 GPL10558    Illumina Inc.
## 2 GSE40224 GPL10558    Illumina Inc.
##                                      gpl.title
## 1 Illumina HumanHT-12 V4.0 expression beadchip
## 2 Illumina HumanHT-12 V4.0 expression beadchip
```

Reproduce using data.table

```r
library(data.table)
```

```
## data.table 1.9.4  For help type: ?data.table
## *** NB: by=.EACHI is now explicit. See README to restore previous behaviour.
## 
## Attaching package: 'data.table'
## 
## The following object is masked _by_ '.GlobalEnv':
## 
##     .N
```

```r
dgse<-data.table(dbGetQuery(geo_con, "SELECT * FROM gse"),key='gse')
dgse_gpl<-data.table(dbGetQuery(geo_con, "SELECT * FROM gse_gpl"),key='gse')
join<-data.table(dgse[dgse_gpl],key='gpl')
dgpl<-data.table(dbGetQuery(geo_con, "SELECT * FROM gpl"),key='gpl')
description2<-dgpl[join][i.title %like% 'HCV' & i.contact %like% 'Yale' & manufacturer %like% 'Illumina'][,list(i.title,gse,gpl,manufacturer,title)]
description2
```

```
##                                                               i.title
## 1: The blood transcriptional signature of chronic HCV [Illumina data]
## 2:                 The blood transcriptional signature of chronic HCV
##         gse      gpl  manufacturer
## 1: GSE40223 GPL10558 Illumina Inc.
## 2: GSE40224 GPL10558 Illumina Inc.
##                                           title
## 1: Illumina HumanHT-12 V4.0 expression beadchip
## 2: Illumina HumanHT-12 V4.0 expression beadchip
```



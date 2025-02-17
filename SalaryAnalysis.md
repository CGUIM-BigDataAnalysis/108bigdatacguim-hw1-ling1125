108-2 大數據分析方法 作業一
================
yu-ling chen

搞不清楚各行各業的薪資差異嗎? 念研究所到底對第一份工作的薪資影響有多大? CP值高嗎? 透過分析**初任人員平均經常性薪資**-
（107年）<https://data.gov.tw/dataset/6647>
（104-105年）<http://ipgod.nchc.org.tw/dataset/a17000000j-020066>
，可初步了解台灣近幾年各行各業、各學歷的起薪。

## 比較104年度和107年度大學畢業者的薪資資料

### 資料匯入與處理

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
library(readr)
X104 <- read_csv("C:/Users/Yu Ling/Desktop/104.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   年度 = col_double(),
    ##   大職業別 = col_character(),
    ##   `經常性薪資-薪資` = col_double(),
    ##   `經常性薪資-女/男` = col_character(),
    ##   `國中及以下-薪資` = col_character(),
    ##   `國中及以下-女/男` = col_character(),
    ##   `高中或高職-薪資` = col_character(),
    ##   `高中或高職-女/男` = col_character(),
    ##   `專科-薪資` = col_character(),
    ##   `專科-女/男` = col_character(),
    ##   `大學-薪資` = col_character(),
    ##   `大學-女/男` = col_character(),
    ##   `研究所及以上-薪資` = col_character(),
    ##   `研究所及以上-女/男` = col_character()
    ## )

``` r
X107 <- read_csv("C:/Users/Yu Ling/Desktop/107.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   年度 = col_double(),
    ##   大職業別 = col_character(),
    ##   `經常性薪資-薪資` = col_character(),
    ##   `經常性薪資-女/男` = col_character(),
    ##   `國中及以下-薪資` = col_character(),
    ##   `國中及以下-女/男` = col_character(),
    ##   `高中或高職-薪資` = col_character(),
    ##   `高中或高職-女/男` = col_character(),
    ##   `專科-薪資` = col_character(),
    ##   `專科-女/男` = col_character(),
    ##   `大學-薪資` = col_character(),
    ##   `大學-女/男` = col_character(),
    ##   `研究所-薪資` = col_character(),
    ##   `研究所-女/男` = col_character()
    ## )

### 107年度薪資較104年度薪資高的職業有哪些?

``` r
x104clean <- X104[,c(2,11)]
x107clean <- X107[,c(2,11)]
x104clean$大職業別<-gsub("部門", "", x104clean$大職業別)
x104clean$大職業別<-gsub("、", "_", x104clean$大職業別)
x104clean$大職業別<-gsub("教育服務業", "教育業", x104clean$大職業別)
x104clean$大職業別<-gsub("醫療保健服務業", "醫療保健業", x104clean$大職業別)
x104clean$大職業別<-gsub("資訊及通訊傳播業", "出版、影音製作、傳播及資通訊服務業", x104clean$大職業別)
x104clean$大職業別<-gsub("營造業", "營建工程", x104clean$大職業別)
Wage <- inner_join(x104clean,x107clean,by="大職業別")
names(Wage)[2] <- "104年大學畢業薪資"
names(Wage)[3] <- "107年大學畢業薪資"
Wage$`104年大學畢業薪資` <- gsub("—|…","",Wage$`104年大學畢業薪資`)
Wage$`107年大學畢業薪資` <- gsub("—|…","",Wage$`107年大學畢業薪資`)
Wage$`104年大學畢業薪資` <- as.numeric(Wage$`104年大學畢業薪資`)
Wage$`107年大學畢業薪資` <- as.numeric(Wage$`107年大學畢業薪資`)
Wage$Rate <- Wage$`107年大學畢業薪資`/Wage$`104年大學畢業薪資`
WageDT <- data.frame(subset(Wage,Wage$Rate>1))
WageDT_desc<-arrange(WageDT,desc(Rate))
WageDT_10<-head(WageDT_desc,10)
knitr::kable(WageDT_10[,c(1,4)])
```

| 大職業別                   |     Rate |
| :--------------------- | -------: |
| 專業\_科學及技術服務業-服務及銷售工作人員 | 1.158908 |
| 教育業-服務及銷售工作人員          | 1.132338 |
| 不動產業-技術員及助理專業人員        | 1.130807 |
| 住宿及餐飲業-服務及銷售工作人員       | 1.108842 |
| 藝術\_娛樂及休閒服務業-事務支援人員    | 1.097632 |
| 金融及保險業-技藝\_機械設備操作及組裝人員 | 1.097194 |
| 教育業                    | 1.096177 |
| 教育業-事務支援人員             | 1.095800 |
| 用水供應及污染整治業-專業人員        | 1.086330 |
| 教育業-專業人員               | 1.086267 |

文字說明：
根據上面的統計顯示，107比104薪資增加幅度最大的是專業\_科學及技術服務業-服務及銷售工作人員，大約漲幅了15%，而在前10名漲幅最大的職業中，幅度最小的教育業-專業人員也有8%的增加率。可以發現107比104薪資高的職業大多為【機器不可取代產業】，在這個資訊越來越發達的時代，機器的發明往往越來越能取代人們的一些職業，相對來說，機器無法取代的職業會更加稀少，進而使薪資越來越提升。以薪資增加率前10名為例，服務及銷售人員的比例相對較高，因為這項工作是需要人類情感的付出，設身處地的為客戶著想，才能有更好的銷售量，就現在的發展而言，機器是做不到情感展現的部分，所以這方面目前還是屬於機器無法取代的職業，薪資也會相對提升。

### 提高超過5%的的職業有哪些?

``` r
WageDT5 <- data.frame(subset(Wage,Wage$Rate>1.05))
names(WageDT5)[1] <- "職業名稱"
names(WageDT5)[4] <- "薪資差異"
knitr::kable(WageDT5[,c(1,4)])
```

| 職業名稱                         |   薪資差異   |
| :--------------------------- | :------: |
| 工業及服務業-服務及銷售工作人員             | 1.066587 |
| 工業-專業人員                      | 1.052347 |
| 工業-技術員及助理專業人員                | 1.053411 |
| 工業-技藝\_機械設備操作及組裝人員           | 1.050328 |
| 製造業-專業人員                     | 1.052507 |
| 製造業-技術員及助理專業人員               | 1.055015 |
| 電力及燃氣供應業-技藝\_機械設備操作及組裝人員     | 1.071044 |
| 用水供應及污染整治業                   | 1.051960 |
| 用水供應及污染整治業-專業人員              | 1.086330 |
| 用水供應及污染整治業-技藝\_機械設備操作及組裝人員   | 1.072507 |
| 營建工程                         | 1.050670 |
| 營建工程-專業人員                    | 1.060715 |
| 營建工程-事務支援人員                  | 1.056960 |
| 營建工程-服務及銷售工作人員               | 1.071877 |
| 服務業-服務及銷售工作人員                | 1.069120 |
| 批發及零售業-服務及銷售工作人員             | 1.052818 |
| 運輸及倉儲業                       | 1.056260 |
| 運輸及倉儲業-技術員及助理專業人員            | 1.070003 |
| 運輸及倉儲業-事務支援人員                | 1.060240 |
| 運輸及倉儲業-服務及銷售工作人員             | 1.054221 |
| 運輸及倉儲業-技藝\_機械設備操作及組裝人員       | 1.065723 |
| 住宿及餐飲業                       | 1.081297 |
| 住宿及餐飲業-事務支援人員                | 1.070685 |
| 住宿及餐飲業-服務及銷售工作人員             | 1.108842 |
| 住宿及餐飲業-技藝\_機械設備操作及組裝人員       | 1.062300 |
| 出版、影音製作、傳播及資通訊服務業            | 1.060594 |
| 出版、影音製作、傳播及資通訊服務業-專業人員       | 1.082141 |
| 出版、影音製作、傳播及資通訊服務業-技術員及助理專業人員 | 1.052421 |
| 出版、影音製作、傳播及資通訊服務業-事務支援人員     | 1.067475 |
| 出版、影音製作、傳播及資通訊服務業-服務及銷售工作人員  | 1.052499 |
| 金融及保險業                       | 1.053951 |
| 金融及保險業-專業人員                  | 1.052928 |
| 金融及保險業-技術員及助理專業人員            | 1.069093 |
| 金融及保險業-服務及銷售工作人員             | 1.068089 |
| 金融及保險業-技藝\_機械設備操作及組裝人員       | 1.097194 |
| 不動產業                         | 1.066421 |
| 不動產業-技術員及助理專業人員              | 1.130807 |
| 不動產業-事務支援人員                  | 1.053410 |
| 不動產業-服務及銷售工作人員               | 1.062117 |
| 不動產業-技藝\_機械設備操作及組裝人員         | 1.063403 |
| 專業\_科學及技術服務業-服務及銷售工作人員       | 1.158908 |
| 支援服務業-服務及銷售工作人員              | 1.060903 |
| 教育業                          | 1.096177 |
| 教育業-專業人員                     | 1.086267 |
| 教育業-事務支援人員                   | 1.095800 |
| 教育業-服務及銷售工作人員                | 1.132338 |
| 醫療保健業-服務及銷售工作人員              | 1.057476 |
| 藝術\_娛樂及休閒服務業                 | 1.059809 |
| 藝術\_娛樂及休閒服務業-專業人員            | 1.056577 |
| 藝術\_娛樂及休閒服務業-技術員及助理專業人員      | 1.051427 |
| 藝術\_娛樂及休閒服務業-事務支援人員          | 1.097632 |
| 藝術\_娛樂及休閒服務業-技藝\_機械設備操作及組裝人員 | 1.055985 |
| 其他服務業-服務及銷售工作人員              | 1.056991 |

### 主要的職業種別是哪些種類呢?

``` r
WageDT5_split <- strsplit (WageDT5[[1]],"-")
for (i in 1:53) {
  WageDT5_split[i] <- WageDT5_split[[i]][1]
}
WageDT5_split = as.vector(unlist(WageDT5_split))
knitr::kable(sort(table(WageDT5_split), decreasing = T))
```

| WageDT5\_split    | Freq |
| :---------------- | ---: |
| 不動產業              |    5 |
| 出版、影音製作、傳播及資通訊服務業 |    5 |
| 金融及保險業            |    5 |
| 運輸及倉儲業            |    5 |
| 藝術\_娛樂及休閒服務業      |    5 |
| 住宿及餐飲業            |    4 |
| 教育業               |    4 |
| 營建工程              |    4 |
| 工業                |    3 |
| 用水供應及污染整治業        |    3 |
| 製造業               |    2 |
| 工業及服務業            |    1 |
| 支援服務業             |    1 |
| 批發及零售業            |    1 |
| 其他服務業             |    1 |
| 服務業               |    1 |
| 專業\_科學及技術服務業      |    1 |
| 電力及燃氣供應業          |    1 |
| 醫療保健業             |    1 |

## 男女同工不同酬現況分析

男女同工不同酬一直是性別平等中很重要的問題，分析資料來源為103到106年度的大學畢業薪資。

### 104和107年度的大學畢業薪資資料，哪些行業男生薪資比女生薪資多?

``` r
x104_gender <- X104[,c(2,12)]
x104_gender$`大學-女/男` <- gsub("—|…","",x104_gender$`大學-女/男`)
x104_gender$`大學-女/男` <- as.numeric(x104_gender$`大學-女/男`)
Boy_Wage_high_104 <- arrange(x104_gender,`大學-女/男`)
Boy_Wage_high_DT104 <- data.frame(subset(Boy_Wage_high_104,`大學-女/男` < 100))
Boy_Wage_high_DT104_10<-head(Boy_Wage_high_DT104,10)
knitr::kable(Boy_Wage_high_DT104_10)
```

| 大職業別                      | 大學.女.男 |
| :------------------------ | :----: |
| 電力及燃氣供應業-技藝、機械設備操作及組裝人員   | 91.69  |
| 教育服務業-服務及銷售工作人員           | 91.90  |
| 礦業及土石採取業-技術員及助理專業人員       | 92.42  |
| 礦業及土石採取業-技藝、機械設備操作及組裝人員   | 93.10  |
| 礦業及土石採取業                  | 95.28  |
| 其他服務業-事務支援人員              | 95.47  |
| 營造業-技藝、機械設備操作及組裝人員        | 95.64  |
| 用水供應及污染整治業-技藝、機械設備操作及組裝人員 | 95.90  |
| 營造業                       | 96.35  |
| 教育服務業                     | 96.44  |

``` r
x107_gender <- X107[,c(2,12)]
x107_gender$`大學-女/男` <- gsub("—|…","",x107_gender$`大學-女/男`)
x107_gender$`大學-女/男` <- as.numeric(x107_gender$`大學-女/男`)
Boy_Wage_high_107 <- arrange(x107_gender,`大學-女/男`)
Boy_Wage_high_DT107 <- data.frame(subset(Boy_Wage_high_107,`大學-女/男` < 100))
Boy_Wage_high_DT107_10<-head(Boy_Wage_high_DT107,10)
knitr::kable(Boy_Wage_high_DT107_10)
```

| 大職業別                 | 大學.女.男 |
| :------------------- | :----: |
| 礦業及土石採取業-專業人員        | 96.02  |
| 電力及燃氣供應業-事務支援人員      | 96.96  |
| 礦業及土石採取業             | 97.11  |
| 營建工程                 | 97.52  |
| 教育業-事務支援人員           | 97.61  |
| 電力及燃氣供應業-服務及銷售工作人員   | 97.73  |
| 營建工程-技術員及助理專業人員      | 97.78  |
| 用水供應及污染整治業-專業人員      | 97.81  |
| 用水供應及污染整治業-服務及銷售工作人員 | 97.94  |
| 電力及燃氣供應業-專業人員        | 98.23  |

### 哪些行業女生薪資比男生薪資多?

``` r
Girl_Wage_high_104 <- arrange(x104_gender,`大學-女/男`)
Girl_Wage_high_DT104 <- data.frame(subset(Girl_Wage_high_104,`大學-女/男` > 100))
Girl_Wage_high_DT104_10<-head(Girl_Wage_high_DT104,10)
knitr::kable(Girl_Wage_high_DT104_10)
```

| 大職業別                       | 大學.女.男 |
| :------------------------- | :----: |
| 專業、科學及技術服務業-技藝、機械設備操作及組裝人員 | 100.26 |

``` r
Girl_Wage_high_107 <- arrange(x107_gender,`大學-女/男`)
Girl_Wage_high_DT107 <- data.frame(subset(Girl_Wage_high_107,`大學-女/男` > 100))
Girl_Wage_high_DT107_10<-head(Girl_Wage_high_DT107,10)
knitr::kable(Girl_Wage_high_DT107_10)
```

| 大職業別 | 大學.女.男 |
| :--- | -----: |

文字說明：
根據上面的統計顯示，男女同工不同酬一直是性別平等中很重要的問題，就連現在也無法完全去除這部分的不平等。不論是在104年還是107年，女生薪資大於男生的職業幾乎是不存在的，而男女生薪資差異不大的也佔少數，幾乎的職業都是男生薪資大於女生，可見得這種情形依然相當普遍。

## 研究所薪資差異

以107年度的資料來看，哪個職業別念研究所最划算呢 (研究所學歷薪資與大學學歷薪資增加比例最多)?

``` r
education_Wage <- X107[,c(2,11,13)]
names(education_Wage)[2] <- "107年大學畢業薪資"
names(education_Wage)[3] <- "107年研究所畢業薪資"
education_Wage$`107年研究所畢業薪資` <- gsub("—|…","",education_Wage$`107年研究所畢業薪資`)
education_Wage$`107年大學畢業薪資` <- gsub("—|…","",education_Wage$`107年大學畢業薪資`)
education_Wage$`107年研究所畢業薪資` <- as.numeric(education_Wage$`107年研究所畢業薪資`)
education_Wage$`107年大學畢業薪資` <- as.numeric(education_Wage$`107年大學畢業薪資`)
education_Wage$Rate107 <- education_Wage$`107年研究所畢業薪資`/education_Wage$`107年大學畢業薪資`
education_WageDT <- arrange(education_Wage,desc(Rate107))
education_WageDT10 <- head(education_WageDT,10)
knitr::kable(education_WageDT10[,c(1,4)])
```

| 大職業別                   |  Rate107 |
| :--------------------- | -------: |
| 其他服務業                  | 1.237694 |
| 專業\_科學及技術服務業           | 1.205362 |
| 專業\_科學及技術服務業-專業人員      | 1.204652 |
| 教育業-事務支援人員             | 1.189864 |
| 出版、影音製作、傳播及資通訊服務業      | 1.183921 |
| 其他服務業-事務支援人員           | 1.180548 |
| 出版、影音製作、傳播及資通訊服務業-專業人員 | 1.179958 |
| 製造業                    | 1.178580 |
| 工業                     | 1.174801 |
| 工業及服務業                 | 1.174391 |

文字說明：
根據上面的統計顯示，在107年中，研究所學歷的薪資比大學學歷薪資增加較多的10個職業大多為技術員或是專業人員，這一類的專業，往往是大學四年中就難完全學習到的，幾乎都是要藉由研究所的歷練來更深究職業的技巧，所以相對來說，研究所學歷的薪資會比大學學歷高出許多，甚至是接近一倍的薪水。

## 我有興趣的職業別薪資狀況分析

### 有興趣的職業別篩選，呈現薪資

``` r
Mine_interest <- filter(education_Wage,大職業別 %in% c("服務業-技術員及助理專業人員","金融及保險業-專業人員",
  "金融及保險業-技術員及助理專業人員","教育業-專業人員")) %>% select(c(1,2,3,4))
knitr::kable(Mine_interest)
```

| 大職業別              | 107年大學畢業薪資 | 107年研究所畢業薪資 |  Rate107 |
| :---------------- | :--------: | :---------: | -------: |
| 服務業-技術員及助理專業人員    |   29773    |    33229    | 1.116078 |
| 金融及保險業-專業人員       |   35490    |    39868    | 1.123359 |
| 金融及保險業-技術員及助理專業人員 |   32602    |    36312    | 1.113797 |
| 教育業-專業人員          |   28911    |    30025    | 1.038532 |

### 這些職業別研究所薪資與大學薪資差多少呢？

``` r
Mine_interest$"薪資差異" <- Mine_interest$`107年研究所畢業薪資`-Mine_interest$`107年大學畢業薪資`
knitr::kable(Mine_interest)
```

| 大職業別              | 107年大學畢業薪資 | 107年研究所畢業薪資 |  Rate107 | 薪資差異 |
| :---------------- | :--------: | :---------: | -------: | :--: |
| 服務業-技術員及助理專業人員    |   29773    |    33229    | 1.116078 | 3456 |
| 金融及保險業-專業人員       |   35490    |    39868    | 1.123359 | 4378 |
| 金融及保險業-技術員及助理專業人員 |   32602    |    36312    | 1.113797 | 3710 |
| 教育業-專業人員          |   28911    |    30025    | 1.038532 | 1114 |

文字說明：
根據上面的統計顯示，金融與保險業的薪資差異較大為4378，相反的教育業的差異非常小，只有1114，雖然差異的排名跟我想像得差不多但沒想到教育業的差異竟然如此不顯著，原本在這幾個行業中，我最傾向於金融及保險業，恰巧他的研究所與達學的薪資差異是相較於其他我有興趣的職業還要大，所以我依然會選擇念研究所，未來如果有機會，我想我願意嘗試接觸金融業的意願可能會比也會因而提升。

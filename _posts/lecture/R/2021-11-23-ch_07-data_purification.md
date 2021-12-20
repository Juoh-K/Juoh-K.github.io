---
layout: single
title:  "쉽게 배우는 R 데이터분석 Ch.7"
categories: R
tag: [R, 결측치 정제, 이상치 정제]
toc: true  # 목차 자동 생성 기능
toc_sticky: True # 목차 위치 고정 
toc_label : "Contents" # 목차명
author_profile: true # 포스트 내 주인장 프로파일 끄기(false)/켜기(true)
# sidebar:
#     nav: "docs" # 작성위치 : _data/naigation.yml
search: true # 검색창에서 검색 제외(false) / 승인(true)
---

## 7장. 데이터 정제



### 07-1 결측치 정제하기



```
# 임의의 결측치 있는 데이터 만들기
df = data.frame(sex = c("M", "F", NA, "M", "F"),
                score = c(5, 4, 3, 4, NA))
df
##    sex score
## 1    M     5
## 2    F     4
## 3 <NA>     3
## 4    M     4
## 5    F    NA
```



```
# 결측치 확인하기
is.na(df) # retrun값이 true 이면 결측치가 있음
##        sex score
## [1,] FALSE FALSE
## [2,] FALSE FALSE
## [3,]  TRUE FALSE
## [4,] FALSE FALSE
## [5,] FALSE  TRUE
table(is.na(df)) # 전체 결측치 개수 확인
## 
## FALSE  TRUE 
##     8     2
table(is.na(df$sex)) # sex 결측치 빈도 확인
## 
## FALSE  TRUE 
##     4     1
table(is.na(df$score)) # score 결측치 빈도 확인
## 
## FALSE  TRUE 
##     4     1
mean(df$score) # 결측치가 포함된 데이터 연산시 연산 안됨. NA출력됨됨
## [1] NA
sum(df$score) # 결측치가 포함된 데이터 연산시 연산 안됨. NA출력됨됨
## [1] NA
```



```
# 결측치 제거하기
library(dplyr)
## 
## 다음의 패키지를 부착합니다: 'dplyr'
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
df %>% filter((is.na(score)))
##   sex score
## 1   F    NA
df %>% filter(!is.na(score))
##    sex score
## 1    M     5
## 2    F     4
## 3 <NA>     3
## 4    M     4
df_nomiss = df %>% filter(!is.na(score))

mean(df_nomiss$score)
## [1] 4
sum(df_nomiss$score)
## [1] 16
```



```
# 여러 변수 동시에 결측치 없는 데이터 추출하기
df_nomiss = df %>% 
  filter(!is.na(score) & !is.na(sex))
df_nomiss
##   sex score
## 1   M     5
## 2   F     4
## 3   M     4
```



```
# 결측치가 하나라도 있으면 제거하기 ( na.omit() )
df_nomiss2 = na.omit(df)
df_nomiss2
##   sex score
## 1   M     5
## 2   F     4
## 4   M     4
```



```
# 함수내의 결측치 제거기능 사용하기 ( na.rm = T )
mean(df$score, na.rm = T) # 결측치 제외하고 평균 산출출
## [1] 4
sum(df$score, na.rm = T)
## [1] 16
```



```
# summarise() 이용하여 통계량 산출할 때도, na.rm 적용 가능
exam = read.csv("./../Dataset/Data/csv_exam.csv")
exam[c(3, 8, 15), "math"] = NA # 강제로 3, 8, 15행의 math 값에 NA 할당
```



```
# math 에 결측치가 있으므로 NA출력됨
exam %>% summarise(mean_math = mean(math)) # math 평균 산출
##   mean_math
## 1        NA
```



```
# mean()에 na.rm = T 적용, 결측치 제외하고 평균 산출
exam %>% summarise(mean_math = mean(math, na.rm = T))
##   mean_math
## 1  55.23529
```



```
# 결측치 대체하기
mean(exam$math, na.rm = T)
## [1] 55.23529
```



```
exam$math = ifelse(is.na(exam$math), 55, exam$math) # 평균값 확인후 대체
table(is.na(exam$math))
## 
## FALSE 
##    20
exam
##    id class math english science
## 1   1     1   50      98      50
## 2   2     1   60      97      60
## 3   3     1   55      86      78
## 4   4     1   30      98      58
## 5   5     2   25      80      65
## 6   6     2   50      89      98
## 7   7     2   80      90      45
## 8   8     2   55      78      25
## 9   9     3   20      98      15
## 10 10     3   50      98      45
## 11 11     3   65      65      65
## 12 12     3   45      85      32
## 13 13     4   46      98      65
## 14 14     4   48      87      12
## 15 15     4   55      56      78
## 16 16     4   58      98      65
## 17 17     5   65      68      98
## 18 18     5   80      78      90
## 19 19     5   89      68      87
## 20 20     5   78      83      58
```



```
mean(exam$math) # 평균값 출력 확인 (결측치를 없엔 후 출력값 정상적으로 나옴)
## [1] 55.2
```



### 혼자서 해보기



```
# mpg 데이터에 이상치 값 생성
mpg = as.data.frame(ggplot2::mpg)
mpg_new = mpg
mpg_new[c(65, 124, 131, 153, 212), "hwy"] = NA
mpg_new
##     manufacturer                  model displ year cyl      trans drv cty hwy
## 1           audi                     a4   1.8 1999   4   auto(l5)   f  18  29
## 2           audi                     a4   1.8 1999   4 manual(m5)   f  21  29
## 3           audi                     a4   2.0 2008   4 manual(m6)   f  20  31
## 4           audi                     a4   2.0 2008   4   auto(av)   f  21  30
## 5           audi                     a4   2.8 1999   6   auto(l5)   f  16  26
## 6           audi                     a4   2.8 1999   6 manual(m5)   f  18  26
## 7           audi                     a4   3.1 2008   6   auto(av)   f  18  27
## 8           audi             a4 quattro   1.8 1999   4 manual(m5)   4  18  26
## 9           audi             a4 quattro   1.8 1999   4   auto(l5)   4  16  25
## 10          audi             a4 quattro   2.0 2008   4 manual(m6)   4  20  28
## 11          audi             a4 quattro   2.0 2008   4   auto(s6)   4  19  27
## 12          audi             a4 quattro   2.8 1999   6   auto(l5)   4  15  25
## 13          audi             a4 quattro   2.8 1999   6 manual(m5)   4  17  25
## 14          audi             a4 quattro   3.1 2008   6   auto(s6)   4  17  25
## 15          audi             a4 quattro   3.1 2008   6 manual(m6)   4  15  25
## 16          audi             a6 quattro   2.8 1999   6   auto(l5)   4  15  24
## 17          audi             a6 quattro   3.1 2008   6   auto(s6)   4  17  25
## 18          audi             a6 quattro   4.2 2008   8   auto(s6)   4  16  23
## 19     chevrolet     c1500 suburban 2wd   5.3 2008   8   auto(l4)   r  14  20
## 20     chevrolet     c1500 suburban 2wd   5.3 2008   8   auto(l4)   r  11  15
## 21     chevrolet     c1500 suburban 2wd   5.3 2008   8   auto(l4)   r  14  20
## 22     chevrolet     c1500 suburban 2wd   5.7 1999   8   auto(l4)   r  13  17
## 23     chevrolet     c1500 suburban 2wd   6.0 2008   8   auto(l4)   r  12  17
## 24     chevrolet               corvette   5.7 1999   8 manual(m6)   r  16  26
## 25     chevrolet               corvette   5.7 1999   8   auto(l4)   r  15  23
## 26     chevrolet               corvette   6.2 2008   8 manual(m6)   r  16  26
## 27     chevrolet               corvette   6.2 2008   8   auto(s6)   r  15  25
## 28     chevrolet               corvette   7.0 2008   8 manual(m6)   r  15  24
## 29     chevrolet        k1500 tahoe 4wd   5.3 2008   8   auto(l4)   4  14  19
## 30     chevrolet        k1500 tahoe 4wd   5.3 2008   8   auto(l4)   4  11  14
## 31     chevrolet        k1500 tahoe 4wd   5.7 1999   8   auto(l4)   4  11  15
## 32     chevrolet        k1500 tahoe 4wd   6.5 1999   8   auto(l4)   4  14  17
## 33     chevrolet                 malibu   2.4 1999   4   auto(l4)   f  19  27
## 34     chevrolet                 malibu   2.4 2008   4   auto(l4)   f  22  30
## 35     chevrolet                 malibu   3.1 1999   6   auto(l4)   f  18  26
## 36     chevrolet                 malibu   3.5 2008   6   auto(l4)   f  18  29
## 37     chevrolet                 malibu   3.6 2008   6   auto(s6)   f  17  26
## 38         dodge            caravan 2wd   2.4 1999   4   auto(l3)   f  18  24
## 39         dodge            caravan 2wd   3.0 1999   6   auto(l4)   f  17  24
## 40         dodge            caravan 2wd   3.3 1999   6   auto(l4)   f  16  22
## 41         dodge            caravan 2wd   3.3 1999   6   auto(l4)   f  16  22
## 42         dodge            caravan 2wd   3.3 2008   6   auto(l4)   f  17  24
## 43         dodge            caravan 2wd   3.3 2008   6   auto(l4)   f  17  24
## 44         dodge            caravan 2wd   3.3 2008   6   auto(l4)   f  11  17
## 45         dodge            caravan 2wd   3.8 1999   6   auto(l4)   f  15  22
## 46         dodge            caravan 2wd   3.8 1999   6   auto(l4)   f  15  21
## 47         dodge            caravan 2wd   3.8 2008   6   auto(l6)   f  16  23
## 48         dodge            caravan 2wd   4.0 2008   6   auto(l6)   f  16  23
## 49         dodge      dakota pickup 4wd   3.7 2008   6 manual(m6)   4  15  19
## 50         dodge      dakota pickup 4wd   3.7 2008   6   auto(l4)   4  14  18
## 51         dodge      dakota pickup 4wd   3.9 1999   6   auto(l4)   4  13  17
## 52         dodge      dakota pickup 4wd   3.9 1999   6 manual(m5)   4  14  17
## 53         dodge      dakota pickup 4wd   4.7 2008   8   auto(l5)   4  14  19
## 54         dodge      dakota pickup 4wd   4.7 2008   8   auto(l5)   4  14  19
## 55         dodge      dakota pickup 4wd   4.7 2008   8   auto(l5)   4   9  12
## 56         dodge      dakota pickup 4wd   5.2 1999   8 manual(m5)   4  11  17
## 57         dodge      dakota pickup 4wd   5.2 1999   8   auto(l4)   4  11  15
## 58         dodge            durango 4wd   3.9 1999   6   auto(l4)   4  13  17
## 59         dodge            durango 4wd   4.7 2008   8   auto(l5)   4  13  17
## 60         dodge            durango 4wd   4.7 2008   8   auto(l5)   4   9  12
## 61         dodge            durango 4wd   4.7 2008   8   auto(l5)   4  13  17
## 62         dodge            durango 4wd   5.2 1999   8   auto(l4)   4  11  16
## 63         dodge            durango 4wd   5.7 2008   8   auto(l5)   4  13  18
## 64         dodge            durango 4wd   5.9 1999   8   auto(l4)   4  11  15
## 65         dodge    ram 1500 pickup 4wd   4.7 2008   8 manual(m6)   4  12  NA
## 66         dodge    ram 1500 pickup 4wd   4.7 2008   8   auto(l5)   4   9  12
## 67         dodge    ram 1500 pickup 4wd   4.7 2008   8   auto(l5)   4  13  17
## 68         dodge    ram 1500 pickup 4wd   4.7 2008   8   auto(l5)   4  13  17
## 69         dodge    ram 1500 pickup 4wd   4.7 2008   8 manual(m6)   4  12  16
## 70         dodge    ram 1500 pickup 4wd   4.7 2008   8 manual(m6)   4   9  12
## 71         dodge    ram 1500 pickup 4wd   5.2 1999   8   auto(l4)   4  11  15
## 72         dodge    ram 1500 pickup 4wd   5.2 1999   8 manual(m5)   4  11  16
## 73         dodge    ram 1500 pickup 4wd   5.7 2008   8   auto(l5)   4  13  17
## 74         dodge    ram 1500 pickup 4wd   5.9 1999   8   auto(l4)   4  11  15
## 75          ford         expedition 2wd   4.6 1999   8   auto(l4)   r  11  17
## 76          ford         expedition 2wd   5.4 1999   8   auto(l4)   r  11  17
## 77          ford         expedition 2wd   5.4 2008   8   auto(l6)   r  12  18
## 78          ford           explorer 4wd   4.0 1999   6   auto(l5)   4  14  17
## 79          ford           explorer 4wd   4.0 1999   6 manual(m5)   4  15  19
## 80          ford           explorer 4wd   4.0 1999   6   auto(l5)   4  14  17
## 81          ford           explorer 4wd   4.0 2008   6   auto(l5)   4  13  19
## 82          ford           explorer 4wd   4.6 2008   8   auto(l6)   4  13  19
## 83          ford           explorer 4wd   5.0 1999   8   auto(l4)   4  13  17
## 84          ford        f150 pickup 4wd   4.2 1999   6   auto(l4)   4  14  17
## 85          ford        f150 pickup 4wd   4.2 1999   6 manual(m5)   4  14  17
## 86          ford        f150 pickup 4wd   4.6 1999   8 manual(m5)   4  13  16
## 87          ford        f150 pickup 4wd   4.6 1999   8   auto(l4)   4  13  16
## 88          ford        f150 pickup 4wd   4.6 2008   8   auto(l4)   4  13  17
## 89          ford        f150 pickup 4wd   5.4 1999   8   auto(l4)   4  11  15
## 90          ford        f150 pickup 4wd   5.4 2008   8   auto(l4)   4  13  17
## 91          ford                mustang   3.8 1999   6 manual(m5)   r  18  26
## 92          ford                mustang   3.8 1999   6   auto(l4)   r  18  25
## 93          ford                mustang   4.0 2008   6 manual(m5)   r  17  26
## 94          ford                mustang   4.0 2008   6   auto(l5)   r  16  24
## 95          ford                mustang   4.6 1999   8   auto(l4)   r  15  21
## 96          ford                mustang   4.6 1999   8 manual(m5)   r  15  22
## 97          ford                mustang   4.6 2008   8 manual(m5)   r  15  23
## 98          ford                mustang   4.6 2008   8   auto(l5)   r  15  22
## 99          ford                mustang   5.4 2008   8 manual(m6)   r  14  20
## 100        honda                  civic   1.6 1999   4 manual(m5)   f  28  33
## 101        honda                  civic   1.6 1999   4   auto(l4)   f  24  32
## 102        honda                  civic   1.6 1999   4 manual(m5)   f  25  32
## 103        honda                  civic   1.6 1999   4 manual(m5)   f  23  29
## 104        honda                  civic   1.6 1999   4   auto(l4)   f  24  32
## 105        honda                  civic   1.8 2008   4 manual(m5)   f  26  34
## 106        honda                  civic   1.8 2008   4   auto(l5)   f  25  36
## 107        honda                  civic   1.8 2008   4   auto(l5)   f  24  36
## 108        honda                  civic   2.0 2008   4 manual(m6)   f  21  29
## 109      hyundai                 sonata   2.4 1999   4   auto(l4)   f  18  26
## 110      hyundai                 sonata   2.4 1999   4 manual(m5)   f  18  27
## 111      hyundai                 sonata   2.4 2008   4   auto(l4)   f  21  30
## 112      hyundai                 sonata   2.4 2008   4 manual(m5)   f  21  31
## 113      hyundai                 sonata   2.5 1999   6   auto(l4)   f  18  26
## 114      hyundai                 sonata   2.5 1999   6 manual(m5)   f  18  26
## 115      hyundai                 sonata   3.3 2008   6   auto(l5)   f  19  28
## 116      hyundai                tiburon   2.0 1999   4   auto(l4)   f  19  26
## 117      hyundai                tiburon   2.0 1999   4 manual(m5)   f  19  29
## 118      hyundai                tiburon   2.0 2008   4 manual(m5)   f  20  28
## 119      hyundai                tiburon   2.0 2008   4   auto(l4)   f  20  27
## 120      hyundai                tiburon   2.7 2008   6   auto(l4)   f  17  24
## 121      hyundai                tiburon   2.7 2008   6 manual(m6)   f  16  24
## 122      hyundai                tiburon   2.7 2008   6 manual(m5)   f  17  24
## 123         jeep     grand cherokee 4wd   3.0 2008   6   auto(l5)   4  17  22
## 124         jeep     grand cherokee 4wd   3.7 2008   6   auto(l5)   4  15  NA
## 125         jeep     grand cherokee 4wd   4.0 1999   6   auto(l4)   4  15  20
## 126         jeep     grand cherokee 4wd   4.7 1999   8   auto(l4)   4  14  17
## 127         jeep     grand cherokee 4wd   4.7 2008   8   auto(l5)   4   9  12
## 128         jeep     grand cherokee 4wd   4.7 2008   8   auto(l5)   4  14  19
## 129         jeep     grand cherokee 4wd   5.7 2008   8   auto(l5)   4  13  18
## 130         jeep     grand cherokee 4wd   6.1 2008   8   auto(l5)   4  11  14
## 131   land rover            range rover   4.0 1999   8   auto(l4)   4  11  NA
## 132   land rover            range rover   4.2 2008   8   auto(s6)   4  12  18
## 133   land rover            range rover   4.4 2008   8   auto(s6)   4  12  18
## 134   land rover            range rover   4.6 1999   8   auto(l4)   4  11  15
## 135      lincoln          navigator 2wd   5.4 1999   8   auto(l4)   r  11  17
## 136      lincoln          navigator 2wd   5.4 1999   8   auto(l4)   r  11  16
## 137      lincoln          navigator 2wd   5.4 2008   8   auto(l6)   r  12  18
## 138      mercury        mountaineer 4wd   4.0 1999   6   auto(l5)   4  14  17
## 139      mercury        mountaineer 4wd   4.0 2008   6   auto(l5)   4  13  19
## 140      mercury        mountaineer 4wd   4.6 2008   8   auto(l6)   4  13  19
## 141      mercury        mountaineer 4wd   5.0 1999   8   auto(l4)   4  13  17
## 142       nissan                 altima   2.4 1999   4 manual(m5)   f  21  29
## 143       nissan                 altima   2.4 1999   4   auto(l4)   f  19  27
## 144       nissan                 altima   2.5 2008   4   auto(av)   f  23  31
## 145       nissan                 altima   2.5 2008   4 manual(m6)   f  23  32
## 146       nissan                 altima   3.5 2008   6 manual(m6)   f  19  27
## 147       nissan                 altima   3.5 2008   6   auto(av)   f  19  26
## 148       nissan                 maxima   3.0 1999   6   auto(l4)   f  18  26
## 149       nissan                 maxima   3.0 1999   6 manual(m5)   f  19  25
## 150       nissan                 maxima   3.5 2008   6   auto(av)   f  19  25
## 151       nissan         pathfinder 4wd   3.3 1999   6   auto(l4)   4  14  17
## 152       nissan         pathfinder 4wd   3.3 1999   6 manual(m5)   4  15  17
## 153       nissan         pathfinder 4wd   4.0 2008   6   auto(l5)   4  14  NA
## 154       nissan         pathfinder 4wd   5.6 2008   8   auto(s5)   4  12  18
## 155      pontiac             grand prix   3.1 1999   6   auto(l4)   f  18  26
## 156      pontiac             grand prix   3.8 1999   6   auto(l4)   f  16  26
## 157      pontiac             grand prix   3.8 1999   6   auto(l4)   f  17  27
## 158      pontiac             grand prix   3.8 2008   6   auto(l4)   f  18  28
## 159      pontiac             grand prix   5.3 2008   8   auto(s4)   f  16  25
## 160       subaru           forester awd   2.5 1999   4 manual(m5)   4  18  25
## 161       subaru           forester awd   2.5 1999   4   auto(l4)   4  18  24
## 162       subaru           forester awd   2.5 2008   4 manual(m5)   4  20  27
## 163       subaru           forester awd   2.5 2008   4 manual(m5)   4  19  25
## 164       subaru           forester awd   2.5 2008   4   auto(l4)   4  20  26
## 165       subaru           forester awd   2.5 2008   4   auto(l4)   4  18  23
## 166       subaru            impreza awd   2.2 1999   4   auto(l4)   4  21  26
## 167       subaru            impreza awd   2.2 1999   4 manual(m5)   4  19  26
## 168       subaru            impreza awd   2.5 1999   4 manual(m5)   4  19  26
## 169       subaru            impreza awd   2.5 1999   4   auto(l4)   4  19  26
## 170       subaru            impreza awd   2.5 2008   4   auto(s4)   4  20  25
## 171       subaru            impreza awd   2.5 2008   4   auto(s4)   4  20  27
## 172       subaru            impreza awd   2.5 2008   4 manual(m5)   4  19  25
## 173       subaru            impreza awd   2.5 2008   4 manual(m5)   4  20  27
## 174       toyota            4runner 4wd   2.7 1999   4 manual(m5)   4  15  20
## 175       toyota            4runner 4wd   2.7 1999   4   auto(l4)   4  16  20
## 176       toyota            4runner 4wd   3.4 1999   6   auto(l4)   4  15  19
## 177       toyota            4runner 4wd   3.4 1999   6 manual(m5)   4  15  17
## 178       toyota            4runner 4wd   4.0 2008   6   auto(l5)   4  16  20
## 179       toyota            4runner 4wd   4.7 2008   8   auto(l5)   4  14  17
## 180       toyota                  camry   2.2 1999   4 manual(m5)   f  21  29
## 181       toyota                  camry   2.2 1999   4   auto(l4)   f  21  27
## 182       toyota                  camry   2.4 2008   4 manual(m5)   f  21  31
## 183       toyota                  camry   2.4 2008   4   auto(l5)   f  21  31
## 184       toyota                  camry   3.0 1999   6   auto(l4)   f  18  26
## 185       toyota                  camry   3.0 1999   6 manual(m5)   f  18  26
## 186       toyota                  camry   3.5 2008   6   auto(s6)   f  19  28
## 187       toyota           camry solara   2.2 1999   4   auto(l4)   f  21  27
## 188       toyota           camry solara   2.2 1999   4 manual(m5)   f  21  29
## 189       toyota           camry solara   2.4 2008   4 manual(m5)   f  21  31
## 190       toyota           camry solara   2.4 2008   4   auto(s5)   f  22  31
## 191       toyota           camry solara   3.0 1999   6   auto(l4)   f  18  26
## 192       toyota           camry solara   3.0 1999   6 manual(m5)   f  18  26
## 193       toyota           camry solara   3.3 2008   6   auto(s5)   f  18  27
## 194       toyota                corolla   1.8 1999   4   auto(l3)   f  24  30
## 195       toyota                corolla   1.8 1999   4   auto(l4)   f  24  33
## 196       toyota                corolla   1.8 1999   4 manual(m5)   f  26  35
## 197       toyota                corolla   1.8 2008   4 manual(m5)   f  28  37
## 198       toyota                corolla   1.8 2008   4   auto(l4)   f  26  35
## 199       toyota land cruiser wagon 4wd   4.7 1999   8   auto(l4)   4  11  15
## 200       toyota land cruiser wagon 4wd   5.7 2008   8   auto(s6)   4  13  18
## 201       toyota      toyota tacoma 4wd   2.7 1999   4 manual(m5)   4  15  20
## 202       toyota      toyota tacoma 4wd   2.7 1999   4   auto(l4)   4  16  20
## 203       toyota      toyota tacoma 4wd   2.7 2008   4 manual(m5)   4  17  22
## 204       toyota      toyota tacoma 4wd   3.4 1999   6 manual(m5)   4  15  17
## 205       toyota      toyota tacoma 4wd   3.4 1999   6   auto(l4)   4  15  19
## 206       toyota      toyota tacoma 4wd   4.0 2008   6 manual(m6)   4  15  18
## 207       toyota      toyota tacoma 4wd   4.0 2008   6   auto(l5)   4  16  20
## 208   volkswagen                    gti   2.0 1999   4 manual(m5)   f  21  29
## 209   volkswagen                    gti   2.0 1999   4   auto(l4)   f  19  26
## 210   volkswagen                    gti   2.0 2008   4 manual(m6)   f  21  29
## 211   volkswagen                    gti   2.0 2008   4   auto(s6)   f  22  29
## 212   volkswagen                    gti   2.8 1999   6 manual(m5)   f  17  NA
## 213   volkswagen                  jetta   1.9 1999   4 manual(m5)   f  33  44
## 214   volkswagen                  jetta   2.0 1999   4 manual(m5)   f  21  29
## 215   volkswagen                  jetta   2.0 1999   4   auto(l4)   f  19  26
## 216   volkswagen                  jetta   2.0 2008   4   auto(s6)   f  22  29
## 217   volkswagen                  jetta   2.0 2008   4 manual(m6)   f  21  29
## 218   volkswagen                  jetta   2.5 2008   5   auto(s6)   f  21  29
## 219   volkswagen                  jetta   2.5 2008   5 manual(m5)   f  21  29
## 220   volkswagen                  jetta   2.8 1999   6   auto(l4)   f  16  23
## 221   volkswagen                  jetta   2.8 1999   6 manual(m5)   f  17  24
## 222   volkswagen             new beetle   1.9 1999   4 manual(m5)   f  35  44
## 223   volkswagen             new beetle   1.9 1999   4   auto(l4)   f  29  41
## 224   volkswagen             new beetle   2.0 1999   4 manual(m5)   f  21  29
## 225   volkswagen             new beetle   2.0 1999   4   auto(l4)   f  19  26
## 226   volkswagen             new beetle   2.5 2008   5 manual(m5)   f  20  28
## 227   volkswagen             new beetle   2.5 2008   5   auto(s6)   f  20  29
## 228   volkswagen                 passat   1.8 1999   4 manual(m5)   f  21  29
## 229   volkswagen                 passat   1.8 1999   4   auto(l5)   f  18  29
## 230   volkswagen                 passat   2.0 2008   4   auto(s6)   f  19  28
## 231   volkswagen                 passat   2.0 2008   4 manual(m6)   f  21  29
## 232   volkswagen                 passat   2.8 1999   6   auto(l5)   f  16  26
## 233   volkswagen                 passat   2.8 1999   6 manual(m5)   f  18  26
## 234   volkswagen                 passat   3.6 2008   6   auto(s6)   f  17  26
##     fl      class
## 1    p    compact
## 2    p    compact
## 3    p    compact
## 4    p    compact
## 5    p    compact
## 6    p    compact
## 7    p    compact
## 8    p    compact
## 9    p    compact
## 10   p    compact
## 11   p    compact
## 12   p    compact
## 13   p    compact
## 14   p    compact
## 15   p    compact
## 16   p    midsize
## 17   p    midsize
## 18   p    midsize
## 19   r        suv
## 20   e        suv
## 21   r        suv
## 22   r        suv
## 23   r        suv
## 24   p    2seater
## 25   p    2seater
## 26   p    2seater
## 27   p    2seater
## 28   p    2seater
## 29   r        suv
## 30   e        suv
## 31   r        suv
## 32   d        suv
## 33   r    midsize
## 34   r    midsize
## 35   r    midsize
## 36   r    midsize
## 37   r    midsize
## 38   r    minivan
## 39   r    minivan
## 40   r    minivan
## 41   r    minivan
## 42   r    minivan
## 43   r    minivan
## 44   e    minivan
## 45   r    minivan
## 46   r    minivan
## 47   r    minivan
## 48   r    minivan
## 49   r     pickup
## 50   r     pickup
## 51   r     pickup
## 52   r     pickup
## 53   r     pickup
## 54   r     pickup
## 55   e     pickup
## 56   r     pickup
## 57   r     pickup
## 58   r        suv
## 59   r        suv
## 60   e        suv
## 61   r        suv
## 62   r        suv
## 63   r        suv
## 64   r        suv
## 65   r     pickup
## 66   e     pickup
## 67   r     pickup
## 68   r     pickup
## 69   r     pickup
## 70   e     pickup
## 71   r     pickup
## 72   r     pickup
## 73   r     pickup
## 74   r     pickup
## 75   r        suv
## 76   r        suv
## 77   r        suv
## 78   r        suv
## 79   r        suv
## 80   r        suv
## 81   r        suv
## 82   r        suv
## 83   r        suv
## 84   r     pickup
## 85   r     pickup
## 86   r     pickup
## 87   r     pickup
## 88   r     pickup
## 89   r     pickup
## 90   r     pickup
## 91   r subcompact
## 92   r subcompact
## 93   r subcompact
## 94   r subcompact
## 95   r subcompact
## 96   r subcompact
## 97   r subcompact
## 98   r subcompact
## 99   p subcompact
## 100  r subcompact
## 101  r subcompact
## 102  r subcompact
## 103  p subcompact
## 104  r subcompact
## 105  r subcompact
## 106  r subcompact
## 107  c subcompact
## 108  p subcompact
## 109  r    midsize
## 110  r    midsize
## 111  r    midsize
## 112  r    midsize
## 113  r    midsize
## 114  r    midsize
## 115  r    midsize
## 116  r subcompact
## 117  r subcompact
## 118  r subcompact
## 119  r subcompact
## 120  r subcompact
## 121  r subcompact
## 122  r subcompact
## 123  d        suv
## 124  r        suv
## 125  r        suv
## 126  r        suv
## 127  e        suv
## 128  r        suv
## 129  r        suv
## 130  p        suv
## 131  p        suv
## 132  r        suv
## 133  r        suv
## 134  p        suv
## 135  r        suv
## 136  p        suv
## 137  r        suv
## 138  r        suv
## 139  r        suv
## 140  r        suv
## 141  r        suv
## 142  r    compact
## 143  r    compact
## 144  r    midsize
## 145  r    midsize
## 146  p    midsize
## 147  p    midsize
## 148  r    midsize
## 149  r    midsize
## 150  p    midsize
## 151  r        suv
## 152  r        suv
## 153  p        suv
## 154  p        suv
## 155  r    midsize
## 156  p    midsize
## 157  r    midsize
## 158  r    midsize
## 159  p    midsize
## 160  r        suv
## 161  r        suv
## 162  r        suv
## 163  p        suv
## 164  r        suv
## 165  p        suv
## 166  r subcompact
## 167  r subcompact
## 168  r subcompact
## 169  r subcompact
## 170  p    compact
## 171  r    compact
## 172  p    compact
## 173  r    compact
## 174  r        suv
## 175  r        suv
## 176  r        suv
## 177  r        suv
## 178  r        suv
## 179  r        suv
## 180  r    midsize
## 181  r    midsize
## 182  r    midsize
## 183  r    midsize
## 184  r    midsize
## 185  r    midsize
## 186  r    midsize
## 187  r    compact
## 188  r    compact
## 189  r    compact
## 190  r    compact
## 191  r    compact
## 192  r    compact
## 193  r    compact
## 194  r    compact
## 195  r    compact
## 196  r    compact
## 197  r    compact
## 198  r    compact
## 199  r        suv
## 200  r        suv
## 201  r     pickup
## 202  r     pickup
## 203  r     pickup
## 204  r     pickup
## 205  r     pickup
## 206  r     pickup
## 207  r     pickup
## 208  r    compact
## 209  r    compact
## 210  p    compact
## 211  p    compact
## 212  r    compact
## 213  d    compact
## 214  r    compact
## 215  r    compact
## 216  p    compact
## 217  p    compact
## 218  r    compact
## 219  r    compact
## 220  r    compact
## 221  r    compact
## 222  d subcompact
## 223  d subcompact
## 224  r subcompact
## 225  r subcompact
## 226  r subcompact
## 227  r subcompact
## 228  p    midsize
## 229  p    midsize
## 230  p    midsize
## 231  p    midsize
## 232  p    midsize
## 233  p    midsize
## 234  p    midsize
```



```
# Q1 (각 변수별 결측치 개수 확인 : table(데이터명$변수명))
table(is.na(mpg_new$drv))
## 
## FALSE 
##   234
table(is.na(mpg_new$hwy))
## 
## FALSE  TRUE 
##   229     5
```



```
# Q2 결측치를 제외하고 어떤 구동방식의 hwy 평균이 높은지 확인
mpg_nomiss = mpg_new %>% 
  filter(!is.na(hwy)) %>% 
  group_by(drv) %>% 
  summarise(mean_hwy = mean(hwy))
mpg_nomiss
## # A tibble: 3 x 2
##   drv   mean_hwy
##   <chr>    <dbl>
## 1 4         19.2
## 2 f         28.2
## 3 r         21
```



### 07-2 이상치 정제하기



```
# sex 이상치 '3' , score 이상치 '6' 넣어서 임의의 DF 만들기
outlier = data.frame(sex = c(1, 2, 1, 3, 2, 1),
                     score = c(5, 4, 3, 4, 2, 6))
outlier
##   sex score
## 1   1     5
## 2   2     4
## 3   1     3
## 4   3     4
## 5   2     2
## 6   1     6
```



```
# 이상치 확인 (table)
table(outlier$sex)
## 
## 1 2 3 
## 3 2 1
table(outlier$score)
## 
## 2 3 4 5 6 
## 1 1 2 1 1
```



```
# 결측 처리 하기( 이상치를 na로 변환)
outlier$sex = ifelse(outlier$sex == 3, NA, outlier$sex)
outlier
##   sex score
## 1   1     5
## 2   2     4
## 3   1     3
## 4  NA     4
## 5   2     2
## 6   1     6
```



```
outlier$score = ifelse(outlier$score > 5, NA, outlier$score)
outlier
##   sex score
## 1   1     5
## 2   2     4
## 3   1     3
## 4  NA     4
## 5   2     2
## 6   1    NA
```



```
# 결측치 제거 함수를 이용하여 통계량 구하기
outlier %>% 
  filter(!is.na(sex) & !is.na(score)) %>% 
  group_by(sex) %>% 
  summarise(mean_score = mean(score))
## # A tibble: 2 x 2
##     sex mean_score
##   <dbl>      <dbl>
## 1     1          4
## 2     2          3
```



### 혼자서 해보기



```
# boxplot 으로 데이터 형태 파악 : 이상치 제거 기준값을 확인하기 위해
boxplot(mpg$hwy)$stats # 상자 그림 통계치 출력력
```

![img](data:image/png;base64,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AgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABBAgoQJKAAQQIKECSgAEECChAkoABB/w88/Qek/Rpy9wAAAABJRU5ErkJggg==)

```
##      [,1]
## [1,]   12
## [2,]   18
## [3,]   24
## [4,]   27
## [5,]   37
```



```
#결측치 처리
mpg$hwy = ifelse(mpg$hwy < 12 | mpg$hwy >37, NA, mpg$hwy)
table(is.na(mpg$hwy))
## 
## FALSE  TRUE 
##   231     3
```



```
# 결측치를 제외하고 간단히 평균 구해보기
mpg %>% 
  group_by(drv) %>% 
  summarise(mean_hwy = mean(hwy, na.rm = T))
## # A tibble: 3 x 2
##   drv   mean_hwy
##   <chr>    <dbl>
## 1 4         19.2
## 2 f         27.7
## 3 r         21
```



### 혼자서 해보기



```
# 임의 이상치값 들어간 데이터프레임 만들기
mpg = as.data.frame(ggplot2::mpg)
mpg_new = mpg
mpg_new[c(10, 14,58, 93), "drv"] = "k"
mpg_new[c(29, 43, 129, 203), "cty"] = c(3, 4, 39, 42)
```



```
# Q1
table(mpg_new$drv)
## 
##   4   f   k   r 
## 100 106   4  24
mpg_new$drv = ifelse(mpg_new$drv %in% c("4", "f", "r"), mpg_new$drv, NA)
table(mpg_new$drv)
## 
##   4   f   r 
## 100 106  24
```



```
# Q2
boxplot(mpg_new$cty)$stats
```

![img](data:image/png;base64,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QTlJQGFokE1r4RQBhaHhRMyFEwQUhgSUCgIKQwJKBQGFIQGlgoDCkIBSQUBhSECpIKAw5DImKggoHHAhPeUEFA55KyfFBBSO6CelBBSOCCilBBQOeQpPMQGFA15EopyAwpDLmKggoDDkQnoqCCgMCSgVBBSGBJQKAgpDAkoFAYUhAaWCgMKQgFJBQGHIZUxUEFA44EJ6ygkoHPJWTooJKBzRT0oJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgAKE/XUAB2mneqNYHbGnuv2zgemneqNYHBPizEFCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChAQUICSgACEBBQgJKEBIQAFCAgoQElCAkIAChP4B2NwdSYeunF4AAAAASUVORK5CYII=)

```
##      [,1]
## [1,]    9
## [2,]   14
## [3,]   17
## [4,]   19
## [5,]   26
mpg_new$cty = ifelse(mpg_new$cty < 9 | mpg_new$cty >26, NA, mpg_new$cty)
boxplot(mpg_new$cty)
```

![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAABUAAAAPACAMAAADDuCPrAAAAsVBMVEUAAAAAADoAAGYAOjoAOmYAOpAAZrY6AAA6OgA6Ojo6ZmY6ZpA6ZrY6kNtmAABmOgBmOjpmZmZmZpBmkJBmkLZmtttmtv+QOgCQZgCQZjqQkLaQtraQttuQtv+Q2/+2ZgC2Zjq2kGa2kLa2tpC2tra2ttu227a229u22/+2///T09PbkDrbkGbbtmbbtpDbtrbb27bb29vb2//b////tmb/25D/27b/29v//7b//9v////UDo43AAAACXBIWXMAAB2HAAAdhwGP5fFlAAAYZElEQVR4nO3d63JcxdmG4ZEDVoCwUUIgG4LtRAT42BpsB+n8D+ybkZEtu23yzuPuWt3Kdf3BmqpZpVI962b2s7sEILLb+hcAWJWAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCAkoAAhAQUICShASEABQgIKEBJQgJCAAoQEFCA0d0B3AN30T1T3I3a09V8buF26N6r3AXsa8D8M4H+WgAKEBBQgJKAAIQEFCAkoQEhAAUICChASUICQgAKE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)

```
# Q3 
mpg_new = mpg_new %>% 
  filter(!is.na(drv) & !is.na(cty)) %>% 
  group_by(drv) %>% 
  summarise(mean_cty = mean(cty))
mpg_new  
## # A tibble: 3 x 2
##   drv   mean_cty
##   <chr>    <dbl>
## 1 4         14.2
## 2 f         19.5
## 3 r         14.0
```
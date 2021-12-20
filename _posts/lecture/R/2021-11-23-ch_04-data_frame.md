---
layout: single
title:  "쉽게 배우는 R 데이터분석 Ch.4"
categories: R
tag: [R, 데이터프레임, 외부데이터]
toc: true  # 목차 자동 생성 기능
toc_sticky: True # 목차 위치 고정 
toc_label : "Contents" # 목차명
author_profile: true # 포스트 내 주인장 프로파일 끄기(false)/켜기(true)
# sidebar:
#     nav: "docs" # 작성위치 : _data/naigation.yml
search: true # 검색창에서 검색 제외(false) / 승인(true)
---



## 4장. 데이터 프레임의 세계로



### 04-2 ‘데이터프레임’ 만들기



```
english = c(90, 80 ,60 ,70)
english
## [1] 90 80 60 70
math = c(50, 60, 100, 20)
math 
## [1]  50  60 100  20
```



```
# english, math로 데이터 프레임 생성해서 df_midterm에 할당
df_midterm = data.frame(english, math)
df_midterm     
##   english math
## 1      90   50
## 2      80   60
## 3      60  100
## 4      70   20
```



```
# 데이터 추가 하기 
class = c(1, 1, 2, 2)
class
## [1] 1 1 2 2
```



```
df_midterm = data.frame(english, math, class)
df_midterm
##   english math class
## 1      90   50     1
## 2      80   60     1
## 3      60  100     2
## 4      70   20     2
```



```
# 분석하기
mean(df_midterm$english)
## [1] 75
mean(df_midterm$math)
## [1] 57.5
```



```
# data frame 한번에 만들기
df_midterm = data.frame(english = c(90, 80, 60, 70),
                        math = c(50, 60, 100, 20),
                        class = c(1, 1, 2, 2))
df_midterm
##   english math class
## 1      90   50     1
## 2      80   60     1
## 3      60  100     2
## 4      70   20     2
```



### !혼자서 해보기기



```
# Q1 데이터 프레임 만들기 
fruit = c("사과", "딸기", "수박")
price = c(1800, 1500, 3000)
volume = c(24, 38, 13)
```



```
df_sales = data.frame(fruit, price, volume)
df_sales
##   fruit price volume
## 1  사과  1800     24
## 2  딸기  1500     38
## 3  수박  3000     13
```



```
# Q2 과일가격 , 판매량 평균 
mean(df_sales$price)
## [1] 2100
mean(df_sales$volume)
## [1] 25
```



### 04-3 외부 데이터 이용하기기



```
# 엑셀 파일 불러오기 위한  readxl 패키지 설치
  # markdown 문서에서 패키지 인스톨 구문 작성시
  # (repos = "http://cran.us.r-project.org") 추가 작성 필요요
install.packages("readxl", repos = "http://cran.us.r-project.org")
## 'C:/Program Files/R/R-4.1.2/library'의 위치에 패키지(들)을 설치합니다.
## (왜냐하면 'lib'가 지정되지 않았기 때문입니다)
## package 'readxl' successfully unpacked and MD5 sums checked
## 
## The downloaded binary packages are in
##  C:\Users\Nyangta\AppData\Local\Temp\Rtmpme3k8K\downloaded_packages
# readxl 패키지 로드(불러오기)
library(readxl)
```



```
# 엑셀파일 불러오기
df_exam = read_excel("./../Dataset/Data/excel_exam.xlsx")
df_exam
## # A tibble: 20 x 5
##       id class  math english science
##    <dbl> <dbl> <dbl>   <dbl>   <dbl>
##  1     1     1    50      98      50
##  2     2     1    60      97      60
##  3     3     1    45      86      78
##  4     4     1    30      98      58
##  5     5     2    25      80      65
##  6     6     2    50      89      98
##  7     7     2    80      90      45
##  8     8     2    90      78      25
##  9     9     3    20      98      15
## 10    10     3    50      98      45
## 11    11     3    65      65      65
## 12    12     3    45      85      32
## 13    13     4    46      98      65
## 14    14     4    48      87      12
## 15    15     4    75      56      78
## 16    16     4    58      98      65
## 17    17     5    65      68      98
## 18    18     5    80      78      90
## 19    19     5    89      68      87
## 20    20     5    78      83      58
```



```
# 첫 번째 행이 변수병이 없는 파일 불러오기
df_exam_novar = read_excel("./../Dataset/Data/excel_exam_novar.xlsx")
## New names:
## * `1` -> `1...1`
## * `1` -> `1...2`
## * `50` -> `50...3`
## * `50` -> `50...5`
df_exam_novar
## # A tibble: 7 x 5
##   `1...1` `1...2` `50...3`  `98` `50...5`
##     <dbl>   <dbl>    <dbl> <dbl>    <dbl>
## 1       2       1       60    97       60
## 2       3       2       25    80       65
## 3       4       2       50    89       98
## 4       5       3       20    98       15
## 5       6       3       50    98       45
## 6       7       4       46    98       65
## 7       8       4       48    87       12
```



```
# col_names = F 파라미터를 설정하여 첫번째 행을 변수명이 아닌 데이터로 인식시킴
df_exam_novar = read_excel("./../Dataset/Data/excel_exam_novar.xlsx", col_names = F)
## New names:
## * `` -> ...1
## * `` -> ...2
## * `` -> ...3
## * `` -> ...4
## * `` -> ...5
df_exam_novar
## # A tibble: 8 x 5
##    ...1  ...2  ...3  ...4  ...5
##   <dbl> <dbl> <dbl> <dbl> <dbl>
## 1     1     1    50    98    50
## 2     2     1    60    97    60
## 3     3     2    25    80    65
## 4     4     2    50    89    98
## 5     5     3    20    98    15
## 6     6     3    50    98    45
## 7     7     4    46    98    65
## 8     8     4    48    87    12
```



```
# 엑셀 파일의 세번재 시트에 있는 데이터 불러오기
df_exam_sheet = read_excel("./../Dataset/Data/excel_exam_sheet.xlsx", sheet = 3)
df_exam_sheet
## # A tibble: 8 x 5
##      id class  math english science
##   <dbl> <dbl> <dbl>   <dbl>   <dbl>
## 1     1     1    50      98      50
## 2     2     1    60      97      60
## 3     3     2    25      80      65
## 4     4     2    50      89      98
## 5     5     3    20      98      15
## 6     6     3    50      98      45
## 7     7     4    46      98      65
## 8     8     4    48      87      12
```



```
# csv 파일 불러오기 
df_csv_exam = read.csv("./../Dataset/Data/csv_exam.csv")
df_csv_exam
##    id class math english science
## 1   1     1   50      98      50
## 2   2     1   60      97      60
## 3   3     1   45      86      78
## 4   4     1   30      98      58
## 5   5     2   25      80      65
## 6   6     2   50      89      98
## 7   7     2   80      90      45
## 8   8     2   90      78      25
## 9   9     3   20      98      15
## 10 10     3   50      98      45
## 11 11     3   65      65      65
## 12 12     3   45      85      32
## 13 13     4   46      98      65
## 14 14     4   48      87      12
## 15 15     4   75      56      78
## 16 16     4   58      98      65
## 17 17     5   65      68      98
## 18 18     5   80      78      90
## 19 19     5   89      68      87
## 20 20     5   78      83      58
```



```
# csv 파일을 데이터 프레임으로 만들기
# 1.데이터 프레임 만들기기
english = c(90, 80, 60, 70)
math = c(50, 60, 100, 20)
class = c(1, 1, 2, 2)
```



```
df_midterm = data.frame(english, math, class)
df_midterm
##   english math class
## 1      90   50     1
## 2      80   60     1
## 3      60  100     2
## 4      70   20     2
```



```
# 2.csv파일로 저장 (write.csv)
write.csv(df_midterm, file = "./../Dataset/Data/df_midterm.csv")
```



```
# RDS 파일 활용하기 (RDS파일 : R전용 데이터 파일)
# 데이터 프레임을 RDS파일로 저장하기
saveRDS(df_midterm, file = "./../Dataset/Data/df_midterm.rds")
```



```
# RDS파일 불러오기 (readRDS)
rm(df_midterm) # 메모리에 저장되어 있는 df_midterm 데이터 삭제제

# 아래 코드 작성시 rmd 파일 변환이 안되므로 주석처리함
# df_midterm # 데이터를 삭제 했으므로 에러메세지 확인

df_midterm = readRDS("./../Dataset/Data/df_midterm.rds") # RDS파일에서 읽어오기
df_midterm
##   english math class
## 1      90   50     1
## 2      80   60     1
## 3      60  100     2
## 4      70   20     2
```


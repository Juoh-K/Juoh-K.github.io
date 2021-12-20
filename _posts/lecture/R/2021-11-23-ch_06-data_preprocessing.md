---
layout: single
title:  "쉽게 배우는 R 데이터분석 Ch.6"
categories: R
tag: [R, 전처리, 추출, 정렬, 파생변수, 요약, 합치기]
toc: true  # 목차 자동 생성 기능
toc_sticky: True # 목차 위치 고정 
toc_label : "Contents" # 목차명
author_profile: true # 포스트 내 주인장 프로파일 끄기(false)/켜기(true)
# sidebar:
#     nav: "docs" # 작성위치 : _data/naigation.yml
search: true # 검색창에서 검색 제외(false) / 승인(true)
---

## 6장. 데이터 가공 하기



### 06-1 데이터 전처리



```
# 데이터 불러오기
library(dplyr)
## 
## 다음의 패키지를 부착합니다: 'dplyr'
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
exam = read.csv("./../Dataset/Data/csv_exam.csv")
exam
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
# exam에서 class가 1인 경우만 추출 (filter)
# 파이프라인 연산자 %>% (단축키: ctrl + shift + m)
exam %>%  filter(class == 1)
##   id class math english science
## 1  1     1   50      98      50
## 2  2     1   60      97      60
## 3  3     1   45      86      78
## 4  4     1   30      98      58
```



```
# 2반인 경우만 추출 (filter)
exam %>% filter(class == 2)
##   id class math english science
## 1  5     2   25      80      65
## 2  6     2   50      89      98
## 3  7     2   80      90      45
## 4  8     2   90      78      25
```



### 06-2 조건에 맞는 데이터만 추출



```
# 다른 케이스 연습해보기기
exam %>% filter(class != 1) # 1반 아닌 경우
##    id class math english science
## 1   5     2   25      80      65
## 2   6     2   50      89      98
## 3   7     2   80      90      45
## 4   8     2   90      78      25
## 5   9     3   20      98      15
## 6  10     3   50      98      45
## 7  11     3   65      65      65
## 8  12     3   45      85      32
## 9  13     4   46      98      65
## 10 14     4   48      87      12
## 11 15     4   75      56      78
## 12 16     4   58      98      65
## 13 17     5   65      68      98
## 14 18     5   80      78      90
## 15 19     5   89      68      87
## 16 20     5   78      83      58
exam %>% filter(math > 50) # 수학 50점 초과한 경우
##    id class math english science
## 1   2     1   60      97      60
## 2   7     2   80      90      45
## 3   8     2   90      78      25
## 4  11     3   65      65      65
## 5  15     4   75      56      78
## 6  16     4   58      98      65
## 7  17     5   65      68      98
## 8  18     5   80      78      90
## 9  19     5   89      68      87
## 10 20     5   78      83      58
exam %>% filter(english <= 80) # 영어 80 이하인 경우
##   id class math english science
## 1  5     2   25      80      65
## 2  8     2   90      78      25
## 3 11     3   65      65      65
## 4 15     4   75      56      78
## 5 17     5   65      68      98
## 6 18     5   80      78      90
## 7 19     5   89      68      87
```



```
# 여러 조건 충족하는 행 추출
exam %>% filter(class == 1 & math >= 50)
##   id class math english science
## 1  1     1   50      98      50
## 2  2     1   60      97      60
exam %>% filter(class == 2 & english >= 80)
##   id class math english science
## 1  5     2   25      80      65
## 2  6     2   50      89      98
## 3  7     2   80      90      45
```



```
exam %>% filter(english < 90 | science < 50)
##    id class math english science
## 1   3     1   45      86      78
## 2   5     2   25      80      65
## 3   6     2   50      89      98
## 4   7     2   80      90      45
## 5   8     2   90      78      25
## 6   9     3   20      98      15
## 7  10     3   50      98      45
## 8  11     3   65      65      65
## 9  12     3   45      85      32
## 10 14     4   48      87      12
## 11 15     4   75      56      78
## 12 17     5   65      68      98
## 13 18     5   80      78      90
## 14 19     5   89      68      87
## 15 20     5   78      83      58
exam %>% filter(class == 1 | class == 3 | class == 5)
##    id class math english science
## 1   1     1   50      98      50
## 2   2     1   60      97      60
## 3   3     1   45      86      78
## 4   4     1   30      98      58
## 5   9     3   20      98      15
## 6  10     3   50      98      45
## 7  11     3   65      65      65
## 8  12     3   45      85      32
## 9  17     5   65      68      98
## 10 18     5   80      78      90
## 11 19     5   89      68      87
## 12 20     5   78      83      58
```



```
# 매치 연산 ( %in% )
exam %>% filter(class %in% c(1,3,5) ) # 1, 3, 5 반에 해당하면 추출
##    id class math english science
## 1   1     1   50      98      50
## 2   2     1   60      97      60
## 3   3     1   45      86      78
## 4   4     1   30      98      58
## 5   9     3   20      98      15
## 6  10     3   50      98      45
## 7  11     3   65      65      65
## 8  12     3   45      85      32
## 9  17     5   65      68      98
## 10 18     5   80      78      90
## 11 19     5   89      68      87
## 12 20     5   78      83      58
```



```
# 추출한 행으로 데이터 만들기
class1 = exam %>% filter(class == 1) # class가 1인 행 추출, class1에 할당
class2 = exam %>% filter(class == 2) # class가 2인 행 추출, class2에 할당
```



```
mean(class1$math) # 1반 수학 점수 평균
## [1] 46.25
mean(class2$math) # 2반 수학 점수 평균
## [1] 61.25
```



### 혼자서 해보기



```
# mpg 데이터 이용
# Q1 배기량에 따라 고속도로 연비가 다른지 알아보기
# displ(배기량)이 4이하 / 5이상 자동차 중
# 어떤 자동차의 hwy(고속도로 연비)가 평균적으로 더 높은지 알아보기
mpg = as.data.frame(ggplot2::mpg)
mpg_disp1_a = mpg %>% filter(displ <= 4) 
mpg_disp1_b = mpg %>% filter(displ >= 5)

mean(mpg_disp1_a$hwy)
## [1] 25.96319
mean(mpg_disp1_b$hwy)
## [1] 18.07895
```



```
# Q2 제조 회사에 따라 도시연비 평균 확인
mpg_audi = mpg %>% filter(manufacturer == "audi")
mpg_toyota = mpg %>% filter(manufacturer == "toyota")

mean(mpg_audi$cty)
## [1] 17.61111
mean(mpg_toyota$cty)
## [1] 18.52941
```



```
# Q3 "chevrolet","ford" ,"honda" 자동차의 고속도로 연비 평균
mpg_data = mpg %>% filter(manufacturer %in% c("chevrolet","ford" ,"honda"))
mean(mpg_data$hwy)
## [1] 22.50943
```



### 06-3 필요한 변수만 추출



```
# 필요 변수만 추출 (select) , 열을 추출할 때 = select
exam %>% select(math)
##    math
## 1    50
## 2    60
## 3    45
## 4    30
## 5    25
## 6    50
## 7    80
## 8    90
## 9    20
## 10   50
## 11   65
## 12   45
## 13   46
## 14   48
## 15   75
## 16   58
## 17   65
## 18   80
## 19   89
## 20   78
exam %>% select(english)
##    english
## 1       98
## 2       97
## 3       86
## 4       98
## 5       80
## 6       89
## 7       90
## 8       78
## 9       98
## 10      98
## 11      65
## 12      85
## 13      98
## 14      87
## 15      56
## 16      98
## 17      68
## 18      78
## 19      68
## 20      83
exam %>% select(class, math, english) # 여러 번수 추출
##    class math english
## 1      1   50      98
## 2      1   60      97
## 3      1   45      86
## 4      1   30      98
## 5      2   25      80
## 6      2   50      89
## 7      2   80      90
## 8      2   90      78
## 9      3   20      98
## 10     3   50      98
## 11     3   65      65
## 12     3   45      85
## 13     4   46      98
## 14     4   48      87
## 15     4   75      56
## 16     4   58      98
## 17     5   65      68
## 18     5   80      78
## 19     5   89      68
## 20     5   78      83
exam %>% select(-math) # '-' 붙이면 해당 변수 제외
##    id class english science
## 1   1     1      98      50
## 2   2     1      97      60
## 3   3     1      86      78
## 4   4     1      98      58
## 5   5     2      80      65
## 6   6     2      89      98
## 7   7     2      90      45
## 8   8     2      78      25
## 9   9     3      98      15
## 10 10     3      98      45
## 11 11     3      65      65
## 12 12     3      85      32
## 13 13     4      98      65
## 14 14     4      87      12
## 15 15     4      56      78
## 16 16     4      98      65
## 17 17     5      68      98
## 18 18     5      78      90
## 19 19     5      68      87
## 20 20     5      83      58
exam %>% select(-math, -english)
##    id class science
## 1   1     1      50
## 2   2     1      60
## 3   3     1      78
## 4   4     1      58
## 5   5     2      65
## 6   6     2      98
## 7   7     2      45
## 8   8     2      25
## 9   9     3      15
## 10 10     3      45
## 11 11     3      65
## 12 12     3      32
## 13 13     4      65
## 14 14     4      12
## 15 15     4      78
## 16 16     4      65
## 17 17     5      98
## 18 18     5      90
## 19 19     5      87
## 20 20     5      58
```



```
# dplyr 함수 조합하기 
# class가 1인 행만 추출한 다음 english 추출
exam %>%  filter(class == 1) %>% select(english)
##   english
## 1      98
## 2      97
## 3      86
## 4      98
```



```
exam %>% 
  select(id, math) %>%  # id, math 추출
  head    # 앞 부분 6행 까지 추출 
##   id math
## 1  1   50
## 2  2   60
## 3  3   45
## 4  4   30
## 5  5   25
## 6  6   50
```



```
exam %>% 
  select(id, math) %>% # id, math 추출
  head(10)  # 앞 부분 10행 까지 추출
##    id math
## 1   1   50
## 2   2   60
## 3   3   45
## 4   4   30
## 5   5   25
## 6   6   50
## 7   7   80
## 8   8   90
## 9   9   20
## 10 10   50
```



### 혼자서 해보기



```
# mpg 데이터 이
# class(자동차 종류), cty(도시 연비) 변수를 추출, 데이터 생성 후 확인

# Q1
mpg = as.data.frame(ggplot2::mpg)

df = mpg %>% select(class, cty)
head(df)
##     class cty
## 1 compact  18
## 2 compact  21
## 3 compact  20
## 4 compact  21
## 5 compact  16
## 6 compact  18
```



```
# 자동차 종류에 따른 도시 연비 확인 
# suv, compact 종류 중 어느 종류 자동차의 cty 평균 연비가 더 좋은가?

# Q2
df_class_suv = df %>% filter(class == "suv")
df_class_com = df %>% filter(class == "compact")

mean(df_class_suv$cty)
## [1] 13.5
mean(df_class_com$cty)
## [1] 20.12766
```



### 06-4 순서대로 정렬하기기



```
# arrange 정렬

exam %>%  arrange(math) # math 오름차순 정렬
##    id class math english science
## 1   9     3   20      98      15
## 2   5     2   25      80      65
## 3   4     1   30      98      58
## 4   3     1   45      86      78
## 5  12     3   45      85      32
## 6  13     4   46      98      65
## 7  14     4   48      87      12
## 8   1     1   50      98      50
## 9   6     2   50      89      98
## 10 10     3   50      98      45
## 11 16     4   58      98      65
## 12  2     1   60      97      60
## 13 11     3   65      65      65
## 14 17     5   65      68      98
## 15 15     4   75      56      78
## 16 20     5   78      83      58
## 17  7     2   80      90      45
## 18 18     5   80      78      90
## 19 19     5   89      68      87
## 20  8     2   90      78      25
exam %>%  arrange(desc(math)) # math 내림차순 정렬렬
##    id class math english science
## 1   8     2   90      78      25
## 2  19     5   89      68      87
## 3   7     2   80      90      45
## 4  18     5   80      78      90
## 5  20     5   78      83      58
## 6  15     4   75      56      78
## 7  11     3   65      65      65
## 8  17     5   65      68      98
## 9   2     1   60      97      60
## 10 16     4   58      98      65
## 11  1     1   50      98      50
## 12  6     2   50      89      98
## 13 10     3   50      98      45
## 14 14     4   48      87      12
## 15 13     4   46      98      65
## 16  3     1   45      86      78
## 17 12     3   45      85      32
## 18  4     1   30      98      58
## 19  5     2   25      80      65
## 20  9     3   20      98      15
exam %>%  arrange(class, math)
##    id class math english science
## 1   4     1   30      98      58
## 2   3     1   45      86      78
## 3   1     1   50      98      50
## 4   2     1   60      97      60
## 5   5     2   25      80      65
## 6   6     2   50      89      98
## 7   7     2   80      90      45
## 8   8     2   90      78      25
## 9   9     3   20      98      15
## 10 12     3   45      85      32
## 11 10     3   50      98      45
## 12 11     3   65      65      65
## 13 13     4   46      98      65
## 14 14     4   48      87      12
## 15 16     4   58      98      65
## 16 15     4   75      56      78
## 17 17     5   65      68      98
## 18 20     5   78      83      58
## 19 18     5   80      78      90
## 20 19     5   89      68      87
```



### 혼자서 해보기



```
# mpg 데이터 이용
# audi 에서 생산한 자동차 중에서 hwy 가 높은 상위 5위 데이터 확인

# Q1
mpg = as.data.frame(ggplot2::mpg)

mpg_audi = mpg %>% filter(manufacturer == "audi") %>% # 아우디 추출
  arrange(desc(hwy)) # 고속도로 연비순 정렬

head(mpg_audi, 5)
##   manufacturer      model displ year cyl      trans drv cty hwy fl   class
## 1         audi         a4   2.0 2008   4 manual(m6)   f  20  31  p compact
## 2         audi         a4   2.0 2008   4   auto(av)   f  21  30  p compact
## 3         audi         a4   1.8 1999   4   auto(l5)   f  18  29  p compact
## 4         audi         a4   1.8 1999   4 manual(m5)   f  21  29  p compact
## 5         audi a4 quattro   2.0 2008   4 manual(m6)   4  20  28  p compact
```



### 06-5 파생변수 추가하기



```
# mutate 사용하여 파생변수 추가
exam %>% 
  mutate(total = math + english + science) %>% 
  head
##   id class math english science total
## 1  1     1   50      98      50   198
## 2  2     1   60      97      60   217
## 3  3     1   45      86      78   209
## 4  4     1   30      98      58   186
## 5  5     2   25      80      65   170
## 6  6     2   50      89      98   237
```



```
# 여러 파생변수 추가
exam %>% 
  mutate(total = math + english + science,
         mean = (math + english + science) / 3) %>% 
  head
##   id class math english science total     mean
## 1  1     1   50      98      50   198 66.00000
## 2  2     1   60      97      60   217 72.33333
## 3  3     1   45      86      78   209 69.66667
## 4  4     1   30      98      58   186 62.00000
## 5  5     2   25      80      65   170 56.66667
## 6  6     2   50      89      98   237 79.00000
```



```
# mutate에 ifelse() 적용용
exam %>% 
  mutate(test = ifelse(science >= 60, "pass", "fail")) %>% 
  head
##   id class math english science test
## 1  1     1   50      98      50 fail
## 2  2     1   60      97      60 pass
## 3  3     1   45      86      78 pass
## 4  4     1   30      98      58 fail
## 5  5     2   25      80      65 pass
## 6  6     2   50      89      98 pass
```



```
# 다중 필터 해보기 
exam %>% 
  mutate(total = math + english + science ) %>% #total 파생변수 추가후 
  arrange(total) %>%  # total 에 대해 오름차순 정렬
  head
##   id class math english science total
## 1  9     3   20      98      15   133
## 2 14     4   48      87      12   147
## 3 12     3   45      85      32   162
## 4  5     2   25      80      65   170
## 5  4     1   30      98      58   186
## 6  8     2   90      78      25   193
```



### 혼자서 해보기



```
# mpg 데이터 이용
# hwy, cty 두 종류 의 연비 변수를 통합한 새로운 통합연비 변수 생성

# Q1
mpg = as.data.frame(ggplot2::mpg)
mpg_new = mpg
mpg_new = mpg %>% 
  mutate(total = cty + hwy)
head(mpg_new, 5)
##   manufacturer model displ year cyl      trans drv cty hwy fl   class total
## 1         audi    a4   1.8 1999   4   auto(l5)   f  18  29  p compact    47
## 2         audi    a4   1.8 1999   4 manual(m5)   f  21  29  p compact    50
## 3         audi    a4   2.0 2008   4 manual(m6)   f  20  31  p compact    51
## 4         audi    a4   2.0 2008   4   auto(av)   f  21  30  p compact    51
## 5         audi    a4   2.8 1999   6   auto(l5)   f  16  26  p compact    42
```



```
# Q2
mpg_new = mpg_new %>% 
  mutate(mean = total/2)
head(mpg_new, 5)
##   manufacturer model displ year cyl      trans drv cty hwy fl   class total
## 1         audi    a4   1.8 1999   4   auto(l5)   f  18  29  p compact    47
## 2         audi    a4   1.8 1999   4 manual(m5)   f  21  29  p compact    50
## 3         audi    a4   2.0 2008   4 manual(m6)   f  20  31  p compact    51
## 4         audi    a4   2.0 2008   4   auto(av)   f  21  30  p compact    51
## 5         audi    a4   2.8 1999   6   auto(l5)   f  16  26  p compact    42
##   mean
## 1 23.5
## 2 25.0
## 3 25.5
## 4 25.5
## 5 21.0
```



```
mpg_new %>% 
  arrange(desc(mean)) %>% 
  head(3)
##   manufacturer      model displ year cyl      trans drv cty hwy fl      class
## 1   volkswagen new beetle   1.9 1999   4 manual(m5)   f  35  44  d subcompact
## 2   volkswagen      jetta   1.9 1999   4 manual(m5)   f  33  44  d    compact
## 3   volkswagen new beetle   1.9 1999   4   auto(l4)   f  29  41  d subcompact
##   total mean
## 1    79 39.5
## 2    77 38.5
## 3    70 35.0
```



```
mpg = as.data.frame(ggplot2::mpg)
mpg_new = mpg
mpg_new = mpg %>% 
  mutate(total = cty + hwy ,
         mean = total/2) %>% 
  arrange(desc(mean))
head(mpg_new, 3) 
##   manufacturer      model displ year cyl      trans drv cty hwy fl      class
## 1   volkswagen new beetle   1.9 1999   4 manual(m5)   f  35  44  d subcompact
## 2   volkswagen      jetta   1.9 1999   4 manual(m5)   f  33  44  d    compact
## 3   volkswagen new beetle   1.9 1999   4   auto(l4)   f  29  41  d subcompact
##   total mean
## 1    79 39.5
## 2    77 38.5
## 3    70 35.0
```



### 06-6 집단별로 요약하기



```
# 집단별로 요약 [group_by(), summarise()]
# 전체 수학에 대한 요약약
exam %>% 
  summarise(mean_math = mean(math)) # math 평균 산출출
##   mean_math
## 1     57.45
```



```
# 집단별로 요약
exam %>% 
  group_by(class) %>% # class 별로 분리
  summarise(mean_math = mean(math)) # math 평균 산출
## # A tibble: 5 x 2
##   class mean_math
##   <int>     <dbl>
## 1     1      46.2
## 2     2      61.2
## 3     3      45  
## 4     4      56.8
## 5     5      78
```



```
# 여러 요약 통계량 한번에 산출
exam %>% 
  group_by(class) %>% 
  summarise(mean_math = mean(math), 
            sum_math = sum(math),
            medidan_math = median(math),
            n = n()) # 학생수
## # A tibble: 5 x 5
##   class mean_math sum_math medidan_math     n
##   <int>     <dbl>    <int>        <dbl> <int>
## 1     1      46.2      185         47.5     4
## 2     2      61.2      245         65       4
## 3     3      45        180         47.5     4
## 4     4      56.8      227         53       4
## 5     5      78        312         79       4
```



```
# 각 집단별로 다시 집단 나누기
mpg = as.data.frame(ggplot2::mpg)
mpg_new = mpg
mpg_new = mpg_new %>% 
  group_by(manufacturer, drv) %>% 
  summarise(mean_cty = mean(cty))
## `summarise()` has grouped output by 'manufacturer'. You can override using the `.groups` argument.
head(mpg_new, 10)
## # A tibble: 10 x 3
## # Groups:   manufacturer [5]
##    manufacturer drv   mean_cty
##    <chr>        <chr>    <dbl>
##  1 audi         4         16.8
##  2 audi         f         18.9
##  3 chevrolet    4         12.5
##  4 chevrolet    f         18.8
##  5 chevrolet    r         14.1
##  6 dodge        4         12  
##  7 dodge        f         15.8
##  8 ford         4         13.3
##  9 ford         r         14.8
## 10 honda        f         24.4
```



```
# mission - dplyr 조합하기기
mpg = as.data.frame(ggplot2::mpg)
mpg_new = mpg
mpg_new = mpg_new %>% 
  group_by(manufacturer) %>% 
  filter(class == "suv") %>% 
  mutate(tot = (cty+hwy)/2) %>% 
  summarise(mean_tot = mean(tot)) %>% 
  arrange(desc(mean_tot))
head(mpg_new, 5)
## # A tibble: 5 x 2
##   manufacturer mean_tot
##   <chr>           <dbl>
## 1 subaru           21.9
## 2 toyota           16.3
## 3 nissan           15.9
## 4 mercury          15.6
## 5 jeep             15.6
```



### 혼자서 해보기



```
# Q1
mpg = as.data.frame(ggplot2::mpg)

mpg %>% 
  group_by(class) %>% 
  summarise(mean_cty = mean(cty)) %>% 
  head
## # A tibble: 6 x 2
##   class      mean_cty
##   <chr>         <dbl>
## 1 2seater        15.4
## 2 compact        20.1
## 3 midsize        18.8
## 4 minivan        15.8
## 5 pickup         13  
## 6 subcompact     20.4
```



```
# Q2
mpg %>% 
  group_by(class) %>% 
  summarise(mean_cty = mean(cty)) %>% 
  arrange(desc(mean_cty)) %>% 
  head
## # A tibble: 6 x 2
##   class      mean_cty
##   <chr>         <dbl>
## 1 subcompact     20.4
## 2 compact        20.1
## 3 midsize        18.8
## 4 minivan        15.8
## 5 2seater        15.4
## 6 suv            13.5
```



```
# Q3
mpg %>% 
  group_by(manufacturer) %>% 
  summarise(mean_hwy = mean(hwy)) %>% 
  arrange(mean_hwy) %>% 
  head(3)
## # A tibble: 3 x 2
##   manufacturer mean_hwy
##   <chr>           <dbl>
## 1 land rover       16.5
## 2 lincoln          17  
## 3 jeep             17.6
```



```
# Q4
mpg %>% 
  filter(class == "compact") %>% 
  group_by(manufacturer) %>%
  summarise(count = n()) %>% 
  arrange(desc(count)) %>% 
  head()
## # A tibble: 5 x 2
##   manufacturer count
##   <chr>        <int>
## 1 audi            15
## 2 volkswagen      14
## 3 toyota          12
## 4 subaru           4
## 5 nissan           2
```



### 06-7 데이터 합치기기



```
# 가로로 합치기
```



```
# 중간고사 데이터 생성
test1 = data.frame(id = c(1,2,3,4,5),
                   midterm = c(60,80,70,90,85))
# 기말고사 데이터 생성
test2 = data.frame(id = c(1,2,3,4,5),
                   final = c(70,83,65,95,80))

test1
##   id midterm
## 1  1      60
## 2  2      80
## 3  3      70
## 4  4      90
## 5  5      85
test2
##   id final
## 1  1    70
## 2  2    83
## 3  3    65
## 4  4    95
## 5  5    80
```



```
# dplyr의 left_join() 이용하여 데이터 가로로 합치기
library(dplyr)
exam = read.csv("./../Dataset/Data/csv_exam.csv")
total = left_join(test1, test2, by = "id") # id를 기준으로 합쳐 total에 할당당
total
##   id midterm final
## 1  1      60    70
## 2  2      80    83
## 3  3      70    65
## 4  4      90    95
## 5  5      85    80
```



```
# 다른 데이터를 활용해 변수 추가하기
name = data.frame(class = c(1,2,3,4,5),
                  teacher = c("kim", "lee", "park", "choi", "jung"))
name
##   class teacher
## 1     1     kim
## 2     2     lee
## 3     3    park
## 4     4    choi
## 5     5    jung
```



```
# exam 데이터와 name 데이터를 class 기준으로 합치기기
exam_new = left_join(exam, name, by = "class")
exam_new
##    id class math english science teacher
## 1   1     1   50      98      50     kim
## 2   2     1   60      97      60     kim
## 3   3     1   45      86      78     kim
## 4   4     1   30      98      58     kim
## 5   5     2   25      80      65     lee
## 6   6     2   50      89      98     lee
## 7   7     2   80      90      45     lee
## 8   8     2   90      78      25     lee
## 9   9     3   20      98      15    park
## 10 10     3   50      98      45    park
## 11 11     3   65      65      65    park
## 12 12     3   45      85      32    park
## 13 13     4   46      98      65    choi
## 14 14     4   48      87      12    choi
## 15 15     4   75      56      78    choi
## 16 16     4   58      98      65    choi
## 17 17     5   65      68      98    jung
## 18 18     5   80      78      90    jung
## 19 19     5   89      68      87    jung
## 20 20     5   78      83      58    jung
```



```
# 세로로 합치기
```



```
# 새로운 데이터 생성
# 학생 1~5번 시험 데이터 생성
group_a = data.frame(id = c(1,2,3,4,5),
                     test = c(60,80,70,90,85))
group_b = data.frame(id = c(6,7,8,9,10),
                     test = c(70,83,65,95,80))
group_a
##   id test
## 1  1   60
## 2  2   80
## 3  3   70
## 4  4   90
## 5  5   85
group_b
##   id test
## 1  6   70
## 2  7   83
## 3  8   65
## 4  9   95
## 5 10   80
```



```
# bind_row() 이용, 데이터 세로로 합치기
group_all = bind_rows(group_a, group_b)
group_all
##    id test
## 1   1   60
## 2   2   80
## 3   3   70
## 4   4   90
## 5   5   85
## 6   6   70
## 7   7   83
## 8   8   65
## 9   9   95
## 10 10   80
```



### 혼자서 해보기



```
# 연료와 가격으로 구성된 데이터 프레임 만들기기
fuel = data.frame(fl = c("c","d","e","p","r"),
                  price_fl = c(2.35, 2.38, 2.21, 2.76, 2.22))
fuel
##   fl price_fl
## 1  c     2.35
## 2  d     2.38
## 3  e     2.21
## 4  p     2.76
## 5  r     2.22
```



```
# Q1
mpg = as.data.frame(ggplot2::mpg)
mpg_new = mpg
mpg_new = left_join(mpg_new, fuel, by = "fl")
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
## 65         dodge    ram 1500 pickup 4wd   4.7 2008   8 manual(m6)   4  12  16
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
## 124         jeep     grand cherokee 4wd   3.7 2008   6   auto(l5)   4  15  19
## 125         jeep     grand cherokee 4wd   4.0 1999   6   auto(l4)   4  15  20
## 126         jeep     grand cherokee 4wd   4.7 1999   8   auto(l4)   4  14  17
## 127         jeep     grand cherokee 4wd   4.7 2008   8   auto(l5)   4   9  12
## 128         jeep     grand cherokee 4wd   4.7 2008   8   auto(l5)   4  14  19
## 129         jeep     grand cherokee 4wd   5.7 2008   8   auto(l5)   4  13  18
## 130         jeep     grand cherokee 4wd   6.1 2008   8   auto(l5)   4  11  14
## 131   land rover            range rover   4.0 1999   8   auto(l4)   4  11  15
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
## 153       nissan         pathfinder 4wd   4.0 2008   6   auto(l5)   4  14  20
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
## 212   volkswagen                    gti   2.8 1999   6 manual(m5)   f  17  24
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
##     fl      class price_fl
## 1    p    compact     2.76
## 2    p    compact     2.76
## 3    p    compact     2.76
## 4    p    compact     2.76
## 5    p    compact     2.76
## 6    p    compact     2.76
## 7    p    compact     2.76
## 8    p    compact     2.76
## 9    p    compact     2.76
## 10   p    compact     2.76
## 11   p    compact     2.76
## 12   p    compact     2.76
## 13   p    compact     2.76
## 14   p    compact     2.76
## 15   p    compact     2.76
## 16   p    midsize     2.76
## 17   p    midsize     2.76
## 18   p    midsize     2.76
## 19   r        suv     2.22
## 20   e        suv     2.21
## 21   r        suv     2.22
## 22   r        suv     2.22
## 23   r        suv     2.22
## 24   p    2seater     2.76
## 25   p    2seater     2.76
## 26   p    2seater     2.76
## 27   p    2seater     2.76
## 28   p    2seater     2.76
## 29   r        suv     2.22
## 30   e        suv     2.21
## 31   r        suv     2.22
## 32   d        suv     2.38
## 33   r    midsize     2.22
## 34   r    midsize     2.22
## 35   r    midsize     2.22
## 36   r    midsize     2.22
## 37   r    midsize     2.22
## 38   r    minivan     2.22
## 39   r    minivan     2.22
## 40   r    minivan     2.22
## 41   r    minivan     2.22
## 42   r    minivan     2.22
## 43   r    minivan     2.22
## 44   e    minivan     2.21
## 45   r    minivan     2.22
## 46   r    minivan     2.22
## 47   r    minivan     2.22
## 48   r    minivan     2.22
## 49   r     pickup     2.22
## 50   r     pickup     2.22
## 51   r     pickup     2.22
## 52   r     pickup     2.22
## 53   r     pickup     2.22
## 54   r     pickup     2.22
## 55   e     pickup     2.21
## 56   r     pickup     2.22
## 57   r     pickup     2.22
## 58   r        suv     2.22
## 59   r        suv     2.22
## 60   e        suv     2.21
## 61   r        suv     2.22
## 62   r        suv     2.22
## 63   r        suv     2.22
## 64   r        suv     2.22
## 65   r     pickup     2.22
## 66   e     pickup     2.21
## 67   r     pickup     2.22
## 68   r     pickup     2.22
## 69   r     pickup     2.22
## 70   e     pickup     2.21
## 71   r     pickup     2.22
## 72   r     pickup     2.22
## 73   r     pickup     2.22
## 74   r     pickup     2.22
## 75   r        suv     2.22
## 76   r        suv     2.22
## 77   r        suv     2.22
## 78   r        suv     2.22
## 79   r        suv     2.22
## 80   r        suv     2.22
## 81   r        suv     2.22
## 82   r        suv     2.22
## 83   r        suv     2.22
## 84   r     pickup     2.22
## 85   r     pickup     2.22
## 86   r     pickup     2.22
## 87   r     pickup     2.22
## 88   r     pickup     2.22
## 89   r     pickup     2.22
## 90   r     pickup     2.22
## 91   r subcompact     2.22
## 92   r subcompact     2.22
## 93   r subcompact     2.22
## 94   r subcompact     2.22
## 95   r subcompact     2.22
## 96   r subcompact     2.22
## 97   r subcompact     2.22
## 98   r subcompact     2.22
## 99   p subcompact     2.76
## 100  r subcompact     2.22
## 101  r subcompact     2.22
## 102  r subcompact     2.22
## 103  p subcompact     2.76
## 104  r subcompact     2.22
## 105  r subcompact     2.22
## 106  r subcompact     2.22
## 107  c subcompact     2.35
## 108  p subcompact     2.76
## 109  r    midsize     2.22
## 110  r    midsize     2.22
## 111  r    midsize     2.22
## 112  r    midsize     2.22
## 113  r    midsize     2.22
## 114  r    midsize     2.22
## 115  r    midsize     2.22
## 116  r subcompact     2.22
## 117  r subcompact     2.22
## 118  r subcompact     2.22
## 119  r subcompact     2.22
## 120  r subcompact     2.22
## 121  r subcompact     2.22
## 122  r subcompact     2.22
## 123  d        suv     2.38
## 124  r        suv     2.22
## 125  r        suv     2.22
## 126  r        suv     2.22
## 127  e        suv     2.21
## 128  r        suv     2.22
## 129  r        suv     2.22
## 130  p        suv     2.76
## 131  p        suv     2.76
## 132  r        suv     2.22
## 133  r        suv     2.22
## 134  p        suv     2.76
## 135  r        suv     2.22
## 136  p        suv     2.76
## 137  r        suv     2.22
## 138  r        suv     2.22
## 139  r        suv     2.22
## 140  r        suv     2.22
## 141  r        suv     2.22
## 142  r    compact     2.22
## 143  r    compact     2.22
## 144  r    midsize     2.22
## 145  r    midsize     2.22
## 146  p    midsize     2.76
## 147  p    midsize     2.76
## 148  r    midsize     2.22
## 149  r    midsize     2.22
## 150  p    midsize     2.76
## 151  r        suv     2.22
## 152  r        suv     2.22
## 153  p        suv     2.76
## 154  p        suv     2.76
## 155  r    midsize     2.22
## 156  p    midsize     2.76
## 157  r    midsize     2.22
## 158  r    midsize     2.22
## 159  p    midsize     2.76
## 160  r        suv     2.22
## 161  r        suv     2.22
## 162  r        suv     2.22
## 163  p        suv     2.76
## 164  r        suv     2.22
## 165  p        suv     2.76
## 166  r subcompact     2.22
## 167  r subcompact     2.22
## 168  r subcompact     2.22
## 169  r subcompact     2.22
## 170  p    compact     2.76
## 171  r    compact     2.22
## 172  p    compact     2.76
## 173  r    compact     2.22
## 174  r        suv     2.22
## 175  r        suv     2.22
## 176  r        suv     2.22
## 177  r        suv     2.22
## 178  r        suv     2.22
## 179  r        suv     2.22
## 180  r    midsize     2.22
## 181  r    midsize     2.22
## 182  r    midsize     2.22
## 183  r    midsize     2.22
## 184  r    midsize     2.22
## 185  r    midsize     2.22
## 186  r    midsize     2.22
## 187  r    compact     2.22
## 188  r    compact     2.22
## 189  r    compact     2.22
## 190  r    compact     2.22
## 191  r    compact     2.22
## 192  r    compact     2.22
## 193  r    compact     2.22
## 194  r    compact     2.22
## 195  r    compact     2.22
## 196  r    compact     2.22
## 197  r    compact     2.22
## 198  r    compact     2.22
## 199  r        suv     2.22
## 200  r        suv     2.22
## 201  r     pickup     2.22
## 202  r     pickup     2.22
## 203  r     pickup     2.22
## 204  r     pickup     2.22
## 205  r     pickup     2.22
## 206  r     pickup     2.22
## 207  r     pickup     2.22
## 208  r    compact     2.22
## 209  r    compact     2.22
## 210  p    compact     2.76
## 211  p    compact     2.76
## 212  r    compact     2.22
## 213  d    compact     2.38
## 214  r    compact     2.22
## 215  r    compact     2.22
## 216  p    compact     2.76
## 217  p    compact     2.76
## 218  r    compact     2.22
## 219  r    compact     2.22
## 220  r    compact     2.22
## 221  r    compact     2.22
## 222  d subcompact     2.38
## 223  d subcompact     2.38
## 224  r subcompact     2.22
## 225  r subcompact     2.22
## 226  r subcompact     2.22
## 227  r subcompact     2.22
## 228  p    midsize     2.76
## 229  p    midsize     2.76
## 230  p    midsize     2.76
## 231  p    midsize     2.76
## 232  p    midsize     2.76
## 233  p    midsize     2.76
## 234  p    midsize     2.76
```



```
# Q2
mpg_new = mpg_new %>% 
  select(model, fl, price_fl)
head(mpg_new, 5)
##   model fl price_fl
## 1    a4  p     2.76
## 2    a4  p     2.76
## 3    a4  p     2.76
## 4    a4  p     2.76
## 5    a4  p     2.76
```



### 분석 도전



```
# Q1
midwest = as.data.frame(ggplot2::midwest)
midwest_new = midwest
head(midwest_new, 5)
##   PID    county state  area poptotal popdensity popwhite popblack popamerindian
## 1 561     ADAMS    IL 0.052    66090  1270.9615    63917     1702            98
## 2 562 ALEXANDER    IL 0.014    10626   759.0000     7054     3496            19
## 3 563      BOND    IL 0.022    14991   681.4091    14477      429            35
## 4 564     BOONE    IL 0.017    30806  1812.1176    29344      127            46
## 5 565     BROWN    IL 0.018     5836   324.2222     5264      547            14
##   popasian popother percwhite  percblack percamerindan  percasian  percother
## 1      249      124  96.71206  2.5752761     0.1482826 0.37675897 0.18762294
## 2       48        9  66.38434 32.9004329     0.1788067 0.45172219 0.08469791
## 3       16       34  96.57128  2.8617170     0.2334734 0.10673071 0.22680275
## 4      150     1139  95.25417  0.4122574     0.1493216 0.48691813 3.69733169
## 5        5        6  90.19877  9.3728581     0.2398903 0.08567512 0.10281014
##   popadults  perchsd percollege percprof poppovertyknown percpovertyknown
## 1     43298 75.10740   19.63139 4.355859           63628         96.27478
## 2      6724 59.72635   11.24331 2.870315           10529         99.08714
## 3      9669 69.33499   17.03382 4.488572           14235         94.95697
## 4     19272 75.47219   17.27895 4.197800           30337         98.47757
## 5      3979 68.86152   14.47600 3.367680            4815         82.50514
##   percbelowpoverty percchildbelowpovert percadultpoverty percelderlypoverty
## 1        13.151443             18.01172        11.009776          12.443812
## 2        32.244278             45.82651        27.385647          25.228976
## 3        12.068844             14.03606        10.852090          12.697410
## 4         7.209019             11.17954         5.536013           6.217047
## 5        13.520249             13.02289        11.143211          19.200000
##   inmetro category
## 1       0      AAR
## 2       0      LHR
## 3       0      AAR
## 4       1      ALU
## 5       0      AAR
midwest_new = midwest_new %>% 
  mutate(ratio_child = (1 - popadults / poptotal) * 100)
head(midwest_new)
##   PID    county state  area poptotal popdensity popwhite popblack popamerindian
## 1 561     ADAMS    IL 0.052    66090  1270.9615    63917     1702            98
## 2 562 ALEXANDER    IL 0.014    10626   759.0000     7054     3496            19
## 3 563      BOND    IL 0.022    14991   681.4091    14477      429            35
## 4 564     BOONE    IL 0.017    30806  1812.1176    29344      127            46
## 5 565     BROWN    IL 0.018     5836   324.2222     5264      547            14
## 6 566    BUREAU    IL 0.050    35688   713.7600    35157       50            65
##   popasian popother percwhite  percblack percamerindan  percasian  percother
## 1      249      124  96.71206  2.5752761     0.1482826 0.37675897 0.18762294
## 2       48        9  66.38434 32.9004329     0.1788067 0.45172219 0.08469791
## 3       16       34  96.57128  2.8617170     0.2334734 0.10673071 0.22680275
## 4      150     1139  95.25417  0.4122574     0.1493216 0.48691813 3.69733169
## 5        5        6  90.19877  9.3728581     0.2398903 0.08567512 0.10281014
## 6      195      221  98.51210  0.1401031     0.1821340 0.54640215 0.61925577
##   popadults  perchsd percollege percprof poppovertyknown percpovertyknown
## 1     43298 75.10740   19.63139 4.355859           63628         96.27478
## 2      6724 59.72635   11.24331 2.870315           10529         99.08714
## 3      9669 69.33499   17.03382 4.488572           14235         94.95697
## 4     19272 75.47219   17.27895 4.197800           30337         98.47757
## 5      3979 68.86152   14.47600 3.367680            4815         82.50514
## 6     23444 76.62941   18.90462 3.275891           35107         98.37200
##   percbelowpoverty percchildbelowpovert percadultpoverty percelderlypoverty
## 1        13.151443             18.01172        11.009776          12.443812
## 2        32.244278             45.82651        27.385647          25.228976
## 3        12.068844             14.03606        10.852090          12.697410
## 4         7.209019             11.17954         5.536013           6.217047
## 5        13.520249             13.02289        11.143211          19.200000
## 6        10.399635             14.15882         8.179287          11.008586
##   inmetro category ratio_child
## 1       0      AAR    34.48631
## 2       0      LHR    36.72125
## 3       0      AAR    35.50130
## 4       1      ALU    37.44076
## 5       0      AAR    31.81974
## 6       0      AAR    34.30845
```



```
# Q2
midwest_new = midwest_new %>%
  arrange(desc(ratio_child)) %>% 
  select(county, ratio_child)
head(midwest_new, 5)
##      county ratio_child
## 1  ISABELLA    51.50117
## 2 MENOMINEE    50.59126
## 3    ATHENS    49.32073
## 4   MECOSTA    49.05918
## 5    MONROE    47.35818
```





```
# Q3
midwest_new = midwest_new %>% 
  mutate(grade = ifelse(ratio_child >= 40, "large",
                        ifelse(ratio_child < 30, "small", "middle"))) %>% 
  group_by(grade) %>% 
  summarise(grade_no = n())

midwest_new
## # A tibble: 3 x 2
##   grade  grade_no
##   <chr>     <int>
## 1 large        32
## 2 middle      396
## 3 small         9
```



```
table(midwest_new$grade) # table 명령어로 한방에 표현 가능...^^;
## 
##  large middle  small 
##      1      1      1
```



```
# Q4
midwest_new1 = midwest
midwest_new1 = midwest_new1 %>% 
  mutate(ratio_asia = popasian/poptotal * 100) %>% 
  select(state, county, ratio_asia) %>% 
  arrange(ratio_asia)
head(midwest_new1, 10)
##    state    county ratio_asia
## 1     WI MENOMINEE 0.00000000
## 2     IN    BENTON 0.01059210
## 3     IN   CARROLL 0.01594981
## 4     OH    VINTON 0.02703190
## 5     WI      IRON 0.03250447
## 6     IL     SCOTT 0.05315379
## 7     IN      CLAY 0.06071645
## 8     MI    OSCODA 0.06375925
## 9     OH     PERRY 0.06654625
## 10    IL     PIATT 0.07074865
```
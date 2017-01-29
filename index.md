---
title: Introdução
date: '2017-01-29'
---





## Lubridate

O pacote lubridate faz o trabalho com datas ser muito mais fácil. Ela foi criada por Garrett Grolemund e Hadley Wickham.


```r
library(lubridate)
```

```
## Loading required package: methods
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

```r
library(magrittr)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:lubridate':
## 
##     intersect, setdiff, union
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

Resumo do que aprenderemos:

- transformar character de diversos formatos em data.
- transformar data em character de diversos formatos.
- extrair informações das datas `y m d h m s`
- funções úteis (`today()`, `now()`, etc)
- timezones
- operações aritméticas com datas
- gerar datas

Vamos usar o banco de dados `lakers` para os exemplos.

## Transformar `character` em `date`


```r
df <- data_frame(`entrada` = c("")
                 , `função` = c("")
                 , `saída` = c(""))
```


Exemplos com datas


```r
lakers %>% 
  select(date) %>% 
  mutate(`date %>% ymd` = date %>% ymd,
         `date %>% ymd_hm` = date %>% ymd_hm) %>% 
  head %>%
  knitr::kable()
```

```
## Warning: All formats failed to parse. No formats found.
```



|     date|date %>% ymd |date %>% ymd_hm |
|--------:|:------------|:---------------|
| 20081028|2008-10-28   |NA              |
| 20081028|2008-10-28   |NA              |
| 20081028|2008-10-28   |NA              |
| 20081028|2008-10-28   |NA              |
| 20081028|2008-10-28   |NA              |
| 20081028|2008-10-28   |NA              |

Exemplos com datas e horas


```r
lakers %>% 
  select(date, time) %>% 
  mutate(`paste(date, time) %>% ymd` = paste(date, time) %>% ymd,
         `paste(date, time) %>% ymd_hm` = paste(date, time) %>% ymd_hm,
         `time %>% hm` = time %>% hm,
         `time %>% hms` = time %>% hms
         ) %>% 
  head %>%
  knitr::kable()
```

```
## Warning: All formats failed to parse. No formats found.
```

```
## Warning in .parse_hms(..., order = "HMS", quiet = quiet): Some strings
## failed to parse
```



|     date|time  |paste(date, time) %>% ymd |paste(date, time) %>% ymd_hm | time %>% hm| time %>% hms|
|--------:|:-----|:-------------------------|:----------------------------|-----------:|------------:|
| 20081028|12:00 |NA                        |2008-10-28 12:00:00          |   12H 0M 0S|           NA|
| 20081028|11:39 |NA                        |2008-10-28 11:39:00          |  11H 39M 0S|           NA|
| 20081028|11:37 |NA                        |2008-10-28 11:37:00          |  11H 37M 0S|           NA|
| 20081028|11:25 |NA                        |2008-10-28 11:25:00          |  11H 25M 0S|           NA|
| 20081028|11:23 |NA                        |2008-10-28 11:23:00          |  11H 23M 0S|           NA|
| 20081028|11:22 |NA                        |2008-10-28 11:22:00          |  11H 22M 0S|           NA|


## Transformar `date` em `character`


## Extrair informações de datas


## Funções úteis


## Operações aritméticas entre datas


## Gerar datas



---
title: Introdução
date: '2017-08-11'
---





## Introdução

Trabalhar com datas no R pode ser uma chateação. As funções do R base são, em geral, contraintuitivas e podem mudar de acordo com o tipo do objeto que você está usando. Além disso, características como fusos horários, anos bissextos, horários de verão, entre outras, podem não estar bem especificadas pelos métodos criados ou nem mesmo sendo levadas em conta.

O pacote lubridate lida com esses problemas, fazendo o trabalho com datas ser muito mais fácil. Ele foi criado por Garrett Grolemund e Hadley Wickham.

Para instalar, siga uma das opções abaixo:


```r
# Instale ao lado de outros pacotes do tidyverse.
install.packages("tidyverse")

# Instale apenas o lubridate.
install.packages("lubridate")

# Instale a versão de desenvolvimento direto do Github.
# Caso não tenha o devtools instalado: install.packages("devtools").
devtools::install_github("tidyverse/lubridate")
```

Para carregar o pacote, basta rodar


```r
library(lubridate)
## Loading required package: methods
## 
## Attaching package: 'lubridate'
## The following object is masked from 'package:base':
## 
##     date
```

Neste seção, aprenderemos:

- Transformar e extrair datas.
- Algumas funções úteis para trabalhar com datas.
- Trabalhar com fusos horários.
- Operações aritméticas com datas.

--------------------------------------------------------------------------------





## A classe "date"

Datas no R são tratadas como um tipo especial de objeto, com classe `date`. Há várias formas de criar objetos dessa classe:


```r
data_string <- "21-10-2015"
class(data_string)
## [1] "character"

data_date <- date(data_string)
## Error in date(data_string): unused argument (data_string)
class(data_date)
## Error in eval(expr, envir, enclos): object 'data_date' not found

data_as_date <- as_date(data_string)
class(data_as_date)
## [1] "Date"

data_mdy <- dmy(data_string)
class(data_mdy)
## [1] "Date"
```

Veja que as funções `date()` e `as_date()` converteram a *string* para data, mas não devolveram o valor esperado. A função `dmy()` resolve esse problema estabelecendo explicitamente a ordem dia-mês-ano. Existem funções para todas as ordens possíveis: `dmy()`, `mdy()`, `myd()`, `ymd()`, `ydm()` etc.

As funções `date()` e `as_date()` assumem que a ordem é ano-mês-dia (ymd).


```r
date("2015-10-21")
## Error in date("2015-10-21"): unused argument ("2015-10-21")
as_date("2015-10-21")
## [1] "2015-10-21"
```

Uma grande facilidade que essas funções trazem é poder criar objetos com classe `date` a partir de números e *strings* em diversos formatos.


```r
dmy(21102015)
## [1] "2015-10-21"
dmy("21102015")
## [1] "2015-10-21"
dmy("21/10/2015")
## [1] "2015-10-21"
dmy("21.10.2015")
## [1] "2015-10-21"
```

Se além da data você precisar especificar o horário, basta usar as funções do tipo `ymd_h()`, `ymd_hm()`, `ymd_hms()`, sendo que também há uma função para cada formato de dia-mês-ano.



```r
ymd_hms(20151021165411)
## [1] "2015-10-21 16:54:11 UTC"
```

--------------------------------------------------------------------------------





## Funções úteis

O `lubridate` traz diversas funções para extrair os componentes de um objeto da classe `date`. 

- `second()` - extrai os segundos.
- `minute()` - extrai os minutos.
- `hour()` - extrai a hora.
- `wday()` - extrai o dia da semana.
- `mday()` - extrai o dia do mês.
- `month()` - extrai o mês.
- `year()` - extrai o ano.

Os nomes são tão óbvios que a explicação do que cada função faz é praticamente desnecessária.


```r
bday <- ymd_hms(19890729030142)
bday
## [1] "1989-07-29 03:01:42 UTC"

second(bday)
## [1] 42
day(bday)
## [1] 29
month(bday)
## [1] 7
year(bday)
## [1] 1989

wday(bday)
## [1] 7
wday(bday, label = TRUE)
## [1] Sat
## Levels: Sun < Mon < Tues < Wed < Thurs < Fri < Sat
```

Você também pode usar essas funções para atribuir esses componentes a uma data:


```r
data <- dmy(01041991)
data
## [1] "1991-04-01"

hour(data) <- 20
data
## [1] "1991-04-01 20:00:00 UTC"
```

Também existem funções para extrair a data no instante da execução.


```r
today() 
## [1] "2017-08-11"
now()
## [1] "2017-08-11 23:28:35 UTC"

# Data e horário do dia em que essa página foi editada pela última vez.
```

--------------------------------------------------------------------------------





## Fusos horários

Uma característica inerente das datas é o fuso horário. Se você tiver trabalhando com datas de eventos no Brasil e na Escócia, por exemplo, é preciso verificar se os valores seguem o mesmo fuso horário. Além disso, quando a data exata de um evento for relevante, você pode precisar converter horários para outros fusos para comunicar seus resultados em outros países.

Para fazer essas coisas, o `lubridate` fornece as funções `with_tz()` e `force_tz()`.


```r
estreia_GoT <- ymd_hms("2017-07-16 22:00:00", tz = "America/Sao_Paulo")
estreia_GoT
## [1] "2017-07-16 22:00:00 BRT"

# Calcula qual seria a data em outro fuso

with_tz(estreia_GoT, tzone = "GMT")
## [1] "2017-07-17 01:00:00 GMT"
with_tz(estreia_GoT, tzone = "US/Alaska")  
## [1] "2017-07-16 17:00:00 AKDT"

# Altera o fuso sem mudar a data

force_tz(estreia_GoT, tzone = "GMT")
## [1] "2017-07-16 22:00:00 GMT"
```

--------------------------------------------------------------------------------





## Operações com datas

O pacote `lubridate` possui ainda funções para calcular intervalos e fazer operações aritméticas com datas.

### Intervalos

Intervalos podem ser salvos em objetos com classe `interval`.


```r
inicio <- dmy("01-04-1991")
evento <- dmy("31-10-1993")

sobrev <- interval(inicio, evento)
sobrev
## [1] 1991-04-01 UTC--1993-10-31 UTC
class(sobrev)
## [1] "Interval"
## attr(,"package")
## [1] "lubridate"
```

Você pode verificar se dois intervalos tem intersecção com a função `int_overlaps()`.


```r
# Outra forma de definir um intervalo: o operador %--%
intervalo_1 <- dmy("01-02-2003") %--% dmy("02-03-2005")  

intervalo_2 <- dmy("04-05-2004") %--% dmy("12-03-2012")  

int_overlaps(intervalo_1, intervalo_2)
## [1] TRUE
```

### Aritmética com datas

Veja alguns exemplos de operações aritméticas que você pode fazer utilizando funções do `lubridate`:


```r

# Somando datas

data <- dmy(31012000)

data + ddays(1)
## [1] "2000-02-01"
data + dyears(1)
## [1] "2001-01-30"

data + months(1)  # Operações que resultam em datas inválidas geram NAs.
## [1] NA

# Criando datas recorrentes

reuniao <- dmy("18-03-2017")
reunioes <- reuniao + weeks(0:10)
reunioes
##  [1] "2017-03-18" "2017-03-25" "2017-04-01" "2017-04-08" "2017-04-15"
##  [6] "2017-04-22" "2017-04-29" "2017-05-06" "2017-05-13" "2017-05-20"
## [11] "2017-05-27"

# Duração de um intervalo 

intervalo <-dmy("01-03-2003") %--% dmy("31-03-2003") 

intervalo / ddays(1)               # Número de dias
## [1] 30
intervalo / dminutes(1)            # Número de minutos
## [1] 43200

as.period(intervalo)
## [1] "30d 0H 0M 0S"
```

Para mais informações sobre o`lubridate`, visite o [vignette do pacote](https://cran.r-project.org/web/packages/lubridate/vignettes/lubridate.html).

--------------------------------------------------------------------------------


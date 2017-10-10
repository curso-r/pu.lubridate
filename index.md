---
title: 'Manipulando datas'
date: '2017-10-10'
---





## Introdução

Trabalhar com datas no R pode ser uma chateação. As funções do R base são, em geral, contraintuitivas e podem mudar de acordo com o tipo do objeto que você está usando. Além disso, características como fusos horários, anos bissextos, horários de verão, entre outras, podem não estar bem especificadas pelos métodos criados ou nem mesmo sendo levadas em conta.

O pacote `lubridate`, criado por [Garrett Grolemund](https://github.com/garrettgman) e [Hadley Wickham](https://github.com/hadley), lida com esses problemas, fazendo o trabalho com datas ser muito mais fácil.

Para instalar, siga uma das opções abaixo:


```r
# Instale ao lado de outros pacotes do tidyverse.
install.packages("tidyverse")

# Instale apenas o lubridate.
install.packages("lubridate")

# Instale a versão de desenvolvimento direto do Github.
devtools::install_github("tidyverse/lubridate")

# Se você não tiver o pacote devtools instalado...
install.packages("devtools")

```

Para carregar o pacote, basta rodar


```r
library(lubridate)
```

Neste seção, falaremos sobre:

- transformar e extrair datas;
- algumas funções úteis para trabalhar com datas;
- trabalhar com fusos horários; e
- operações aritméticas com datas.

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

As funções `date()` e `as_date()` assumem que a ordem segue o padrão da língua inglesa: ano-mês-dia (ymd).


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
## [1] "2017-10-10"
now()
## [1] "2017-10-10 14:21:21 UTC"

# Data e horário do dia em que essa página foi editada pela última vez.
```

--------------------------------------------------------------------------------





## Fusos horários

Uma característica inerente das datas é o fuso horário. Se você estiver trabalhando com datas de eventos no Brasil e na Escócia, por exemplo, é preciso verificar se os valores seguem o mesmo fuso horário. Além disso, quando a data exata de um evento for relevante, você pode precisar converter horários para outros fusos para comunicar seus resultados em outros países.

Para fazer essas coisas, o `lubridate` fornece as funções `with_tz()` e `force_tz()`. Veja um exemplo de como usá-las.


```r
estreia_GoT <- ymd_hms("2017-07-16 22:00:00", tz = "America/Sao_Paulo")
estreia_GoT
## [1] "2017-07-16 22:00:00 BRT"

# Devolve qual seria a data em outro fuso

with_tz(estreia_GoT, tzone = "GMT")
## [1] "2017-07-17 01:00:00 GMT"
with_tz(estreia_GoT, tzone = "US/Alaska")  
## [1] "2017-07-16 17:00:00 AKDT"

# Altera o fuso sem mudar a hora

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



## Exercícios

Nos exercícios a seguir, vamos utilizar a base `lakers`, que contém estatísticas jogo a jogo do [Los Angeles Lakers](http://www.nba.com/lakers/) na temporada 2008-2009.


```r
lakers %>% as_tibble()
## Error in lakers %>% as_tibble(): could not find function "%>%"
```

--------------------------------------------------------------------------------

**1.**

Repare que a coluna `date` no data.frame é um vetor de inteiros. Transforme essa coluna em um vetor de valores com classe `date`.

--------------------------------------------------------------------------------

**2.**

Crie uma coluna que junte as informações de data e tempo de jogo (colunas `date` e `time`) em objetos da classe `date`.

--------------------------------------------------------------------------------

**3.**

Crie as colunas `dia`, `mês` e `ano` com as respectivas informações sobre a data do jogo.

--------------------------------------------------------------------------------

**4.**

Em média, quanto tempo o Lakers demora para arremessar a primeira bola no primeiro período?

**Dica**: arremessos são representados pela categoria `shot` da coluna `etype`.

--------------------------------------------------------------------------------

**5.**

Em média, quanto tempo demora para sair a primeira cesta de três pontos? Considere toda a base, e cestas de ambos os times.

--------------------------------------------------------------------------------

**6.**

Construa boxplots do tempo entre pontos consecultivos para cada períodos. Considere toda a base de dados e apenas pontos do Lakers.

--------------------------------------------------------------------------------

**7.**

Qual foi o dia e mês do jogo que o Lakers demorou mais tempo para fazer uma cesta? Quanto tempo foi?

--------------------------------------------------------------------------------






```
## Error in library(dplyr): there is no package called 'dplyr'
```


## Respostas

<div class='admonition note'>
<p class='admonition-title'>
Nota
</p>
<p>
Não há apenas uma maneira de resolver os exercícios. Você pode encontrar soluções diferentes das nossas, algumas vezes mais eficientes, outras vezes menos. Quando estiver fazendo suas análises, tente buscar o equilíbrio entre eficiência e praticidade. Economizar 1 hora com a execução do código pode não valer a pena se você demorou 2 horas a mais para programá-lo.
</p>
</div>

--------------------------------------------------------------------------------

**1.**

Crie uma nova coluna no data.frame com as datas no formato dia-mês-ano.


```r
lakers %>% 
  mutate(date = ymd(date))
## Error in mutate(., date = ymd(date)): could not find function "mutate"
```

Repare que `as_date()` não funciona neste caso.


```r
lakers %>%
  mutate(date = as_date(date))
## Error in mutate(., date = as_date(date)): could not find function "mutate"
```

Para entender porque a função devolveu um dia do ano 56949, rode os códigos abaixo.


```r
as_date(0)
## [1] "1970-01-01"
as_date(-3:3)
## [1] "1969-12-29" "1969-12-30" "1969-12-31" "1970-01-01" "1970-01-02"
## [6] "1970-01-03" "1970-01-04"
as_date(20081027:20081029)
## [1] "56949-12-26" "56949-12-27" "56949-12-28"
```


--------------------------------------------------------------------------------

**2.**

Crie uma coluna que junte as informações de data e hora (colunas `date` e `time`) em objetos da classe `date`.


```r
lakers %>% 
  mutate(date_time = paste0(date, " 00:", time) %>% ymd_hms) %>% 
  select(date_time)
## Error in mutate(., date_time = paste0(date, " 00:", time) %>% ymd_hms): could not find function "mutate"
```


--------------------------------------------------------------------------------

**3.**

Crie as colunas `dia`, `mes` e `ano` com as respectivas informações sobre a data do jogo.


```r
lakers %>%
  mutate(date = ymd(date),
         dia = day(date),
         mes = month(date),
         ano = year(date)) %>% 
  select(date, dia, mes, ano)
## Error in mutate(., date = ymd(date), dia = day(date), mes = month(date), : could not find function "mutate"
```


--------------------------------------------------------------------------------

**4.**

Em média, quanto tempo o Lakers demora para arremessar a primeira bola no primeiro período?

**Dicas**: arremessos são representados pela categoria `shot` da coluna `etype` e cada período tem 12 minutos.



```r
lakers %>% 
  dplyr::filter(etype == "shot", period == 1, team == "LAL") %>% 
  dplyr::mutate(time = hms(paste0("00:", time)),
                cronometro = 12*60 - minute(time)*60 - second(time)) %>% 
  dplyr::group_by(date) %>% 
  dplyr::filter(cronometro == min(cronometro)) %>% 
  dplyr::ungroup() %>% 
  dplyr::summarise(media = mean(cronometro))
## Error in loadNamespace(name): there is no package called 'dplyr'
```

O que foi feito:

- Primeiro filtramos a base para arremessos, `etype == "shot"`, do primeiro período, `period == `, que fossem do Lakers, `team == "LAL"`. 

- Em seguida, mudamos a classe da coluna `time`, de `character` para `period`, e criamos a coluna `cronometro`, que contém o tempo passado (em segundos) até o instante do evento.

- Então agrupamos a base pelo dia e a filtramos apenas para o primeiro evento de cada dia, isto é, o evento que tem o menor valor na coluna `cronometro`. Assim, a coluna `cronometro` da base resultante terá o tempo do primeiro arremesso de cada jogo.

- Por fim, desagrupamos a base e calculamos a média da coluna `cronometro`.

--------------------------------------------------------------------------------

**5.**

Em média, quanto tempo demora para sair a primeira cesta de três pontos em cada um dos quatro períodos? Considere toda a base, e cestas de ambos os times.


```r
lakers %>% 
  dplyr::filter(etype == "shot", period %in% 1:4, points == "3") %>% 
  dplyr::mutate(time = hms(paste0("00:", time)),
                cronometro = 12*60 - minute(time)*60 - second(time)) %>% 
  dplyr::group_by(date, period) %>% 
  dplyr::filter(cronometro == min(cronometro)) %>% 
  dplyr::ungroup() %>%
  dplyr::group_by(period) %>% 
  dplyr::summarise(media = mean(cronometro))
## Error in loadNamespace(name): there is no package called 'dplyr'
```

A resolução desse exercício é análoga ao anterior, só mudamos o filtro inicial e o agrupamento. Se você ficou com dúvidas, consulte o passo a passo na resolução do exercício 4.

Repare que não precisarímos filtrar por `etype == "shot"`, já que o único evento que gera 3 pontos é a cesta de 3 pontos.

--------------------------------------------------------------------------------

**6.**

Construa boxplots do tempo entre pontos consecultivos para cada períodos. Considere toda a base de dados e apenas pontos do Lakers.

--------------------------------------------------------------------------------

**7.**

Qual foi o dia e mês do jogo que o Lakers demorou mais tempo para fazer uma cesta? Quanto tempo foi?

--------------------------------------------------------------------------------


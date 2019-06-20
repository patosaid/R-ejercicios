Estadísticas descriptivas y manipulación de datos (workflow)
================

A continuación se generarán estadísticas descriptivas rapidamente con
`tidyverse`, específicamente con `dplyr`. `dplyr` contiene varias
funciones para manipulación de datos y cálculos de estadísticas muy
simples. Se trabajará con un conjunto de datos que viene en `dplyr`, por
tanto no se importarán los datos. `dplyr` es util cuando la forma de los
datos ya viene preparada. Cuando los datos estan desordenados, primero
se deberán manipular para que tengan un formato *tidy*, para esto se
recomienda usar `tidyr`.  
Entonces, se usará el paquete `tidyr` para *reshape* o cambiar la forma
y limpiar los datos. Se usarán las “verbos” o funciones de `tidyverse`,
pero también a modo de comparación, se hará un análisis con las
funciones estandar o funciones bases de R.

## Cargamos los paquetes y datos

Cargamos el conjunto de datos `starwars` que está incluido en el paquete
`dplyr`:

``` r
library(dplyr)
data(starwars)
```

Un vistazo rápido al conjunto:

``` r
head(starwars)
```

    ## # A tibble: 6 x 13
    ##   name  height  mass hair_color skin_color eye_color birth_year gender
    ##   <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> 
    ## 1 Luke~    172    77 blond      fair       blue            19   male  
    ## 2 C-3PO    167    75 <NA>       gold       yellow         112   <NA>  
    ## 3 R2-D2     96    32 <NA>       white, bl~ red             33   <NA>  
    ## 4 Dart~    202   136 none       white      yellow          41.9 male  
    ## 5 Leia~    150    49 brown      light      brown           19   female
    ## 6 Owen~    178   120 brown, gr~ light      blue            52   male  
    ## # ... with 5 more variables: homeworld <chr>, species <chr>, films <list>,
    ## #   vehicles <list>, starships <list>

Este conjunto de datos contiene información sobre los personajes de Star
Wars. Por ejemplo, una pregunta podría ser: la altura promedio de los
personajes:

``` r
#Calcular la media de altura:
mean(starwars$height)
```

    ## [1] NA

``` r
# la salida es NA, obviamente hay NAs en esa columna
anyNA(starwars$height)
```

    ## [1] TRUE

``` r
# Cuantos NA?
sum(is.na(starwars$height))
```

    ## [1] 6

``` r
# Cuales?
print(starwars[is.na(starwars$height),])
```

    ## # A tibble: 6 x 13
    ##   name  height  mass hair_color skin_color eye_color birth_year gender
    ##   <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> 
    ## 1 Arve~     NA    NA brown      fair       brown             NA male  
    ## 2 Finn      NA    NA black      dark       dark              NA male  
    ## 3 Rey       NA    NA brown      light      hazel             NA female
    ## 4 Poe ~     NA    NA brown      light      brown             NA male  
    ## 5 BB8       NA    NA none       none       black             NA none  
    ## 6 Capt~     NA    NA unknown    unknown    unknown           NA female
    ## # ... with 5 more variables: homeworld <chr>, species <chr>, films <list>,
    ## #   vehicles <list>, starships <list>

**Entonces**, una alternativa para obtener la media de la altura es con
con un parámetro de la función `mean()`:

``` r
mean(starwars$height , na.rm = T)
```

    ## [1] 174.358

Veamos la desviación estandar de la altura:

``` r
sd(starwars$height, na.rm = TRUE)
```

    ## [1] 34.77043

Sería interesante calcular estas dos estadísticas según especie
(`species`). Se puede calcular con `aggregate()`:

``` r
aggregate(starwars$height,
          by = list(Species = starwars$species),
          mean)
```

    ##           Species        x
    ## 1          Aleena  79.0000
    ## 2        Besalisk 198.0000
    ## 3          Cerean 198.0000
    ## 4        Chagrian 196.0000
    ## 5        Clawdite 168.0000
    ## 6           Droid       NA
    ## 7             Dug 112.0000
    ## 8            Ewok  88.0000
    ## 9       Geonosian 183.0000
    ## 10         Gungan 208.6667
    ## 11          Human       NA
    ## 12           Hutt 175.0000
    ## 13       Iktotchi 188.0000
    ## 14        Kaleesh 216.0000
    ## 15       Kaminoan 221.0000
    ## 16        Kel Dor 188.0000
    ## 17       Mirialan 168.0000
    ## 18   Mon Calamari 180.0000
    ## 19           Muun 191.0000
    ## 20       Nautolan 196.0000
    ## 21      Neimodian 191.0000
    ## 22         Pau'an 206.0000
    ## 23       Quermian 264.0000
    ## 24         Rodian 173.0000
    ## 25        Skakoan 193.0000
    ## 26      Sullustan 160.0000
    ## 27     Tholothian 184.0000
    ## 28        Togruta 178.0000
    ## 29          Toong 163.0000
    ## 30      Toydarian 137.0000
    ## 31     Trandoshan 190.0000
    ## 32        Twi'lek 179.0000
    ## 33     Vulptereen  94.0000
    ## 34        Wookiee 231.0000
    ## 35          Xexto 122.0000
    ## 36 Yoda's species  66.0000
    ## 37         Zabrak 173.0000

Para utilizar `aggregate()` se necesitan 3 cosas, las variables que se
quiere trabajar, una lista de variables agrupadas y la función que se
aplicará a cada subset.Por ejemplo con otro subconjutno:

``` r
aggregate(starwars$height,
          by = list(Species = starwars$species,
                    Homeworld = starwars$homeworld),
          mean)
```

    ##         Species      Homeworld        x
    ## 1         Human       Alderaan 176.3333
    ## 2        Aleena    Aleen Minor  79.0000
    ## 3         Human         Bespin 175.0000
    ## 4         Human     Bestine IV 180.0000
    ## 5     Neimodian Cato Neimoidia 191.0000
    ## 6        Cerean          Cerea 198.0000
    ## 7      Chagrian       Champala 196.0000
    ## 8         Human      Chandrila 150.0000
    ## 9         Human   Concord Dawn 183.0000
    ## 10        Human       Corellia 175.0000
    ## 11        Human      Coruscant 168.5000
    ## 12   Tholothian      Coruscant 184.0000
    ## 13       Zabrak       Dathomir 175.0000
    ## 14      Kel Dor          Dorin 188.0000
    ## 15         Ewok          Endor  88.0000
    ## 16        Human         Eriadu 180.0000
    ## 17    Geonosian       Geonosis 183.0000
    ## 18     Nautolan    Glee Anselm 196.0000
    ## 19        Human     Haruun Kal 188.0000
    ## 20     Iktotchi        Iktotch 188.0000
    ## 21       Zabrak       Iridonia 171.0000
    ## 22      Kaleesh          Kalee 216.0000
    ## 23        Human         Kamino 183.0000
    ## 24     Kaminoan         Kamino 221.0000
    ## 25      Wookiee       Kashyyyk 231.0000
    ## 26          Dug      Malastare 112.0000
    ## 27     Mirialan         Mirial 168.0000
    ## 28 Mon Calamari       Mon Cala 180.0000
    ## 29         Muun     Muunilinst 191.0000
    ## 30        Droid          Naboo  96.0000
    ## 31       Gungan          Naboo 208.6667
    ## 32        Human          Naboo 168.4000
    ## 33         Hutt      Nal Hutta 175.0000
    ## 34     Besalisk           Ojom 198.0000
    ## 35     Quermian        Quermia 264.0000
    ## 36       Rodian          Rodia 173.0000
    ## 37      Twi'lek         Ryloth 179.0000
    ## 38        Human        Serenno 193.0000
    ## 39      Togruta          Shili 178.0000
    ## 40      Skakoan          Skako 193.0000
    ## 41        Human        Socorro 177.0000
    ## 42        Human        Stewjon 182.0000
    ## 43    Sullustan        Sullust 160.0000
    ## 44        Droid       Tatooine 132.0000
    ## 45        Human       Tatooine 179.2500
    ## 46    Toydarian       Toydaria 137.0000
    ## 47   Trandoshan      Trandosha 190.0000
    ## 48        Xexto        Troiken 122.0000
    ## 49        Toong           Tund 163.0000
    ## 50       Pau'an         Utapau 206.0000
    ## 51   Vulptereen        Vulpter  94.0000
    ## 52     Clawdite          Zolan 168.0000

o con otra función:

``` r
aggregate(starwars$height,
          by = list(Species = starwars$species),
          sd)
```

    ##           Species         x
    ## 1          Aleena        NA
    ## 2        Besalisk        NA
    ## 3          Cerean        NA
    ## 4        Chagrian        NA
    ## 5        Clawdite        NA
    ## 6           Droid        NA
    ## 7             Dug        NA
    ## 8            Ewok        NA
    ## 9       Geonosian        NA
    ## 10         Gungan 14.189198
    ## 11          Human        NA
    ## 12           Hutt        NA
    ## 13       Iktotchi        NA
    ## 14        Kaleesh        NA
    ## 15       Kaminoan 11.313708
    ## 16        Kel Dor        NA
    ## 17       Mirialan  2.828427
    ## 18   Mon Calamari        NA
    ## 19           Muun        NA
    ## 20       Nautolan        NA
    ## 21      Neimodian        NA
    ## 22         Pau'an        NA
    ## 23       Quermian        NA
    ## 24         Rodian        NA
    ## 25        Skakoan        NA
    ## 26      Sullustan        NA
    ## 27     Tholothian        NA
    ## 28        Togruta        NA
    ## 29          Toong        NA
    ## 30      Toydarian        NA
    ## 31     Trandoshan        NA
    ## 32        Twi'lek  1.414214
    ## 33     Vulptereen        NA
    ## 34        Wookiee  4.242641
    ## 35          Xexto        NA
    ## 36 Yoda's species        NA
    ## 37         Zabrak  2.828427

La función `aggregate()` devuelve un objeto `data.frame`:

``` r
class(aggregate(starwars$height, by = list(Species = starwars$species), mean))
```

    ## [1] "data.frame"

**Otra alternativa** usando *R base* es:

``` r
tapply(starwars$height, list(starwars$species), mean)
```

    ##         Aleena       Besalisk         Cerean       Chagrian       Clawdite 
    ##        79.0000       198.0000       198.0000       196.0000       168.0000 
    ##          Droid            Dug           Ewok      Geonosian         Gungan 
    ##             NA       112.0000        88.0000       183.0000       208.6667 
    ##          Human           Hutt       Iktotchi        Kaleesh       Kaminoan 
    ##             NA       175.0000       188.0000       216.0000       221.0000 
    ##        Kel Dor       Mirialan   Mon Calamari           Muun       Nautolan 
    ##       188.0000       168.0000       180.0000       191.0000       196.0000 
    ##      Neimodian         Pau'an       Quermian         Rodian        Skakoan 
    ##       191.0000       206.0000       264.0000       173.0000       193.0000 
    ##      Sullustan     Tholothian        Togruta          Toong      Toydarian 
    ##       160.0000       184.0000       178.0000       163.0000       137.0000 
    ##     Trandoshan        Twi'lek     Vulptereen        Wookiee          Xexto 
    ##       190.0000       179.0000        94.0000       231.0000       122.0000 
    ## Yoda's species         Zabrak 
    ##        66.0000       173.0000

La función `tapply` devuelve un objeto `array`, similar a un vector.

``` r
class(tapply(starwars$height, list(starwars$species), mean))
```

    ## [1] "array"

Sin embargo, `tapply()` no funciona si tu quieres el promedio por
especies para todas las variables del conjunto:

``` r
tapply(starwars, list(starwars$species), mean)
# da error!
```

En ambos casos, se puede especificar una función. Si quieres el promedio
y de desviación estantar, debes hacerlo en dos pasos.

Continuemo calculando el promedio de alturas por especies, pero en
hombres:

``` r
#hacemos la división den conjunto con subset()
starwars_males <- subset(starwars, gender == "male")
aggregate(starwars_males$height,
          by = list(Species = starwars_males$species),
          mean)
```

    ##           Species        x
    ## 1          Aleena  79.0000
    ## 2        Besalisk 198.0000
    ## 3          Cerean 198.0000
    ## 4        Chagrian 196.0000
    ## 5             Dug 112.0000
    ## 6            Ewok  88.0000
    ## 7       Geonosian 183.0000
    ## 8          Gungan 208.6667
    ## 9           Human       NA
    ## 10       Iktotchi 188.0000
    ## 11        Kaleesh 216.0000
    ## 12       Kaminoan 229.0000
    ## 13        Kel Dor 188.0000
    ## 14   Mon Calamari 180.0000
    ## 15           Muun 191.0000
    ## 16       Nautolan 196.0000
    ## 17      Neimodian 191.0000
    ## 18         Pau'an 206.0000
    ## 19       Quermian 264.0000
    ## 20         Rodian 173.0000
    ## 21        Skakoan 193.0000
    ## 22      Sullustan 160.0000
    ## 23          Toong 163.0000
    ## 24      Toydarian 137.0000
    ## 25     Trandoshan 190.0000
    ## 26        Twi'lek 180.0000
    ## 27     Vulptereen  94.0000
    ## 28        Wookiee 231.0000
    ## 29          Xexto 122.0000
    ## 30 Yoda's species  66.0000
    ## 31         Zabrak 173.0000

`subset()` puede ser usado para seleccionar columnas. Si queremos el
promedio de la altura y masa para los hombres, se podría hacer con algo
así:

``` r
#un subset de altura y masa para hombres
starwars_males_height_mass <- subset(starwars, gender == "male", select = c(height, mass, species))
# calculo de los promedios
aggregate(starwars_males_height_mass,
          by = list(Species = starwars_males_height_mass$species),
          mean)
```

    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA
    
    ## Warning in mean.default(X[[i]], ...): argument is not numeric or logical:
    ## returning NA

    ##           Species   height mass species
    ## 1          Aleena  79.0000   15      NA
    ## 2        Besalisk 198.0000  102      NA
    ## 3          Cerean 198.0000   82      NA
    ## 4        Chagrian 196.0000   NA      NA
    ## 5             Dug 112.0000   40      NA
    ## 6            Ewok  88.0000   20      NA
    ## 7       Geonosian 183.0000   80      NA
    ## 8          Gungan 208.6667   NA      NA
    ## 9           Human       NA   NA      NA
    ## 10       Iktotchi 188.0000   NA      NA
    ## 11        Kaleesh 216.0000  159      NA
    ## 12       Kaminoan 229.0000   88      NA
    ## 13        Kel Dor 188.0000   80      NA
    ## 14   Mon Calamari 180.0000   83      NA
    ## 15           Muun 191.0000   NA      NA
    ## 16       Nautolan 196.0000   87      NA
    ## 17      Neimodian 191.0000   90      NA
    ## 18         Pau'an 206.0000   80      NA
    ## 19       Quermian 264.0000   NA      NA
    ## 20         Rodian 173.0000   74      NA
    ## 21        Skakoan 193.0000   48      NA
    ## 22      Sullustan 160.0000   68      NA
    ## 23          Toong 163.0000   65      NA
    ## 24      Toydarian 137.0000   NA      NA
    ## 25     Trandoshan 190.0000  113      NA
    ## 26        Twi'lek 180.0000   NA      NA
    ## 27     Vulptereen  94.0000   45      NA
    ## 28        Wookiee 231.0000  124      NA
    ## 29          Xexto 122.0000   NA      NA
    ## 30 Yoda's species  66.0000   17      NA
    ## 31         Zabrak 173.0000   NA      NA

Esto se pone más trabajoso pero es una herramienta poderosa.

## Modo `tidyverse`

A continuación se empezará a trabajar con el modo de `tidyverse`:

Primero usando la pipa: \*\* %\>% \*\* El uso de la pipa viene
implementado en varios paquetes, uno de ellos es `magrittr`.

``` r
library(magrittr)
sqrt(16)
```

    ## [1] 4

``` r
16 %>% sqrt
```

    ## [1] 4

La forma para calcular la raiz cuadrada en la linea anterior es un poco
extraña, pero facil de entender, ese es el objetivo de la pipa. La pipa
tomó el `16` y le aplicó la función `sqrt()`. De esta manera se pueden
encadenar multiples funciones:

``` r
16 %>% sqrt %>% `+`(18)
```

    ## [1] 22

Tambiés se puede escribir así:

``` r
16 %>% 
  sqrt %>% 
  `+`(18) 
```

    ## [1] 22

Sin las pipas sería de esta manera:

``` r
sqrt(16) + 18
```

    ## [1] 22

Hay otras operaciónes que vienen en `magrittr`, como `%T%`, `%<>%` and
`%$%`.

# Primeros pasos con `dplyr`

Cargamos el paquete y los datos `starwars`.

``` r
library(dplyr)
data(starwars) #carga los datos starwars que incluye el paquete dplyr
head(starwars)
```

    ## # A tibble: 6 x 13
    ##   name  height  mass hair_color skin_color eye_color birth_year gender
    ##   <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> 
    ## 1 Luke~    172    77 blond      fair       blue            19   male  
    ## 2 C-3PO    167    75 <NA>       gold       yellow         112   <NA>  
    ## 3 R2-D2     96    32 <NA>       white, bl~ red             33   <NA>  
    ## 4 Dart~    202   136 none       white      yellow          41.9 male  
    ## 5 Leia~    150    49 brown      light      brown           19   female
    ## 6 Owen~    178   120 brown, gr~ light      blue            52   male  
    ## # ... with 5 more variables: homeworld <chr>, species <chr>, films <list>,
    ## #   vehicles <list>, starships <list>

R incluye funciones para estadisticas descriptivas, como `mean()`,
`sd()`, `cov()`, y muchas más. Lo bueno de la libreria `dplyr` es que es
posible aplicar estas fuciones de R al conjunto de datos facilmente. Por
ejemplo, imgina que quieres calcular el promedio de la altura para todo
el conjunto:

  - Con las funciones básicas de R sería:

<!-- end list -->

``` r
mean(starwars$height)
```

    ## [1] NA

Ahora, si queremos calcular el promedio por especie, usando `dplyr`:

``` r
starwars %>% 
  group_by(species) %>% 
  summarise(mean(height))
```

    ## # A tibble: 38 x 2
    ##    species   `mean(height)`
    ##    <chr>              <dbl>
    ##  1 <NA>                  NA
    ##  2 Aleena                79
    ##  3 Besalisk             198
    ##  4 Cerean               198
    ##  5 Chagrian             196
    ##  6 Clawdite             168
    ##  7 Droid                 NA
    ##  8 Dug                  112
    ##  9 Ewok                  88
    ## 10 Geonosian            183
    ## # ... with 28 more rows

Sin el uso de la pipa `%>%`, el código anterior se escribiría:

``` r
summarise(group_by(starwars, species), mean(height))
```

    ## # A tibble: 38 x 2
    ##    species   `mean(height)`
    ##    <chr>              <dbl>
    ##  1 <NA>                  NA
    ##  2 Aleena                79
    ##  3 Besalisk             198
    ##  4 Cerean               198
    ##  5 Chagrian             196
    ##  6 Clawdite             168
    ##  7 Droid                 NA
    ##  8 Dug                  112
    ##  9 Ewok                  88
    ## 10 Geonosian            183
    ## # ... with 28 more rows

Como se puede ver, es mucho mas dificil de leer la línea anterior.
Ahora, si queremos la altura promedio por especies, pero solo para
hombres:

``` r
starwars %>% 
  filter(gender == "male") %>% 
  group_by(species) %>% 
  summarise( mean(height))
```

    ## # A tibble: 32 x 2
    ##    species   `mean(height)`
    ##    <chr>              <dbl>
    ##  1 <NA>                183 
    ##  2 Aleena               79 
    ##  3 Besalisk            198 
    ##  4 Cerean              198 
    ##  5 Chagrian            196 
    ##  6 Dug                 112 
    ##  7 Ewok                 88 
    ##  8 Geonosian           183 
    ##  9 Gungan              209.
    ## 10 Human                NA 
    ## # ... with 22 more rows

De nuevo, la línea anterior sin las pipas se escribiría:

``` r
summarise(group_by(filter(starwars, gender == "male"), species), mean(height))
```

    ## # A tibble: 32 x 2
    ##    species   `mean(height)`
    ##    <chr>              <dbl>
    ##  1 <NA>                183 
    ##  2 Aleena               79 
    ##  3 Besalisk            198 
    ##  4 Cerean              198 
    ##  5 Chagrian            196 
    ##  6 Dug                 112 
    ##  7 Ewok                 88 
    ##  8 Geonosian           183 
    ##  9 Gungan              209.
    ## 10 Human                NA 
    ## # ... with 22 more rows

Es dificil de leer. Una manera de hacerlo más legible podría ser
guardando los cambios en variables intermedias:

``` r
starwars_hombres <- filter(starwars, gender == "male")

starwars_agrupado_hombres <- group_by(starwars_hombres, species)

summarise(starwars_agrupado_hombres, mean(height))
```

    ## # A tibble: 32 x 2
    ##    species   `mean(height)`
    ##    <chr>              <dbl>
    ##  1 <NA>                183 
    ##  2 Aleena               79 
    ##  3 Besalisk            198 
    ##  4 Cerean              198 
    ##  5 Chagrian            196 
    ##  6 Dug                 112 
    ##  7 Ewok                 88 
    ##  8 Geonosian           183 
    ##  9 Gungan              209.
    ## 10 Human                NA 
    ## # ... with 22 more rows

Pero tambíen es tedioso.

Las funciones `filter()`, `group_by()` y `summarise()` vienen incluidas
en `dplyr`. `mean()` en *nativa* de R.

El resultado de las operaciones que usa `dplyr` son datasets o
`tibbles`. Estas se pueden guardar como otras variables para volver a
usarlas.

``` r
mean_height <- starwars %>%
  group_by(species) %>%
  summarise(mean(height))

class(mean_height)
```

    ## [1] "tbl_df"     "tbl"        "data.frame"

``` r
head(mean_height)
```

    ## # A tibble: 6 x 2
    ##   species  `mean(height)`
    ##   <chr>             <dbl>
    ## 1 <NA>                 NA
    ## 2 Aleena               79
    ## 3 Besalisk            198
    ## 4 Cerean              198
    ## 5 Chagrian            196
    ## 6 Clawdite            168

Si necesitas más que solo el promedio de la altura, solo añadir las
funciones que necesitas:

``` r
summary_table <- starwars %>% 
  group_by(species) %>%
  summarise(ave_height = mean(height), var_height = var(height), n_obs = n(), sd_altura = sd(height))

print(summary_table)
```

    ## # A tibble: 38 x 5
    ##    species   ave_height var_height n_obs sd_altura
    ##    <chr>          <dbl>      <dbl> <int>     <dbl>
    ##  1 <NA>              NA         NA     5       NaN
    ##  2 Aleena            79         NA     1       NaN
    ##  3 Besalisk         198         NA     1       NaN
    ##  4 Cerean           198         NA     1       NaN
    ##  5 Chagrian         196         NA     1       NaN
    ##  6 Clawdite         168         NA     1       NaN
    ##  7 Droid             NA         NA     5       NaN
    ##  8 Dug              112         NA     1       NaN
    ##  9 Ewok              88         NA     1       NaN
    ## 10 Geonosian        183         NA     1       NaN
    ## # ... with 28 more rows

Las funciones `var()` y `n()` provienen de `dplyr` y no de R base. Esto
es muy util, porque se pueden ver varias especies con solo UN individuo.
Pongamos focos en las especies con más de una observación. Guardando
todas las operaciones anteriores en una variable:

``` r
summary_table2 <- summary_table %>%
  filter(n_obs > 1)

print(summary_table2)
```

    ## # A tibble: 9 x 5
    ##   species  ave_height var_height n_obs sd_altura
    ##   <chr>         <dbl>      <dbl> <int>     <dbl>
    ## 1 <NA>            NA         NA      5    NaN   
    ## 2 Droid           NA         NA      5    NaN   
    ## 3 Gungan         209.       201.     3     14.2 
    ## 4 Human           NA         NA     35    NaN   
    ## 5 Kaminoan       221        128      2     11.3 
    ## 6 Mirialan       168          8      2      2.83
    ## 7 Twi'lek        179          2      2      1.41
    ## 8 Wookiee        231         18      2      4.24
    ## 9 Zabrak         173          8      2      2.83

Hay muchos valores `NA`s, esto es porque si una observación en `NA` las
funciones `mean()` y `var()` devuelven `NA`. Hay que ver más
detalladamente el conjunto. Dentro de esas funciones se pueden ignorar:

``` r
starwars %>%
  group_by(species) %>%
  summarise(ave_height = mean(height, na.rm = TRUE), var_height = var(height, na.rm = TRUE), n_obs = n()) %>%
  filter(n_obs > 1)
```

    ## # A tibble: 9 x 4
    ##   species  ave_height var_height n_obs
    ##   <chr>         <dbl>      <dbl> <int>
    ## 1 <NA>           160       1826      5
    ## 2 Droid          140       2705.     5
    ## 3 Gungan         209.       201.     3
    ## 4 Human          177.       157.    35
    ## 5 Kaminoan       221        128      2
    ## 6 Mirialan       168          8      2
    ## 7 Twi'lek        179          2      2
    ## 8 Wookiee        231         18      2
    ## 9 Zabrak         173          8      2

Aún hay un `NA` en especies.

``` r
starwars %>%
  filter(is.na(species))
```

    ## # A tibble: 5 x 13
    ##   name  height  mass hair_color skin_color eye_color birth_year gender
    ##   <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> 
    ## 1 Ric ~    183    NA brown      fair       blue              NA male  
    ## 2 Quar~    183    NA black      dark       brown             62 male  
    ## 3 R4-P~     96    NA none       silver, r~ red, blue         NA female
    ## 4 Sly ~    178    48 none       pale       white             NA female
    ## 5 Capt~     NA    NA unknown    unknown    unknown           NA female
    ## # ... with 5 more variables: homeworld <chr>, species <chr>, films <list>,
    ## #   vehicles <list>, starships <list>

La función `is.na()` es de R base. Se puede usar para filtrar los `NA`:

``` r
starwars %>%
  filter(!is.na(species))
```

    ## # A tibble: 82 x 13
    ##    name  height  mass hair_color skin_color eye_color birth_year gender
    ##    <chr>  <int> <dbl> <chr>      <chr>      <chr>          <dbl> <chr> 
    ##  1 Luke~    172    77 blond      fair       blue            19   male  
    ##  2 C-3PO    167    75 <NA>       gold       yellow         112   <NA>  
    ##  3 R2-D2     96    32 <NA>       white, bl~ red             33   <NA>  
    ##  4 Dart~    202   136 none       white      yellow          41.9 male  
    ##  5 Leia~    150    49 brown      light      brown           19   female
    ##  6 Owen~    178   120 brown, gr~ light      blue            52   male  
    ##  7 Beru~    165    75 brown      light      blue            47   female
    ##  8 R5-D4     97    32 <NA>       white, red red             NA   <NA>  
    ##  9 Bigg~    183    84 black      light      brown           24   male  
    ## 10 Obi-~    182    77 auburn, w~ fair       blue-gray       57   male  
    ## # ... with 72 more rows, and 5 more variables: homeworld <chr>,
    ## #   species <chr>, films <list>, vehicles <list>, starships <list>

Seleccionando las especies que no tienen `NA` :

``` r
starwars %>%
  filter(!is.na(species)) %>%
  group_by(species) %>%
  summarise(ave_height = mean(height, na.rm = TRUE), var_height = var(height, na.rm = TRUE), n_obs = n()) %>%
  filter(n_obs > 1)
```

    ## # A tibble: 8 x 4
    ##   species  ave_height var_height n_obs
    ##   <chr>         <dbl>      <dbl> <int>
    ## 1 Droid          140       2705.     5
    ## 2 Gungan         209.       201.     3
    ## 3 Human          177.       157.    35
    ## 4 Kaminoan       221        128      2
    ## 5 Mirialan       168          8      2
    ## 6 Twi'lek        179          2      2
    ## 7 Wookiee        231         18      2
    ## 8 Zabrak         173          8      2

**Y estratificando por genero:**

``` r
starwars %>%
  filter(!is.na(species)) %>%
  group_by(species, gender) %>%
  summarise(ave_height = mean(height, na.rm = TRUE), var_height = var(height, na.rm = TRUE), n_obs = n()) %>%
  filter(n_obs > 1)
```

    ## # A tibble: 8 x 5
    ## # Groups:   species [6]
    ##   species  gender ave_height var_height n_obs
    ##   <chr>    <chr>       <dbl>      <dbl> <int>
    ## 1 Droid    <NA>         120      1657       3
    ## 2 Droid    none         200        NA       2
    ## 3 Gungan   male         209.      201.      3
    ## 4 Human    female       160.       48.8     9
    ## 5 Human    male         182.       67.1    26
    ## 6 Mirialan female       168         8       2
    ## 7 Wookiee  male         231        18       2
    ## 8 Zabrak   male         173         8       2

ok… ahora entremos más en detalle a las funciones de `dplyr`.

## Filtrando datasets con `filter()`

Ahora, usaremos el conjunto `Gasoline` del paquete `plm`.

``` r
install.packages("plm")
```

Y cargamos el dataset y `dplyr`:

``` r
data(Gasoline, package = "plm")  
library(dplyr)
```

Este dataser contiene el consumo de gasolina de 18 paises desde 1960 a
1978. Cuando cargas el data de esta manera, es un `data.frame` estandar.
Las funciones de `dplyr` pueden ser usadas sobre objetos `data.frame`,
pero también en `tibble`’s. Las `tibbles`’s son como los `data.frame`
pero con un método de `print` mejor ( y otras mejoras sutiles ).
Discutiremos el paquete `tibble` después. Por ahora, convirtamos los
datos a `tibble` y cambiemos su nombre:

``` r
gasoline <- as_tibble(Gasoline) 
```

La función `filter()` es sencilla. Por ejemplo, si quieres un
subconjunto de datos enfocados en el año 1969, simple:

``` r
filter(gasoline , year== 1969)
```

    ## # A tibble: 18 x 6
    ##    country   year lgaspcar lincomep  lrpmg lcarpcap
    ##    <fct>    <int>    <dbl>    <dbl>  <dbl>    <dbl>
    ##  1 AUSTRIA   1969     4.05    -6.15 -0.559    -8.79
    ##  2 BELGIUM   1969     3.85    -5.86 -0.355    -8.52
    ##  3 CANADA    1969     4.86    -5.56 -1.04     -8.10
    ##  4 DENMARK   1969     4.17    -5.72 -0.407    -8.47
    ##  5 FRANCE    1969     3.77    -5.84 -0.315    -8.37
    ##  6 GERMANY   1969     3.90    -5.83 -0.589    -8.44
    ##  7 GREECE    1969     4.89    -6.59 -0.180   -10.7 
    ##  8 IRELAND   1969     4.21    -6.38 -0.272    -8.95
    ##  9 ITALY     1969     3.74    -6.28 -0.248    -8.67
    ## 10 JAPAN     1969     4.52    -6.16 -0.417    -9.61
    ## 11 NETHERLA  1969     3.99    -5.88 -0.417    -8.63
    ## 12 NORWAY    1969     4.09    -5.74 -0.338    -8.69
    ## 13 SPAIN     1969     3.99    -5.60  0.669    -9.72
    ## 14 SWEDEN    1969     3.99    -7.77 -2.73     -8.20
    ## 15 SWITZERL  1969     4.21    -5.91 -0.918    -8.47
    ## 16 TURKEY    1969     5.72    -7.39 -0.298   -12.5 
    ## 17 U.K.      1969     3.95    -6.03 -0.383    -8.47
    ## 18 U.S.A.    1969     4.84    -5.41 -1.22     -7.79

Usemos el operador pipa:

``` r
gasoline %>%  filter(year==1969)
```

    ## # A tibble: 18 x 6
    ##    country   year lgaspcar lincomep  lrpmg lcarpcap
    ##    <fct>    <int>    <dbl>    <dbl>  <dbl>    <dbl>
    ##  1 AUSTRIA   1969     4.05    -6.15 -0.559    -8.79
    ##  2 BELGIUM   1969     3.85    -5.86 -0.355    -8.52
    ##  3 CANADA    1969     4.86    -5.56 -1.04     -8.10
    ##  4 DENMARK   1969     4.17    -5.72 -0.407    -8.47
    ##  5 FRANCE    1969     3.77    -5.84 -0.315    -8.37
    ##  6 GERMANY   1969     3.90    -5.83 -0.589    -8.44
    ##  7 GREECE    1969     4.89    -6.59 -0.180   -10.7 
    ##  8 IRELAND   1969     4.21    -6.38 -0.272    -8.95
    ##  9 ITALY     1969     3.74    -6.28 -0.248    -8.67
    ## 10 JAPAN     1969     4.52    -6.16 -0.417    -9.61
    ## 11 NETHERLA  1969     3.99    -5.88 -0.417    -8.63
    ## 12 NORWAY    1969     4.09    -5.74 -0.338    -8.69
    ## 13 SPAIN     1969     3.99    -5.60  0.669    -9.72
    ## 14 SWEDEN    1969     3.99    -7.77 -2.73     -8.20
    ## 15 SWITZERL  1969     4.21    -5.91 -0.918    -8.47
    ## 16 TURKEY    1969     5.72    -7.39 -0.298   -12.5 
    ## 17 U.K.      1969     3.95    -6.03 -0.383    -8.47
    ## 18 U.S.A.    1969     4.84    -5.41 -1.22     -7.79

Incluso se puede filtrar más de un solo año, usando el operador `%in%`:

``` r
gasoline %>% filter(year %in% seq(1969, 1973 ,by = 2))
```

    ## # A tibble: 54 x 6
    ##    country  year lgaspcar lincomep  lrpmg lcarpcap
    ##    <fct>   <int>    <dbl>    <dbl>  <dbl>    <dbl>
    ##  1 AUSTRIA  1969     4.05    -6.15 -0.559    -8.79
    ##  2 AUSTRIA  1971     4.11    -6.04 -0.654    -8.64
    ##  3 AUSTRIA  1973     4.20    -5.90 -0.594    -8.49
    ##  4 BELGIUM  1969     3.85    -5.86 -0.355    -8.52
    ##  5 BELGIUM  1971     3.87    -5.76 -0.399    -8.41
    ##  6 BELGIUM  1973     3.90    -5.64 -0.373    -8.31
    ##  7 CANADA   1969     4.86    -5.56 -1.04     -8.10
    ##  8 CANADA   1971     4.90    -5.48 -1.06     -8.04
    ##  9 CANADA   1973     4.90    -5.41 -1.13     -7.94
    ## 10 DENMARK  1969     4.17    -5.72 -0.407    -8.47
    ## # ... with 44 more rows

``` r
gasoline %>% filter(year %in% 1969:1973)
```

    ## # A tibble: 90 x 6
    ##    country  year lgaspcar lincomep  lrpmg lcarpcap
    ##    <fct>   <int>    <dbl>    <dbl>  <dbl>    <dbl>
    ##  1 AUSTRIA  1969     4.05    -6.15 -0.559    -8.79
    ##  2 AUSTRIA  1970     4.08    -6.08 -0.597    -8.73
    ##  3 AUSTRIA  1971     4.11    -6.04 -0.654    -8.64
    ##  4 AUSTRIA  1972     4.13    -5.98 -0.596    -8.54
    ##  5 AUSTRIA  1973     4.20    -5.90 -0.594    -8.49
    ##  6 BELGIUM  1969     3.85    -5.86 -0.355    -8.52
    ##  7 BELGIUM  1970     3.87    -5.80 -0.378    -8.45
    ##  8 BELGIUM  1971     3.87    -5.76 -0.399    -8.41
    ##  9 BELGIUM  1972     3.91    -5.71 -0.311    -8.36
    ## 10 BELGIUM  1973     3.90    -5.64 -0.373    -8.31
    ## # ... with 80 more rows

También se puede usar `between()`:

``` r
gasoline %>% filter(between(year, 1969, 1973))
```

    ## # A tibble: 90 x 6
    ##    country  year lgaspcar lincomep  lrpmg lcarpcap
    ##    <fct>   <int>    <dbl>    <dbl>  <dbl>    <dbl>
    ##  1 AUSTRIA  1969     4.05    -6.15 -0.559    -8.79
    ##  2 AUSTRIA  1970     4.08    -6.08 -0.597    -8.73
    ##  3 AUSTRIA  1971     4.11    -6.04 -0.654    -8.64
    ##  4 AUSTRIA  1972     4.13    -5.98 -0.596    -8.54
    ##  5 AUSTRIA  1973     4.20    -5.90 -0.594    -8.49
    ##  6 BELGIUM  1969     3.85    -5.86 -0.355    -8.52
    ##  7 BELGIUM  1970     3.87    -5.80 -0.378    -8.45
    ##  8 BELGIUM  1971     3.87    -5.76 -0.399    -8.41
    ##  9 BELGIUM  1972     3.91    -5.71 -0.311    -8.36
    ## 10 BELGIUM  1973     3.90    -5.64 -0.373    -8.31
    ## # ... with 80 more rows

Seleccionar algunas específicas:

``` r
gasoline %>% filter(year %in% c(1969, 1973, 1977))
```

    ## # A tibble: 54 x 6
    ##    country  year lgaspcar lincomep  lrpmg lcarpcap
    ##    <fct>   <int>    <dbl>    <dbl>  <dbl>    <dbl>
    ##  1 AUSTRIA  1969     4.05    -6.15 -0.559    -8.79
    ##  2 AUSTRIA  1973     4.20    -5.90 -0.594    -8.49
    ##  3 AUSTRIA  1977     3.93    -5.83 -0.422    -8.25
    ##  4 BELGIUM  1969     3.85    -5.86 -0.355    -8.52
    ##  5 BELGIUM  1973     3.90    -5.64 -0.373    -8.31
    ##  6 BELGIUM  1977     3.85    -5.56 -0.432    -8.14
    ##  7 CANADA   1969     4.86    -5.56 -1.04     -8.10
    ##  8 CANADA   1973     4.90    -5.41 -1.13     -7.94
    ##  9 CANADA   1977     4.81    -5.34 -1.07     -7.77
    ## 10 DENMARK  1969     4.17    -5.72 -0.407    -8.47
    ## # ... with 44 more rows

## Seleccionando columnas con `select()`

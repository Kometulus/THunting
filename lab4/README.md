# Основы обработки данных с помощью R

## Цель работы
1.Закрепить практические навыки использования языка программирования R для обработки данных
2.ЗакрепитьзнанияосновныхфункцийобработкиданныхэкосистемыtidyverseязыкаR
3.Развить пркатические навыки использования функций обработки данных пакета dplyr – функции select(), filter(), mutate(), arrange(), group_by()

## Задание
Проанализировать встроенные в пакет nycflights13 наборы данных с помощью языка R и ответить на вопросы.
```{r}
install.packages('dplyr')
install.packages('nycflights13')
library(dplyr)
library(nycflights13)
```

### Задание 1 Сколько встроенных в пакет nycflights13 датафреймов?
```{r}
df_package <- ls(package:nycflights13)
df_package %>%
length
```
### Задание 2 Сколько строк в каждом датафрейме?
```{r}
al <- nycflights13::airlines
ap <- nycflights13::airports
f <- nycflights13::flights
p <- nycflights13::planes
w <- nycflights13::weather
al %>% nrow() 
ap %>% nrow()
f %>% nrow()
p %>% nrow()
w %>% nrow()
```

### Задание 3 Сколько столбцов в каждом датафрейме?
```{r}
al %>% ncol() 
ap %>% ncol()
f %>% ncol()
p %>% ncol()
w %>% ncol()
```

### Задание 4 Как просмотреть примерный вид датафрейма?
```{r}
al %>% glimpse() 
ap %>% glimpse() 
f %>% glimpse() 
p %>% glimpse() 
w %>% glimpse() 
```

## Задание 5 Сколько компаний-перевозчиков (carrier) учитывают эти наборы данных (представлено в наборах данных)?
```{r}
al %>%
  select(carrier) %>%
  distinct() %>%
  count()
```

##Задание 6 Сколько рейсов принял аэропорт John F Kennedy Intl в мае?
```{r}
a <- airports %>%
  filter(name=='John F Kennedy Intl')%>%
  select(faa)
f %>%
  filter(!is.na(time_hour))%>%
  filter(dest==a, time_hour >= as.POSIXct("2013-05-01 00:00:00", tz="UTC"), time_hour <= as.POSIXct("2013-05-31 24:59:59", tz="UTC")) %>%
  count()
```

## Задание 7 Какой самый северный аэропорт?
```{r}
ap %>%
  filter(lat==max(lat))
```

## Задание 8 Какой аэропорт самый высокогорный (находится выше всех над уровнем моря)?
```{r}
ap %>%
  filter(lon==max(lon))
```

## Задание 9 Какие бортовые номера у самых старых самолетов?
```{r}
planes %>%
  filter(!is.na(year))%>%
  filter(year==min(year))%>%
  select(tailnum)
```

## Задание 10 Какая средняя температура воздуха была в сентябре в аэропорту John F Kennedy Intl (в градусах Цельсия)?
```{r}
a <- airports %>%
  filter(name=='John F Kennedy Intl')%>%
  select(faa)
b<-weather %>%
  filter(origin==a$faa, month==9)%>%
  select(temp)
5/9*(mean(b$temp)-32)
```

## Задание 11 Самолеты какой авиакомпании совершили больше всего вылетов в июне?
```{r}
flights%>%
  filter(month==6)%>%
  group_by(carrier)%>%
  summarise(count_flights=length(flight), .groups = "drop")%>%
  arrange(desc(count_flights))%>%
  slice(1:1)
```

## Задание 12 Самолеты какой авиакомпании задерживались чаще других в 2013 году?
```{r}
flights
flights%>%
  filter(year==2013)%>%
  filter(dep_delay>0)%>%
  group_by(carrier)%>%
  summarise(delay_times=length(flight), .groups = "drop")%>%
  arrange(desc(delay_times))%>%
  slice(1:1)
```

# Analisis-datos-policiales

---
título: "Análisis Policial 2022"
autor: "Allan Marín Campos"
fecha: '2022-06-20'
salida: html_document
---

'''{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Tarea 2


### Carga de biblioteca de R


'''{r carga de paquetes, message=FALSE}
biblioteca(DT)
biblioteca(ggplot2)
biblioteca(plotly)
biblioteca(dplyr)
biblioteca(folleto)
biblioteca(sf)
biblioteca(readxl)
biblioteca(tidyverse)
biblioteca(ggthemes)
```

### Carga de datos

También puede incrustar gráficos, por ejemplo:

'''{r, message=FALSE}
datos_policiales2022 <- 
  readxl::read_excel("/Users/marin/OneDrive/Documentos/RMDE/Procesamiento/datos_policiales2022.xlsx")
datos_policiales2022$Fecha <- as. Fecha(datos_policiales2022$Fecha, format = "%Y-%m-%d")
```

# Desarrollo

## Tabla DT

'''{r, message=FALSE}
# Tabla interactiva del paquete DT
datos_policiales2022 %>%
  dplyr::select(Delito,
                Fecha,
                Victima,
                Edad,
                Genero,
                Provincia,
                Cantón) %>%
 mutate(Fecha = as. Fecha(Fecha, formato = "%d/%m/%Y")) %>%
  datatable(
    options = list(
      pageLength = 15,
      language = list(url = '//cdn.datatables.net/plug-ins/1.10.11/i18n/Spanish.json')
    ),
    colnames = c(
      # encabezados de las columnas
      "Delito",
      "Fecha",
      "Víctima",
      "Edad",
      "Género",
      "Provincia",
      "Cantón"
    )
  )
```

## Gráfico de barras simples de Cantidad de Delitos por tipo de Delito
```{r}
Cantidad_de_delito_por_tipo <-
  datos_policiales2022 %>%
  count(Delito) %>%
  ggplot(aes(x = reorder(Delito, n), y = n)) +
  geom_bar(stat = "identity") +
  ggtitle("Cantidad de delitos por tipo de delito") +
  xlab("Tipo de Delito") +
  ylab("Cantidad") +
  coord_flip() +
  theme_hc()
Cantidad_de_delito_por_tipo %>%
  ggplotly() %>%
  config(locale = "es")
```

## Gráfico simple de Cantidad de Delitos por mes

```{r Transformacion de datos, message=FALSE, echo=FALSE}
datos_policiales2022 <-
  datos_policiales2022 %>%
  mutate(mes = format(Fecha, "%B")) %>%
  select(Delito, Fecha, Victima, Edad, Genero, Provincia, Canton, mes)%>%
  mutate(Fecha = as.Date(Fecha, format = "%d/%m/%Y"))
```

```{r}
Grafico_por_mes <-
  datos_policiales2022 %>%
  ggplot(aes(x = mes)) +
  geom_bar() +
  ggtitle("Cantidad de delitos por Mes") +
  xlab("Meses") +
  ylab("Cantidad de delitos") +
  coord_flip() +
  theme_minimal()
ggplotly(Grafico_por_mes) %>% config(locale = 'es')
```

## Gráfico de barras apiladas de Proporción de Delitos por Género

```{r}
Proporcion_de_Delito_por_Genero <-
  datos_policiales2022 %>%
  ggplot(aes(x = Genero, fill = Delito)) +
  geom_bar(position = "fill") +
  ggtitle("Proporciones de Delito por tipo de Género") +
  xlab("Género") +
  ylab("Proporción") +
  labs(fill = "Delito") +
  theme_minimal()
ggplotly(Proporcion_de_Delito_por_Genero) %>% config(locale = 'es')
```

## Gráfico simple de Cantidad de Delitos en San José, Alajuela, Cartago y Heredia 

'''{r}
grafico <- filter(datos_policiales2022, grepl('HEREDIA| ALAJUELA| CARTAGO| SAN JOSÉ', Provincia))
Delitos_SanJose_Alajuela_Cartago_Heredia <-
  ggplot(data= grafico, aes(x=Provincia)) +
  geom_bar( ) +
 ggtitle("Delitos en San José Alajuela Cartago Heredia") +
  xlab("Provincias") +
  ylab("Cantidad de Delitos") +
  theme_minimal()
ggplotly(Delitos_SanJose_Alajuela_Cartago_Heredia) %>% config(locale = 'es')
```

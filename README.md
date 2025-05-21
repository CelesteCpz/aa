---
title: "Tendencias del Cambio Climático Global"
author: "Celeste Céspedes"
format: html
toc: true
---

# Análisis de temperaturas globales (1900–2015)

Este sitio muestra un análisis de la evolución de la temperatura media en varios países entre 1900 y 2015. Se utilizaron datos públicos de Berkeley Earth, disponibles en [Kaggle](https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data).

## Introducción

El cambio climático es uno de los fenómenos más urgentes de nuestra época. Sus impactos se reflejan en el aumento de temperaturas, cambios en los patrones climáticos y consecuencias sociales y ambientales. El presente análisis examina cómo ha variado la temperatura promedio en diferentes países del mundo en los últimos 100 años.

El objetivo es: - Observar si hay tendencias claras de aumento de temperatura. - Comparar la evolución entre países de diferentes continentes. - Analizar un año reciente para ver qué países presentan temperaturas más altas.

## Imagen relacionada

![](images/nasa-cambio-climatico.jpg)

> **Figura 1.** Cambio climatico. Fuente: [climate.nasa.gov](https://climate.nasa.gov)

## Descripción del conjunto de datos

El conjunto de datos utilizado proviene de Berkeley Earth y contiene registros de temperatura promedio mensual por país desde el siglo XVIII. Para este análisis se consideraron solo los datos desde 1900 hasta 2015, y se seleccionaron cinco países: Costa Rica, Estados Unidos, Brasil, Alemania e India.

Las variables principales utilizadas fueron: - `dt`: Fecha de medición. - `AverageTemperature`: Temperatura media mensual (en grados Celsius). - `Country`: Nombre del país.

La columna `dt` fue procesada para extraer el año, permitiendo así análisis agregados anuales.

## Preguntas que se intentan responder

-   ¿Ha habido un aumento sostenido en la temperatura media global?
-   ¿Qué países han experimentado los mayores aumentos?
-   ¿Cómo se comparan entre sí los países tropicales, templados y continentales?

## Datos y gráficos

```{r}
library(tidyverse)
library(readr)
library(plotly)

# Leer los datos
data <- read_csv("data/GlobalLandTemperaturesByCountry.csv")

# Limpiar y transformar
df <- data %>%
  filter(!is.na(AverageTemperature)) %>%
  mutate(Year = as.integer(substr(dt, 1, 4))) %>%
  filter(Year >= 1900, Country %in% c("Costa Rica", "United States", "Brazil", "Germany", "India"))

# Gráfico 1: Distribución de temperaturas medias anuales por país (1900–2015)
graf1_boxplot <- df %>%
  group_by(Year, Country) %>%
  summarise(TempMedia = mean(AverageTemperature, na.rm = TRUE), .groups = "drop") %>%
  ggplot(aes(x = Country, y = TempMedia, fill = Country)) +
  geom_boxplot() +
  theme_minimal() +
  labs(title = "Distribución de temperaturas medias anuales por país (1900–2015)",
       y = "Temperatura media anual (°C)",
       x = "País")

print(ggplotly(graf1_boxplot))
El gráfico muestra la distribución de la temperatura media anual en cada país entre 1900 y 2015. Se observa que Costa Rica y Brasil, ubicados en zonas tropicales, presentan temperaturas más altas y con menor variabilidad. En contraste, Alemania y Estados Unidos tienen una distribución más amplia, reflejando una mayor variación estacional a lo largo del tiempo. India se posiciona entre ambos extremos, lo que sugiere una combinación de factores tropicales y continentales.

```{r}
# Crear columna de zona climática
df_zonas <- df %>%
  mutate(Zona = case_when(
    Country %in% c("Costa Rica", "Brazil") ~ "Tropical",
    Country %in% c("United States", "Germany") ~ "Templado",
    Country == "India" ~ "Continental"
  ))

# Crear gráfico tipo boxplot para comparar zonas climáticas
graf4 <- df_zonas %>%
  ggplot(aes(x = Zona, y = AverageTemperature, fill = Zona)) +
  geom_boxplot() +
  theme_minimal() +
  labs(title = "Comparación de temperaturas según zona climática (1900–2015)",
       y = "Temperatura promedio mensual (°C)",
       x = "Zona climática") +
  theme(legend.position = "none")

print(ggplotly(graf4))
```

El gráfico muestra que los países tropicales presentan temperaturas consistentemente más altas en comparación con las zonas templadas y continentales. Además, se observa que las zonas templadas tienen una mayor variabilidad, mientras que la zona continental (representada por India) tiene valores intermedios.

## Referencias:

*Climate change: Earth surface temperature data*. (2017, 1 mayo). Kaggle. https://www.kaggle.com/datasets/berkeleyearth/climate-change-earth-surface-temperature-data

Ward, K., & Ward, K. (2025, 6 febrero). *Climate change - NASA Science*. NASA Science. https://science.nasa.gov/climate-change/

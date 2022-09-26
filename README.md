# GDABellabeat
Proyecto final de Análisis para Certificación Google Data Analytics basado en Bellabeat

# Tabla de Contenidos
1. Resumen
2. Descripcion de fuente de datos
3. Limpieza y Manipulación de datos
4. Analisis y Visualización
5. Recomendación en base al analisis


# 1. Resumen
[Bellabeat](https://bellabeat.com/) es una marca enfocada en el bienestar de las mujeres, el cual genero un ecosistema de productos y servicios enfocados en la salud y bienestar de las mujeres.
Su lema es "Empoderando las mujeres para desbloquear todo su potencial".
Uno de sus productos estrella, "Leaf" un weareable tipo pulsera con gran estilo, permite recopilar información del usuario y otorgarle mediante su app recomendaciónes y sugerencias para mejorar su salud y bien estar en general.
Gracias al gran aumento de interes por parte del publico en la salud, los dispositivos tipo Weareables fueron incrementando en atenciòn y ventas alrededor del mundo.
Por tal movito, Co-fundadora y CCO de Bellabea, Urška Sršen, considera que este aumento de interes podria generar nuevas oportunidades de crecimiento, para esto es necesario analizar el comportamiento de uso y generar una nueva campaña de marketing.

En este proyecto, la idea del mismo es, mediante la utilizaciòn de la informaciòn recopilada por "FitBit Fitness", para obtener una perspectiva del alcance del servicio, que tanto lo utilizan y como lo hacen. Esto le permitira al equipo de Marketing generar una nueva campaña con el fin de incrementar el uso/compras del producto y servicio propio.
Podran encontrar las recomendaciònes en base a lo encontrado al final de todo.

1.a Utilizando los datos recopilados a travez de la app FitBit Fitness se espera responder las siguientes preguntas.

  1. ¿Cuales son las tendencias en el uso de dispositivos inteligentes tipo Wearables?
  2. ¿Como estas tendencias se podrian aplicar a los consumidores de Bellabeat?
  3. ¿Como podria utilizar el equipo de Marketing de Bellabeat las tendencias encontradas?

1.b Partes Interesadas (Stakeholders)
  - Urška Sršen: Co-fundadora y CCo de Bellabeat.
  - Sando Mur: Co-Fundador y Matematico, miembro clave del equipo ejecutivo de Bellabeat.
  - Equipo Analitico de Marketing de Bellabeat: Grupo de analistas de datos responsables de la colecta, analisis y reportes de datos que ayuda a guiar la nueva             estrategia de marketing de Bellabeat.


# 2. Descripcion de fuente de datos
La fuente de datos fue generada en base a una encuesta realizada a traves del sistema "Amazon Mechanical Turk" entre las fechas 03/12/2016 y 05/12/2016.
Treinta de los usuarios encuestados dieron su consentimiento para compartir los datos recopilados a travez de la app FitBit Fitness, la cual incluye:
  Reportes por minuto de:
  - Distintos tipos de actividades fisicas.
  - Pulsaciónes del corazon.
  - Monitoreo del Sueño.
A considerar:
  - Las variaciònes de lo reportado corresponde al comportamiento y preferencias de cada individuo.
  - Debemos asumir que este conjunto de datos de los usuarios de Fitbit sera representativo de los usuarios de "Leaf", aunque tenemos que tener en cuenta que los           usuarios de Fitbit pueden pertenecer a ambos generos, mientras que el publico destinado para "Leaf" son las mujeres.

De todas formas, el conjunto de datos se puede considerar como bueno debido a:
  - Confiable: Es precisa, completa, e imparcial para el uso que se le va a dar.
  - Original: No fue realizada por una fuente externa, sino que proviene directamente del usuario final.
  - Comprensiva: Posee todos los datos necesarios para responder las preguntas del proyecto.
  - Actual: La mas actualizada al momento de realizar el projecto.
  - Citada: Obtenida directamente desde la fuente e explicita

# 3. Limpieza y Manipulación de datos

Herramientas utilizadas:
  - Microsoft Excel: Herramienta de procesamiento de datos, util para el manejo de bajas cantidades de datos, analisis y ciertos tipos de visualizaciónes.
  - R: Lenguaje de programación enfocado al uso de estadisticas y graficos. Normalmente utilizado para la limpieza, analisis y visualizaciónes de grandes volumenes         de datos.
  - Tableau: Herramienta multiproposito enfocado en la visualización de distintos tipos de volumenes de datos, enfocada para las areas de Inteligencia                   Comercial.
  
Paquetes utilizados en R:

```
install.packages("tidyverse")
install.packages("dplyr")
install.packages("metan")
install.packages("lubridate")

library(tidyverse)
library(dplyr)
library(metan)
library(lubridate)
```

Como se importan los datasets:

```
activity <- read.csv("dailyActivity_merged.csv")
sleep <- read.csv("sleepDay_merged.csv")
weight <- read.csv("weightLogInfo_merged.csv")
daily_calories <- read.csv("dailyCalories_merged.csv")
daily_steps <- read.csv("dailySteps_merged.csv")
daily_intensities <- read.csv("dailyIntensities_merged.csv")
```

Control de los dataframes asignados:

```
summary(activity)
summary(sleep)
summary(weight)
summary(daily_calories)
summary(daily_steps)
summary(daily_intensities)
```

Luego de controlar los dataframes, encontramos que hay similitudes de datos dentro de "activity" para los dataframes "daily_calories","daily_steps","daily_intensities"
Para validar que se encuentran todos los datos, utilizamos el operador %in%
```
daily_calories %in% activity
daily_steps %in% activity
daily_intensities %in% activity
```
Como los tres resultados fueron verdadero, se eliminan los dataframes indicados del proyecto.





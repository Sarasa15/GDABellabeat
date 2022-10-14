# GDABellabeat
Proyecto final de Análisis para Certificación Google Data Analytics basado en [Bellabeat](https://bellabeat.com/)

# Tabla de Contenidos
1. Resumen
2. Descripcion de fuente de datos
3. Limpieza y Manipulación de datos
4. Análisis  y Visualización
5. Recomendaciones en base a lo analizado


# 1. Resumen
[Bellabeat](https://bellabeat.com/) es una marca enfocada en el bienestar de las mujeres, el cual generó un ecosistema de productos y servicios enfocados en la salud.
Su lema es "Empoderando a las mujeres para desbloquear todo su potencial".
Uno de sus productos estrella, "Leaf" un weareable tipo pulsera con gran estilo, permite recopilar información del usuario y otorgarle mediante su app recomendaciones y sugerencias para mejorar su salud y bienestar en general.
Gracias al gran aumento de interés por parte del público en la salud, los dispositivos tipo Weareables fueron incrementando en atención y ventas alrededor del mundo.
Por tal motivo, Co-fundadora y CCO de Bellabea, Urška Sršen, considera que este aumento de interés podría generar nuevas oportunidades de crecimiento, para esto es necesario analizar el comportamiento de uso y generar una nueva campaña de marketing.

En este proyecto, la idea del mismo es, mediante la utilización de la información recopilada por "FitBit Fitness", para obtener una perspectiva del alcance del servicio, que tanto lo utilizan y cómo lo hacen. Esto le permitirá al equipo de Marketing generar una nueva campaña con el fin de incrementar el uso/compras del producto y servicio propio.
Podrán encontrar las recomendaciones en base a lo encontrado al final del documento.

1.a Utilizando los datos recopilados a través de la app FitBit Fitness se espera responder las siguientes preguntas.

  1. ¿Cuales son las tendencias en el uso de dispositivos inteligentes tipo Wearables?
  2. ¿Como estas tendencias se podrían aplicar a los consumidores de Bellabeat?
  3. ¿Como podría utilizar el equipo de Marketing de Bellabeat las tendencias encontradas?

1.b Partes Interesadas (Stakeholders)
  - Urška Sršen: Co-fundadora y CCo de Bellabeat.
  - Sando Mur: Co-Fundador y Matemático, miembro clave del equipo ejecutivo de Bellabeat.
  - Equipo Analítico de Marketing de Bellabeat: Grupo de analistas de datos responsables de la colecta, análisis y reportes de datos que ayuda a guiar la nueva             estrategia de marketing de Bellabeat.


# 2. Descripcion de fuente de datos
La fuente de datos fue generada en base a una encuesta realizada a traves del sistema "Amazon Mechanical Turk" entre las fechas 03/12/2016 y 05/12/2016.
Treinta de los usuarios encuestados dieron su consentimiento para compartir los datos recopilados a través de la app FitBit Fitness, la cual incluye:
  Reportes por minuto de:
  - Distintos tipos de actividades físicas.
  - Pulsaciones del corazón.
  - Monitoreo del Sueño.
A considerar:
  - Las variaciones de lo reportado corresponde al comportamiento y preferencias de cada individuo.
  - Debemos asumir que este conjunto de datos de los usuarios de Fitbit será representativo de los usuarios de "Leaf", aunque tenemos que tener en cuenta que los           usuarios de Fitbit pueden pertenecer a ambos géneros, mientras que el público destinado para "Leaf" son las mujeres.

De todas formas, el conjunto de datos se puede considerar como bueno debido a:
  - Confiable: Es precisa, completa, e imparcial para el uso que se le va a dar.
  - Original: No fue realizada por una fuente externa, sino que proviene directamente del usuario final.
  - Comprensiva: Posee todos los datos necesarios para responder las preguntas del proyecto.
  - Actual: La más actualizada al momento de realizar el proyecto.
  - Citada: Obtenida directamente desde la fuente e explicita

# 3. Limpieza y Manipulación de datos

Herramientas utilizadas:
  - Microsoft Excel: Herramienta de procesamiento de datos, útil para el manejo de bajas cantidades de datos, análisis y ciertos tipos de visualizaciones.
  - R: Lenguaje de programación enfocado al uso de estadísticas y gráficos. Normalmente utilizado para la limpieza, análisis y visualizaciones de grandes volúmenes         de datos.
  - Tableau: Herramienta multiproposito enfocado en la visualización de distintos tipos de volúmenes de datos, enfocada para las áreas de Inteligencia                   Comercial.
  
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

Como se importan los dataframes:

```
activity <- read.csv("dailyActivity.csv")
sleep <- read.csv("sleepDay.csv")
weight <- read.csv("weightLogInfo.csv")
daily_calories <- read.csv("dailyCalories.csv")
daily_steps <- read.csv("dailySteps.csv")
daily_intensities <- read.csv("dailyIntensities.csv")
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

Luego de controlar los dataframes, encontramos que hay similitudes de datos dentro de la tabla "activity" para los dataframes "daily_calories","daily_steps","daily_intensities"
Para validar que se encuentran todos los datos, utilizamos el operador %in%
```
daily_calories %in% activity
daily_steps %in% activity
daily_intensities %in% activity
```
Como los tres resultados fueron verdaderos, se eliminan los dataframes indicados del proyecto.


Comenzamos con la limpieza de la tabla "Activity"
Al verificar la cantidad de ID's registrados
```
n_distinct(activity$Id)
```
Encontramos que hay 30 IDs que contemplan todos los campos de informaciòn y 3Id's que poseen datos incompletos, es probable que los mismos correspondan a antiguos dispositivos o que estaban utilizando dos dispositivos ese mismo dia, pero debido a que la falta de datos nos genera incongruencias en el resultado, se procede a eliminar del análisis todo ID que no contemple todos los campos de informaciòn

Continuamos validando las columnas TotalDistance y TrackerDistance, donde encontramos que los valores son idénticos, excepto en algunos pocos casos, posiblemente porque el wereable trackea todo el dia y tambien debe tener guardar este valor por separado cuando el usuario inicia/finaliza cada actividad.
Para mantener la consistencia, pasamos a eliminar las diferencias.

```
difference <- activity$TotalDistance == activity$TrackerDistance
todelete <- which(difference == FALSE)
clean_activity <- activity[-todelete, ]
```

Quitamos las columnas que nos quedan sobrantes relacionadas, en este caso serian TrackerDistance y LoggedActivitiesDistance
```
clean_activity <- select(clean_activity, -c(TrackerDistance, LoggedActivitiesDistance))
```

Convertimos los strings de fecha al formato correspondiente para poder utilizar más adelante, con las funciones que nos provee  la librería "lubridate"
Se utiliza formato de fecha Americano
```
clean_activity$ActivityDate <- mdy(clean_activity$ActivityDate)
sleep$SleepDay <- mdy_hms(sleep$SleepDay)
weight$Date <- mdy_hms(weight$Date)
weight$Date <- format(as.Date(weight$Date), "%Y-%m-%d")
```

Unificamos todas las tablas en una nueva y modificamos el nombre de la 2da columna final.
Finalizamos agregando una nueva columna con el detalle de que dia de la semana se guardó ese registro con "lubridate"
```
sleep_activity <- merge(sleep, clean_activity, by.x = c("Id", "Sleepday"), by.y = c("Id", "ActivityDate"), all = TRUE)
sleep_activity_weight <- merge(sleep_activity, weight, by.x = c("Id", "Sleepday"), by.y = c("Id", "Date"), all = TRUE)
colnames(sleep_activity_weight)[2] <- "Date"
dayoftheweek <- wday(sleep_activity_weight$Date, label = T, week_start = 1)
complete_activity <- add_column(sleep_activity_weight, dayoftheweek, .after = "Date")
```

Generamos una nueva tabla con el promedio de los datos de la tabla "complete_activity" la cual utilizaremos para realizar distintas tipos de visualizaciones.

```
to_viz <- complete_activity %>% group_by(dayoftheweek) %>%
    summarise('Total Steps' = mean(TotalSteps, na.rm = T), 
              'Total Distance' = mean(TotalDistance, na.rm = T),
              'Calories' = mean(Calories, na.rm = T),
              'Total Hours Asleep' = mean(TotalMinutesAsleep, na.rm = T)/60,
              'Total Hours In Bed' = mean(TotalTimeInBed, na.rm = T)/60,
              'Very Active Minutes' = mean(VeryActiveMinutes, na.rm = T),
              'Fairly Active Minutes' = mean(FairlyActiveMinutes, na.rm = T),
              'Lightly Active Minutes' = mean(LightlyActiveMinutes, na.rm = T),
              'Sedentary Minutes' = mean(SedentaryMinutes, na.rm = T)
              )
```

Terminamos exportando esta nueva tabla como .csv.

```
write.csv(to_viz, "to_viz.csv")
```

# 4. Análisis y Visualización

 Mediante la utilización de Tableau Public, generamos el siguiente gráfico para poder comprender el comportamiento de los usuarios.
 
![image](https://user-images.githubusercontent.com/34684651/193837415-3cd4a4d5-7f55-4769-aa7a-6a9b83c51907.png)

Resumen Gráfico: Días de la semana, Calorías Y Pasos Realizados Vs Distancia Recorrida

¿Que es lo que nos cuenta el gráfico?
  -Podemos observar el comportamiento de los usuarios durante los dias de la semana,  el primer pico lo vemos al segundo día de la semana donde la actividad física se incrementa, posiblemente por el entusiasmo que genera una vida saludable y realizar actividad física, pero mientras van pasando los dias, este comportamiento va decayendo.
 
  Para el fin de semana observamos un incremento tanto en calorías consumidas, ya que la gente tiende a no preparar las comidas para los fines de semana o tienen encuentros sociales donde no pueden prever lo que van a ingerir. No obstante podemos encontrar un aumento en la actividad física ya que se dispone de más horas libres los fines de semana contra los dias de semana, lo que genera que las personas sean más propensas a realizar actividad física leve o moderada pero por un tiempo prolongado (ej: salir a caminar o recorrer centros turísticos).
 
  -Destacamos el dia Domingo por su bajo registro de actividad física(Pasos realizados y Distancia recorrida), ya que en este dia las personas tienden a tomarlo como un descanso total de todo tipo de actividades, y quedarse en casa a recuperar energías para prepararse para el inicio de la semana.
  
 
 ![image](https://user-images.githubusercontent.com/34684651/193853876-ba9ec3d6-c7c5-439d-920f-6f2f5135124e.png)
  * los datos de "Fairly Active y Very Active" se suman ya que suponemos que corresponden a la misma actividad fisica solo que se marca que corresponde a una u otra cuando el usuario pasa cierto rango de frecuencia cardiaca.
  
 ¿Cuanto tiempo se emplea en cada actividad?
  -Todos los dias los usuarios han cumplido la cantidad mínima de minutos de actividad física recomendada por la OMS (150 a 300 Min por semana, 28Min por dia).
  -En promedio los usuarios registran mas de 16hs (960 Min) en un estado de sedentarismo, este valor es la suma del tiempo total sin realizar actividad alguna (ej: Trabajo de oficina, Dormir, Comer, etc.)
  
 ![image](https://user-images.githubusercontent.com/34684651/193864938-eebee878-cc30-49c6-85cd-c0d149095390.png)

¿Existen relaciones entre las "horas en la cama" y "horas de sueño"?

  -Vemos que los usuarios no siempre llegan a cumplir los tiempos recomendados por la OMS en horas de sueño (7hs en total), únicamente los fines de semana y los dias miércoles, es muy probable que esto es a efecto de que los dias Martes son los dias de mayor actividad fisica y por naturaleza del cuerpo, este requiere más tiempo de sueño para recuperarse.
  -Los dias Domingos las personas llegan a estar casi 1 hora más de tiempo acostado contra las horas de sueño, haciendo hincapié en que estos dias las personas tienden a relajarse más y preferir utilizar la cama no solo para dormir, probablemente para ver la TV, leer libros, utilizar su celular.
  
# 5. Recomendaciones en base a lo analizado

  5.a - Utilizaciòn de un sistema de notificaciones push para incrementar actividad física, reducir sueño:
        Como vimos anteriormente, ciertos usuarios tienden a no realizar tanta actividad fisica en dias puntuales, podríamos aprovechar mediante la utilizaciòn de una notificaciòn al usuario, alentando a salir a caminar, bailar o hacer alguna actividad física para así poder incrementar la cantidad de calorías consumidas durante la semana.
        Esta misma herramienta también podríamos utilizarla para recomendar no pasar demasiado tiempo en la cama si no es para dormir y así poder mejorar la calidad y tiempos de sueño de los usuarios.
        
  5.b - Alertar a los usuarios si pasaron demasiado tiempo sin realizar movimiento alguno, como en un trabajo de oficina, y sugerirles realizar algún tipo de Break para no acumular tantas horas de sedentarismo.
        Agregar una barra de progreso mostrando la cantidad de actividad física que realizaron y cuánto les falta para cumplir el objetivo diario predefinido.
  
  5.c - Crear un apartado tipo comunidad/grupos para incentivar la realización de actividad física, ya que está comprobando que cuando las personas hacen actividad física en grupo tienden a auto-superarse en cada objetivo, motivarse para continuar ejercitándose y ser más consistente.

Alan Torres

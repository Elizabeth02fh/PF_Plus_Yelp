# Análisis del Mercado Estadounidense 
Link de la documentación en Notion:

https://vigorous-shawl-3d2.notion.site/Documentaci-n-PlusYelp-4b2f3a69253e4c03b224971729391dbc

https://www.youtube.com/watch?v=OdFMln8FgOY&list=PLwvJe5JICe1EmQpI6hE8nQH5IuPuqGyx8&index=5

## Definición del problema

El mundo del emprendimiento es dinámico y desafiante, y aunque muchos nuevos negocios logran establecerse en el mercado, un porcentaje significativo enfrenta dificultades para mantenerse a largo plazo. Según datos de la Oficina de Estadísticas Laborales de EE.UU. (BLS), aproximadamente el 20% de las nuevas empresas fracasan en su primer año, mientras que el 50% no sobrevive más allá de los cinco años y alrededor del 65% cierra antes de los diez años. Estas cifras reflejan la complejidad del entorno empresarial y la necesidad de una toma de decisiones informada para asegurar la viabilidad y el crecimiento de los negocios.

### Objetivos:

- Predecir qué líneas de negocio crecerán o declinarán más.

- Predecir la ubicación más conveniente para los nuevos locales de los negocios evaluados.

- Implementar un sistema de recomendaciones para los usuarios de la plataforma PlusYelp.


<img src="https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExd3A1dmF4ZTcyM3dvcndldjNoMW81ZXEyaXR2ZGEycTh5MjhjbDZjdiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/w6NLkHuoWlcdXIHktr/giphy.gif" width="200">

### Usuarios finales de los resultados 

- Se dirige al mercado estadounidense y trabaja principalmente con datos de Yelp para esa región.
- El enfoque está en restaurantes y empresas relacionadas; no se incluirán otros países ni una variedad de tipos de negocios.
- El software estará diseñado para funcionar en cualquier computadora como una aplicación web para cualquier usuario, que solo necesitará un servidor para su implementación.
- El software se desarrollará en un plazo de 3 semanas.

### KPI’s y Métricas

1) Variación porcentual del promedio de estrellas(calificación):
    PCC= (promedio estrellas año 2) - (promedio estrellas año 1) / ((promedio estrellas año 1) *100

2) Variación porcentual de reviews:
    PCR = (cantidad de reviews año 2) - (cantidad de reviews año 1) /((cantidad de reviews año 1))

3) popularidad: 
    PO = promedio(polaridad*(1-subjetividad))

4) porcentaje de sucursales cerradas por rubro
    CSC = (cantidad de sucursales cerradas) / (cantidad de sucursales totales por rubro) *100

- Numero de ordenes por dia en un restaurante

## Recoleccion y Calidad de Datos

Se descargaron de la pagina de Yelp

https://www.yelp.com/

## Análisis de datos y limpieza de datos

Para nuestro proceso de limpieza, comenzamos delimitando los datos disponibles. Luego, inspeccionamos los datasets y seleccionamos las columnas de interés. Durante la limpieza, realizamos:
- Imputación de valores faltantes según el contexto.
- Estandarización de valores redundantes para mantener coherencia.
- Conversión de datos categóricos para evitar duplicidades.
- Corrección de errores estructurales (tipográficos y de formato).
Además, para garantizar una correcta ingesta en MySQL, corregimos:
- Columnas no relevantes, valores nulos y duplicados.
- Errores en caracteres especiales y tipos de datos.
- Por último, aplicamos PLN en los comentarios para limpiar texto, corregir ortografía y -estandarizar términos.

## Calidad de los datos
Luego de la limpieza de los datos, se procede a normalizar los nombres de columnas, de tal manera coincida y sea la misma en todas las tablas, posterior procedemos a modelar la estructura relacional que tendrá nuestros datos en el Datawarehouse. Hasta ahora se cuenta con 4 tablones, los cuales serán normalizados hasta alcanzar la Tercera Forma Normal, y se alcanza de la siguiente manera:
- Primera Forma Normal: Una relación se encuentra en 1FN sólo si cada uno de sus atributos contiene un único valor para un registro determinado.
- Segunda Forma Normal: Una relación se encuentra en 2FN sólo si se cumple 1FN y todos sus atributos no clave dependen en forma completa de la clave.
- Tercera Forma Normal: Una relación se encuentra en 3FN sólo si se cumple 2FN y los campos no clave dependen únicamente de la clave o los campos no clave no dependen unos de otros.

![image](https://github.com/user-attachments/assets/4e50c7f7-c80f-4f80-b513-670ef4fe63e9)

## Arquitectura implementada

La arquitectura empleada para el proceso de ETL se basa en el lenguaje Python y SQL en conjunto con los servicios de la nube de GCP.

Una vez que se tuvieron las tablas definidas, así como sus campos y conexiones (llaves primarias y secundarias), se creó en la nube de GCP una database en el servicio Google Cloud SQL. Luego, se estableció la conexión mediante un punto de acceso y se creó un Compute Engine en Google Cloud SQL - MySQL. En la base de datos creada en la nube, se definió la estructura de las tablas con sus llaves primarias y secundarias, lo que permitió establecer la conexión con el editor de código y cargar los datos limpios en la base de datos de Google Cloud SQL.

Para almacenar cada tabla en la nube, se creó un Cloud Storage. Esto se realizó para cada tabla mediante Cloud Dataflow, el cual generaba un archivo CSV a partir de una tarea de única vez, filtrando los datos por la última fecha.

Para la carga incremental, se creó otro pipeline en Cloud Dataflow, que se ejecutaba diariamente y generaba un archivo CSV incremental únicamente para las tablas de hechos. Este pipeline filtraba la carga con la última fecha de las tablas. Para unir la carga incremental y la carga inicial, se generó un clúster en BigQuery, en el cual las tablas de hechos tenían una tabla de carga inicial y otra de carga incremental. En la tabla de carga inicial, se ingerían los datos de los archivos CSV almacenados en Google Cloud Storage. Para cargar los datos de los archivos CSV de carga incremental a la tabla incremental en BigQuery, se creó un Job en Cloud Data Fusion, asegurándose de que no se repitan llaves primarias al fusionar los datos con la tabla de carga inicial.

![image](https://github.com/user-attachments/assets/007f1d42-87c2-4d7d-99a4-7c37eb92a546)


Para que el Job se ejecutara automáticamente cuando se insertaba un nuevo archivo CSV en las carpetas de carga incremental, se configuró un Cloud Function. Esta función detectaba nuevas inserciones y/o modificaciones en los archivos CSV y activaba el Job correspondiente. Con este proceso, se mantiene actualizado el data warehouse en BigQuery.

![image](https://github.com/user-attachments/assets/039fe7ed-2f40-4110-8b29-732ae11e213a)

## Dashboard

### Página 1.
## Tendencias
California y Lousiana presentan altos nivel de satisfacción mayor, pero crecimiento del mercado medianos.
Idaho y Florida presentan mejores perspectivas de crecimiento de negocios exitosos y en satisfacción del cliente no se encuentran muy alejados california y louisiana. 
Florida presentó menor decrecimiento debido a la pandemia y mejor recuperación.
De las categorías principal, en Florida la que  presenta mayor crecimiento antes de la pandemia y mejor recuperación en el 2021 es FOOD.
subcategorías y atributos que mas se repiten en florida para la categoria FOOD
Algunos de los negocios que destacan por su retención o satisfacción de sus clientes en florida son: 

 subcategorías y atributos que mas se repiten en florida para la categoria FOOD

![image](https://github.com/user-attachments/assets/b97cbff9-9370-468f-9a7a-641aad08ce6f)

### Página 2.
## Riesgos
Porcentaje de negocios cerrados por estado
California es el estado con la mayor satisfacción del cliente en todas las categorías, también es el estado con el mayor número de cierres de sucursales, lo que puede ser un indicio de saturación del mercado.
Top ten del porcentaje de empresas con mayor cierre y por  estado
Entre las grandes franquicias como Starbucks o Subway, las que menos riesgo tienen son Taco Bell, Burger King y McDonald's, ya que el porcentaje de negocios cerrados y su valor absoluto son cifras atractivas para una inversión
Porcentaje de negocios de cada categoría por año
Muestra que los negocios de night life y art & entertaiment, presenta un mayor porcentaje de negocios cerrado a comparacion de las demas categorías
ya que debido al covid 19 muchos locales de nighlife se cerraron por completo asi como también, los lugares de artes y entretenimiento.  


![image](https://github.com/user-attachments/assets/ac185563-67bf-458c-9a91-a2c46eeff553)


### Página 3.
## Oportunidades
Se crea un indice que permite identificar los negocios con demanda insatisfecha en funcion de reseñas negativas
Se puede precisar negocios con reseñas mas negativas (menos estrellas) pero implica tambien menor cantidad de reseñas. 
Se puede escoger la categoria por analizar. 

![image](https://github.com/user-attachments/assets/5f053613-52ca-4387-b03b-a97c03721ab0)

## SISTEMA DE RECOMENDACIÓN DE RESTAURANTES
Usuarios Antiguos
Sistema de  recomendacion en base a contenido
Tomando en cuenta datos del historial de reseñas del usuario se proporciona sugerencias en base a los restaurantes que mas le gusto y  comparándolo con nuestra base de datos de restaurantes previamente categorizados y clasificados
Usuarios Nuevos
Sistema de  recomendacion en base a popularidad
Al carecer de información del usuario  se  le muestra una serie de recomendaciones en base a la popularidad total de los restaurantes, en base a cantidad de reseñas y puntuación alta en estrellas

![image](https://github.com/user-attachments/assets/606a610c-5b9d-4c90-860f-c93839c8348f)

![image](https://github.com/user-attachments/assets/8cca7c59-7c06-4536-a31d-4c35f9ec2bbc)

## stack tecnológico
- Extraction and transformation:
- load and storage:
- analysis:
- incremental load or streaming
- Presentation: dash plotly

## Diccionario de datos

users:
'n_user_id': integer user code,
'review_count': number of reviews written by the user, 
'yelping_since': user registration date, 
'useful': number of useful votes given by the users to other user's reviews,
'funny': number of funny votes given by the users to other user's reviews, 
'cool': number of cool votes given by the users to other user's reviews, 
'friends': comma separated str with friend's alphanumeric codes , 
'fans': number of fans, 
'average_stars': average reviews stars given by the user.

users_ids:
'n_user_id': integer user code,
'user_id': alphanumeric user code 

# Work Flow
## Extraction and tranformation

### Users dataset:
- load: it was made in chunks of 400.000 rows to the dataframe called "users"
- drop columns: all the columns with amounts of compliments.
- change data type to use less memory: 'average_stars':np.float32 , 'fans':'uint16', 'review_count':'uint16', 'cool':'uint32', 'useful':'uint32', 'funny':'uint32', yelping_since:'%Y-%m-%d %H:%M:%S'
-  get integer index to replace alphanumeric index: to use less memory the alphanumerid user_id columns was replaced by the dataframe index(0 - 1987896)
-   No duplicated or null founded 



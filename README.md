# Análisis del Mercado Estadounidense 
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

## stack tecnológico
- Extraction and transformation:
- load and storage:
- analysis:
- incremental load or streaming
- Presentation: dash plotly

## Calidad de los datos
Luego de la limpieza de los datos, se procede a normalizar los nombres de columnas, de tal manera coincida y sea la misma en todas las tablas, posterior procedemos a modelar la estructura relacional que tendrá nuestros datos en el Datawarehouse. Hasta ahora se cuenta con 4 tablones, los cuales serán normalizados hasta alcanzar la Tercera Forma Normal, y se alcanza de la siguiente manera:

- Primera Forma Normal: Una relación se encuentra en 1FN sólo si cada uno de sus atributos contiene un único valor para un registro determinado.
- Segunda Forma Normal: Una relación se encuentra en 2FN sólo si se cumple 1FN y todos sus atributos no clave dependen en forma completa de la clave.
- Tercera Forma Normal: Una relación se encuentra en 3FN sólo si se cumple 2FN y los campos no clave dependen únicamente de la clave o los campos no clave no dependen unos de otros.


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



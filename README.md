## Definición del problema

El mundo del emprendimiento es dinámico y desafiante, y aunque muchos nuevos negocios logran establecerse en el mercado, un porcentaje significativo enfrenta dificultades para mantenerse a largo plazo. Según datos de la Oficina de Estadísticas Laborales de EE.UU. (BLS), aproximadamente el 20% de las nuevas empresas fracasan en su primer año, mientras que el 50% no sobrevive más allá de los cinco años y alrededor del 65% cierra antes de los diez años. Estas cifras reflejan la complejidad del entorno empresarial y la necesidad de una toma de decisiones informada para asegurar la viabilidad y el crecimiento de los negocios.

![image](https://github.com/user-attachments/assets/5cb04fbb-d398-4e62-abab-c80e124be9d1)



Uno de los factores determinantes en el éxito o fracaso de un negocio es la elección del rubro y su capacidad de adaptación a las tendencias del mercado. Sectores como la restauración, el turismo, el entretenimiento y el comercio minorista han demostrado ser especialmente sensibles a las fluctuaciones económicas, cambios en los hábitos de consumo y evolución tecnológica. Durante los últimos años, por ejemplo, el auge del comercio electrónico ha afectado significativamente las ventas minoristas tradicionales, mientras que la digitalización ha redefinido la experiencia en la industria del entretenimiento.

Además, la ubicación de un negocio juega un papel crucial en su rentabilidad. Un estudio de Yelp reveló que las empresas situadas en áreas con alto tráfico peatonal y cercanas a zonas residenciales con ingresos medios-altos tienen mayor probabilidad de éxito. Sin embargo, muchas startups y pequeños emprendedores no cuentan con herramientas adecuadas para analizar estos factores y terminan eligiendo ubicaciones menos estratégicas, lo que puede afectar su sostenibilidad a largo plazo.
<img src="https://github.com/user-attachments/assets/d4963d17-197e-4725-9faa-31377dda6962" width="400">

Para abordar estos desafíos, el análisis de datos se ha convertido en una herramienta clave para predecir qué sectores tienen mayor potencial de crecimiento o declive y determinar la mejor ubicación para nuevas sucursales. A través de técnicas como el procesamiento de lenguaje natural (NLP) para analizar reseñas de usuarios, modelos de predicción basados en tendencias históricas y métricas clave como la variación en calificaciones y la tasa de cierre de sucursales, es posible generar insights valiosos para la toma de decisiones estratégicas.

El presente proyecto busca aplicar ingeniería de datos para analizar y predecir el comportamiento de distintos sectores del mercado estadounidense, ayudando a emprendedores e inversionistas a tomar decisiones fundamentadas que reduzcan el riesgo de fracaso y aumenten las probabilidades de éxito en el competitivo entorno empresarial, puedes redimensionar, mas pequeño, la imagen


## Metricas

Son indicadores que mide la empresa, usando todos los datos que tiene a disposicion, ej. numero de ordenes por dia en un restaurante



### Objetivos:

- Predecir cuales son los rubros de negocios que mas creceran o decaeran
- Predecir la localizacin mas conveniente de nuevos locales de estos negocios evaluados
- Implementar un sistema de recomendaciones para usuarios

### KPI’s (Key Performance Indicator)

Es una metrica **clave,** estas son solo unas cuantas respecto a las metricas pero tienen un alto impacto en el negocio, por lo que mejorarlas representara un inpacto positivo para la empresa,

entendemos asi que todos los KPI’s son metricas pero no todas las metricas son KPI’s

1) Variación porcentual del promedio de estrellas(calificación):
    PCC= (promedio estrellas año 2) - (promedio estrellas año 1) / ((promedio estrellas año 1) *100

2) Variación porcentual de reviews:
    PCR = (cantidad de reviews año 2) - (cantidad de reviews año 1) /((cantidad de reviews año 1))

3) popularidad: 
    PO = promedio(polaridad*(1-subjetividad))

4) porcentaje de sucursales cerradas por rubro
    CSC = (cantidad de sucursales cerradas) / (cantidad de sucursales totales por rubro) *100


### Alcance del proyecto

### stack tecnológico
- Extraction and transformation:
- load and storage:
- analysis:
- incremental load or streaming
- Presentation: dash plotly

### Diccionario de datos

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



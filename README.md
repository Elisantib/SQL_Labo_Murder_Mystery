# SQL Murder Mystery!

Adapted By: Martin Arroyo.

Edit for: Elisa Santibañez Palma

<a href="https://colab.research.google.com/drive/1Q3LJaeH3eF8wzTWc84yABuGMcrwbNNwF?authuser=3#scrollTo=ZbLp7m-_OzAR" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/></a>

![sleuth](https://github.com/Laboratoria/DEV013-dataverse-chat/assets/142262726/67c23345-36ce-4ced-91bc-59b698032025)

## Escenario

¡Se ha cometido un crimen y los detectives necesitan tu ayuda! Te dieron el informe de la escena del crimen, pero de alguna manera lo perdiste. Recuerdas vagamente que el crimen fue un `murder` _(asesinato)_ que ocurrió en algún momento el 15 de Enero de 2018 y que tuvo lugar en `SQL City`. Depende de ti averiguar quién es el asesino usando solo tus habilidades en SQL y tu ingenio. Te proporcionan una conexión a la base de datos del Departamento de Policía, que tiene todas las pistas que necesitarás para atrapar al asesino.

Usa las habilidades que desarrollaste en SQL 101 y 102, junto con cualquier recurso que desees, para resolver el `SQL Murder Mystery`!"

## Conectandonos a la base de datos del Departamento de Policía

Para comenzar y ejecutar nuestras consultas, presionamos play en la celda de abajo para conectarnos a la base de datos del Departamento de Policía.

Para ejecutar las consultas, creamos una nueva celda de `Code` y escribimos `%%sql` en la parte superior. Luego podemos escribir nuestras consultas debajo.

```python
%%capture
# @title Press Play { display-mode: "form" }
# Install `teachdb` and `coop_grader`
print("Installing `teachdb` and its dependencies...")
!pip install --quiet --upgrade git+https://github.com/freestackinitiative/teachingdb.git git+https://github.com/martinmarroyo/coop_grader.git
print("Successfully installed `teachdb`")
import pandas as pd
from teachdb.teachdb import connect_teachdb
from coop_grader.sql_murder_mystery.check_suspect import check_suspect
# Set configurations for notebook
%load_ext sql
%config SqlMagic.autopandas = True
%config SqlMagic.feedback = False
%config SqlMagic.displaycon = False
pd.set_option('display.max_rows', None)
pd.set_option('display.max_columns', None)
pd.set_option('display.width', None)
pd.set_option('display.max_colwidth', 99)
# Load data
con = connect_teachdb(database="sql_murder_mystery")

%sql con
```

## Descubriendo tablas en la base de datos

Comenzamos nuestra búsqueda para encontrar al asesino explorando la base de datos del Departamento de Policía. Pero aún no has visto la base de datos y no sabes cuáles son las tablas, así que, ¿cómo sabes qué buscar?

Afortunadamente, la mayoría de los sistemas de gestión de bases de datos relacionales tienen esta información almacenada en un lugar donde puedes consultarla. Muy a menudo, se utiliza un esquema especial conocido como [`information_schema`](https://en.wikipedia.org/wiki/Information_schema) para almacenar información sobre las tablas y columnas en tu base de datos (también conocido como metadata). La base de datos del Departamento de Policía tiene un information schema, con la vista `tables` que te muestra qué tablas están disponibles, y la vista `columns` que te muestra todas las columnas de cada tabla y sus tipos de datos.

### Listando todas las tablas en la base de datos de la Policía

#### Revisa la [descripción de `information_schema.tables`](https://duckdb.org/docs/sql/information_schema.html#tables-and-views)

Primero, veremos todas las tablas disponibles para nosotros consultando la vista `information_schema.tables`. Te daremos la primera consulta para empezar, pero de aquí en adelante tendrás que idear las consultas restantes utilizando tu conocimiento de SQL y tu ingenio.

Aquí está la consulta necesaria para mostrarte las tablas en la base de datos del Departamento de Policía. Cópiala/Pégala en la celda de abajo y ejecútala para ver las tablas disponibles para ti:

>```sql
>SELECT *
>FROM information_schema.tables
>```

### Listando todas las tablas y sus columnas en la base de datos de la Policía

**Revisa la [descripción de `information_schema.columns`](https://duckdb.org/docs/sql/information_schema.html#columns)**

¡Genial! Ahora que sabemos qué tablas están disponibles, es hora de averiguar las columnas que tiene cada tabla junto con el tipo de datos de cada columna. Escribimos una consulta que muestre el nombre de la tabla, el nombre de la columna y el tipo de datos para cada tabla en la base de datos del Departamento de Policía utilizando la vista `information_schema.columns`. Nos aseguramos de que la salida esté ordenada por nombre de tabla y luego por nombre de columna (ascendente):

### Diagrama de Relaciones de Entidad 

Después de hacer un poco de trabajo de detective y encontrar las tablas en la base de datos del Departamento de Policía, descubres que hay un ERD (Diagrama de Relaciones de Entidad). Esto puede ser realmente útil en tu búsqueda para encontrar al asesino. Si lo prefieres, desafíate a ti mismo (y tus habilidades en SQL) a continuar solo consultando el `information_schema` según sea necesario. De lo contrario, puedes hacer clic en el desplegable a continuación para revelar el ERD que te ayudará a ver las tablas y las relaciones en la base de datos del Departamento de Policía de un vistazo:

![murder_mystery_schema](https://github.com/Laboratoria/DEV013-dataverse-chat/assets/142262726/6aaa7c81-7337-417a-8dcf-e597b64df88d)

------------

## Espacio de Trabajo

Aquí en adelante podrán encontrar todas las consultas que se realizaron en base a la base de datos del Departamento de Policía para encontrar al asesino. Cada una con su debida descripción del caso, hasta que pudimos dar con el sospechoso.

Finalmente el sospechoso fue verificado con éxito.

Usamos la celda de abajo para verificar a nuestro sospechoso, ejecutando su nombre a través de la función `check_suspect`. Si el nombre del sospechoso es correcto, la función nos lo dirá.

No olvidamos usar `%%sql` en la parte superior de las celdas de `Code` para que nuestras consultas SQL funcionen.

------------

### 1. Reuniendo pistas de la escena del crimen

Las imágenes de seguridad muestran que hubo 2 testigos. El primer testigo vive en la última casa de "Northwestern Dr". La segunda testigo, llamada Annabel, vive en algún lugar de "Franklin Ave".

    %%sql
    
    SELECT *
    FROM crime_scene_report
    WHERE type = 'murder' AND date = (20180115) AND city = 'SQL City'

### 2. Encontrando a Annabel Miller y Morty Schapiro

Reunimos algunos datos de los dos principales testigos de la escena del crimen

    %%sql
    
    SELECT *
    FROM person
    WHERE address_street_name IN ('Franklin Ave') AND name LIKE 'Annabel%'

---

    %%sql
    
    SELECT *
    FROM person
    WHERE address_street_name IN ('Northwestern Dr')
    order by address_number DESC

### 3. Entrevista a testigos Annabel y Morty

Revisamos las entrevistas correspondientes de los dos testigos. Esto fue lo que dijeron:

**Annabel** = *“Vi ocurrir el asesinato, y reconocí al asesino de mi gimnasio cuando estaba haciendo ejercicio la semana pasada el 9 de enero”*

**Morty** = *“Escuché un disparo y luego vi a un hombre salir corriendo. Tenía una bolsa de "Get Fit Now Gym". El número de membresía en la bolsa comenzaba con "48Z". Solo los miembros gold tienen esas bolsas. El hombre se subió a un coche con una placa que incluía "H42W".“*

    %%sql
    
    SELECT *
    FROM interview
    WHERE person_id IN ('16371', '14887')

### 4. Check in del 9 de enero

Averiguando sobre las personas que asistieron al "Get Fit Now Gym" ese 9 de enero que Annabel detalló y además según el id de membresía que vió el testigo Morty

    %%sql

    SELECT *
    FROM get_fit_now_check_in
    WHERE check_in_date = 20180109 AND membership_id LIKE '48Z%'

### 5. Placa de vehículo a donde subió el supuesto sospechoso

La placa del vehículo a la que se subió el supuesto asesino contenía los siguientes caracteres: ‘H42W…’

    %%sql

    SELECT *
    FROM drivers_license
    WHERE plate_number LIKE 'H42W%'

### 6. Dueño del vehículo

La dueña del vehículo en el que se subió el asesino es ‘Maxine Whitely’

    %%sql
    
    SELECT *
    FROM person
    WHERE license_id IN ('183779')

### 7. Miembros registrados en "Get Fit Now Gym"

Averiguamos quienes son los miembros del GYM que estuvieron ese 9 de enero según el id que vio el testigo Morty.

Estas dos personas, miembros gold del Gym hasta ahora son sospechosas del asesinato, ya que, los dos coinciden con las pistas que hemos reunido hasta ahora, desde la fecha, el status de miembro y el id de membresía.

    %%sql
    
    SELECT *
    FROM get_fit_now_member
    WHERE membership_status = 'gold' AND id IN ('48Z7A', '48Z55')

### 8. Entrevista sospechosos

Revisamos nuevamente las entrevistas, pero de estos dos sospechosos, solo se encontró una.

**Jeremy Bowers** = *“Fui contratado por una mujer con mucho dinero. No sé su nombre pero sé que mide alrededor de 5'5" (65") o 5'7" (67"). Tiene el cabello rojo y conduce un Tesla Model S. Sé que asistió al Concierto Sinfónico SQL 3 veces en diciembre de 2017.”*

    %%sql
    
    SELECT *
    FROM interview
    WHERE person_id IN ('28819', '67318')

### 9. Coincidencias con descripción de Jeremy

    %%sql
    
     SELECT *
     FROM drivers_license
     WHERE hair_color = 'red' AND car_make = 'Tesla' AND gender = 'female'
---
    %%sql
    
    SELECT *
    FROM person
    WHERE license_id IN ('202298', '291182', '918773')

### 10. Descubriendo a la mujer que describió Jeremy

Miranda Priestly es la mujer que contrató a Jeremy, coincide exactamente con su descripción.

    %%sql
    
    SELECT *
    FROM facebook_event_checkin
    WHERE person_id IN ('99716', '90700')
---
    %%sql
    
    SELECT *
    FROM income
    WHERE ssn = 987756388

# CASO RESUELTO!!!

ASESINA ENCONTRADA!

    check_suspect("Miranda Priestly")
Congrats, you found the brains behind the murder!
Everyone in SQL City hails you as the greatest SQL detective of all time.
Time to break out the champagne!
True


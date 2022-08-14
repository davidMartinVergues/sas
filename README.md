# Qué es SAS?

Es un conjunto integrado de programas que abarca múltiples áreas de trabajo del campo científico,
centrándose especialmente en todas las ramas de la estadística aplicada. Diseñado de manera tal que respondiera a una recolección, transformación, análisis y reporte de datos.

## Características de SAS

1. Permite realizar operaciones sobre las filas d eun conjunto de datos.
2. El lenguaje SAS opera principalmente sobre tablas de datos:
	* puede leerlas
	* transformalas
	* combinarlas
	* resumirlas
	* crear informes a partir de ellas

3. es un intérprete de SQL

4. las key word no están reservadas así q cuidado pq se pueden usar como identificadores

SAS cuenta con:
1. `SAS Studio` es una aplicación virtual (necesita un software de virtualización para ejecutarse) donde pondremos programar en SAS
2. `SAS interprise Guide` es la herramineta más amigable que SAS pone a nuetsra disposición para realizar análisis de datos y creación
                         de modelos para usuario sin muchos conocimientos en estadística ni gestión de bbdd. 

## Instalación SAS university edition (gratuito)

1. descargar el software de virtualización (virtualbox)
2. descargar SAS university studio (.ova)
3. Crear carpeta en el path local q queramos`SASUniversityEdition`-> `myfolders`
4. en virtualbox configurar como carpeta compartida myfolders creada anteriormente
5. cargar y arrancar la máquina virtual de SAS (.ova)

## Componentes de SAS

1. SAS Base
Provee acceso, manejo, análisis y presentación de datos con un opderoso ambiente de desarrollo de apps.

2. SAS/ACCESS
Provee enlace entre SAS y bbdd como DB2,Oracle,ADABAS, ...

3.SAS/AF
Utilidad interactiva usada para crear apps de ventanas, por ejm menus,...

4. SAS/ASSISST
Asistente para crear diversas tareas, tales como acceso, manejo y presentación de datos, etc

5. SAS/ETS
Herramienta de análisis de econometría, análisis y predicción de series de tiempo, modelado y simulación de sistemas

6. SAS/GRAPH
Produce gráficos de alta calidad con variedad de patrones

7. SAS/IML
Lenguaje interactivo de matrices, además de ser dinámico

8. SAS/OR
Herramienta para la solución de problemas de investigación de operaciones

9. SAS/STAT
Provee análisis fundamentales estadístico, por ejemplo varianza, regresión multivariable, cluster, categórico, etc

10. SAS/Share
Permite el acceso concurrente a datasets

11. SAS/LAB
Provee un ambiente integrado para ejecutar análisis de datos com regresión y análisis de varianza.

## Beneficios de SAS

- simplifica la presentación de datos gráficamente
- crea modelos de manera fácil y rápida
- procesos interactivos y automatizados que funcionan en entornos por lotes y en tiempo real.
- INteractua con la información de manera rápida e intuitiva.

## Conceptos fundamentales de SAS

### Los datos, tablas y datasets

Los datos q vamos a utilizar los tenemos contenidos o bien en bbdd, en ficheros de texto, excel, csv,... pero para poderlos utilizar con SAS los debemos transformar en tablas SAS o datasets. Estos datasets o tablas SAS tienen extensión `.sas7bdat` En las tablas de SAS los registros se les llama `observaciones` y las columnas o campos se llaman `variables`.

### Los programas en SAS 

Hay 4 modos distintos para poder escribir un programa en SAS:

1. Pasos DATA
2. Procedimientos o PROCs
3. Lenguaje SQL
4. Lenguaje MACRO

### PASO DATA y Procedimientos SAS

```sql
data table_nueva;
a=1;
b="casa";
run;

proc print data=table_nueva;
run;
```
Este es un ejemplo de cómo escribir un paso data y un proc para imprimir el resultado.

#### Pasos data or DATA STEP

Nos sirve para crear tablas o datasets, para ello podemos:

1. crear tablas nuevas

Para ello podemos:

##### construirlas manualmente, indicando variable-valor
  
por defecto la variable en sas es number, como la `a ` si fuera texto hay q especificarlo como en la variable `b`

```sql
data tabla_nueva;
input a b: $10. ;
datalines;
1 casa
2 casa2
;
run;
```

si quisieramos añadir una observación más (row) hay q crear una tabla idéntica y hacer un a`append`


```sql
data tmp;
input a b: $10. ;
DATALINES;
3 casa3
;
run;

proc append base=tabla_nueva data=tmp;
run;
```

Una vez fusionado debemos borrar la tabla temporal

```sql
proc delete data=tmp;
run;
```

##### importarlas desde bbdd o ficheros
   
Usando las sentencias `INFILE` y `INPUT`

##### a partir de datasets ya existentes

Usando las sentencias `SET` y `MERGE`

La sintaxi más habitual en unu paso DATA es:

```
DATA <nombre nueva(s) tabla(s)>

SET/MERGE/INFILE <tablas a partir de las q crear la nueva>

<otras órdenas para el programa>

run;
```

###### usando paso  `DATA / SET`


###### 
Para crear una tabla de salida a partir de otras tablas de entrada. 

```
data vars_cocodrilo;
animal="cocodrilo";
color="verde";
edad=3;
fecha="03aug2013";
run;

data vars_elefante;
animal="elefante";
color="gris";
edad=5;
fecha="25mar2011";
run;

data vars_caracol;
animal="caracol";
color="marron";
edad=8;
fecha="30jul2014";
run;

* paso data para crear una tabla a partir de las 3 anteriores;

data animales;

SET vars_cocodrilo vars_elefante vars_caracol;
run;
```
###### Podemos crear dos tablas de salida con SET

```
data animales copia_animales;

SET vars_cocodrilo vars_elefante vars_caracol;
run;
```

Lo primero que hace el paso DATA en el caso q usemos la sentencia SET es leer las tablas de entrada. Si hay más de una primero coge la primera y ejecuta el resto de líneas de nuestro programa. Según el siguiente ejemplo

```
data copia1 copia2 copia3;
SET animales;
if substr(animal,1,1)='c' then output copia1;
if substr(animal,1,1)='e' then output copia2;
run;
```
Lo primero lee la tabla de entrada (SET), `animales` lee la primera observación y para esa fila ejecuta todas las instrucciones del programa en nuestro caso ejecuta los distintos `ifs`. Cuando termina lleva esa obervación a la tabla de salida (data) que corresponda y acontinuación pasa a leer la siguiente obervación de la entrada. Y así cn todas las observaciones de las entradas del programa.

Así vemos cómo sas procesa los datos horizontalmente, por observación. Por ejemplo tenemos la siguinete tabla:

###### Sumatorios horizontales - SUM()

```
* Sumas horizontales;

data SampleData;    

input name $ classDato $ dato1 dato2 dato3;

datalines; 

registo1 tipo1 10 10 -10
registo2 tipo2 20  . -20 
registo3 tipo1 30 30 -30 
registo4 tipo2 40 40 -40 
registo5 tipo1 50 50 -50 
registo6 tipo1 60 60 -60 
;
run; 

```

podemos añadir una nueva columna(variable) con el sumatorio de los campos anteriores (dato1,dato2,dato3) utilizando distintas aproximaciones para la suma

```
data sum_rows;
SET SampleData;
AddVar = dato1+dato2+dato3;
SumVar = sum(dato1, dato2,dato3);
SumList= sum(of dato1-dato3); * para sumar como una lista los campos tienen q tener el mismo prefijo del campo dato1 al dato3;
SumList_null= sum(0,of dato1-dato3); * si se encuentra con un valor null para ponerle un 0;
SumList_abs= sumabs(of dato1-dato3); * suma los valores absolutos(así si hay valores neg pasa a +) e ignora los nulls;
run;

	Name		ClassDato	datos1	datos2  datos3  AddVar 	SumVar 	 SumList 	SumList_null   SumListAbs
1	registo1	tipo1		10		10		-10			10		10		10			10				30
5	registo2	tipo2		20		.		-10			.		10		10			10				30
2	registo3	tipo1		30		30		-10			50		50		50			50				70
6	registo4	tipo2		40		40		-10			70		70		70			70				90
3	registo5	tipo1		50		50		-10			90		90		90			90				110
4	registo6	tipo1		60		60		-10			110		110		110			110				130
```
Si hicieramos `sum(0,of dato2)` en el registro2 en lugar de poner `.` pondría un `0`.

###### Sumatorios columnas - proc print

para sumar columnas podemos usar el proc print pero esto solo permite hacer el output en el ouput window, es decir no nos crea una nueva tabla;
```

proc print data=sum_rows;
sum dato1 dato2 dato3;
run;


Obs	name	classDato	dato1	dato2	dato3	AddVar	SumVar	SumList	SumList_null	SumList_abs
1	registo1	tipo1	10		10		-10		10			10		10			10			30
2	registo2	tipo2	20		.		-10		.			10		10			10			30
3	registo3	tipo1	30		30		-10		50			50		50			50			70
4	registo4	tipo2	40		40		-10		70			70		70			70			90
5	registo5	tipo1	50		50		-10		90			90		90			90			110
6	registo6	tipo1	60		60		-10		110			110		110			110			130
 	 	 				
						210		190		-60	 	 	 	 	 

```

podemos ordenar los registros por variable;
```
proc sort data=sum_rows;
by classDato;
run;

```
una vez ordenado podemos hacer un sum con proc print pero solo xa visualizarlo (no podemos crear un output a otra tabla);
muy importante previamente debe estar ordenado por el campo q haremos el groupby

```
proc print data=sum_rows;
Title 'Primer report';
Title2 'esto es un subtitle';
by classDato;
sum dato1 dato2 dato3;
run;


								Primer report

							esto es un subtitle

								classDato=tipo1

Obs	name	dato1	dato2	dato3	AddVar	SumVar	SumList	SumList_null	SumList_abs
1	registo1	10	10		-10			10		10		10		10			30
2	registo3	30	30		-10			50		50		50		50			70
3	registo5	50	50		-10			90		90		90		90			110
4	registo6	60	60		-10			110		110		110		110			130

classDato	 	150	150		-40	 

								classDato=tipo2

Obs	name	dato1	dato2	dato3	AddVar	SumVar	SumList	SumList_null	SumList_abs
5	registo2	20		.		-10		.		10		10		10			30
6	registo4	40		40		-10		70		70		70		70			90
classDato	 	60		40		-20	 	 	 	 	 
 	 			
				210		190		-60	 	 	 	 	 



```
###### Sumatorios columnas - proc sql

podemos usar un PROC SQL para el mismo fin (sumar horizontalmente), con SQL por defecto el output es window pero podemos añadir la sentencia "create table as" para crear un nuevo datset;

```
proc sql;
create table sum_rows_sql as 
select dato1 , dato2, dato3,
sum(dato1,dato2,dato3) as totalSum
from sum_rows;
quit;


	datos1	datos2  datos3 TotalSum
1	10		10		-10		10
2	30		30		-10		50
3	50		50		-10		90
4	60		60		-10		110
5	20		.		-10		10
6	40		40		-10		70


```

y finalmente podemos hacer un sumatorio de las columnas

```
proc sql;
create table sum_columns_sql as 
select sum(dato1) as totalDato1, sum(dato2) as totalDato2, sum(dato3) as totalDato3
from sum_rows;
quit;

	datos1	   datos2  	   datos3
1	210			190			-60


```

###### Sumatorios columnas - proc means

```
*utilizando proc means;

proc means data=sum_rows sum;
var dato1 dato2 dato3;
run;

```
Podemos agrupar por una variable usando `Class`

```
proc means data=sum_rows sum;
Class classDato;
var dato1 dato2 dato3;
run;
```
Por defecto el output se hace contra output window pero podemos evitar este comportamiento y guardarlo en un dataset nuevo

```
proc means data=sum_rows NOPRINT;
var dato1 dato2 dato3;
Output out =nuevo_dataset sum(dato1)=sumDato1 sum(dato2)=SumDato2 sum(dato3)=/autoname;
run;

	_Type_ _Freq_ sumDato1	   sumDato2  	   dato3_Sum
1		0    0			210		190	     		-60

```
podemos eliminar las columnas type y freq q vienen por defecto con DROP
```
proc means data=sum_rows NOPRINT;
var dato1 dato2 dato3;
Output out =nuevo_dataset (drop = _type_ _freq_) sum(dato1)=sumDato1 sum(dato2)=sum(dato3)=/autoname;
run;


	sumDato1	   dato2_Sum  	   dato3_Sum
1	210					190			-60
```



#### Procedimientos

Nos permite trabajar con los datasets o tablas. Hemos utilizado el proc print para mostrar los datos pero hay muchos otros procedimientos como ordenar datos, mostrar estadísticas,crear informes,...

#### Sintaxis de un programa SAS

Todas las líneas terminan en ';' incluso los comentarios para indicar q esa línea ha finalizado.
Al final de la instrucción para q se ejecute debemos terminar con un `run;` 

Si procedemos a escribir un paso data la línea debe empezar x la keyword `data` y si nos disponemos a escribir un procedimientos debe empezar cn la keyword `proc`.




# Variables

Las variables son las propiedades de una población que se analizan en estadística y pueden adquirir distintos valores (datos que permiten observar cómo una característica cambia de un elemento a otro).

Se estudia la relación que hay entre dos o más variables para establecer cómo un evento influencia a otro. Para ello, se mide cómo los valores de la variable dependiente se modifican según los cambios que se producen en los valores de la variable independiente.

En este tipo de estudios, es necesario tener en cuenta que una variable que es considerada dependiente en un análisis puede ser independiente en otro y viceversa, porque depende de cómo el investigador establece la relación de causalidad entre dos eventos.

| Variables dependientes                                 | Variables independientes                                            |
|--------------------------------------------------------|---------------------------------------------------------------------|
| Son el centro de la investigación                      | Se estudian por la influencia que tienen en la variable dependiente |
| Se modifican en relación con la variable independiente | Se modifican por la manipulación del investigador                   |
| Son los efectos de un fenómeno                         | Son las causas de un fenómeno                                       |
| Se representan con una Y                               | Se representan con una X                                            |


Por ejemplo: En un estudio sobre la repercusión que el suelo tiene en los cultivos de flores, el tipo de suelo es la variable independiente y el crecimiento de las flores es la variable dependiente, ya que el suelo, al proporcionar nutrientes, incide en el desarrollo de las flores.


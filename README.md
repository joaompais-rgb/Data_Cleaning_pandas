# Data_Cleaning_pandas
Data_Cleaning_pandas I shark_attacks
 

**Dónde abrir, dónde no y cuándo reforzar la vigilancia, según 75 años de ataques de tiburón registrados**

Proyecto de limpieza y análisis de datos con pandas · Ironhack Data Analytics · 2026
João Miguel Pais y Juan Carlos Del Mar Lostanau

---

## Objetivo

Limpiar el *Global Shark Attack File* y usarlo para responder a una pregunta de negocio: dónde debería abrir (y dónde no) una escuela de surf que quiere vender seguridad, en qué épocas y horas conviene reforzar la vigilancia y para qué público.

El proyecto tiene dos partes con el mismo peso: dejar una tabla limpia y reproducible, y sacar de ella recomendaciones que se puedan defender con datos, diciendo también hasta dónde llegan esos datos.

## Contexto

El cliente imaginario es una cadena de escuelas de surf que quiere abrir un nuevo centro y hacer de la seguridad su argumento comercial. Necesita saber:

- en qué países, estados y playas se concentran los ataques a surfistas;
- qué destinos de surf conocidos no registran ataques;
- en qué estación y a qué hora ocurren los ataques registrados;
- qué perfil de edad tienen las víctimas, porque decide si abre grupos infantiles.

## Dataset

| | |
|---|---|
| Fuente | Global Shark Attack File, [sharkattackfile.net](https://www.sharkattackfile.net/incidentlog.htm) |
| Archivo | `GSAF5.xls`, descargado directamente por el notebook |
| Tamaño original | 7.125 filas × 23 columnas |
| Unidad | una fila = un incidente registrado |
| Periodo analizado | 1951-2025, en tres bloques de 25 años |
| Descarga | septiembre de 2026 (el archivo se actualiza cada semana) |

Columnas con las que trabajamos: `Date`, `Year`, `Type`, `Country`, `State`, `Location`, `Activity`, `Sex`, `Age`, `Injury`, `Fatal Y/N` y `Time`.


## Preguntas clave


1. **¿Dónde sí abrir?** De una lista cerrada de 22 destinos de surf, cuáles no registran ataques.
2. **¿Cuándo reforzar la vigilancia?** Estación del año, con el hemisferio corregido, y franja horaria.
3. **¿Para qué público?** Perfil de edad de los surfistas atacados, por estado.
4. **¿El surf es más peligroso que otras actividades?** Gravedad de los ataques dentro de cada actividad.
5. **¿Ha cambiado con el tiempo?** Mortalidad por bloque de 25 años.

## Proceso

**1. Exploración.** `shape`, `columns`, `info()`, `nunique()`, `head()` y `tail()`. Los vacíos se cuentan de tres formas distintas (`len - count`, `isna().sum()` y la primera ordenada) como comprobación cruzada.

**2. Selección y nombres.** De 23 columnas a 12, trabajando siempre sobre una copia (`.copy()`) para no tocar el original. Nombres en minúsculas y sin espacios; `Date` pasa a `month` y `Fatal Y/N` a `fatality`.

**3. Duplicados.** Se buscan después de quitar las columnas técnicas, se revisan uno a uno con `duplicated(keep=False)` y se eliminan los 8.

**4. Limpieza columna a columna.**

**5. El periodo.** `ANIO_MAX = 2025`, el último año completo, fijado a mano para que el notebook sea reproducible aunque la web actualice el archivo. `ANIO_MIN = ANIO_MAX - 74 = 1951`, para tener tres bloques exactos de 25 años.

**6. Análisis.** Todo el análisis de recomendación usa el bloque más reciente, 2001-2025, y solo ataques no provocados:
- `surf_mundo`: ataques a surfistas en todo el mundo, para buscar destinos;
- `df_top10`: todas las actividades en los 10 estados con más ataques a surfistas, para comparar el surf con el resto;
- `df_surf`: solo surfistas en esos estados, para cuándo y para quién.


**7. Verificación.** Los mortales se cuentan por dos caminos independientes, la columna `fatality` y el texto de `injury`, y se comparan antes de presentar ninguna tasa de mortalidad.

## Resultados

- **El embudo.** De 7.125 registros quedan 5.199 limpios entre 1951 y 2025. De ellos, 2.714 caen en el bloque 2001-2025, y 967 son ataques no provocados a surfistas: la base de la recomendación.
- **Lo que sí cambia con el tiempo.** La mortalidad de los ataques registrados baja del 25,2% en 1951-1975 al 16,2% en 1976-2000 y al 9,8% en 2001-2025: menos de la mitad. El número de ataques sube (1.262, 1.223 y 2.714), pero eso mide sobre todo que hoy se registra mejor, y por eso no lo comparamos.
- **Dónde sí abrir.** 10 de los 22 destinos no registran ningún ataque a surfistas entre 2001 y 2025. Ocho siguen a cero en todo el periodo, de 1951 a 2025: Portugal, Francia, Marruecos, Nicaragua, Perú, Chile, Sri Lanka y Maldivas.
- **Cuándo.** No hay una temporada universal. Florida concentra el 38,1% de sus ataques en otoño y California el 45,7%; las dos Carolinas, en verano (81,8% y 72,7%, sobre 22 casos cada una); Hawái y Australia Occidental los reparten por todo el año. Por hora, de 667 surfistas con hora registrada, el 51,1% de los ataques ocurren por la tarde (12 a 17 h) y el 42,4% por la mañana.
- **Surf frente a otras actividades.** En los diez estados con más casos, el 5,2% de los ataques a surfistas son mortales, frente al 6,3% en natación, el 6,8% en pesca y el 16,0% en buceo. Y el 19,7% acaban sin herida, frente al 0,6% en natación.
- **Público.** Las víctimas más jóvenes están en las dos Carolinas y en Florida: entre 18,8 y 26,2 años de media en los hombres, con víctimas de 6 años. En Hawái, Nueva Gales del Sur, Australia Meridional, California y Australia Occidental la media pasa de 30.

## Recomendaciones

- **Abrir** en un destino con cero ataques registrados en todo el periodo y escuelas ya operando: Portugal, Francia o Marruecos.
- **Vigilar según la estación de cada destino**, con un calendario de riesgo por estado en vez de un cierre anual: otoño en Florida y California, verano en las Carolinas.
- **Priorizar las clases de mañana.** La tarde reúne la mitad de los ataques registrados con hora; la diferencia con la mañana (42%) es pequeña, así que es una medida de coste cero, no una garantía.
- **Medir**: contar alumnos en el agua por franja durante una temporada, que es el dato que falta para pasar de volumen de ataques a riesgo por persona.

## Limitaciones

- **Contamos ataques, no surfistas.** Sin saber cuánta gente había en el agua no se puede calcular riesgo por persona: un sitio con muchos ataques puede ser simplemente un sitio con mucha gente surfeando.
- **El registro no es uniforme.** Los países con más medios documentan más, y hoy se documenta mejor que hace setenta años. Por eso comparamos porcentajes entre bloques y no recuentos.
- **Un cero es ausencia de registro, no ausencia de riesgo.** Es la mejor noticia para el cliente y también la afirmación más frágil.
- **Variables construidas por nosotros.** Actividad, gravedad, estación y franja salen de texto libre con palabras clave, y alguna fila quedará mal clasificada. La lista de países del hemisferio sur y la de 22 destinos son decisiones del equipo.
- **Datos incompletos.** La hora falta en la mitad de los registros y la edad en cuatro de cada diez. Las conclusiones sobre hora y edad se apoyan solo en los casos que sí la tienen, y se presentan siempre con su número de casos.

## Próximos pasos

- Cruzar con datos de turismo o de licencias de surf para pasar de volumen de ataques a riesgo por persona.
- Bajar el análisis a la playa como unidad, con un mapa interactivo por localidad.
- Comprobar si la caída de la mortalidad se explica por el tiempo de rescate y la atención médica.
- Pasar las funciones de limpieza a un módulo `.py` con una función `clean_data()`, para aplicarlas a futuras versiones del archivo.

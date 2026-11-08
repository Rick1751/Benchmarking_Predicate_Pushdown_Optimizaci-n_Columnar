# Práctica de Laboratorio 6: Benchmarking, Predicate Pushdown y Optimización Columnar

## Objetivo de la Práctica
Manipular y extender el script de exploración en Google Colab para evaluar de forma empírica el impacto del cuello de botella de I/O y el filtrado avanzado en disco (Predicate Pushdown) entre formatos basados en filas y formatos columnares masivos.

---

## Características del entorno de ejecución

La práctica se ejecutó en Google Colab utilizando un backend de **Python 3
sobre Google Compute Engine**. 
Model name:                              Intel(R) Xeon(R) CPU @ 2.20GHz


* **Memoria RAM del sistema:** 12.7 GB.
* **Almacenamiento de disco:** 107.7 GB. de 108 GB
* **Uso observado de todos los recursos durante la consulta:** aproximadamente 3.4 GB de RAM y
  1.0 GB de disco.  
**No incluye ninguna tarjeta grafica es pura CPU**


---

## Retos Desarrollados

### Reto 1: Test de Estrés de Memoria
* **Descripción:** Se modificó la celda de generación de datos para elevar el volumen a **15,000,000 de registros (15 millones)**.
* **Análisis de Impacto:** Se documentó y comparó analíticamente el comportamiento de la memoria RAM y los tiempos de ejecución en el entorno de Google Colab al procesar este volumen de datos bajo dos enfoques:
  * **Enfoque Tradicional (CSV):** Evaluación del consumo de recursos y cuellos de botella por lectura secuencial.
  * **Enfoque Columnar (Parquet):** Evaluación de la eficiencia en la carga selectiva y compresión nativa.

### Reto 2: Consulta Analítica Avanzada (Predicate Pushdown)
* **Descripción:** Extensión del código de filtrado para resolver una consulta de negocio de alta complejidad bajo múltiples filtros a nivel de disco.
* **Consulta Evaluada:** Extracción del monto de transacciones bajo los siguientes criterios:
  * Categoría: `"Juguetes"`
  * Estado: `"Completado"`
* **Métricas Analizadas:** Rendimiento temporal (tiempo de ejecución) y uso/carga de columnas en memoria.

---

## Resumen de Resultados y Benchmarking

A continuación se presenta un resumen de las métricas obtenidas durante el laboratorio al procesar los 15 millones de registros. Los valores corresponden a las ejecuciones realizadas en Google Colab:

| Métrica / Formato | Enfoque Tradicional (CSV) | Enfoque Columnar (Parquet) | % de Mejora / Impacto |
| :--- | :--- | :--- | :--- |
| **Tamaño en disco** | 740.53 MB | 225.39 MB | 69.56\% menos espacio |
| **Tiempo de escritura** | 79.32 s | 7.51 s | 10.56 veces más rápido |
| **Tiempo de lectura completa** | 11.48 s | 3.18 s | 3.61 veces más rápido |
| **Consulta selectiva de `monto`** | 8.1532 s | 0.2231 s | 36.55 veces más rápido |
| **Tiempo de consulta del Reto 2** | 14.6732 s | 1.3975 s | 90.48\% menos tiempo |
| **Memoria del resultado del Reto 2** | 11.4577 MB | 5.7290 MB | 49.99\% menos memoria |

---

## Conclusiones Principales

1. **Eficiencia en I/O:** Parquet redujo el tamaño del archivo de 740.53 MB a 225.39 MB y fue 10.56 veces más rápido durante la escritura. En la lectura completa también obtuvo una mejora de 3.61 veces frente a CSV.
2. **Ventaja del almacenamiento columnar:** Al solicitar únicamente la columna `monto`, Parquet fue 36.55 veces más rápido que CSV. Esto demuestra el beneficio del *column pruning*, porque evita leer columnas que no participan en la consulta.
3. **Predicate Pushdown:** En el Reto 2 se aplicaron simultáneamente los filtros `categoria == "Juguetes"` y `estado == "Completado"`, extrayendo únicamente `monto`. Ambos formatos devolvieron 750,894 filas, pero Parquet ejecutó la consulta en 1.3975 segundos frente a 14.6732 segundos de CSV.
4. **Uso de memoria y escalabilidad:** El resultado de la consulta ocupó 5.7290 MB en Parquet y 11.4577 MB en CSV. Por ello, Parquet redujo la memoria del resultado en 49.99\% y el tiempo de ejecución en 90.48\%, favoreciendo el procesamiento de grandes volúmenes de datos.

---

## Estructura del Repositorio

* `README.md`: Este archivo con la documentación del laboratorio.
* `resolucion.ipynb`: Notebook de Google Colab con el código extendido, las optimizaciones y analisis de rendimiento

## Autor
* **Estudiante:** Ricardo León 
* **Asignatura:** Big Data
* **Fecha:** 07 de Noviembre 2026

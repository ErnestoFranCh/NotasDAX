# ***DAX QUERY VIEW***
Es una sección dentro de ``Power BI Desktop`` que te permite escribir consultas ``DAX`` completas para:

- Explorar datos tabulares.

- Crear vistas de resultados (filtrados, transformados, resumidos).

- Depurar cálculos o validar expresiones.

- Exportar resultados para análisis.

(similar a cómo usarías ``SQL`` en una base de datos)

***No se usa para crear medidas ni columnas calculadas. Se usa para consultar los datos.***

## `EVALUATE`
Una consulta ``DAX`` típicamente comienza con la función ``EVALUATE`` y puede incluir funciones de tabla, definiciones de variables, ordenamientos y más.  

``EVALUATE`` Es la instrucción obligatoria que inicia cualquier consulta ``DAX``. Le dice a Power BI:

*“Devuélveme una tabla con este resultado”.*

```dax
EVALUATE
<tabla_dax>

[ORDER BY ...]
[START AT ...]
```
**Consulta básica**
```dax
EVALUATE
Sales
```
Devuelve la tabla `Sales`

**Seleccionar columnas especificas**
```dax
EVALUATE
SELECTCOLUMNS(
    Sales,
    "Cliente", Sales[CustomerName],
    "Total", Sales[TotalAmount]
)
```
**Agregar columnas con `ADDCOLUMNS()`**
```dax
EVALUATE
ADDCOLUMNS(
    Sales,
    "IVA", Sales[TotalAmount] * 0.16
)
```
**Agrupar datos con `SUMMARIZE()`**

```dax
EVALUATE
SUMMARIZE(
    Sales,
    Sales[CustomerName],
    "TotalVentas", SUM(Sales[TotalAmount])
)
```
**Filtrar datos con `FILTER`**
```dax
EVALUATE
FILTER(
    Sales,
    Sales[TotalAmount] > 1000
)
```
**Mostrar una unica fila y columna `{ }`, `ROW()`**

```dax
EVALUATE { NOW() }
```
Convierte el valor escalar ``NOW()`` en una tabla de una sola fila y columna.
Alternativa:
```dax
EVALUATE ROW("Total", SUM(Sales[Amount]))
```
Muestra la suma total convirtiendola en una tabla de una sola fila y columna.


**Utilidad**
- Probar expresiones DAX sin afectar tu modelo.

- Analizar resultados paso a paso.

- Explorar relaciones o combinaciones entre tablas.

- Extraer muestras de datos fácilmente.

- Optimizar o depurar medidas (antes de formalizarlas).

- Exportar resultados a Excel u otros formatos.




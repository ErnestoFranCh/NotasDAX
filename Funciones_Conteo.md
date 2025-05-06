# 4. Funciones de Conteo
- [4. Funciones de Conteo](#4-funciones-de-conteo)
  - [4.1 `COUNT()`, `COUNTA()`, `COUNTAX()`](#41-count-counta-countax)
    - [`COUNT()`](#count)
    - [`COUNTA()`](#counta)
    - [`COUNTX()`](#countx)
  - [4.2 - `COUNTROWS()`](#42---countrows)
  - [4.3 `DISTINCTCOUNT()`](#43-distinctcount)

## 4.1 `COUNT()`, `COUNTA()`, `COUNTAX()`
### `COUNT()`
````dax
COUNT(columna)
````
- Cuenta solo los valores numéricos, fechas (en formato ``date`` o `datetime`) no nulos en la columna especificada.

- Ignora texto, errores, valores en blanco y columnas calculadas no numéricas.

| Edad |
| ---- |
| 25   |
| 32   |
| -    |
| abc  |
| 45   |
````dax
COUNT(Personas[Edad])  // Resultado: 3
````
### `COUNTA()`
````dax
COUNTA(columna)
````
Cuenta todos los valores no vacíos, sin importar si son texto, número, fecha, etc.
Solo ignora ``BLANK()``.
```dax
COUNTA(Personas[Edad])  // Resultado: 4
```
### `COUNTX()`
````dax
COUNTAX(tabla, expresión)
````
- Recorre una tabla y evalúa una expresión por fila.
- Cuenta cuántos resultados no son BLANK().

````dax
COUNTAX(FILTER(Ventas, Ventas[Monto] > 1000), Ventas[Cliente])
````
Cuenta cuántos clientes hay en ventas mayores a 1000.
## 4.2 - `COUNTROWS()`
Cuenta el número de filas en una tabla o una tabla filtrada.
````dax
COUNTROWS(tabla)
````
``tabla``: puede ser una tabla física o una tabla virtual (como resultado de una función ``FILTER``, ``VALUES``, ``SUMMARIZE``, etc.)
````dax
TotalVentas = COUNTROWS(Ventas)
````
Cuenta cuántas filas hay en la tabla Ventas.
````dax
VentasAltas = COUNTROWS(
    FILTER(Ventas, Ventas[Monto] > 1000)
)
````
## 4.3 `DISTINCTCOUNT()`
Cuenta cuántos valores distintos (únicos) hay en una columna, excluyendo los valores en blanco (``BLANK()``).
````dax
DISTINCTCOUNT(columna)
````
Supon que tienes la siguiente columna de ``Ventas[ClienteID]`
| ClienteID |
| --------- |
| 101       |
| 102       |
| 101       |
| 103       |
| (BLANK)   |
````dax
ClientesUnicos = DISTINCTCOUNT(Ventas[ClienteID])
````
Cuenta solo los IDs únicos: 101, 102, 103 (el ``BLANK`` se excluye).

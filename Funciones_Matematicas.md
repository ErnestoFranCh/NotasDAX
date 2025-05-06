# 3. Funciones Matemáticas y Aritméticas
- [3. Funciones Matemáticas y Aritméticas](#3-funciones-matemáticas-y-aritméticas)
  - [3.1 `SUM()`, `SUMX()`](#31-sum-sumx)
    - [`SUM()`](#sum)
    - [`SUMX()`.](#sumx)
  - [3.2 `AVERAGE()`, `AVERAGEX()`](#32-average-averagex)
    - [`AVERAGE()`](#average)
    - [`AVERAGEX()`](#averagex)
  - [3.3 `MIN()`, `MAX()`](#33-min-max)
    - [`MIN()`](#min)
    - [``MAX()``](#max)
  - [3.4 `MINX()`,`MAXX()`](#34-minxmaxx)
  - [3.5 `ROUND()`, `ROUNDUP()`, `ROUNDDOWN()`](#35-round-roundup-rounddown)
    - [`ROUND()`](#round)
    - [`ROUNDUP()`](#roundup)
    - [`ROUNDDOWN()`](#rounddown)
  - [3.6 ``INT() ``,`MOD()`](#36-int-mod)
    - [`INT()`](#int)
    - [`MOD()`](#mod)
  - [3.7 `DIVIDE()`](#37-divide)

Para realizar operaciones numéricas.

## 3.1 `SUM()`, `SUMX()`
### `SUM()`
Suma directa de una columna.
````dax
SUM(tabla[columna])
````
Suma todos los valores de una columna, respetando los filtros activos del modelo (segmentaciones, relaciones, etc.).
```dax
TotalVentas = SUM(Ventas[Monto])
```
Esto suma el total de la columna Monto de la tabla Ventas.

### `SUMX()`.
Suma iterativa con expresión personalizada.
````dax
SUMX(tabla, expresión)
`````
- ``expresión``: cálculo a realizar fila por fila
Itera por cada fila de la tabla, evalúa la expresión, y suma los resultados.

```dax
TotalVentas = SUMX(Ventas, Ventas[Precio] * Ventas[Cantidad])
````

Aquí no existe una columna que tenga directamente el total de cada venta. ``SUMX()`` lo calcula y suma todo.

Suma de un calculo con condiciones.
````dax
SumaSoloGrandes = SUMX(FILTER(Ventas, Ventas[Cantidad] > 10), Ventas[Cantidad] * Ventas[Precio])
````
## 3.2 `AVERAGE()`, `AVERAGEX()`
### `AVERAGE()`
Promedio directo de una columna.
Calcula el promedio de los valores numéricos de una columna, respetando el contexto de filtros.
````dax
AVERAGE(tabla[columna])
````
- Solo acepta una columna numérica como argumento.
````dax
PromedioVentas = AVERAGE(Ventas[Monto])
````
### `AVERAGEX()`
Promedio de una expresión personalizada.
Itera por cada fila de la tabla, evalúa la expresión personalizada y devuelve el promedio de los resultados.
````dax
AVERAGEX(tabla, expresión)
````
Promedio fila a fila:
````dax
PromedioVentaPorProducto = AVERAGEX(Ventas, Ventas[Precio] * Ventas[Cantidad])
````
Promedio segun un filtro.
````dax
PromedioGrandesVentas = AVERAGEX(FILTER(Ventas, Ventas[Cantidad] > 10), Ventas[Precio] * Ventas[Cantidad])
````
## 3.3 `MIN()`, `MAX()`
### `MIN()`
Valor mínimo de una columna.
````dax
MIN(tabla[columna])
````
La columna puede ser numérica, de fecha o texto (orden alfabético para texto).

````dax
FechaMásAntigua = MIN(Ventas[Fecha])
MenorPrecio = MIN(Productos[Precio])
````
En fechas devuelve la **más antigua**, y en números, el valor más pequeño.
### ``MAX()``  
Valor máximo de una columna.
````dax
MAX(tabla[columna])
````
````dax
FechaMásReciente = MAX(Ventas[Fecha])
MayorPrecio = MAX(Productos[Precio])
````
## 3.4 `MINX()`,`MAXX()`
Si necesitas calcular valores mínimos o máximos de una expresión, puedes usar:
````dax
MINX(tabla, expresión)
MAXX(tabla, expresión)
````
````dax
MenorTotalPorVenta = MINX(Ventas, Ventas[Precio] * Ventas[Cantidad])
````
Esto evalúa el total por fila y devuelve el más bajo.
## 3.5 `ROUND()`, `ROUNDUP()`, `ROUNDDOWN()`
### `ROUND()`
Redondeo estándar.
````dax
ROUND(número, número_de_decimales)
````
- ``número_de_decimales``: cantidad de decimales deseados (positivo para decimales, 0 para enteros, negativo para múltiplos de 10, 100, etc.).
    - Si es negativo estás indicando que quieres redondear no en los decimales, sino en las cifras enteras mayores.
    - 
Si el dígito siguiente ≥ 5 → redondea hacia arriba.
Si < 5 → redondea hacia abajo. (misma logica para los negativos).
````dax
ROUND(5.678, 2)        // 5.68
ROUND(5.674, 2)        // 5.67
ROUND(1234.56, -2)     // 1200
````
### `ROUNDUP()`
Redondeo hacia arriba (siempre).
````dax
ROUNDUP(número, número_de_decimales)
````
Redondea el número hacia arriba en todos los casos, sin importar el valor decimal, es decir, no sigue la regla de redondeo, siempre redondeara hacia arriba si hay decimal.
````dax
ROUNDUP(5.671, 2)      // 5.68
ROUNDUP(5.611, 0)      // 6
ROUNDUP(1234.56, -2)   // 1300
````
### `ROUNDDOWN()`
Redondeo hacia abajo (siempre).
````dax
ROUNDDOWN(número, número_de_decimales)
````
Redondea el número hacia abajo, truncando los valores decimales adicionales.
````dax
ROUNDDOWN(5.679, 2)    // 5.67
ROUNDDOWN(5.999, 0)    // 5
ROUNDDOWN(1234.56, -2) // 1200
````
## 3.6 ``INT() ``,`MOD()`
### `INT()`
Parte entera del número.
````dax
INT(número)
````
Devuelve la parte entera (entera negativa o positiva) de un número redondeando siempre hacia abajo, al entero menor más cercano.
````dax
INT(5.9)       // 5
INT(5.1)       // 5
INT(-5.1)      // -6 ← ¡Ojo! No devuelve -5 (redondeo hacia abajo)
INT(-5.9)      // -6
````
### `MOD()`
````dax
MOD(número, divisor)
````
- Devuelve el residuo de dividir número entre divisor.

- El resultado siempre tiene el mismo signo que el divisor.
````dax
MOD(10, 3)     // 1   → 10 ÷ 3 = 3 con residuo 1
MOD(7, 2)      // 1
MOD(15, 5)     // 0   → Exacto
MOD(-10, 3)    // 2   ← porque 3 × (-4) = -12 → residuo es -10 - (-12) = 2
MOD(10, -3)    // -2  ← signo del divisor
````
El redondeo en `DAX` es diferente a otros programas:
````dax
MOD(n, d) = n - d * INT(n / d)
````
En el caso de ``MOD(-10, 3)`` se tiene: 
- `-10/3 = -3.3333`
- `INT(-10/3)= -4` (redondeo hacia abajo)
- `-4*3 = -12`
- `10-12=-2`
## 3.7 `DIVIDE()`
División segura.
````dax
DIVIDE(numerador, denominador, [valor_alternativo])
````
- ``[valor_alternativo]``: (Opcional) Qué valor devolver si el denominador es cero o nulo. Si no se especifica, devuelve ``BLANK()``.

Ventaje frente a `/`:
A diferencia del operador ``/``, que devuelve un error si el denominador es cero, ``DIVIDE()``:

- Devuelve el resultado normal si se puede dividir.

- Devuelve el valor alternativo (o ``BLANK()``) si no se puede dividir.
````dax
DIVIDE(10, 2)       // Resultado: 5
DIVIDE(10, 0)       // Resultado: BLANK()
DIVIDE(10, 0, 0)    // Resultado: 0
````
Supongamos que tienes una tabla de ventas con columnas:
- ``Ventas[TotalVentas]``
- ``Ventas[Clientes]``

Y quieres calcular ventas promedio por cliente, pero puede haber filas con ``Clientes = 0``.

````dax
VentasPromedio = DIVIDE(Ventas[TotalVentas], Ventas[Clientes], 0)
````
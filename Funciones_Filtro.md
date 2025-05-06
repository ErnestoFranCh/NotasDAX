# 6. Funciones de Filtro y Evaluación de Contexto
Esenciales para cálculos avanzados y segmentación.
- [6. Funciones de Filtro y Evaluación de Contexto](#6-funciones-de-filtro-y-evaluación-de-contexto)
  - [6.1 `CALCULATE()`](#61-calculate)
  - [6.2 `FILTER()`](#62-filter)
  - [6.3 `ALL()`, `ALLEXCEPT()`, `ALLSELECTED()`](#63-all-allexcept-allselected)
    - [`ALL()`](#all)
    - [`ALLEXCEPT()`](#allexcept)
    - [`ALLSELECTED()`](#allselected)
  - [6.4 `VALUES()`](#64-values)
  - [6.5 `RELATED()`](#65-related)
  - [6.6 `RELATEDTABLE()`](#66-relatedtable)
  - [6.7 `REMOVEFILTERS()`](#67-removefilters)
  - [6.8 `KEEPFILTERS()`](#68-keepfilters)
  - [6.9 `USERELATIONSHIP()`](#69-userelationship)

## 6.1 `CALCULATE()`
Modifica el contexto de filtro de una expresión, permitiéndote aplicar nuevos filtros sobre un cálculo.
````dax
CALCULATE(<expresión>, <filtro1>, <filtro2>, ...)
````
- ``expresión``: una medida o cálculo (por ejemplo: ``SUM(...)``, ``AVERAGE(...)``, etc.). No está limitada solo a funciones de agregación. Puede ser cualquier expresión válida que devuelva un valor escalar (número, texto, fecha, etc.).

    La expresión puede ser:
    - Una función de agregación (``SUM``, ``AVERAGE``, ``COUNT``, etc.)
    - Una medida ya creada.
    - Una operación matemática (``[TotalVentas] * 0.1``)
    - Una función condicional (``IF(...)``, ``SWITCH(...)``, etc.)
    - Un cálculo simple (``Ventas[Precio] * Ventas[Cantidad]``)
    - Incluso una función de fecha (``TODAY()``, ``YEAR(TODAY())``, etc.)
- ``filtro(s)``: expresiones booleanas o funciones que cambian el contexto del cálculo (como ``FILTER()``, ``ALL()``, ``DATESYTD()``, etc.).

Suma del monto de ventas solo para productos de la categoría "Electrónica":
````dax
VentasElectrónica = 
CALCULATE(
    SUM(Ventas[Monto]),
    Ventas[Categoría] = "Electrónica"
)
-- CALCULATE() aplica un filtro temporal a Ventas[Categoría] antes de realizar la suma.
````

Mas de un filtro:
````dax
PromedioVentasVIP2024 = 
CALCULATE(
    AVERAGE(Ventas[Monto]),
    Ventas[Año] = 2024,
    Clientes[Segmento] = "VIP"
)
-- Cambia el contexto del promedio para que solo considere ventas de 2024 y clientes VIP.
````
Filtro usando `FILTER()`
````dax
VentasGrandes = 
CALCULATE(
    SUM(Ventas[Monto]),
    FILTER(Ventas, Ventas[Monto] > 1000)
)
-- Aquí usas FILTER() para crear una tabla temporal con solo las ventas grandes.
````
Con medidas existentes:
````dax
Ventas2023 = 
CALCULATE(
    [TotalVentas], 
    Ventas[Año] = 2023
)
````
## 6.2 `FILTER()`
Devuelve una tabla filtrada con las filas de una tabla original que cumplen una condición lógica.
Se usa frecuentemente en funciones como ``CALCULATE()``, ``SUMX()``, ``AVERAGEX()``, etc., ya que estas esperan como argumento una tabla filtrada.
````dax
FILTER(tabla, condición)
````
- ``tabla``: tabla a la que se le aplicará el filtro.
- ``condición``: expresión booleana (``TRUE``/``FALSE``) para filtrar filas.

````dax
VentasMayores1000 = 
CALCULATE(
    SUM(Ventas[Monto]),
    FILTER(Ventas, Ventas[Monto] > 1000)
)
````
Aquí ``FILTER()`` filtra la tabla Ventas y solo conserva las filas con ``monto > 1000``. Luego ``CALCULATE()`` cambia el contexto de la medida para sumar solo esas filas.
**TIPS**
- Se usa mejor dentro de funciones que esperan una tabla como argumento (ej. CALCULATE, SUMX, etc.).

- La condición puede incluir múltiples criterios usando ``&&`` y ``||``.
## 6.3 `ALL()`, `ALLEXCEPT()`, `ALLSELECTED()`
### `ALL()`
Quita todos los filtros de una columna o tabla. Se usa para calcular totales globales, ignorando segmentadores o filtros del reporte.
````dax
ALL(<tabla o columna>)
````
Ejemplo: Porcentaje del total.
```dax
% del Total Ventas = 
DIVIDE(
    SUM(Ventas[Monto]),
    CALCULATE(SUM(Ventas[Monto]), ALL(Ventas))
)
```
El denominador usa ``ALL(Ventas)`` para quitar todos los filtros y calcular el total global, permitiendo calcular el porcentaje.
### `ALLEXCEPT()`
Quita todos los filtros excepto los especificados (preserva filtros en ciertas columnas).
```dax
ALLEXCEPT(<tabla>, <columna1>, <columna2>, ...)
```
Promedio por categoría (ignorando segmentadores).
```dax
Promedio por Categoría = 
CALCULATE(
    AVERAGE(Ventas[Monto]),
    ALLEXCEPT(Ventas, Ventas[Categoría])
)
```
Calcula el promedio de ventas por categoría ignorando otros filtros como país, año, cliente, etc.
### `ALLSELECTED()`
Quita filtros excepto los seleccionados por el usuario en segmentadores o visuales.
Ideal para mantener contexto del usuario pero ignorar filtros de otras visualizaciones.
````dax
ALLSELECTED(<tabla o columna>)
````
Usa los filtros aplicados por el usuario en el reporte (por ejemplo, en segmentadores, visuales cruzadas).
````dax
PorcentajeFiltrado = 
DIVIDE(
    SUM(Ventas[Monto]),
    CALCULATE(SUM(Ventas[Monto]), ALLSELECTED(Ventas))
)
````
Calcula la participación dentro del conjunto seleccionado, útil para mostrar subtotales dinámicos.

**Estas funciones se usan en medidas, ya que modifican el contexto de evaluación que solo existe al momento de visualizar los datos.**
## 6.4 `VALUES()`
Devuelve una tabla de una sola columna con los valores únicos que tiene una columna en el contexto actual.
````dax
VALUES(<nombre_columna>)
````
Para tablas:
````dax
VALUES(<nombre_tabla>)
````
Ej con tabla `Ventas`
| Producto | Categoría | Monto |
| -------- | --------- | ----- |
| A        | X         | 100   |
| A        | X         | 100   |
| B        | Y         | 200   |

```dax
VALUES(Ventas)
-- Devuelve: 
```
| Producto | Categoría | Monto |
| -------- | --------- | ----- |
| A        | X         | 100   |
| B        | Y         | 200   |


Ejemplos: Cuantos productos unicos hay:
````dax
ProductosUnicos = 
COUNTROWS(VALUES(Ventas[Producto]))
````
## 6.5 `RELATED()`
Devuelve un valor de una columna relacionada en una tabla relacionada hacia “uno” (relación many-to-one), usando las relaciones del modelo de datos de ``Power BI``.
```dax
RELATED(<nombre_columna_relacionada>)
```
- Solo puede traer informacion desde la tabla “uno” hacia la tabla “muchos”.
- La informacion se queda en "muchos"
- No puedes usarla al revés (de “muchos” a “uno”).

**Ejemplo de uso (Columna calculada)**
Supongamos:
Tabla ``Ventas`` (muchos) tiene: ``ID_Producto``, ``Cantidad``
Tabla ``Productos`` (uno) tiene: ``ID_Producto``, ``Precio``

Y están relacionadas por ``ID_Producto``.
````dax
PrecioUnitario = RELATED(Productos[Precio])
````
Esto crea una nueva columna en ``Ventas`` trayendo el precio desde la tabla ``Productos``.
**Ejemplo de medida**
```dax
MarcaCantidad = 
RELATED(Productos[Marca]) & ": " & FORMAT(Ventas[Cantidad], "0")
-- SALIDA: Marca: Canitad
```
## 6.6 `RELATEDTABLE()`
 Devuelve una tabla relacionada con la fila actual, en una relación de uno a muchos.
Se usa típicamente en el lado "uno" de una relación para acceder a las filas relacionadas del lado "muchos".
 ```dax
 RELATEDTABLE(<NombreTablaRelacionada>)
 ```
 Ej: Imagina dos tablas:
 Tabla: `Productos`
 | ProductoID | Marca  |
| ---------- | ------ |
| 1          | Nike   |
| 2          | Adidas |
| 3          | Puma   |

Tabla:`Ventas`
| VentaID | ProductoID | Cantidad |
| ------- | ---------- | -------- |
| 101     | 1          | 10       |
| 102     | 1          | 5        |
| 103     | 2          | 7        |
| 104     | 3          | 2        |

Relación: ``Productos[ProductoID]`` **1:\*** ``Ventas[ProductoID]``
**Uso en columna calculada**
Digamos que quieres contar cuántas ventas tiene cada producto. En la tabla ``Productos``, puedes crear:

```dax
VentasPorProducto = 
COUNTROWS(RELATEDTABLE(Ventas))
```
Esto recorrerá la tabla ``Ventas`` buscando todas las filas relacionadas con cada ``ProductoID``.

**Uso en medida**
Supongamos que quieres la suma de cantidades por producto desde la tabla Productos, puedes hacer:
```dax
TotalCantidadPorProducto = 
SUMX(
    RELATEDTABLE(Ventas),
    Ventas[Cantidad]
)
```
Esto recorre las filas de Ventas relacionadas con el producto actual y suma ``Cantidad``.

**Comparacion:**

| Función          | Desde dónde se usa     | Devuelve          | Ejemplo típico                    |
| ---------------- | ---------------------- | ----------------- | --------------------------------- |
| `RELATED()`      | Desde el lado *muchos* | Valor escalar     | `RELATED(Productos[Marca])`       |
| `RELATEDTABLE()` | Desde el lado *uno*    | Tabla relacionada | `COUNTROWS(RELATEDTABLE(Ventas))` |
## 6.7 `REMOVEFILTERS()`
Quita los filtros aplicados en una o más columnas o tablas dentro de un contexto de evaluación (como un visual, una segmentación o una medida filtrada).
Es muy útil cuando quieres ignorar filtros aplicados por el usuario o por el contexto.

```dax
REMOVEFILTERS([<Tabla o Columna>])
```
- Puedes pasarle una columna, varias columnas o una tabla completa.
- Devuelve una tabla sin filtros, que normalmente se usa dentro de ``CALCULATE()``.
Supón que tienes una tabla Ventas con estas columnas:

``Fecha``

``Producto``

``Cantidad``

Y quieres calcular el total de ventas sin importar el producto filtrado en un gráfico.

```dax
TotalVentasSinFiltroProducto = 
CALCULATE(
    SUM(Ventas[Cantidad]),
    REMOVEFILTERS(Ventas[Producto])
)
```
Esto hace que, aunque el usuario seleccione un producto específico en un gráfico o segmentador, la **medida ignore ese filtro** y devuelva el total general.

````dax
TotalGlobal = 
CALCULATE(
    SUM(Ventas[Cantidad]),
    REMOVEFILTERS(Ventas)
)
````
Esto elimina **todos los filtros aplicados a cualquier columna** de la tabla Ventas, mostrando el total absoluto.
## 6.8 `KEEPFILTERS()`
La función ``KEEPFILTERS()`` se usa en el contexto de ``CALCULATE()`` para mantener los filtros que ya están aplicados en el contexto actual, pero agregando filtros adicionales sin afectar los filtros previos.

Es útil cuando quieres aplicar un filtro adicional, pero no quieres que este filtro reemplace los filtros previos (como podría pasar con ``REMOVEFILTERS()``)
```dax
KEEPFILTERS(<Expresión de filtro>)
```
- Se usa dentro de CALCULATE() para modificar el contexto de filtro.

- No elimina los filtros existentes; solo los mantiene y agrega los nuevos filtros.

**Ejemplo práctico en una medida**
Supón que tienes una tabla ``Ventas`` con las siguientes columnas:

``Fecha``

``Producto``

``Cantidad``

Si deseas calcular el total de ventas para productos específicos, pero asegurándote de que los filtros previos (por ejemplo, un filtro de fecha) se mantengan, usarías ``KEEPFILTERS()`` para agregar el filtro de producto sin afectar los filtros de fecha.

```dax
VentasConFiltroProducto = 
CALCULATE(
    SUM(Ventas[Cantidad]),
    KEEPFILTERS(Ventas[Producto] = "Nike")
)
```
## 6.9 `USERELATIONSHIP()`
La función ``USERELATIONSHIP()`` se usa para activar una relación inactiva entre dos tablas en un modelo de datos de ``Power BI``, permitiendo realizar cálculos usando esa relación específica.

En ``Power BI``, puedes tener múltiples relaciones entre dos tablas, pero solo una puede estar activa en cualquier momento.`` USERELATIONSHIP()`` te permite hacer uso de relaciones inactivas en ciertos cálculos, sin tener que cambiar el estado de las relaciones.

```dax
USERELATIONSHIP(<Columna1>, <Columna2>)
```
- ``<Columna1>``: La columna de la primera tabla.

- ``<Columna2>``: La columna de la segunda tabla.

Nota: Las dos columnas deben estar en tablas relacionadas, pero **la relación entre ellas debe estar inactiva** (es decir, no es la relación por defecto).

**Ejemplo práctico** 
Supón que tienes dos tablas:

``Ventas``:

- ``FechaVenta``

- ``FechaFactura``

- ``ProductoID``

- ``Monto``

``Fechas``:

- ``Fecha``

- ``Año``

- ``Mes``

Imagina que tienes dos relaciones entre ``Ventas`` y ``Fechas``:

Una relación activa entre ``Ventas[FechaVenta]`` y ``Fechas[Fecha]``.

Una relación inactiva entre ``Ventas[FechaFactura]`` y ``Fechas[Fecha]``.

Si deseas calcular las ventas por la fecha de la factura en lugar de la fecha de venta (usando la relación inactiva), puedes usar ``USERELATIONSHIP()``.

```dax
VentasPorFactura = 
CALCULATE(
    SUM(Ventas[Monto]),
    USERELATIONSHIP(Ventas[FechaFactura], Fechas[Fecha])
)
```


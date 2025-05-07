# 7. Funciones de Tabla
Crean o manipulan tablas, base para cálculos avanzados.
- [7. Funciones de Tabla](#7-funciones-de-tabla)
  - [7.1 `ADDCOLUMNS()`](#71-addcolumns)
  - [7.2 `SUMMARIZE()`](#72-summarize)
  - [7.3 `SELECTCOLUMNS()`](#73-selectcolumns)
  - [7.4 `CROSSJOIN()`](#74-crossjoin)
  - [7.5 `UNION()`, `EXCEPT()`, `INTERSECT()`](#75-union-except-intersect)
    - [`UNION()`](#union)
    - [`EXCEPT()`](#except)
    - [`INTERSECT()`](#intersect)
  - [7.6 `GENERATE()`, `GENERATEALL()`](#76-generate-generateall)
    - [`GENERATE()`](#generate)
    - [`GENERATEALL()`](#generateall)
  - [7.7 `DISTINCT()`](#77-distinct)

## 7.1 `ADDCOLUMNS()`

Es una función que te permite agregar columnas calculadas a una tabla o expresión de tabla existente.
No modifica la tabla original, sino que devuelve una nueva tabla con las columnas adicionales.
```dax
ADDCOLUMNS(<Tabla o Expresión de Tabla>, <NombreColumna1>, <Expresión1>,
                            ...        [<NombreColumna2>, <Expresión2>, ...])
```
- ``<Tabla o Expresión de Tabla>``: La tabla o expresión de tabla a la que deseas agregar las nuevas columnas.

- ``<NombreColumna1>``: El nombre de la columna que se agregará.

- ``<Expresión1>``: La expresión que calcula el valor de la nueva columna.

- Puedes agregar múltiples columnas adicionales, cada una con su propio nombre y expresión.

**Ejemplo**
Imagina que tienes una tabla de ``Ventas`` y otra de ``Productos`` y deseas agregar una nueva columna a la tabla de ``Ventas`` que te muestre el nombre del producto de cada venta.

``Ventas``:

- ``VentaID``

- ``ProductoID``

- ``Monto``

``Productos``:

- ``ProductoID``

- ``NombreProducto``

Queremos agregar el ``NombreProducto`` de la tabla ``Productos`` a la tabla ``Ventas`` usando ``ADDCOLUMNS()``.

```dax
VentasConProducto = 
ADDCOLUMNS(
    Ventas,
    "NombreProducto", RELATED(Productos[NombreProducto])
)
```
- ``ADDCOLUMNS()``: Crea una nueva tabla con las columnas originales de la tabla ``Ventas`` y agrega una columna adicional llamada ``NombreProducto``.

- ``RELATED(Productos[NombreProducto])``: Trae el valor del ``NombreProducto`` desde la tabla ``Productos``, basándose en la relación entre ``Ventas[ProductoID]`` y ``Productos[ProductoID]``.

**Resultado**
La nueva tabla ``VentasConProducto`` tendrá las mismas columnas que la tabla original ``Ventas``, pero también incluirá la columna ``NombreProducto``, la cual contiene el nombre del producto correspondiente a cada venta.
## 7.2 `SUMMARIZE()`
Permite agrupar los datos según una o más columnas y agregar nuevas columnas con cálculos agregados (como sumas, promedios, etc.).
```dax
SUMMARIZE(
    <Tabla>,
    <Columna1>, <Columna2>, ...,    // Columnas por las que deseas agrupar
    <NombreColumna1>, <Expresión1>, // Nombres de las nuevas columnas con agregaciones
    [<NombreColumna2>, <Expresión2>],
    ...
)
```
**Ejemplo**
magina que tienes la siguiente tabla de ``Ventas``:

``Ventas``:

- ``ProductoID``

- ``FechaVenta``

- ``Monto``

Y deseas crear una tabla resumen que te diga el total de ventas por producto.

```dax
ResumenVentasPorProducto = 
SUMMARIZE(
    Ventas,                            // Tabla a resumir
    Ventas[ProductoID],                // Columna para agrupar (ProductoID)
    "TotalVentas", SUM(Ventas[Monto])  // Nueva columna con la suma de Monto
)
```
- ``SUMMARIZE()``: Crea una nueva tabla que agrupa los datos de la tabla ``Ventas`` por ``ProductoID``.

- ``"TotalVentas"``, ``SUM(Ventas[Monto])``: La nueva columna ``TotalVentas`` calcula la suma de las ventas (monto) para cada ``ProductoID``.

**Resultado**
La tabla ``ResumenVentasPorProducto`` tendrá las siguientes columnas:

- ``ProductoID``: El ID de cada producto.

- ``TotalVentas``: La suma de las ventas (monto) por cada producto.

**Resumen por Producto y Año**
```dax
ResumenVentasPorProductoYAnio = 
SUMMARIZE(
    Ventas, 
    Ventas[ProductoID], 
    YEAR(Ventas[FechaVenta]),       // Agrupar por Año de la fecha de venta
    "TotalVentas", SUM(Ventas[Monto]), 
    "PromedioVentas", AVERAGE(Ventas[Monto])
)
```
Este ejemplo crea una tabla ``ResumenVentasPorProductoYAnio`` que tiene:

- ``ProductoID``: El ID del producto.

- ``YEAR(Ventas[FechaVenta])``: El año de la venta.

- ``TotalVentas``: La suma de las ventas por producto y año.

- ``PromedioVentas``: El promedio de las ventas por producto y año.
## 7.3 `SELECTCOLUMNS()`
Crea una nueva tabla a partir de una tabla existente, seleccionando solo las columnas que necesitas y, opcionalmente, renombrando esas columnas.
```dax
SELECTCOLUMNS(
    <Tabla>,          // La tabla de donde seleccionar las columnas
    <NombreColumna1>, <Expresión1>,   // El nombre de la nueva columna y la expresión
    <NombreColumna2>, <Expresión2>, 
    ...
)
```
Imagina que tienes una tabla llamada ``Ventas`` con las siguientes columnas:

``Ventas``:

- ``ProductoID``

- ``FechaVenta``

- ``Monto``

- ``Cantidad``

Ahora, supongamos que solo te interesa obtener las columnas ``ProductoID`` y ``Monto``, pero deseas renombrar la columna ``Monto`` como ``VentasTotales``.

```dax
VentasSeleccionadas = 
SELECTCOLUMNS(
    Ventas,                  // Tabla de donde seleccionar las columnas
    "IDProducto", Ventas[ProductoID], // Renombramos la columna ProductoID a IDProducto
    "VentasTotales", Ventas[Monto]   // Renombramos Monto a VentasTotales
)
```
**Resultado:**
La tabla ``VentasSeleccionadas`` tendrá las siguientes columnas:

- ``IDProducto``: El ID del producto.
- ``VentasTotales``: El monto total de ventas.

**Ejemplo**
Selección y creación de nuevas columnas
Supongamos que además de seleccionar columnas, deseas crear nuevas columnas basadas en cálculos. Por ejemplo, puedes calcular un 20% de descuento sobre la columna ``Monto``.

```dax
VentasConDescuento = 
SELECTCOLUMNS(
    Ventas, 
    "ProductoID", Ventas[ProductoID], 
    "VentasTotales", Ventas[Monto], 
    "Descuento20", Ventas[Monto] * 0.2  // Calculamos el descuento del 20%
)
```
## 7.4 `CROSSJOIN()`
Función que te permite realizar un producto cartesiano entre dos o más tablas, lo que significa que crea todas las combinaciones posibles de las filas entre esas tablas. En otras palabras, si tienes dos tablas con **N** y **M** filas respectivamente, el ``CROSSJOIN()`` devolverá una nueva tabla con **N * M** filas (todas las combinaciones posibles de las filas de ambas tablas).

Es útil cuando necesitas combinar cada fila de una tabla con todas las filas de otra tabla.

```dax
CROSSJOIN(
    <Tabla1>,
    <Tabla2>,
    ...
)
```

**Ejemplo**
Imagina que tienes las siguientes tablas:

``Productos``:

- ``ProductoID``

- ``ProductoNombre``
- 
| ProductoID | ProductoNombre |
| ---------- | -------------- |
| 1          | Producto A     |
| 2          | Producto B     |

``Colores``:

- ``Color``

| Color |
| ----- |
| Rojo  |
| Azul  |
| Verde |

Supongamos que deseas combinar cada producto con cada color para saber todas las combinaciones posibles de producto y color.

```dax
Combinaciones = 
CROSSJOIN(
    Productos,
    Colores
)
```
| ProductoID | ProductoNombre | Color |
| ---------- | -------------- | ----- |
| 1          | Producto A     | Rojo  |
| 1          | Producto A     | Azul  |
| 1          | Producto A     | Verde |
| 2          | Producto B     | Rojo  |
| 2          | Producto B     | Azul  |
| 2          | Producto B     | Verde |
## 7.5 `UNION()`, `EXCEPT()`, `INTERSECT()`
Estas funciones trabajan con tablas y devuelven una nueva tabla como resultado. Se utilizan para combinar, comparar o extraer diferencias entre conjuntos de datos.

### `UNION()`
Combina dos o más tablas uniendo sus filas (como ``UNION`` en ``SQL``). Las columnas deben coincidir en número y en tipo de datos.

```dax
UNION(Table1, Table2)
```
**Ejemplo**
Supón que tienes dos tablas:

- ``Ventas2023``
- ``Ventas2024``

Ambas con columnas Producto, Cantidad.
```dax
VentasTotales = 
UNION(Ventas2023, Ventas2024)
```
Esto devuelve una sola tabla con todas las filas de ``Ventas2023`` seguidas por todas las filas de ``Ventas2024``.

### `EXCEPT()`
Devuelve las filas de la primera tabla que no están presentes en la segunda. Es como una diferencia de conjuntos.
```dax
EXCEPT(Table1, Table2)
```
```dax
SoloVentas2023 = 
EXCEPT(Ventas2023, Ventas2024)
```
Devuelve solo las filas que están en ``Ventas2023`` pero no en ``Ventas2024``.

### `INTERSECT()`
Devuelve solo las filas que están presentes en ambas tablas.
```dax
INTERSECT(Table1, Table2)
```
```dax
VentasComunes = 
INTERSECT(Ventas2023, Ventas2024)
```
Devuelve solo las filas que están idénticas en ambas tablas.
## 7.6 `GENERATE()`, `GENERATEALL()`
Estas funciones devuelven una tabla resultante de la combinación de dos tablas. Son útiles cuando una tabla contiene filas que a su vez devuelven subtablas (por ejemplo, usando ``RELATEDTABLE`` o ``SELECTCOLUMNS``).

### `GENERATE()`
Toma cada fila de la primera tabla y la combina (tipo ``CROSSJOIN``) con todas las filas de la segunda tabla que están relacionadas con esa fila (o generadas dinámicamente).

```dax
GENERATE(Table1, Table2Expression)
```

Supón que tienes dos tablas:

``Clientes`` con columnas: ``ClienteID``, ``Nombre``

``Ventas`` con columnas: ``VentaID``, ``ClienteID``, ``Producto``

Y tienes una relación entre ambas (``Clientes[ClienteID]`` → ``Ventas[ClienteID]``)
Puedes crear una tabla con cada cliente y todas sus ventas así:
```dax
VentasPorCliente = 
GENERATE(
    Clientes,
    SELECTCOLUMNS(
        RELATEDTABLE(Ventas),
        "Producto", Ventas[Producto]
    )
)
```
**Resultado**: Para cada cliente, obtienes una fila por cada producto que compró.

### `GENERATEALL()`
Funciona igual que ``GENERATE()``, pero ignora relaciones activas. Combina cada fila de la primera tabla con todas las filas posibles de la segunda, como si no hubiera filtros.

```dax
GENERATEALL(Table1, Table2Expression)
```
```dax
TodasCombinaciones = 
GENERATEALL(
    Clientes,
    SELECTCOLUMNS(Ventas, "Producto", Ventas[Producto])
)
```
**Resultado**: Cada cliente aparece con todos los productos, haya comprado o no. Es decir, combina todos los clientes con todas las ventas sin importar relaciones.
## 7.7 `DISTINCT()`
Devuelve una tabla de una sola columna con todos los valores distintos (sin duplicados) de esa columna, sin considerar el contexto de filtro como lo hace ``VALUES()``.

```dax
DISTINCT(Columna)
```
Solo se puede usar con columnas, no con tablas completas.

```dax
ProductosGlobales = 
COUNTROWS(DISTINCT(Ventas[Producto]))
```
Esta medida ignorará el contexto actual (por ejemplo, filtros por región o por año), devolviendo el número de productos distintos de toda la tabla.

En contraste, con ``VALUES()`` el resultado sí depende del contexto del visual o del filtro aplicado.

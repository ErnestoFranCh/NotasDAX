8. Funciones de Relaciones y Modelado
Para acceder a columnas de otras tablas o relaciones.

## 8.1 `RELATEDTABLE()`
Devuelve una tabla que contiene todas las filas de una tabla relacionada que están vinculadas a la fila actual de la tabla donde se usa la función.
Se usa cuando estás en una tabla del lado uno de la relación y quieres obtener todas las filas relacionadas del lado muchos.

```dax
RELATEDTABLE(NombreTabla)
```
- Retorna una tabla con las filas relacionadas.

- Se puede usar dentro de funciones de tabla como ``COUNTROWS()``, ``SUMX()``, ``FILTER()``, etc.

- Solo se puede usar en contextos de filas (como columnas calculadas) o en iteradores.

Supón que tienes dos tablas:

``Productos``

| ProductoID | NombreProducto |
| ---------- | -------------- |
| 101        | Galletas       |
| 102        | Refresco       |

`Ventas`

| ID\_Venta | ProductoID | Cantidad |
| --------- | ---------- | -------- |
| 1         | 101        | 3        |
| 2         | 102        | 2        |
| 3         | 101        | 1        |

Y existe una relación entre ``Productos[ProductoID]`` (uno) y ``Ventas[ProductoID]`` (muchos).

Ahora, en la tabla ``Productos``, puedes crear una columna para contar cuántas ventas tiene cada producto:

````dax
VentasPorProducto = COUNTROWS(RELATEDTABLE(Ventas))
````
Este código:
- Toma cada fila de la tabla Productos

- Busca todas las filas relacionadas en Ventas

- Cuenta cuántas filas hay

**Resultado**

| ProductoID | NombreProducto | VentasPorProducto |
| ---------- | -------------- | ----------------- |
| 101        | Galletas       | 2                 |
| 102        | Refresco       | 1                 |
## 8.2 `LOOKUPVALUE()`
Busca un valor en una columna con base en uno o más criterios, como un VLOOKUP en Excel, pero más flexible y específico en contexto.
```dax
LOOKUPVALUE(
    resultado_columna,
    columna_busqueda1, valor_busqueda1,
    [columna_busqueda2, valor_busqueda2], ...
)
```
- ``resultado_columna``: columna desde donde quieres recuperar el valor.

- ``columna_busquedaN``: columna donde se realiza la búsqueda.

- ``valor_busquedaN``: valor que debe coincidir para hacer la búsqueda.

Supón que tienes:
``Productos``

| ProductoID | NombreProducto | Precio |
| ---------- | -------------- | ------ |
| 101        | Galletas       | 25     |
| 102        | Refresco       | 15     |

``Ventas``

| ID\_Venta | ProductoID | Cantidad |
| --------- | ---------- | -------- |
| 1         | 101        | 3        |
| 2         | 102        | 2        |
| 3         | 101        | 1        |

Y quieres traer el precio desde ``Productos`` hacia ``Ventas``:

```dax
PrecioUnitario = 
LOOKUPVALUE(
    Productos[Precio],
    Productos[ProductoID], Ventas[ProductoID]
)
```
Esto busca en la tabla ``Productos`` el ``Precio`` donde ``ProductoID`` coincida con el ``ProductoID`` en la tabla ``Ventas``.

**Resultado**

| ID\_Venta | ProductoID | Cantidad | PrecioUnitario |
| --------- | ---------- | -------- | -------------- |
| 1         | 101        | 3        | 25             |
| 2         | 102        | 2        | 15             |
| 3         | 101        | 1        | 25             |

**Consideraciones**
Consideraciones
- Si hay más de una coincidencia, retorna un error.

- Si no hay coincidencias, retorna BLANK.

- No necesita relaciones entre tablas (aunque es buena práctica tenerlas si es posible).


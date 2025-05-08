# 10. Funciones de Limpieza y Validación
Ayudan a manejar errores y vacíos.
- [10. Funciones de Limpieza y Validación](#10-funciones-de-limpieza-y-validación)
  - [10.1 `ISBLANK()`](#101-isblank)
  - [10.2 `ISNUMBER()`, `ISTEXT()`](#102-isnumber-istext)
  - [10.3 `BLANK()`](#103-blank)
  - [10.4 `COALESCE()`](#104-coalesce)
## 10.1 `ISBLANK()`
Devuelve ``TRUE`` si el valor es en blanco (``BLANK()``), y ``FALSE`` si contiene algún valor.

Es útil para hacer validaciones, controles o evitar errores en medidas o columnas.
```dax
ISBLANK(<value>)
```
- ``<value>``: expresión o columna que deseas evaluar.
**columna calculada**
Supongamos que en tu tabla ``Ventas``, hay una columna ``Monto`` y quieres crear una columna que diga si ese valor está vacío:
```dax
EsBlanco = ISBLANK(Ventas[Monto])
```
Esto devolverá ``TRUE`` si ``Monto`` está en blanco, y ``FALSE`` en caso contrario.

Puedes crear una medida para contar cuántos registros tienen Monto en blanco:
```dax
Ventas en Blanco = 
CALCULATE(
    COUNTROWS(Ventas),
    FILTER(Ventas, ISBLANK(Ventas[Monto]))
)
```
## 10.2 `ISNUMBER()`, `ISTEXT()`
``ISNUMBER()``: Devuelve ``TRUE`` si el valor es numérico.

``ISTEXT()``: Devuelve ``TRUE`` si el valor es texto.
```dax
ISNUMBER(<value>)
ISTEXT(<value>)
```

Filtro para contar solo celdas numéricas
Supón que tienes una tabla ``Ventas`` con una columna ``CódigoDescuento`` que debería ser un número, pero algunas celdas contienen texto. Puedes contar cuántos códigos válidos hay:
```dax
CodigosValidos = 
CALCULATE(
    COUNTROWS(Ventas),
    FILTER(Ventas, ISNUMBER(Ventas[CódigoDescuento]))
)
``` 
## 10.3 `BLANK()`
La función ``BLANK()`` genera un valor vacío o nulo. Es el equivalente a una celda en blanco en ``Excel``.
Se utiliza en expresiones cuando se quiere representar un resultado vacío o limpiar valores.
```dax
BLANK()
```
**Columna calculada**
Supongamos que tienes una columna ``Ventas[Monto]``, pero quieres que los valores menores a 100 se consideren como "sin valor" (vacíos):
```dax
MontoFiltrado = 
IF(Ventas[Monto] < 100, BLANK(), Ventas[Monto])
```
Esto reemplaza montos pequeños con un valor en blanco.

**USO**
- Ocultar valores que no cumplen una condición.

- Evitar errores visuales en tablas o tarjetas.

- Sirve con funciones como ``ISBLANK()`` para hacer validaciones.

- A veces se usa en lugar de 0 para distinguir entre "cero" y "no hay dato".
## 10.4 `COALESCE()`
Devuelve el primer valor que no sea ``BLANK`` de una lista de expresiones.
```dax
COALESCE(<exp1>, <exp2>[, <exp3>, ...])
```
- Evalúa cada expresión en orden.

- Devuelve el primer valor que no sea ``BLANK()``.

- Si todos son ``BLANK``, el resultado es ``BLANK``.

**Medida con valor por defecto**
Imagina que quieres mostrar el valor de una medida, pero si está en blanco, mostrar 0:
```dax
VentasConCero = 
COALESCE(SUM(Ventas[Monto]), 0)
```
**Prioridad entre columnas**
Supón que en la tabla ``Clientes`` tienes varias columnas de contacto opcionales:

``Celular``

``TelefonoCasa``

``TelefonoTrabajo``

Y quieres crear una nueva columna con el **primer número disponible**:

```dax
TelefonoPrincipal = 
COALESCE(Clientes[Celular], Clientes[TelefonoCasa], 
                            Clientes[TelefonoTrabajo])
```
**Evitar BLANK en cálculo de diferencia**
```dax
Diferencia = 
COALESCE(Ventas[Actual], 0) - COALESCE(Ventas[Anterior], 0)
```
Esto evita que la resta devuelva BLANK si uno de los valores falta.

- ``COALESCE()`` reemplaza el clásico ``IF(ISBLANK(...), ..., ...)``.
- Es más limpio, más legible y puede evaluar más de dos alternativas.

- Muy útil en visuales, KPI y cálculos acumulados.
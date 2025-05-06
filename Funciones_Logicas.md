# 5. Funciones Lógicas
- [5. Funciones Lógicas](#5-funciones-lógicas)
  - [5.1 `IF()`](#51-if)
  - [5.2 ``SWITCH()``](#52-switch)
  - [5.3 `AND()`, `OR()`, `NOT()`](#53-and-or-not)
    - [`AND()`](#and)
    - [`OR()`](#or)
    - [`NOT()`](#not)
    - [Operadores Lógicos](#operadores-lógicos)
  - [5.4 `IFERROR()` (equivalente en DAX con `IF(ISERROR(...))`)](#54-iferror-equivalente-en-dax-con-ifiserror)
  - [5.5 `SELECTEDVALUE()`](#55-selectedvalue)

## 5.1 `IF()`
Evalúa una condición lógica y devuelve un resultado si es verdadera, y otro si es falsa.
````dax
IF(condición, valor_si_verdadero, [valor_si_falso])
````
- ``condición``: una expresión lógica (por ejemplo:`` Ventas[Monto] > 1000``)
- ``valor_si_verdadero``: resultado si la condición se cumple
- ``[valor_si_falso]``: (opcional) resultado si no se cumple. Si se omite, devuelve ``BLANK()`` por defecto.

`IF()` Anidado:
````dax
Clasif = IF([Total] > 1000, "Alto",
         IF([Total] > 500, "Medio", "Bajo"))
````
## 5.2 ``SWITCH()``
Evalúa una expresión o condición y devuelve un resultado según los casos definidos.
````dax
SWITCH(expresión, valor1, resultado1, valor2, resultado2, ..., [resultado_predeterminado])
````
- Evalúa expresión, y si coincide con ``valor1``, devuelve ``resultado1``, y así sucesivamente.
- Si no hay coincidencia, devuelve el ``resultado_predeterminado`` (si se incluye) o ``BLANK()``.
Ejemplo con valores exactos.
````dax
MesNombre = SWITCH(
    MesNumero,
    1, "Enero",
    2, "Febrero",
    3, "Marzo",
    "Otro"
)
````
````dax
ClasificacionMonto = 
SWITCH(
    TRUE(),                            // Activa el modo de condiciones lógicas
    Ventas[Monto] > 1000, "Alto",      // Si el monto es mayor a 1000 → "Alto"
    Ventas[Monto] > 500, "Medio",      // Si no, pero es mayor a 500 → "Medio"
    "Bajo"                             // Si no cumple ninguno → "Bajo"
)
````
- Evalúa cada condición como una expresión booleana, y devuélveme el resultado de la primera condición que sea verdadera.
- Evalúa las condiciones en orden hasta encontrar la primera verdadera.
- Es el equivalente a ``IF()`` anidados, pero mucho más limpio.
## 5.3 `AND()`, `OR()`, `NOT()`
### `AND()`
Devuelve ``TRUE`` si ambas condiciones son verdaderas; de lo contrario, devuelve ``FALSE``.
````dax
AND(condición1, condición2)
````
````dax
Estatus =
IF(AND(Venta[Monto] > 1000, Venta[Descuento] < 10), "Aprobado", "Revisión")
````
### `OR()`
Devuelve ``TRUE`` si al menos una de las condiciones es verdadera.
````dax
OR(condición1, condición2)
````
````dax
Estatus =
IF(OR(Venta[Monto] > 1000, Venta[Cliente] = "Premium"), "Aprobado", "Revisión")
````
### `NOT()`
Invierte el valor lógico de una condición. Si la condición es ``TRUE``, devuelve ``FALSE`` y viceversa.
````dax
NOT(condición)
````
````dax
Status =
IF(NOT(Cliente[Estado] = "Activo"), "Inactivo", "Activo")
````
Se pueden usar los operadores logicos en su lugar:
### Operadores Lógicos
| Lógico | Alternativa a función |
|--------|------------------------|
| `&&`   | `AND()`                |
| `\|\|` | `OR()`                 |
| `!`    | `NOT()`                |

Ejemplo:
````dax
ColAprobado = 
IF(
    Ventas[Monto] > 1000 && Ventas[Descuento] < 10,
    "Sí",
    "No"
)
````
````dax
ColEstado = 
IF(
    !(Ventas[Estado] = "Activo"),
    "Inactivo",
    "Activo"
)
````
## 5.4 `IFERROR()` (equivalente en DAX con `IF(ISERROR(...))`)
Devuelve ``TRUE`` si la expresión genera un error, y ``FALSE`` si no hay error.
````dax
ISERROR(expresión)
````
En Excel existe la función ``IFERROR()`` que se usa para capturar errores y devolver un valor alternativo. Aunque DAX no tiene ``IFERROR()`` como tal, puedes lograr el mismo efecto con esta estructura:
````dax
IF(
    ISERROR(expresión),
    valor_si_error,
    expresión
)
````
Supón que estás dividiendo una columna ``Ventas[Monto]`` entre otra columna ``Ventas[Unidades]``, pero algunas filas pueden tener ``Unidades = 0``.
````dax
ColumnaSegura = 
IF(
    ISERROR(Ventas[Monto] / Ventas[Unidades]),
    BLANK(),  -- o 0, o "Error"
    Ventas[Monto] / Ventas[Unidades]
)
````
## 5.5 `SELECTEDVALUE()`
Devuelve el valor único seleccionado en una columna (dentro del contexto de filtros). Si hay más de un valor seleccionado, devuelve el valor alternativo (si se proporciona) o ``BLANK()`` por defecto.
````dax
SELECTEDVALUE(Columna, [valor_alternativo])
````
- ``Columna``: la columna de la que se quiere obtener un valor.
- ``valor_alternativo`` (opcional): lo que se devolverá si hay múltiples valores seleccionados o ninguno.

**Ejemplo 1: en un segmentador**
Supón que tienes un segmentador con la columna ``Clientes[Nombre]``. Puedes crear una medida:
````dax
ClienteSeleccionado = SELECTEDVALUE(Clientes[Nombre], "Varios o Ninguno")
````
Esta medida devolverá:
- El nombre del cliente si hay uno solo seleccionado.
- ``"Varios o Ninguno"`` si hay más de uno o ninguno.
**Ejemplo 2: usar en un título dinámico**
````dax
TítuloReporte = 
"Reporte de ventas para: " & SELECTEDVALUE(Clientes[Nombre], "Todos los clientes")
`````
Esto se puede usar como título de visualizaciones, mostrando el cliente filtrado o una leyenda general.
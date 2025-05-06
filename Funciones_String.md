## Índice de Funciones de Texto (Strings) en DAX

# Sección 1: Funciones Básicas
- [1. Funciones de Texto (Strings)](#1-funciones-de-texto-strings)
  - [1.1 `LEFT()`, `RIGHT()`, `MID()`](#11-left-right-mid)
  - [1.2 `LEN()`](#12-len)
  - [1.3 `TRIM()`](#13-trim)
  - [1.4 `UPPER()`, `LOWER()`](#14-upper-lower)
  - [1.5 `CONCATENATE()` / `CONCAT()`, `&`](#15-concatenate--concat-)
  - [1.6 `REPLACE()`, `SUBSTITUTE()`](#16-replace-substitute)
  - [1.7 `SEARCH()`, `FIND()`](#17-search-find)
  - [1.8 `FORMAT()`](#18-format)
  - [1.9 `VALUE()`](#19-value)
Usadas para limpiar, manipular y analizar textos.

## 1.1 `LEFT()`, `RIGHT()`, `MID()`.
Extraer caracteres desde el inicio (`LEFT()`), medio (`MID()`) o el final (`RIGHT()`) de una cadena.
```dax
-- Sintaxis
LEFT(texto,num_chars)
RIGHT(texto,num_chars)
```

```dax
MID(texto, start_num, num_chars)
-- start_num: Posicion donde inicia a tomar caracteres, los strings empiezan en 1.
```
Supon que tienes la tabla `Productos` columna `Codigos`, con uno de los siguientes registros: `AB1234`

```dax
 Primeros caracteres = LEFT(Productos[Codigos],2)
 -- Salida: AB

 Ultimos caracteres = RIGHT(Productos[Codigos],4)
 -- Salida: 1234

 Caracteres medios = MID(Productos[Codigos],3,3)
 -- Salida: 123
```
## 1.2 `LEN()`.
Devuelve la longitud (número de caracteres) de una cadena de texto, inlcuye espacios y signos.
``` dax
LEN(texto)
```

Supón que tienes la tabla `Productos` y columna ``NombreProducto`` con el valor ``"ChocoPayaso"``:

``` dax
LargoNombre = LEN(Productos[NombreProducto])
-- Salida: 12
```
¿Para que sirve?
- Filtrar datos mal escritos o incompletos.

- Usarlo en condiciones: por ejemplo, ``IF(LEN(columna) > 5, ...)``
## 1.3 `TRIM()`
Elimina todos los espacios en blanco al inicio y al final de una cadena de texto, y deja solo un espacio simple entre palabras internas.

```dax
TRIM(texto)
```
Supón que tienes este valor en una columna ``NombreCliente``:
```arduino
"   Juan   Pérez   "
```
```dax
NombreLimpio = TRIM(Tabla[NombreCliente])
--SALIDA: "Juan Pérez"
```

OBS: No quita tabulaciones u otros caracteres invisibles especiales (para eso se necesita una limpieza más avanzada con ``SUBSTITUTE()``).

### `LEN()` + `TRIM()`
Si quieres saber cuantos espacios blancos tienes:

```dax
EspaciosExtra = LEN(Tabla[TextoOriginal]) - LEN(TRIM(Tabla[TextoOriginal]))
-- OPERACION: CharsTotal - CharsSinEspacios
```

## 1.4 ``UPPER()``, ``LOWER()``.
Convierte todo el texto en minusculas o mayusculas.

``` dax
UPPER(texto)
```

```dax
NombreMayus = UPPER(Tabla[NombreCliente])
SALIDA: Devuelve todos los registros en mayusculas

NombreMinus = LOWER(Tabla[NombreCliente])
SALIDA: Devuelve todos los registros en minusculas.
```

## 1.5 ``CONCATENATE()`` / ``CONCAT()``, `&`

### ``CONCAT()`` 
Une cualquier cantidad de valores de texto en una columna.
``` dax
CONCAT(texto)
```
```dax
NombresTodos = CONCAT(Tabla[Nombre])
-- SALIDA: Une todos los elemento de la columna Nombre en una sola cadena.
```
### ``CONCATENATE()`` 
Une dos cadenas de texto en una sola.

````dax
CONCATENATE(texto1, texto2)
````
````dax
NombreCompleto = CONCATENATE(Tabla[Nombre], Tabla[Apellido])
````
Si tienes:
Nombre: "Juan"
Apellido: "Pérez"
Devuelve: "JuanPérez"

(Sin espacio. Para agregar espacio debes hacerlo manualmente:)

````dax
NombreCompleto = CONCATENATE(Tabla[Nombre] & " ", Tabla[Apellido])
````

### `&` Operador.
````dax
NombreCompleto = Tabla[Nombre] & " " & Tabla[Apellido]
````
## 1.6 `REPLACE()`, `SUBSTITUTE()`
### `REPLACE()`
Reemplaza una parte del texto basada en la posición y la cantidad de caracteres.

````dax
REPLACE(texto_original, posición_inicial, número_de_caracteres, nuevo_texto)
````
````dax
REPLACE("AB-1234-Z", 4, 4, "XXXX")
--SALIDA: "AB-XXXX-Z"
````
## `SUBSTITUTE()`
Sustituye una subcadena específica por otra, sin importar la posición.
````dax
SUBSTITUTE(texto_original, texto_a_reemplazar, nuevo_texto)
````
````dax
SUBSTITUTE("AB-1234-Z", "-", "/")
````
## 1.7 `SEARCH()`, `FIND()`
### `SEARCH()`
Busca un texto dentro de otro texto y devuelve la posición inicial donde lo encuentra.
No distingue entre mayúsculas y minúsculas.
````dax
SEARCH(texto_buscado, dentro_de_este_texto, [posición_inicial])
````
- ``posición_inicial`` es opcional y por defecto es 1.
````dax
SEARCH("perez", "Juan Perez")
--SALIDA: 6
````
### `FIND()`
Hace lo mismo que `SEARCH()`, pero distingue entre mayúsculas y minúsculas.
````dax
FIND(texto_buscado, dentro_de_este_texto, [posición_inicial])
````
````dax
FIND("perez", "Juan Perez")
--SALIDA:Error ya que "Perez" es con P mayuscula
````
**En ambas funciones, si no se encuetra el resultado, manda ``ERROR``**

## 1.8 `FORMAT()`
Convierte un número o una fecha a texto con formato personalizado, como moneda, porcentajes, fechas legibles, etc.
````dax
FORMAT(valor, formato)
````
``valor``: puede ser un número, fecha, o medida.
``formato``: una cadena de texto que define el formato de salida.
Algunos ejemplos:
````dax
FORMAT(1234.5, "#,##0.00")
--SALIDA: "1,234.50"
````
````dax
FORMAT(TODAY(), "DD/MM/YYYY")
--SALIDA: "05/05/2025"
````
````dax
FORMAT(1234.5, "$#,##0.00")
--SALIDA: "$1,234.50"
````
Algunos formatos comunes:
| Tipo       | Ejemplo de formato | Salida       |
|------------|--------------------|--------------|
| Moneda     | `"$#,##0.00"`      | `$1,000.50`  |
| Porcentaje | `"0.00%"`          | `25.00%`     |
| Fecha      | `"DD/MM/YYYY"`     | `05/05/2025` |
| Día        | `"dddd"`           | `lunes`      |
| Hora       | `"HH:mm"`          | `13:45`      |

- ``FORMAT()`` devuelve texto, por lo tanto no puedes hacer cálculos posteriores con el resultado.
- Úsalo cuando quieras mostrar el dato en reportes, títulos, etiquetas o tablas visuales.

## 1.9 `VALUE()`
Convierte un valor texto que parece número o fecha a un valor numérico real (tipo decimal o datetime).
````dax
VALUE(texto)
````
````dax
VALUE("1234.56")
--SALIDA: 1234.56
````
````dax
VALUE("05/05/2025")
--SALIDA: Valor de tipo datetime
````
¿Cuando usar?
- Cuando importas datos desde Excel, CSV o sistemas externos que traen números como texto.
- Evitar erros de calculo de tipo `Texto` vs `numerico`
  
- **Si el texto no es numérico ni una fecha válida, devuelve error.**

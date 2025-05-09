# Estructura de bloque
## `VAR`
La palabra clave ``VAR`` en ``DAX`` se usa para declarar una variable dentro de una medida o una columna calculada.
Sirve para:

- Para guardar un valor temporal (como una suma, una fecha, un filtro, etc.).
- Para evitar repetir cálculos (mejor rendimiento y legibilidad).

Tipos de datos que puede guardar `VAR`:
- Escalares (valores individuales)
  - Números

  - Texto

  - Fecha/hora

  - Booleanos (TRUE/FALSE)
- Tablas
```dax
VAR nombre_variable = expresión
```
Ejemplo:
```dax
VAR VentasActuales = SUM(Ventas[Monto])
```
Aquí estás guardando el resultado de ``SUM(Ventas[Monto])`` en una variable llamada ``VentasActuales``.

**Las variables solo existen dentro del bloque donde se declaran.**

## `RETURN`
La palabra clave ``RETURN`` se usa para especificar qué valor debe devolver la medida después de calcular todas las variables con ``VAR``.

```dax
RETURN
   expresión_final
```
Ejemplo:
```dax
RETURN
   VentasActuales - VentasPasado
```
Aquí estás usando las variables definidas previamente para calcular y devolver el resultado final.

## Estructura General de Bloque

```dax
NombreMedida =
VAR nombreVar1 = expresión1
VAR nombreVar2 = expresión2
...
RETURN
   expresión_final_usando_las_VARs
```
Ejemplo:
Supongamos que estás calculando un promedio ajustado en base a ventas del año actual y del año pasado:
```dax
Promedio Ajustado =
VAR VentasActual = SUM(Ventas[Monto])
VAR VentasPasado = CALCULATE(SUM(Ventas[Monto]), DATEADD(Calendario[Fecha], -1, YEAR))
VAR TotalPromedio = (VentasActual + VentasPasado) / 2
RETURN
TotalPromedio
``` 
Esta estrutura:

- Calcula dos sumas.

- Luego calcula un promedio entre ambas.

- Finalmente devuelve ese resultado.


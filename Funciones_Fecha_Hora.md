## Índice - Funciones de Fecha y Hora en DAX

# 2. Funciones de Fecha y Hora
- [2.1 `TODAY()`, `NOW()`](#21-today-now)
- [2.2 `YEAR()`, `MONTH()`, `DAY()`](#22-year-month-day)
- [2.3 `WEEKNUM()`](#23-weeknum)
- [2.4 `HOUR()`, `MINUTE()`, `SECOND()`](#24-hour-minute-second)
- [2.5 `DATEDIFF()`](#25-datediff)
- [2.6 `DATE()`](#26-date)
- [2.7 `EDATE()`, `EOMONTH()`](#27-edate-eomonth)
- [2.8 `FORMAT()` (para fechas)](#28-format-para-fechas)
- [2.9 `CALENDAR()`, `CALENDARAUTO()`](#29-calendar-calendarauto)
 
Ideales para calcular diferencias de tiempo, agrupar por fecha, etc.

## 2.1 `TODAY()`, `NOW()`
### `TODAY()`
Devuelve la fecha actual (sin la hora) como un valor de tipo date.
````dax
TODAY()
-- No necesita argumentos.
````
Ejemplo: Calcular edad:
````dax
Edad = YEAR(TODAY()) - YEAR(Tabla[FechaNacimiento])
````
### ``NOW()``
Devuelve un valor de tipo ``datetime`` con fecha + hora actual del sistema.
````dax
NOW()
````

## 2.2 `YEAR()`, `MONTH()`, `DAY()`
Extraen partes de una fecha.
````dax
YEAR(fecha)
MONTH(fecha)
DAY(fecha)
````
- fecha: un valor de tipo ``date`` o ``datetime``.
````dax
AñoVenta = YEAR('Ventas'[Fecha])
MesVenta = MONTH('Ventas'[Fecha])
DiaVenta = DAY('Ventas'[Fecha])
/*
Si la fecha es 2025-05-05, obtienes:
AñoVenta → 2025
MesVenta → 5
DiaVenta → 5

*/
````
## 2.3 `WEEKNUM()`
Devuelve el número de semana del año (del 1 al 52 o 53) correspondiente a una fecha dada.
````dax
WEEKNUM(fecha, [tipo])
````
- ``fecha``: una fecha válida.

- ``tipo`` (opcional):

  - 1 → Semana empieza el domingo (predeterminado).

  - 2 → Semana empieza el lunes.

````dax
SemanaDelAño = WEEKNUM('Ventas'[Fecha])
--SALIDA: Si la fecha es 2025-05-05, devuelve: 19 (semana 19 del año)
````

````dax
SemanaLunes = WEEKNUM('Ventas'[Fecha], 2)
 Esto empieza a contar las semanas desde lunes.
````
**IMPORTANTE**
- El resultado depende del tipo de calendario que estés utilizando.
- Si necesitas usar semana ``ISO 8601`` (la estándar internacional donde la semana empieza el lunes y la primera semana incluye el primer jueves del año), debes hacer una fórmula más avanzada, porque WEEKNUM() no lo maneja directamente.
## 2.4 `HOUR()`, `MINUTE()`, `SECOND()`
Extraer partes de la hora.
````dax
HOUR(fecha_hora)
MINUTE(fecha_hora)
SECOND(fecha_hora)
````
``fecha_hora``: un valor de tipo ``datetime`` que incluye fecha y hora.

````dax
Hora     = HOUR(FechaHora)     // 14
Minuto   = MINUTE(FechaHora)   // 36
Segundo  = SECOND(FechaHora)   // 22
````
- Si la columna tiene solo fecha sin hora, HOUR(), MINUTE() y SECOND() devolverán cero (0).
- No redondea, solo extrae el componente exacto.

Adicionalmente:
````dax
HOUR("14:23:22")   // No válido: es texto
HOUR(14:23:22)     // No válido: error de sintaxis
````
Alternativa:
````dax
HOUR(TIME(14, 23, 22)) // Devuelve 14
````
Si tienes una cadena como "14:23:22"
````dax
HoraDesdeTexto = TIME(
    VALUE(LEFT("14:23:22", 2)),
    VALUE(MID("14:23:22", 4, 2)),
    VALUE(RIGHT("14:23:22", 2))
)
// Resultado: TIME(14, 23, 22) → válido para usar en HOUR()
````
## 2.5 ``DATEDIFF()``
Calcula la diferencia entre dos fechas.
````dax
DATEDIFF(fecha_inicial, fecha_final, unidad)
````
- ``unidad``: Unidad de tiempo para la diferencia. Debe ser uno de los siguientes:

| Unidad  | Significado      |
| ------- | ---------------- |
| SECOND  | Segundos         |
| MINUTE  | Minutos          |
| HOUR    | Horas            |
| DAY     | Días             |
| WEEK    | Semanas (7 días) |
| MONTH   | Meses            |
| QUARTER | Trimestres       |
| YEAR    | Años             |

Devuelve la cantidad de unidades completas entre dos fechas (por ejemplo, cuántos días, semanas o años han pasado entre ellas).

````dax
DiasTranscurridos = DATEDIFF('Ventas'[FechaPedido], 'Ventas'[FechaEntrega], DAY)
--SALIDA: Esto devuelve la cantidad de días entre pedido y entrega.
````
La diferencia entre ``DATEDIFF()`` y simplemente restar fechas: ``([fecha2] - [fecha1])`` es que ``DATEDIFF()`` permite elegir la unidad exacta.

## 2.6 `DATE()`
Construye una fecha a partir de año, mes y día. 
````dax
DATE(año, mes, día)
````
````dax
MiFecha = DATE(2000, 3, 24)
// Resultado: 2000-03-24
````
Devuelve un valor de tipo ``date`` a partir de los números que representen el año, mes y día.
**Manejo del desborde.**
Puede manejar valores fuera de rango automáticamente:
````dax
DATE(2025, 13, 1) → 2026-01-01
DATE(2025, 0, 15) → 2024-12-15
DATE(2025, 2, 30) → 2025-03-02  // febrero no tiene 30 días, lo ajusta
````
## 2.7 ``EDATE()``,`EOMONTH()`
### `EDATE()`
Suma o resta **meses** a una fecha.
````dax
EDATE(fecha, número_de_meses)
````
- ``número_de_meses``: número entero positivo o negativo.
Devuelve la misma fecha pero desplazada cierta cantidad de meses hacia adelante o atrás.

````dax
MesSiguiente = EDATE('Ventas'[Fecha], 1)
MesAnterior  = EDATE('Ventas'[Fecha], -1)
````
Si ``Fecha = 2025-05-15``, entonces:

``EDATE(Fecha, 1)`` → 2025-06-15

``EDATE(Fecha, -1)`` → 2025-04-15
### `EOMONTH()`
Fin de mes desplazado
````dax
EOMONTH(fecha, número_de_meses)
````
- ``número_de_meses``: cuántos meses moverte (0 = mes actual).
Devuelve el último día del mes luego de desplazar la fecha una cierta cantidad de meses.
Ej:
````dax
FinMesActual   = EOMONTH('Ventas'[Fecha], 0)
FinMesAnterior = EOMONTH('Ventas'[Fecha], -1)
````
Si ``Fecha = 2025-05-15``:

``EOMONTH(Fecha, 0)`` → 2025-05-31

``EOMONTH(Fecha, -1)`` → 2025-04-30
## 2.8 ``FORMAT()``
Ver Funciones_String
## 2.9 ``CALENDAR()``,`CALENDARAUTO()`
### ``CALENDAR()``
Genera un rango de fechas manual.
````dax
CALENDAR(fecha_inicial, fecha_final)
````
Ambas fechas deben ser literales (``DATE()``, columnas constantes o fechas fijas).
Crea una tabla de una sola columna llamada [Date] con todas las fechas entre fecha_inicial y fecha_final.

````dax
MiCalendario = CALENDAR(DATE(2020, 1, 1), DATE(2025, 12, 31))
````
**SALIDA**: Una tabla con todas las fechas entre enero 2020 y diciembre 2025.
**USO**
Crear una tabla calendario base y luego agregarle columnas calculadas respecto al calendario, ideal para calculos en tus dashboards.

### `CALENDARAUTO()`
Crea la tabla calendario automáticamente.
````dax
CALENDARAUTO([año_fin_fiscal])
````
El parámetro es opcional y se usa para definir en qué mes termina tu año fiscal (por ejemplo, 6 si tu año fiscal termina en junio).

**Detecta automáticamente** el mínimo y máximo valor de fecha usado en tu modelo de datos, y genera una tabla de fechas que cubra todo ese rango.

Ej:
````dax
CalendarioAuto = CALENDARAUTO()
````
Si tus tablas tienen fechas entre 2022-05-01 y 2025-11-30, esta función crea una tabla con todas las fechas entre enero 2022 y diciembre 2025.




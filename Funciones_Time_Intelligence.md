# 9 Time Intelligence (Inteligencia de Tiempo)
Crucial para análisis por periodo (meses, años, comparaciones).
- [9 Time Intelligence (Inteligencia de Tiempo)](#9-time-intelligence-inteligencia-de-tiempo)
  - [9.1 `TOTALYTD()`, `TOTALQTD()`, `TOTALMTD()`](#91-totalytd-totalqtd-totalmtd)
    - [`TOTALYTD()`](#totalytd)
    - [``TOTALQTD()``](#totalqtd)
    - [``TOTALMTD()``](#totalmtd)
  - [9.2 `DATESYTD()`, `DATESMTD()`, `DATESQTD()`](#92-datesytd-datesmtd-datesqtd)
    - [`DATESYTD()`](#datesytd)
    - [`DATESMTD()`](#datesmtd)
    - [`DATESQTD()`](#datesqtd)
  - [9.3 `SAMEPERIODLASTYEAR()`](#93-sameperiodlastyear)
  - [9.4 `PARALLELPERIOD()`](#94-parallelperiod)
  - [9.5 `DATEADD()`](#95-dateadd)
  - [9.6 `PREVIOUSYEAR()`, `PREVIOUSMONTH()`, `PREVIOUSDAY()`](#96-previousyear-previousmonth-previousday)
  - [9.7 `NEXTYEAR()`, `NEXTMONTH()`, `NEXTDAY()`](#97-nextyear-nextmonth-nextday)

## 9.1 `TOTALYTD()`, `TOTALQTD()`, `TOTALMTD()`
Definen el rango temporal sobre el cual se aplicará la operación que tú les indiques.
Estas funciones permiten calcular acumulados (totales) en distintos periodos de tiempo:

**YTD**: Year to Date (acumulado del año)

**QTD**: Quarter to Date (acumulado del trimestre)

**MTD**: Month to Date (acumulado del mes)

### `TOTALYTD()`

```dax
TOTALYTD(<expression>, <dates>, [<filter>], [<year_end_date>])
```
- ``<expression>``: la expresión o columna a agregar.

- ``<dates>``: columna de fechas.

- ``[filter]``: opcional, para aplicar filtros.

- ``[year_end_date]``: opcional, si tu año fiscal termina diferente al 31 de diciembre.

**Ejemplo de medida**
Suma los valores desde el inicio del año hasta la fecha seleccionada.
```dax
Ventas YTD = 
TOTALYTD(
    SUM(Ventas[Monto]),
    Calendario[Fecha]
)
```
### ``TOTALQTD()``
```dax
TOTALQTD(<expression>, <dates>, [<filter>])
```
**Ejemplo de medida**
```dax
Ventas QTD = 
TOTALQTD(
    SUM(Ventas[Monto]),
    Calendario[Fecha]
)
```
Suma los valores desde el inicio del trimestre hasta la fecha seleccionada.

### ``TOTALMTD()``
```dax
TOTALMTD(<expression>, <dates>, [<filter>])
```
```dax
Ventas MTD = 
TOTALMTD(
    SUM(Ventas[Monto]),
    Calendario[Fecha]
)
```
Suma los valores desde el inicio del mes hasta la fecha seleccionada.

**Requisitos:**
- Tener una tabla de calendario con una columna de tipo fecha (``Calendario[Fecha]``).

- Esa tabla debe estar relacionada con la tabla de hechos (como ``Ventas``).

- El campo de fecha debe estar en el formato correcto de fecha, no como texto.
## 9.2 `DATESYTD()`, `DATESMTD()`, `DATESQTD()`

Estas funciones devuelven una tabla de fechas desde el inicio del periodo (año, mes o trimestre) hasta la fecha máxima del contexto actual.
Se usan comúnmente dentro de funciones como CALCULATE() para modificar el contexto de tiempo.
###  `DATESYTD()`
Devuelve todas las fechas desde el inicio del año hasta la última fecha del contexto actual.
```dax
DATESYTD(<dates>, [<year_end_date>])
```
- ``<dates>``: columna de fechas.

- ``[year_end_date]``: opcional; por defecto es 31 de diciembre.

```dax
Ventas YTD = 
CALCULATE(
    SUM(Ventas[Monto]),
    DATESYTD('Calendario'[Fecha])
)
```
### `DATESMTD()` 
Devuelve todas las fechas desde el inicio del mes hasta la fecha actual.
```dax
DATESMTD(<dates>)
```
```dax
Ventas MTD = 
CALCULATE(
    SUM(Ventas[Monto]),
    DATESMTD('Calendario'[Fecha])
)
```
### `DATESQTD()`
Devuelve todas las fechas desde el inicio del trimestre hasta la fecha actual.
```dax
DATESQTD(<dates>)
```
```dax
Ventas QTD = 
CALCULATE(
    SUM(Ventas[Monto]),
    DATESQTD('Calendario'[Fecha])
)
```
Diferenca con **`TOTAL`**
| Función      | ¿Qué hace?               | ¿Devuelve valor directamente? | ¿Requiere CALCULATE()? |
| ------------ | ------------------------ | ----------------------------- | ---------------------- |
| `TOTALYTD()` | Hace la suma acumulada   | ✅ Sí                          | ❌ No                   |
| `DATESYTD()` | Devuelve rango de fechas | ❌ No                          | ✅ Sí                   |
## 9.3 `SAMEPERIODLASTYEAR()`
Devuelve una tabla de fechas del mismo periodo del año anterior según el contexto actual.
Es muy útil para crear medidas que comparen, por ejemplo, las ventas de este mes con el mismo mes del año pasado, o este día con el mismo día del año anterior.
```dax
SAMEPERIODLASTYEAR(<dates>)
```
- ``<dates>`` debe ser una columna de una tabla de fechas continua (como ``Calendario[Fecha]``).
  
**Medida: Ventas del mismo periodo del año pasado**
```dax
Ventas LY = 
CALCULATE(
    SUM(Ventas[Monto]),
    SAMEPERIODLASTYEAR(Calendario[Fecha])
)
```
Esta medida te da la suma de ventas del mismo periodo del año anterior, respetando el nivel de agregación: día, mes, trimestre, etc.

**Ejemplo visual**
| Fecha      | Ventas actuales | Ventas LY |
| ---------- | --------------- | --------- |
| 2024-05-01 | \$10,000        | \$9,000   |
| 2024-05-02 | \$8,500         | \$8,700   |

Usando esta medida, podrías mostrar una línea con las ventas actuales y otra con las del año pasado para el mismo periodo.

- Puedes usarla junto a ``DATEADD()`` o ``PARALLELPERIOD()`` para comparar con años, meses o trimestres anteriores (pero esas funciones te dan más flexibilidad que ``SAMEPERIODLASTYEAR()``).

- Requiere un modelo con una tabla de fechas bien relacionada.
## 9.4 `PARALLELPERIOD()`
Devuelve una tabla de fechas desplazada un número específico de periodos (días, meses, trimestres o años), hacia adelante o hacia atrás.
```dax
PARALLELPERIOD(<dates>, <number_of_intervals>, <interval>)
```
- ``<dates>``: Columna de tipo fecha (normalmente ``Calendario[Fecha]``).

- ``<number_of_intervals>``: Número de unidades que deseas mover. Puede ser negativo (pasado) o positivo (futuro).

- ``<interval>``: Unidad del desplazamiento. Acepta: ``"DAY"``, ``"MONTH"``, ``"QUARTER"``, ``"YEAR"``.

**Medida para ventas del mes anterior**
```dax
Ventas Mes Anterior = 
CALCULATE(
    SUM(Ventas[Monto]),
    PARALLELPERIOD(Calendario[Fecha], -1, MONTH)
)
```
Este cálculo devuelve las ventas del mismo rango de días del mes anterior.
**Ventas de hace 2 años**
```dax
Ventas Hace 2 Años = 
CALCULATE(
    SUM(Ventas[Monto]),
    PARALLELPERIOD(Calendario[Fecha], -2, YEAR)
)
```
Esto obtiene los datos del mismo rango pero dos años atrás.

**Requisitos**
- Una tabla de fechas continua y relacionada.

- No admite múltiples desplazamientos al mismo tiempo.
## 9.5 `DATEADD()`
Devuelve una tabla de fechas desplazadas hacia el pasado o futuro día por día, mes por mes, etc., según una columna de fechas.

Es muy útil para comparar periodos desplazados con mucha precisión.
```dax
DATEADD(<dates>, <number_of_intervals>, <interval>)
```
- ``<dates>``: Columna de fechas (como ``Calendario[Fecha]``).

- ``<number_of_intervals>``: Cuántas unidades de tiempo quieres mover. Negativo (pasado), positivo (futuro).

- ``<interval>``: ``"DAY"``, ``"MONTH"``, ``"QUARTER"``, ``"YEAR"``.

**Ventas del mes anterior**
```dax
Ventas Mes Anterior = 
CALCULATE(
    SUM(Ventas[Monto]),
    DATEADD('Calendario'[Fecha], -1, MONTH)
)
```
Devuelve las ventas del mismo día del mes pasado (por ejemplo, del 8 de abril si estás en 8 de mayo).

**Crecimiento años con años**
```dax
Crecimiento YoY % =
VAR VentasActuales = SUM(Ventas[Monto])
VAR VentasPasado = CALCULATE(
    SUM(Ventas[Monto]),
    DATEADD('Calendario'[Fecha], -1, YEAR)
)
RETURN
DIVIDE(VentasActuales - VentasPasado, VentasPasado)
```
Compara las ventas actuales con las del mismo día del año anterior.

**Requisitos**
- Tabla de fechas continua y correctamente relacionada.

- Es sensible al nivel de detalle: si estás viendo días, desplazará día por día; si meses, mes por mes.

**Tablas comparativas**

| Función              | Desplaza fechas (día a día) | Conserva el rango exacto |
| -------------------- | --------------------------- | ------------------------ |
| `DATEADD()`          | ✅                           | ❌                        |
| `PARALLELPERIOD()`   | ❌                           | ✅                        |
| `SAMEPERIODLASTYEAR` | ❌ (solo año anterior)       | ✅                        |


| Función              | ¿Qué hace?                                                           |
| -------------------- | -------------------------------------------------------------------- |
| `SAMEPERIODLASTYEAR` | Mismo periodo del año anterior                                       |
| `DATEADD()`          | Permite desplazar por días, meses, años                              |
| `PARALLELPERIOD()`   | Similar a DATEADD pero conserva el mismo rango (no suma/día por día) |
## 9.6 `PREVIOUSYEAR()`, `PREVIOUSMONTH()`, `PREVIOUSDAY()`
Estas funciones devuelven las fechas del período completo anterior (año, mes o día) basado en el contexto actual.
```dax
PREVIOUSYEAR(<dates>, [year_end_date])
PREVIOUSMONTH(<dates>)
PREVIOUSDAY(<dates>)
```
- ``<dates>``: Columna de fechas (ej. ``Calendario[Fecha]``)

- ``[year_end_date]`` (solo en ``PREVIOUSYEAR()``): Opcional. Formato ``"MM-DD"`` para definir el fin del año fiscal.

 **Ventas del año anterior**
```dax
Ventas Año Anterior =
CALCULATE(
    SUM(Ventas[Monto]),
    PREVIOUSYEAR('Calendario'[Fecha])
)
```
Este cálculo devuelve la suma de ventas del mismo período pero en el año anterior completo.

**Ventas del mes anterior**
```dax
Ventas Mes Anterior =
CALCULATE(
    SUM(Ventas[Monto]),
    PREVIOUSMONTH('Calendario'[Fecha])
)
```
Devuelve el monto total del mes anterior completo.

**Ventas del día anterior**
```dax
Ventas Día Anterior =
CALCULATE(
    SUM(Ventas[Monto]),
    PREVIOUSDAY('Calendario'[Fecha])
)
```
## 9.7 `NEXTYEAR()`, `NEXTMONTH()`, `NEXTDAY()`
Estas funciones devuelven una tabla de fechas correspondiente al período siguiente completo (año, mes o día) con base en el contexto actual de una columna de fechas.
```dax
NEXTYEAR(<dates>, [year_end_date])
NEXTMONTH(<dates>)
NEXTDAY(<dates>)
```
- ``<dates>``: Columna de fechas (ej. ``Calendario[Fecha]``)

- ``[year_end_date]`` (solo en ``PREVIOUSYEAR()``): Opcional. Formato ``"MM-DD"`` para definir el fin del año fiscal.

**Ventas del año siguiente**
```dax
Ventas Año Siguiente =
CALCULATE(
    SUM(Ventas[Monto]),
    NEXTYEAR('Calendario'[Fecha])
)
```
Este cálculo devuelve la suma de ventas del mismo período pero del año siguiente completo.

**Ventas del mes siguiente**
```dax
Ventas Mes Siguiente =
CALCULATE(
    SUM(Ventas[Monto]),
    NEXTMONTH('Calendario'[Fecha])
)
```
Devuelve el total de ventas del mes siguiente completo.
**Ventas del día siguiente**
````dax
Ventas Día Siguiente =
CALCULATE(
    SUM(Ventas[Monto]),
    NEXTDAY('Calendario'[Fecha])
)
````
Obtiene las ventas del día siguiente a la fecha actual del contexto.

**Nota**
Estas funciones son ideales cuando analizas datos agregados por períodos completos (como año anterior, próximo mes, etc.), y son especialmente útiles en análisis comparativos o tablas de tendencias.
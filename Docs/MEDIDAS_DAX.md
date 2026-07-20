# 📊 Medidas DAX – Tablero Power BI Nike (2020–2024)

Este documento reúne las principales medidas DAX utilizadas en el dashboard desarrollado en **Power BI**, organizadas según la estructura del proyecto.

---

# 📁 Análisis General

## 🔹 Calificación Crecimiento

```DAX
Crecimiento =
VAR Pelota = UNICHAR(127936)

VAR TasaCrecimiento =
    VAR Ventas_Actuales = [Fact. Total]
    VAR Ventas_Anteriores =
        CALCULATE(
            [Fact. Total],
            DATEADD('Calendario DAX'[Date], -1, QUARTER)
        )

    RETURN
        DIVIDE(Ventas_Actuales - Ventas_Anteriores, Ventas_Anteriores)

RETURN
    SWITCH(
        TRUE(),
        TasaCrecimiento <= 0, REPT(Pelota,1),
        TasaCrecimiento <= 0.05, REPT(Pelota,2),
        TasaCrecimiento <= 0.15, REPT(Pelota,3),
        TasaCrecimiento <= 0.30, REPT(Pelota,4),
        REPT(Pelota,5)
    )
```

---

## 🔹 Facturación Promedio

```DAX
Facturación Promedio =
AVERAGE(Ventas[Total Facturado])
```

---

## 🔹 Facturación Total

```DAX
Facturación Total =
SUM(Ventas[Total Facturado])
```

---

## 🔹 Margen Alto (%)

```DAX
Margen Alto (%) =
CALCULATE(
    COUNTROWS(Ventas),
    Ventas[Margen_Evaluado] = "Margen Alto"
) / COUNTROWS(Ventas)
```

---

## 🔹 Margen Bajo (%)

```DAX
Margen Bajo (%) =
CALCULATE(
    COUNTROWS(Ventas),
    Ventas[Margen_Evaluado] = "Margen Bajo"
) / COUNTROWS(Ventas)
```

---

## 🔹 Margen Medio (%)

```DAX
Margen Medio (%) =
CALCULATE(
    COUNTROWS(Ventas),
    Ventas[Margen_Evaluado] = "Margen Medio"
) / COUNTROWS(Ventas)
```

---

## 🔹 Margen Operativo

```DAX
Margen Operativo =
SUM(Ventas[Margen Operativo])
```

---

## 🔹 Rentabilidad

```DAX
Rentabilidad =
SUM(Ventas[Rentabilidad ($)])
```

---

## 🔹 Tasa Facturación YoY

```DAX
Tasa Facturación YoY =
VAR __PREV_YEAR =
    CALCULATE(
        [Facturación Total],
        DATEADD('CALENDARIO'[Fecha], -1, YEAR)
    )

RETURN
IF(
    NOT(ISBLANK(__PREV_YEAR)),
    DIVIDE([Facturación Total] - __PREV_YEAR, __PREV_YEAR),
    BLANK()
)
```

---

## 🔹 Total Facturado

```DAX
Fact. Total =
SUM(Nike_Ventas[Total Facturado])
```

---

## 🔹 Cantidad de Ventas

```DAX
Cant de Ventas =
COUNTROWS(Nike_Ventas)
```

---

# 📁 Comportamiento Comercial

## 🔹 Evaluación Tasa

```DAX
Evaluación Tasa =

VAR Zapatilla = UNICHAR(128095)

VAR __PREV_MONTH =
    CALCULATE(
        [Fact. Total],
        DATEADD('Calendario DAX'[Date], -1, MONTH)
    )

VAR __VARIACION =
    DIVIDE([Fact. Total] - __PREV_MONTH, __PREV_MONTH, 0)

RETURN

IF(
    SELECTEDVALUE('Calendario DAX'[Mes]) IN VALUES('Calendario DAX'[Mes]),

    SWITCH(
        TRUE(),
        __VARIACION < 0, REPT(Zapatilla,1),
        __VARIACION <= 0.05, REPT(Zapatilla,2),
        __VARIACION <= 0.10, REPT(Zapatilla,3),
        __VARIACION <= 0.20, REPT(Zapatilla,4),
        REPT(Zapatilla,5)
    ),

    BLANK()
)
```

---

## 🔹 Mensaje Resumen

```DAX
Mensaje_Resumen =
"Explore los productos más rentables, campañas más efectivas y el comportamiento mensual en el año "
&
SELECTEDVALUE('Calendario'[Año])
```

---

# 📁 Conclusión

## 🔹 Mediana

```DAX
Mediana =
PERCENTILE.INC(
    Nike_Ventas[Rentabilidad ($)],
    0.50
)
```

---

## 🔹 Margen Operativo

```DAX
Margen Operativo =
SUM(Nike_Ventas[Margen Operativo])
```

---

# 📁 Tooltips

## 🔹 Narrativa Tooltip

```DAX
Texto Tooltip =

VAR Texto = "Para el Estado de "
VAR Texto1 = ", se consolidó una facturación de "
VAR Texto2 = ", una rentabilidad de "
VAR Texto3 = " y un total de "
VAR Texto4 = " ventas."

VAR Estados =
    SELECTEDVALUE(Estado[State])

VAR Facturacion =
    FORMAT([Fact. Total], "$ #,##0")

VAR Rentabilidad =
    FORMAT([Rentabilidad], "$ #,##0")

VAR CantidadVentas =
    FORMAT([Cant de Ventas], "#,##0")

RETURN

Texto &
Estados &
Texto1 &
Facturacion &
Texto2 &
Rentabilidad &
Texto3 &
CantidadVentas &
Texto4
```

---

# 📁 Análisis de Eficiencia

## 🔹 Parámetro de Campos

```DAX
{
    ("Margen Bajo (%)", NAMEOF('Tabla Medidas'[Margen Bajo (%)]), 0),
    ("Margen Medio (%)", NAMEOF('Tabla Medidas'[Margen Medio (%)]), 1),
    ("Margen Alto (%)", NAMEOF('Tabla Medidas'[Margen Alto (%)]), 2)
}
```


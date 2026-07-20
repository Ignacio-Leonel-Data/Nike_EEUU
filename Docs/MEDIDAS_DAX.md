# 📊 Medidas DAX – Tablero Power BI Nike (2020–2024)

Este documento describe las medidas DAX utilizadas en el proyecto, organizadas por carpeta funcional según su ubicación en Power BI.

---

## 📁 Análisis Gral.

### 🔸 Calificación Crecimiento
```dax
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
        TasaCrecimiento <= 0, REPT(Pelota, 1),
        TasaCrecimiento <= 0.05, REPT(Pelota, 2),
        TasaCrecimiento <= 0.15, REPT(Pelota, 3),
        TasaCrecimiento <= 0.30, REPT(Pelota, 4),
        REPT(Pelota, 5)
    )

### 🔸 Facturación promedio
```dax
AVERAGE(Ventas[Total Facturado])
```

### 🔸 Facturación Total
```dax
SUM(Ventas[Total Facturado])
```

### 🔸 Margen Alto (%)
```dax
CALCULATE(
    COUNTROWS(Ventas),
    Ventas[Margen_Evaluado] = "Margen Alto"
) / COUNTROWS(Ventas)
```

### 🔸 Margen Bajo (%)
```dax
CALCULATE(
    COUNTROWS(Ventas),
    Ventas[Margen_Evaluado] = "Margen Bajo"
) / COUNTROWS(Ventas)
```

### 🔸 Margen Medio (%)
```dax
CALCULATE(
    COUNTROWS(Ventas),
    Ventas[Margen_Evaluado] = "Margen Medio"
) / COUNTROWS(Ventas)
```

### 🔸 Margen Operativo
```dax
SUM(Ventas[Margen Operativo])
```

### 🔸 Rentabilidad
```dax
SUM(Ventas[Rentabilidad ($) ])
```

### 🔸 Tasa Facturación YoY
```dax
VAR __PREV_YEAR = CALCULATE([Facturación Total], DATEADD('CALENDARIO'[Fecha], -1, YEAR))
RETURN IF(
    NOT(ISBLANK(__PREV_YEAR)),
    DIVIDE([Facturación Total] - __PREV_YEAR, __PREV_YEAR),
    BLANK()
)
```

### 🔸 Total Facturado
```dax
Fact. Total = SUM(Nike_Ventas[Total Facturado])
```


### 🔸 Ventas Totales
```dax
Cant de Ventas = COUNTROWS(Nike_Ventas)
```

### 🔸 Análisis de eficiencia
```dax
{
    ("Margen Bajo (%)", NAMEOF('Tabla Medidas'[Margen Bajo (%)]), 0),
    ("Margen Medio (%)", NAMEOF('Tabla Medidas'[Margen Medio (%)]), 1),
    ("Margen Alto (%)", NAMEOF('Tabla Medidas'[Margen Alto (%)]), 2)
}


## 📁 Evolucion comercial

Evaluación Tasa = 
VAR Zapatilla = UNICHAR(128095) 
VAR __PREV_MONTH = 
    CALCULATE([Fact. Total], DATEADD('Calendario DAX'[Date], -1, MONTH)) 
VAR __VARIACION = 
    DIVIDE([Fact. Total] - __PREV_MONTH, __PREV_MONTH, 0) 

RETURN 
IF(SELECTEDVALUE('Calendario DAX'[Mes]) IN VALUES('Calendario DAX'[Mes]),
    SWITCH(
        TRUE(),
        __VARIACION < 0, REPT(Zapatilla, 1),  
        __VARIACION <= 0.05, REPT(Zapatilla, 2), 
        __VARIACION <= 0.10, REPT(Zapatilla, 3), 
        __VARIACION <= 0.20, REPT(Zapatilla, 4), 
        REPT(Zapatilla, 5)  
    ), 
    BLANK()
)

### 🔸 Mensaje_Resumen
```dax
"Explore los productos más rentables, campañas más efectivas y el comportamiento mensual en el año " &
SELECTEDVALUE('Calendario'[Año])
```
## 📁 Conclusión

### 🔸 Mediana
```dax
Mediana = PERCENTILE.INC(Nike_Ventas[Rentabilidad ($)], 0.50)
```

### 🔸 Margen Operativo
```dax
Margen Operativo = SUM(Nike_Ventas[Margen Operativo])
```

## 📁 Tooltips

### 🔸 Narrativa Tooltip
```dax

```
Texto Tooltip = 
VAR Texto = "Para el Estado de "
VAR Texto1 = ", se consolidó una facturación de "
VAR Texto2 = ", una rentabilidad de "
VAR Texto3 = " y un total de "
VAR Texto4 = " ventas."
VAR Estados = SELECTEDVALUE ( Estado[State] )
VAR Facturacion = FORMAT ( [Fact. Total], "$ #,##0" )
VAR Rentabilidad = FORMAT ( [Rentabilidad], "$ #,##0" )
VAR CantidadVentas = FORMAT ( [Cant de Ventas], "#,##0" )

RETURN  
    Texto & Estados &
    Texto1 & Facturacion &
    Texto2 & Rentabilidad &
    Texto3 & CantidadVentas & Texto4



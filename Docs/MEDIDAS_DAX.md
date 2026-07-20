# 📊 Medidas DAX – Tablero Power BI Nike (2020–2024)

Este documento describe las medidas DAX utilizadas en el proyecto, organizadas por carpeta funcional según su ubicación en Power BI.

---

## 📁 Análisis Gral.

### 🔸 Calificación Crecimiento
```dax
VAR Estrella = UNICHAR(11088)
VAR Crecimiento = 
    VAR Ventas_Actuales = [Facturación Total]
    VAR Ventas_Anteriores = CALCULATE([Facturación Total], DATEADD('CALENDARIO'[Fecha], -1, QUARTER))
    RETURN DIVIDE(Ventas_Actuales - Ventas_Anteriores, Ventas_Anteriores)

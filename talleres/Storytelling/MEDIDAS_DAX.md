# Medidas DAX del taller

[← Inicio](00_INICIO.md)

```DAX
Ventas Totales =
SUM(Datos[Ventas])

Ventas Brutas =
SUM(Datos[Venta_bruta])

Costo Total =
SUM(Datos[Costo])

Utilidad Total =
SUM(Datos[Utilidad])

Margen % =
DIVIDE([Utilidad Total], [Ventas Totales])

Descuentos Totales =
SUM(Datos[Descuento])

Tasa de Descuento % =
DIVIDE([Descuentos Totales], [Ventas Brutas])

Pedidos =
DISTINCTCOUNT(Datos[Pedido])

Unidades Vendidas =
SUM(Datos[Unidades])

Ticket Promedio =
DIVIDE([Ventas Totales], [Pedidos])

Pedidos a Tiempo =
CALCULATE(
    [Pedidos],
    Datos[Entrega_a_tiempo] = "Sí"
)

OTIF % =
DIVIDE([Pedidos a Tiempo], [Pedidos])

Días Promedio de Entrega =
AVERAGE(Datos[Días_entrega])

Devoluciones =
CALCULATE(
    [Pedidos],
    Datos[Devolución] = "Sí"
)

Tasa de Devolución % =
DIVIDE([Devoluciones], [Pedidos])

Satisfacción Promedio =
AVERAGE(Datos[Satisfacción])

Pedidos Tardíos =
CALCULATE(
    [Pedidos],
    Datos[Entrega_a_tiempo] = "No"
)

Tasa de Entregas Tardías % =
DIVIDE([Pedidos Tardíos], [Pedidos])

Meta Margen =
CALCULATE(
    MAX(Metas[Meta]),
    Metas[Indicador] = "Margen"
)

Meta Entrega a Tiempo =
CALCULATE(
    MAX(Metas[Meta]),
    Metas[Indicador] = "Entrega a tiempo"
)

Meta Devolución =
CALCULATE(
    MAX(Metas[Meta]),
    Metas[Indicador] = "Tasa de devolución"
)

Meta Satisfacción =
CALCULATE(
    MAX(Metas[Meta]),
    Metas[Indicador] = "Satisfacción promedio"
)

Ventas Mes Anterior =
CALCULATE(
    [Ventas Totales],
    DATEADD(Calendario[Date], -1, MONTH)
)

Variación Ventas % =
DIVIDE(
    [Ventas Totales] - [Ventas Mes Anterior],
    [Ventas Mes Anterior]
)
```

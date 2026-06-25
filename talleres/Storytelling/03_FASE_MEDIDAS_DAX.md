# Fase 3. Medidas DAX

[← Fase anterior](02_FASE_DATOS_MODELO.md) | [Inicio](00_INICIO.md) | [Siguiente: diseño narrativo →](04_FASE_DISENO_NARRATIVO.md)

## Objetivo

Crear las métricas que sostendrán la narrativa.

## Medidas mínimas

Copie las medidas desde [MEDIDAS_DAX.md](MEDIDAS_DAX.md).

Cree al menos:

- Ventas Totales.
- Utilidad Total.
- Margen %.
- Descuentos Totales.
- Tasa de Descuento %.
- Pedidos.
- Ticket Promedio.
- OTIF %.
- Días Promedio de Entrega.
- Tasa de Devolución %.
- Satisfacción Promedio.
- Variación Ventas %.
- Metas.

## Validación

Revise que:

- `Margen % = Utilidad / Ventas`.
- `OTIF % = Pedidos a tiempo / Pedidos`.
- `Tasa de devolución = Devoluciones / Pedidos`.
- las medidas cambien al usar filtros.

## Medida narrativa recomendada

```DAX
Título Rentabilidad =
VAR MargenActual = [Margen %]
VAR Meta = [Meta Margen]
VAR Diferencia = MargenActual - Meta
RETURN
IF(
    MargenActual >= Meta,
    "El margen supera la meta en "
        & FORMAT(Diferencia, "0.0 p.p."),
    "El margen está por debajo de la meta en "
        & FORMAT(ABS(Diferencia), "0.0 p.p.")
)
```

## Error que debe evitarse

Promediar porcentajes fila a fila para calcular el margen agregado.

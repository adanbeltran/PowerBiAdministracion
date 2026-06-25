# Fase 2. Datos y modelo

[← Fase anterior](01_FASE_CONTEXTO.md) | [Inicio](00_INICIO.md) | [Siguiente: medidas DAX →](03_FASE_MEDIDAS_DAX.md)

## Objetivo

Importar, validar y modelar el dataset.

## Paso 1. Cargar el archivo

1. Abra Power BI Desktop.
2. Seleccione **Inicio → Obtener datos → Excel**.
3. Cargue [Dataset_Taller_Storytelling_PowerBI.xlsx](Dataset_Taller_Storytelling_PowerBI.xlsx).
4. Seleccione las hojas:
   - Datos.
   - Diccionario.
   - Metas.
5. Pulse **Transformar datos**.

## Paso 2. Revisar tipos

Verifique especialmente:

- `Fecha`: fecha.
- `Unidades`: entero.
- `Ventas`, `Costo`, `Utilidad`: decimal fijo.
- `Tasa_descuento`, `Margen`: porcentaje.
- `Satisfacción`: decimal.
- Campos categóricos: texto.

## Paso 3. Validar calidad

Compruebe:

- pedidos duplicados;
- valores nulos;
- ventas negativas;
- satisfacción fuera de 1 a 5;
- categorías inconsistentes;
- fechas fuera de 2025.

## Paso 4. Crear tabla calendario

```DAX
Calendario =
ADDCOLUMNS(
    CALENDAR(
        MIN(Datos[Fecha]),
        MAX(Datos[Fecha])
    ),
    "Año", YEAR([Date]),
    "MesNumero", MONTH([Date]),
    "Mes", FORMAT([Date], "MMM"),
    "AñoMes", FORMAT([Date], "YYYY-MM"),
    "Trimestre", "T" & FORMAT([Date], "Q")
)
```

## Paso 5. Ordenar y relacionar

1. Ordene `Mes` por `MesNumero`.
2. Relacione:

```text
Calendario[Date] 1 ───── * Datos[Fecha]
```

3. Marque `Calendario` como tabla de fechas.

## Resultado esperado

Modelo simple con:

- una tabla de hechos: `Datos`;
- una tabla calendario;
- una tabla de metas;
- una tabla diccionario.

## Error que debe evitarse

Construir visualizaciones con campos de fecha sin una tabla calendario.

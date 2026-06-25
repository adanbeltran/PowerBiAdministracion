# Construcción del informe Power BI: Storytelling empresarial

## Restricción técnica

El archivo `.pbix` debe generarse o convertirse mediante Power BI Desktop. Este paquete contiene todos los insumos para reproducir el informe: datos, medidas DAX, tema, storyboard y material académico.

## 1. Carga de datos

1. Abra Power BI Desktop.
2. Seleccione **Obtener datos > Texto/CSV**.
3. Cargue `financial_sample_storytelling_powerbi.csv`.
4. Verifique que `Date` sea fecha y que las métricas sean numéricas.
5. Renombre la tabla como `financial_sample_storytelling_powerbi`.

## 2. Tema

Seleccione **Vista > Temas > Examinar temas** y cargue:

`Tema_Storytelling_Empresarial.json`

## 3. Medidas

Cree las medidas incluidas en `Medidas_DAX.txt`.

## 4. Página 1: Panorama ejecutivo

**Título narrativo:**  
Enterprise vende aproximadamente 19,6 millones, pero destruye cerca de 614 mil de utilidad.

**Visuales:**
- Tarjeta: Ventas.
- Tarjeta: Utilidad.
- Tarjeta: Margen %.
- Barras horizontales: Segment en eje y Utilidad en valores.
- Segmentador opcional: Country.
- Cuadro de texto con la limitación temporal.

**Configuración narrativa:**
- Ordenar las barras por Utilidad ascendente.
- Resaltar Enterprise.
- Mantener una línea o referencia visual en cero.
- No utilizar gráfico circular.

## 5. Página 2: Diagnóstico de Enterprise

Aplique filtro de página: `Segment = Enterprise`.

**Título narrativo:**  
El precio neto promedio no cubre el costo unitario de Enterprise.

**Visuales:**
- Tarjeta: Ventas por Unidad.
- Tarjeta: Costo por Unidad.
- Tarjeta: Utilidad por Unidad.
- Barras: Discount_Band vs Margen %.
- Barras: Product vs Utilidad.
- Tooltip con Ventas, Costo de Ventas, Descuentos, Margen % y Unidades.

**Notas:**
- Utilice “coincide con” o “se asocia con”.
- No afirme que los descuentos causaron todas las pérdidas.
- Evite una tabla extensa en la página principal.

## 6. Página 3: Acción y seguimiento

**Título narrativo:**  
La intervención debe priorizar descuentos altos y el producto Carretera dentro de Enterprise.

**Visuales:**
- Matriz: Product por Discount_Band.
- Indicadores: Margen %, Utilidad por Unidad y Tasa de Descuento %.
- Cuadro de recomendación con acción, responsable, plazo e indicador.
- Nota metodológica.

## 7. Navegación e interacción

- Cree botones: **Panorama**, **Diagnóstico**, **Acción**.
- Cree marcadores para:
  1. Vista general.
  2. Enterprise.
  3. Enterprise con banda alta.
- Cree una página de drillthrough por Product.
- Cree una página tooltip de tamaño Tooltip.

## 8. Controles de calidad

- No compare 2013 y 2014 como años completos: 2013 contiene solo septiembre a diciembre.
- No promedie porcentajes fila a fila para calcular margen.
- No use títulos genéricos.
- No utilice color sin función.
- No confunda correlación con causalidad.
- No use narración inteligente sin verificar cifras y filtros.

## 9. Guardado

Cuando termine, seleccione **Archivo > Guardar como** y guarde el informe en formato `.pbix`.
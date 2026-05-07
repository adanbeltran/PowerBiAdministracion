# Taller de Power BI con datos Olist/Kaggle

## Análisis de ventas por mes, producto, categoría y tendencias usando CRISP-DM

---

## 1. Propósito del taller

Construir un dashboard analítico en **Power BI** para estudiar el comportamiento de ventas del marketplace Olist, usando datos reales descargados de Kaggle.

El análisis se enfocará en responder:

> ¿Cómo evolucionan las ventas de Olist por mes, producto y categoría, y qué tendencias comerciales se pueden identificar?

El taller seguirá la metodología **CRISP-DM**, porque permite organizar un proyecto de analítica desde la comprensión del problema hasta la comunicación de resultados.

---

## 2. Justificación de la metodología CRISP-DM

CRISP-DM organiza un proyecto de análisis de datos en seis fases:

| Fase CRISP-DM | Aplicación en este taller |
|---|---|
| 1. Comprensión del negocio | Definir preguntas comerciales sobre ventas, productos y categorías |
| 2. Comprensión de los datos | Revisar tablas, columnas, granularidad y calidad de los datos |
| 3. Preparación de los datos | Limpiar, transformar, relacionar tablas y crear calendario |
| 4. Modelado | Construir modelo de datos y medidas DAX |
| 5. Evaluación | Validar resultados, detectar errores y analizar tendencias |
| 6. Despliegue | Crear dashboard final y presentar hallazgos |

En este taller no se usará “modelado” en el sentido de machine learning, sino como **modelado analítico y semántico en Power BI**: relaciones, medidas DAX, indicadores y visualizaciones.

---

## 3. Archivos utilizados

Los archivos cargados corresponden al dataset público de Olist:

| Archivo | Uso en el análisis |
|---|---|
| `olist_orders_dataset.csv` | Pedidos, fechas y estado de la orden |
| `olist_order_items_dataset.csv` | Productos vendidos, precio, flete y vendedor |
| `olist_products_dataset.csv` | Información del producto y categoría |
| `product_category_name_translation.csv` | Traducción de categorías al inglés |
| `olist_customers_dataset.csv` | Información geográfica del cliente |
| `olist_sellers_dataset.csv` | Información geográfica del vendedor |
| `olist_order_payments_dataset.csv` | Métodos y valores de pago |
| `olist_order_reviews_dataset.csv` | Calificación del cliente |
| `olist_geolocation_dataset.csv` | Coordenadas geográficas |

Para el análisis central de ventas se usarán principalmente:

```text
olist_orders_dataset
olist_order_items_dataset
olist_products_dataset
product_category_name_translation
```

---

# Fase 1. Comprensión del negocio

## Objetivo de la fase

Definir claramente qué problema analítico se quiere resolver antes de cargar gráficos o crear medidas.

En CRISP-DM, esta fase es esencial porque evita que el análisis sea una simple exploración de tablas sin propósito. En Power BI, esto se traduce en construir un dashboard orientado a preguntas de negocio.

---

## Paso 1. Definir el caso de negocio

La empresa ficticia **Olist Analytics Team** quiere entender cómo se comportan sus ventas a lo largo del tiempo.

El equipo comercial necesita saber:

1. Qué meses tuvieron más ventas.
2. Qué categorías generaron más ingresos.
3. Qué productos tuvieron mejor desempeño.
4. Qué tendencias aparecen en el tiempo.
5. Si existen meses atípicos o periodos incompletos.
6. Qué categorías tienen alto valor comercial.
7. Qué productos o categorías podrían priorizarse en campañas.

---

## Paso 2. Formular preguntas analíticas

Las preguntas principales del taller serán:

- ¿Cuánto se vendió por mes?
- ¿Qué categorías concentran las ventas?
- ¿Qué productos generan mayor ingreso?
- ¿Qué productos se venden más en cantidad?
- ¿Cómo cambia la venta mensual?
- ¿Qué meses muestran crecimiento o caída?
- ¿Qué categorías presentan tendencia creciente?

---

## Paso 3. Definir indicadores clave

| Indicador | Interpretación |
|---|---|
| Ventas producto | Suma del precio de los productos vendidos |
| Flete | Suma del valor de envío |
| Ventas con flete | Precio + flete |
| Pedidos | Número de órdenes únicas |
| Ítems vendidos | Número de líneas de producto vendidas |
| Productos únicos | Productos diferentes vendidos |
| Ticket promedio | Venta promedio por pedido |
| Precio promedio | Precio promedio por ítem |
| Variación mensual | Cambio de ventas frente al mes anterior |
| % variación mensual | Crecimiento o caída porcentual mensual |
| Ranking de categoría | Posición de cada categoría según ventas |

---

## Paso 4. Definir alcance del análisis

Para evitar errores de interpretación, el análisis principal se hará con pedidos:

```text
order_status = delivered
```

Esto significa que se analizarán ventas entregadas, no simplemente pedidos creados.

Justificación:

- Un pedido cancelado no representa una venta final.
- Un pedido no entregado puede distorsionar los ingresos reales.
- El estado `delivered` permite analizar ventas más consolidadas.

---

## Entregable de la fase 1

Una breve definición del problema:

> El objetivo del análisis es identificar la evolución mensual de las ventas entregadas de Olist, determinar las categorías y productos con mayor aporte comercial, y detectar tendencias o meses atípicos que puedan orientar decisiones de negocio.

---

# Fase 2. Comprensión de los datos

## Objetivo de la fase

Explorar las tablas disponibles, entender su granularidad y decidir cuáles son útiles para el análisis.

En CRISP-DM, esta fase permite conocer la estructura, calidad y limitaciones de los datos antes de transformarlos.

---

## Paso 1. Revisar las tablas principales

### Tabla `olist_orders_dataset`

Contiene un registro por pedido.

| Columna | Significado |
|---|---|
| `order_id` | Identificador del pedido |
| `customer_id` | Identificador del cliente |
| `order_status` | Estado del pedido |
| `order_purchase_timestamp` | Fecha y hora de compra |
| `order_approved_at` | Fecha de aprobación |
| `order_delivered_customer_date` | Fecha de entrega al cliente |
| `order_estimated_delivery_date` | Fecha estimada de entrega |

Uso en el taller:

> Esta tabla será la base temporal del análisis.

---

### Tabla `olist_order_items_dataset`

Contiene los productos incluidos en cada pedido.

| Columna | Significado |
|---|---|
| `order_id` | Pedido al que pertenece el ítem |
| `order_item_id` | Número del ítem dentro del pedido |
| `product_id` | Producto vendido |
| `seller_id` | Vendedor |
| `price` | Precio del producto |
| `freight_value` | Valor del flete |

Uso en el taller:

> Esta será la tabla principal de ventas.

Importante:

- La venta del producto se calculará con `price`.
- El flete se analizará por separado con `freight_value`.

---

### Tabla `olist_products_dataset`

Contiene información descriptiva de los productos.

| Columna | Significado |
|---|---|
| `product_id` | Identificador del producto |
| `product_category_name` | Categoría en portugués |
| `product_weight_g` | Peso del producto |
| `product_length_cm` | Largo |
| `product_height_cm` | Alto |
| `product_width_cm` | Ancho |

Uso en el taller:

> Permite analizar ventas por producto y categoría.

---

### Tabla `product_category_name_translation`

Contiene la traducción de categorías.

| Columna | Significado |
|---|---|
| `product_category_name` | Categoría original en portugués |
| `product_category_name_english` | Categoría traducida al inglés |

Uso en el taller:

> Permite mostrar categorías con nombres más comprensibles.

---

## Paso 2. Entender la granularidad

La granularidad indica qué representa cada fila de una tabla.

| Tabla | Granularidad |
|---|---|
| `olist_orders_dataset` | Una fila por pedido |
| `olist_order_items_dataset` | Una fila por producto dentro de un pedido |
| `olist_products_dataset` | Una fila por producto |
| `olist_customers_dataset` | Una fila por cliente asociado a pedido |
| `olist_order_payments_dataset` | Una o varias filas por pedido, según pagos |
| `olist_order_reviews_dataset` | Una o varias filas por pedido, según reseñas |

Este punto es importante porque evita errores de duplicación.

Ejemplo:

> Si se suman pagos junto con productos sin cuidado, el valor pagado puede duplicarse cuando un pedido tiene varios productos.

Por eso, para ventas por producto se usará:

```text
olist_order_items_dataset[price]
```

No se recomienda usar directamente:

```text
olist_order_payments_dataset[payment_value]
```

para el ranking de productos o categorías.

---

## Paso 3. Diagnóstico inicial de los datos

Con los archivos cargados se observa aproximadamente:

| Elemento | Valor aproximado |
|---|---:|
| Pedidos totales | 99.441 |
| Pedidos entregados | 96.478 |
| Ítems vendidos | 112.650 |
| Productos únicos | 32.951 |
| Clientes únicos | 96.096 |
| Vendedores | 3.095 |
| Rango temporal | septiembre de 2016 a octubre de 2018 |

Para análisis de ventas mensuales conviene tener cuidado con:

```text
2016-09
2016-12
2018-09
2018-10
```

Algunos meses pueden estar incompletos o tener pocos registros.

---

## Paso 4. Identificar posibles problemas de calidad

| Problema | Tratamiento sugerido |
|---|---|
| Categorías nulas | Reemplazar por “Sin categoría” |
| Fechas con valores nulos | Mantenerlas, pero usar fecha de compra como eje principal |
| Pedidos cancelados | Excluir del análisis principal |
| Pagos a nivel pedido | No mezclarlos directamente con productos |
| Meses incompletos | Señalarlos en la interpretación |

---

## Entregable de la fase 2

Una tabla resumen con:

- Nombre de la tabla.
- Número de filas.
- Nivel de granularidad.
- Uso dentro del modelo.
- Riesgos de calidad.

---

# Fase 3. Preparación de los datos

## Objetivo de la fase

Limpiar, transformar y organizar las tablas para que puedan ser analizadas correctamente en Power BI.

En CRISP-DM, esta fase suele ser la más extensa, porque la calidad del análisis depende directamente de la calidad de la preparación.

---

## Paso 1. Cargar los datos en Power BI

En Power BI Desktop:

```text
Inicio → Obtener datos → Texto/CSV
```

Cargar los archivos:

```text
olist_orders_dataset.csv
olist_order_items_dataset.csv
olist_products_dataset.csv
product_category_name_translation.csv
olist_customers_dataset.csv
olist_sellers_dataset.csv
olist_order_payments_dataset.csv
olist_order_reviews_dataset.csv
```

El archivo `olist_geolocation_dataset.csv` puede dejarse para una fase avanzada, porque es grande y no es necesario para el análisis inicial de ventas.

---

## Paso 2. Revisar tipos de datos

### En `olist_orders_dataset`

Convertir a fecha/hora:

```text
order_purchase_timestamp
order_approved_at
order_delivered_carrier_date
order_delivered_customer_date
order_estimated_delivery_date
```

Explicación:

> Power BI necesita que las fechas tengan tipo fecha o fecha/hora para construir series temporales, jerarquías de fecha y cálculos mensuales.

### En `olist_order_items_dataset`

Verificar:

| Columna | Tipo |
|---|---|
| `price` | Número decimal |
| `freight_value` | Número decimal |
| `shipping_limit_date` | Fecha/hora |
| `order_item_id` | Número entero |

Explicación:

> Las medidas de ventas y flete dependen de que `price` y `freight_value` estén correctamente definidos como valores numéricos.

---

## Paso 3. Crear fecha de compra

En Power Query, crear una nueva columna en `olist_orders_dataset`:

```powerquery
Fecha Compra = Date.From([order_purchase_timestamp])
```

Explicación:

> La columna `order_purchase_timestamp` incluye fecha y hora. Para análisis mensual, diario o anual, conviene tener una columna de fecha limpia sin hora.

---

## Paso 4. Filtrar pedidos entregados

En `olist_orders_dataset`, filtrar:

```text
order_status = delivered
```

Explicación:

> El análisis de ventas se enfocará en pedidos entregados, porque representan transacciones más consolidadas. Esto evita que pedidos cancelados, creados o no entregados distorsionen el análisis comercial.

Alternativa metodológica:

> También se pueden conservar todos los estados y crear un segmentador de `order_status`. Sin embargo, para el taller inicial se recomienda trabajar con `delivered`.

---

## Paso 5. Traducir categorías de producto

En Power Query, combinar:

```text
olist_products_dataset
```

con:

```text
product_category_name_translation
```

Usar la columna común:

```text
product_category_name
```

Después expandir:

```text
product_category_name_english
```

Renombrar como:

```text
Categoría
```
<img width="868" height="813" alt="image" src="https://github.com/user-attachments/assets/fdbca9bd-19ce-484c-a740-1020df5b7aeb" />


Explicación:

> La tabla de productos tiene categorías en portugués. La tabla de traducción permite mostrar categorías en inglés, lo cual facilita la interpretación del dashboard.

---

## Paso 6. Tratar categorías nulas

En la columna traducida de categoría, reemplazar valores nulos por:

```text
Sin categoría
```

Explicación:

> Si existen productos sin categoría, no deben desaparecer del análisis. Agruparlos como “Sin categoría” permite mantener la trazabilidad de las ventas.

---

## Paso 7. Crear columnas auxiliares

En `olist_order_items_dataset`, crear:

```powerquery
Venta Producto = [price]
```

```powerquery
Venta Total con Flete = [price] + [freight_value]
```

Explicación:

- `Venta Producto` permite medir el ingreso asociado al producto.
- `Venta Total con Flete` permite analizar el valor total cobrado por ítem incluyendo transporte.

Recomendación:

- Para ranking de productos y categorías, usar `Venta Producto`.
- Para análisis logístico o costo para el cliente, usar `Venta Total con Flete`.

---

## Paso 8. Cerrar y aplicar

Luego de las transformaciones:

```text
Inicio → Cerrar y aplicar
```

Power BI cargará las tablas transformadas al modelo.

---

## Entregable de la fase 3

El estudiante debe entregar:

- Modelo cargado en Power BI.
- Fechas correctamente transformadas.
- Categorías traducidas.
- Pedidos filtrados o segmentados por estado.
- Columnas auxiliares creadas.

---

# Fase 4. Modelado

## Objetivo de la fase

Construir el modelo analítico, definir relaciones y crear medidas DAX.

En CRISP-DM, el modelado transforma los datos preparados en una estructura capaz de responder preguntas. En Power BI, esto implica relaciones, tabla calendario, medidas y jerarquías.

---

## Paso 1. Construir el modelo de relaciones

Relaciones recomendadas:

| Tabla origen | Campo | Tabla destino | Campo | Tipo |
|---|---|---|---|---|
| `olist_orders_dataset` | `order_id` | `olist_order_items_dataset` | `order_id` | 1 a muchos |
| `olist_products_dataset` | `product_id` | `olist_order_items_dataset` | `product_id` | 1 a muchos |
| `olist_customers_dataset` | `customer_id` | `olist_orders_dataset` | `customer_id` | 1 a muchos |
| `olist_sellers_dataset` | `seller_id` | `olist_order_items_dataset` | `seller_id` | 1 a muchos |
| `olist_orders_dataset` | `order_id` | `olist_order_payments_dataset` | `order_id` | 1 a muchos |
| `olist_orders_dataset` | `order_id` | `olist_order_reviews_dataset` | `order_id` | 1 a muchos |

Explicación:

> La tabla de ventas real es `olist_order_items_dataset`, porque allí está el precio del producto. La tabla de pedidos aporta la fecha. La tabla de productos aporta la categoría.

---

## Paso 2. Evitar errores de duplicación

No se recomienda sumar directamente:

```text
payment_value
```

junto con:

```text
price
```

en una misma visualización por producto.

Explicación:

> `payment_value` está a nivel de pedido. `price` está a nivel de ítem. Un pedido puede tener varios productos. Si se cruza pago con productos sin control, el pago puede repetirse varias veces.

Para este taller:

```text
Ventas = SUM(price)
```

---

## Paso 3. Crear tabla calendario

En Power BI, ir a:

```text
Modelado → Nueva tabla
```

Crear:

```DAX
Calendario =
ADDCOLUMNS(
    CALENDAR(
        MIN(olist_orders_dataset[Fecha Compra]),
        MAX(olist_orders_dataset[Fecha Compra])
    ),
    "Año", YEAR([Date]),
    "Mes Número", MONTH([Date]),
    "Mes", FORMAT([Date], "MMMM"),
    "Año-Mes", FORMAT([Date], "YYYY-MM"),
    "Trimestre", "T" & FORMAT([Date], "Q")
)
```

Explicación:

> La tabla calendario permite analizar ventas por año, mes, trimestre y construir medidas de tendencia temporal como variación mensual.

Relacionar:

```text
Calendario[Date] → olist_orders_dataset[Fecha Compra]
```

Tipo de relación:

```text
Uno a muchos
```

---

## Paso 4. Ordenar el mes correctamente

Seleccionar la columna:

```text
Calendario[Mes]
```

Luego:

```text
Ordenar por columna → Calendario[Mes Número]
```

Explicación:

> Sin este paso, Power BI puede ordenar los meses alfabéticamente: abril, agosto, diciembre, etc. Eso daña la lectura temporal.

---

## Paso 5. Crear medidas de ventas

Crear una tabla de medidas o usar una tabla existente.

### Ventas producto

```DAX
Ventas Producto =
SUM(olist_order_items_dataset[price])
```

Explicación:

> Mide el valor vendido excluyendo flete.

### Flete

```DAX
Flete =
SUM(olist_order_items_dataset[freight_value])
```

Explicación:

> Mide el valor total del transporte asociado a los ítems vendidos.

### Ventas con flete

```DAX
Ventas con Flete =
[Ventas Producto] + [Flete]
```

Explicación:

> Permite analizar el valor total pagado por producto más envío.

---

## Paso 6. Crear medidas de volumen

### Pedidos

```DAX
Pedidos =
DISTINCTCOUNT(olist_order_items_dataset[order_id])
```

Explicación:

> Cuenta pedidos únicos. Es mejor que contar filas, porque un pedido puede tener varios productos.

### Ítems vendidos

```DAX
Items Vendidos =
COUNTROWS(olist_order_items_dataset)
```

Explicación:

> Cuenta cuántas líneas de producto fueron vendidas.

### Productos únicos vendidos

```DAX
Productos Únicos Vendidos =
DISTINCTCOUNT(olist_order_items_dataset[product_id])
```

Explicación:

> Indica cuántos productos diferentes aparecen en las ventas.

---

## Paso 7. Crear medidas de ticket y precio promedio

### Ticket promedio por pedido

```DAX
Ticket Promedio por Pedido =
DIVIDE([Ventas Producto], [Pedidos])
```

Explicación:

> Mide cuánto se vende en promedio por pedido.

### Precio promedio por ítem

```DAX
Precio Promedio por Ítem =
AVERAGE(olist_order_items_dataset[price])
```

Explicación:

> Mide el precio promedio de los productos vendidos.

---

## Paso 8. Crear medidas de tendencia

### Ventas del mes anterior

```DAX
Ventas Mes Anterior =
CALCULATE(
    [Ventas Producto],
    DATEADD(Calendario[Date], -1, MONTH)
)
```

Explicación:

> Obtiene el valor de ventas del mes inmediatamente anterior.

### Variación mensual absoluta

```DAX
Variación Mensual Ventas =
[Ventas Producto] - [Ventas Mes Anterior]
```

Explicación:

> Mide cuánto aumentaron o disminuyeron las ventas frente al mes anterior.

### Variación mensual porcentual

```DAX
% Variación Mensual Ventas =
DIVIDE(
    [Variación Mensual Ventas],
    [Ventas Mes Anterior]
)
```

Explicación:

> Mide el crecimiento o caída relativa de las ventas.

Formato recomendado:

```text
Porcentaje
```

---

## Paso 9. Crear medidas de participación y ranking

### Participación por categoría

```DAX
Participación Ventas Categoría =
DIVIDE(
    [Ventas Producto],
    CALCULATE(
        [Ventas Producto],
        ALL(olist_products_dataset[product_category_name_english])
    )
)
```

Explicación:

> Mide qué porcentaje del total de ventas representa cada categoría.

### Ranking de categoría

```DAX
Ranking Categoría =
RANKX(
    ALL(olist_products_dataset[product_category_name_english]),
    [Ventas Producto],
    ,
    DESC
)
```

Explicación:

> Ordena las categorías desde la que más vende hasta la que menos vende.

---

## Entregable de la fase 4

El estudiante debe entregar:

- Modelo de relaciones correcto.
- Tabla calendario funcional.
- Medidas DAX creadas.
- Indicadores validados en tarjetas.

---

# Fase 5. Evaluación

## Objetivo de la fase

Validar que el modelo y las visualizaciones respondan correctamente a las preguntas del negocio.

En CRISP-DM, evaluar no significa solo mirar si los gráficos son atractivos. Significa comprobar que los resultados sean coherentes, útiles y no estén afectados por errores de modelado.

---

## Paso 1. Validar indicadores generales

Crear tarjetas con:

- Ventas Producto.
- Pedidos.
- Items Vendidos.
- Productos Únicos Vendidos.
- Ticket Promedio por Pedido.
- Flete.

Con pedidos entregados, los resultados esperados deben estar cerca de:

| Indicador | Valor esperado aproximado |
|---|---:|
| Pedidos entregados | 96.478 |
| Ítems vendidos | 110.197 |
| Productos únicos vendidos | 32.216 |
| Ventas producto | 13,4 millones aprox. |
| Flete | 2,2 millones aprox. |

Estos valores pueden cambiar ligeramente si se filtran estados, fechas o categorías.

---

## Paso 2. Evaluar ventas mensuales

Crear un gráfico de líneas:

| Elemento | Campo |
|---|---|
| Eje X | `Calendario[Año-Mes]` |
| Eje Y | `[Ventas Producto]` |

Preguntas:

- ¿Cuál fue el mes con más ventas?
- ¿Qué meses presentan crecimiento fuerte?
- ¿Qué meses parecen incompletos?
- ¿La tendencia es creciente, estable o decreciente?

Hallazgo esperado:

> Noviembre de 2017 aparece como uno de los meses más fuertes en ventas. Entre marzo y mayo de 2018 también se observa un nivel alto de ventas. Los meses finales de 2018 deben interpretarse con cuidado porque el dataset no cubre completamente el año.

---

## Paso 3. Evaluar categorías principales

Crear gráfico de barras:

| Elemento | Campo |
|---|---|
| Eje Y | Categoría |
| Eje X | Ventas Producto |

Categorías con alto desempeño esperado:

| Categoría | Interpretación |
|---|---|
| `health_beauty` | Alta venta total |
| `watches_gifts` | Alto valor comercial |
| `bed_bath_table` | Alta rotación |
| `sports_leisure` | Categoría fuerte y estable |
| `computers_accessories` | Categoría relevante por valor |

Preguntas:

- ¿Qué categorías concentran las ventas?
- ¿Las categorías líderes venden por volumen o por precio?
- ¿Hay dependencia de pocas categorías?

---

## Paso 4. Evaluar productos

Crear una tabla con:

```text
product_id
Categoría
Ventas Producto
Items Vendidos
Precio Promedio por Ítem
Pedidos
```

Preguntas:

- ¿Cuál producto tiene mayor venta total?
- ¿Cuál producto tiene mayor cantidad vendida?
- ¿Son el mismo producto?
- ¿Qué diferencia hay entre vender mucho en cantidad y vender mucho en valor?

Explicación:

> Un producto puede liderar en unidades vendidas, pero no necesariamente en ingresos. Otro producto puede vender menos unidades, pero generar más ventas por tener mayor precio.

---

## Paso 5. Evaluar tendencias mensuales

Crear una visualización combinada:

| Visual | Campo |
|---|---|
| Línea | Ventas Producto |
| Columnas | Variación Mensual Ventas |

También crear una tabla con:

```text
Año-Mes
Ventas Producto
Ventas Mes Anterior
Variación Mensual Ventas
% Variación Mensual Ventas
```

Preguntas:

- ¿Qué mes tuvo mayor crecimiento?
- ¿Qué mes tuvo mayor caída?
- ¿La caída puede explicarse por menor actividad real o por datos incompletos?

---

## Paso 6. Evaluar coherencia del modelo

Hacer las siguientes comprobaciones:

- Las ventas por categoría deben sumar el total general de ventas.
- Los pedidos no deben duplicarse al cruzar con productos.
- El ranking debe cambiar correctamente al usar filtros.
- Los meses deben aparecer en orden cronológico.
- Los filtros de categoría deben afectar productos y ventas.

Si algo falla, revisar:

- Relaciones.
- Dirección de filtro.
- Tipos de datos.
- Uso incorrecto de `payment_value`.
- Fechas sin relación con calendario.

---

## Entregable de la fase 5

Un informe breve con:

- 3 hallazgos principales.
- 2 validaciones realizadas.
- 1 posible limitación del dataset.
- 1 recomendación comercial.

---

# Fase 6. Despliegue y comunicación

## Objetivo de la fase

Construir el dashboard final y comunicar los resultados de forma clara.

En CRISP-DM, esta fase busca que el análisis sea útil para la toma de decisiones. En Power BI, esto se refleja en páginas organizadas, visualizaciones comprensibles y conclusiones accionables.

---

## Página 1. Resumen ejecutivo

### Objetivo

Dar una visión general del desempeño comercial.

### Visuales sugeridos

| Visual | Campos |
|---|---|
| Tarjeta | Ventas Producto |
| Tarjeta | Pedidos |
| Tarjeta | Items Vendidos |
| Tarjeta | Ticket Promedio por Pedido |
| Línea | Ventas Producto por Año-Mes |
| Columnas | Pedidos por Año-Mes |
| Segmentador | Año |
| Segmentador | Categoría |

### Explicación

Esta página debe responder rápidamente:

- ¿Cuánto se vendió?
- ¿Cuántos pedidos hubo?
- ¿Cuándo se vendió más?
- ¿Cómo evolucionaron las ventas?

### Pregunta de análisis

> ¿Cuál es el comportamiento general de las ventas de Olist durante el periodo analizado?

---

## Página 2. Ventas por categoría

### Objetivo

Identificar qué categorías explican la mayor parte de las ventas.

### Visuales sugeridos

| Visual | Campos |
|---|---|
| Barras horizontales | Ventas Producto por Categoría |
| Treemap | Ventas Producto por Categoría |
| Matriz | Categoría, Ventas, Pedidos, Ticket Promedio |
| Línea | Ventas por Año-Mes y Categoría |
| Segmentador | Categoría |

### Explicación

Esta página permite distinguir categorías:

- De alto valor.
- De alta rotación.
- De comportamiento estable.
- De comportamiento estacional.

### Preguntas de análisis

- ¿Cuáles son las 5 categorías más importantes?
- ¿Qué categoría tiene mayor participación?
- ¿Qué categorías tienen tendencia creciente?
- ¿Qué categorías tienen alto ticket promedio?

---

## Página 3. Ventas por producto

### Objetivo

Analizar el desempeño individual de los productos.

### Visuales sugeridos

| Visual | Campos |
|---|---|
| Tabla | product_id, Categoría, Ventas Producto |
| Tabla | product_id, Items Vendidos |
| Dispersión | Precio Promedio vs Items Vendidos |
| Barras | Top 10 productos por ventas |
| Segmentador | Categoría |

### Explicación

Esta página permite diferenciar:

- Producto líder por ingreso.
- Producto líder por cantidad.
- Producto de alto precio.
- Producto de alta rotación.

### Preguntas de análisis

- ¿Qué producto genera más ventas?
- ¿Qué producto vende más unidades?
- ¿Los productos más vendidos pertenecen a las categorías líderes?
- ¿Existen productos con pocas unidades pero alto valor?

---

## Página 4. Tendencias mensuales

### Objetivo

Analizar crecimiento, caída y comportamiento temporal.

### Visuales sugeridos

| Visual | Campos |
|---|---|
| Línea | Ventas Producto por Año-Mes |
| Columnas | Variación Mensual Ventas |
| Línea | % Variación Mensual Ventas |
| Matriz con formato condicional | Categoría vs Año-Mes |
| Segmentador | Año |

### Explicación

Esta página debe permitir detectar:

- Meses pico.
- Meses de caída.
- Periodos de crecimiento.
- Categorías con comportamiento estacional.
- Datos posiblemente incompletos.

### Preguntas de análisis

- ¿Qué meses tuvieron mayor crecimiento?
- ¿Qué meses tuvieron caídas relevantes?
- ¿Qué categorías impulsaron los picos?
- ¿La tendencia general es positiva o negativa?

---

## Página 5. Calidad comercial

### Objetivo

Relacionar ventas con satisfacción del cliente.

### Medidas DAX

```DAX
Calificación Promedio =
AVERAGE(olist_order_reviews_dataset[review_score])
```

```DAX
Cantidad Reviews =
COUNTROWS(olist_order_reviews_dataset)
```

### Visuales sugeridos

| Visual | Campos |
|---|---|
| Tarjeta | Calificación Promedio |
| Barras | Calificación Promedio por Categoría |
| Dispersión | Ventas Producto vs Calificación Promedio |
| Tabla | Categoría, Ventas, Calificación Promedio, Pedidos |

### Explicación

Esta página permite identificar categorías que venden mucho, pero podrían tener problemas de satisfacción.

### Preguntas de análisis

- ¿Las categorías que más venden tienen buena calificación?
- ¿Qué categorías combinan altas ventas y baja satisfacción?
- ¿Dónde debería priorizar Olist acciones de mejora?

---

# Actividad central del taller

## Situación

El equipo directivo de Olist quiere decidir en qué categorías enfocar sus campañas comerciales del siguiente trimestre.

Cada grupo debe usar Power BI para responder:

> ¿Qué categorías deberían priorizarse y por qué?

La respuesta debe basarse en:

- Ventas.
- Pedidos.
- Productos.
- Tendencia mensual.
- Ticket promedio.
- Satisfacción del cliente.

---

# Secuencia práctica del taller

## Paso 1. Importar datos

Cargar los CSV en Power BI.

Resultado esperado:

> Todas las tablas visibles en el panel de campos.

---

## Paso 2. Transformar fechas y valores numéricos

Asegurar que:

- Las fechas sean fecha/hora.
- `price` y `freight_value` sean decimales.
- `payment_value` sea decimal.
- `review_score` sea entero.

Resultado esperado:

> Los campos se pueden usar correctamente en gráficos y medidas.

---

## Paso 3. Filtrar pedidos entregados

Aplicar filtro a:

```text
order_status = delivered
```

Resultado esperado:

> El análisis se concentra en pedidos completados.

---

## Paso 4. Traducir categorías

Combinar productos con tabla de traducción.

Resultado esperado:

> Cada producto tiene una categoría traducida.

---

## Paso 5. Crear tabla calendario

Crear la tabla `Calendario`.

Resultado esperado:

> El análisis mensual se organiza correctamente.

---

## Paso 6. Crear relaciones

Construir el modelo.

Resultado esperado:

> Las ventas pueden analizarse por fecha, producto, categoría, cliente y vendedor.

---

## Paso 7. Crear medidas DAX

Crear medidas de:

- Ventas.
- Pedidos.
- Items.
- Ticket.
- Ranking.
- Variación mensual.
- Participación.

Resultado esperado:

> Los indicadores responden correctamente a los filtros.

---

## Paso 8. Construir visualizaciones

Crear páginas:

- Resumen ejecutivo.
- Categorías.
- Productos.
- Tendencias.
- Calidad comercial.

Resultado esperado:

> Dashboard navegable y coherente.

---

## Paso 9. Interpretar hallazgos

Cada grupo debe redactar conclusiones.

Formato sugerido:

```text
Hallazgo:
La categoría health_beauty lidera las ventas por valor.

Evidencia:
Aparece como la categoría con mayor suma de Ventas Producto.

Interpretación:
Tiene alto potencial comercial y puede ser priorizada en campañas.

Recomendación:
Diseñar campañas enfocadas en health_beauty y comparar su rentabilidad frente a categorías de alta rotación como bed_bath_table.
```

---

# Reto avanzado 1. Clasificación ABC de categorías

## Objetivo

Clasificar categorías según su contribución a las ventas.

## Idea

Separar categorías en:

| Grupo | Criterio |
|---|---|
| A | Categorías que acumulan cerca del 80 % de las ventas |
| B | Categorías siguientes hasta cerca del 95 % |
| C | Categorías restantes |

## Pregunta

> ¿Las ventas de Olist dependen de pocas categorías o están diversificadas?

---

# Reto avanzado 2. Matriz de calor mensual

## Objetivo

Detectar comportamiento temporal por categoría.

## Visual

Crear matriz:

| Campo | Ubicación |
|---|---|
| Categoría | Filas |
| Año-Mes | Columnas |
| Ventas Producto | Valores |

Aplicar:

```text
Formato condicional por color
```

## Pregunta

> ¿Qué categorías crecen, caen o aparecen solo en ciertos periodos?

---

# Reto avanzado 3. Ventas vs satisfacción

## Objetivo

Cruzar desempeño comercial con experiencia del cliente.

## Visual

Crear gráfico de dispersión:

| Elemento | Campo |
|---|---|
| Eje X | Calificación Promedio |
| Eje Y | Ventas Producto |
| Tamaño | Pedidos |
| Leyenda | Categoría |

## Pregunta

> ¿Qué categorías venden mucho, pero tienen calificaciones relativamente bajas?

---

# Producto final del taller

Cada grupo debe entregar un archivo `.pbix` con mínimo cinco páginas:

1. Resumen ejecutivo.
2. Ventas por categoría.
3. Ventas por producto.
4. Tendencias mensuales.
5. Calidad comercial.

Además, debe entregar una conclusión ejecutiva con:

1. Hallazgo principal.
2. Evidencia visual.
3. Interpretación del negocio.
4. Recomendación.

---

# Rúbrica de evaluación

| Criterio | Peso |
|---|---:|
| Comprensión del problema de negocio | 10 % |
| Limpieza y preparación de datos | 15 % |
| Modelo relacional correcto | 20 % |
| Medidas DAX | 20 % |
| Visualizaciones | 15 % |
| Interpretación de tendencias | 10 % |
| Storytelling y recomendación final | 10 % |

---

# Resultado esperado del aprendizaje

Al finalizar el taller, el estudiante debe ser capaz de:

- Construir un modelo de datos en Power BI.
- Crear medidas DAX para ventas y tendencias.
- Analizar ventas mensuales.
- Identificar productos y categorías líderes.
- Detectar cambios en el comportamiento comercial.
- Comunicar hallazgos con enfoque de negocio.

---

# Conclusión metodológica

El uso de CRISP-DM permite que el taller no sea solo una práctica técnica de Power BI, sino un ejercicio completo de analítica aplicada.

La lógica del taller es:

1. Primero se entiende el negocio.
2. Luego se entienden los datos.
3. Después se preparan y modelan.
4. Finalmente se evalúan los resultados y se comunican decisiones.

Esto conecta el uso de Power BI con una práctica real de análisis de datos orientada a la toma de decisiones.

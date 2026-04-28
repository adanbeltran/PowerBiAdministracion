# Taller 2. Integración de fuentes heterogéneas para análisis comercial

**Dataset sugerido:** Olist Brazilian E-Commerce — https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

**Elementos de Power BI a enseñar:** Folder import, Power Query, merge queries, append queries, expand columns, auditoría de joins, tabla y matriz, slicers.

**Tema de estadística aplicado:** Técnicas de muestreo aleatorio.

## 1. Objetivo del taller
Integrar múltiples tablas de una operación de comercio electrónico, documentar su lógica de unión y producir una vista analítica lista para modelado.

## 2. Marco conceptual y teórico de los temas aplicado en el taller
En escenarios empresariales reales, la información suele estar fragmentada en sistemas diferentes. Power Query permite combinar tablas mediante merge y append, resolver discrepancias de claves y estandarizar estructuras. Desde el enfoque estadístico aplicado, el muestreo aleatorio simple ayuda a validar rápidamente la coherencia de una integración sin revisar la totalidad de registros.

## 3. Contexto del caso de estudio y explicación del dataset a utilizar
Una empresa de e-commerce tiene datos separados de pedidos, clientes, pagos, productos y reseñas. El dataset sugerido es Brazilian E-Commerce Public Dataset by Olist. Descarga: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce. El objetivo es construir una base integrada para análisis comercial, logístico y de experiencia de cliente.

## 4. Instrucciones paso a paso
1. Descargue y descomprima el dataset de Olist. Identifique al menos estas tablas: pedidos, ítems de pedido, clientes, productos, pagos y reseñas.
2. En Power BI, importe las tablas por separado y asigne nombres comprensibles: `Pedidos`, `ItemsPedido`, `Clientes`, `Pagos`, `Productos`, `Resenas`.
3. En Power Query, verifique tipos de dato y normalice nombres de columnas clave como `order_id`, `customer_id`, `product_id`.
4. Cree una tabla de control donde documente: tabla origen, clave primaria esperada, posible clave foránea y número de registros.
5. Realice un **Merge** entre `Pedidos` y `Clientes` por `customer_id`. Use unión izquierda y expanda solo las columnas necesarias para evitar duplicidad innecesaria.
6. Una `Pedidos` con `Pagos` por `order_id`; luego una con `ItemsPedido`; finalmente incorpore atributos de `Productos`.
7. Revise la multiplicación de filas al expandir `ItemsPedido`. Explique por qué una orden con varios ítems incrementa el número de registros.
8. Construya un muestreo aleatorio manual: tome 30 `order_id` al azar usando un índice y un filtro o exporte una muestra para contrastar si las uniones preservaron coherencia.
9. Cree una tabla de validación con: número total de órdenes, número total de filas integradas, órdenes sin pago, órdenes sin reseña y órdenes con múltiples ítems.
10. En el reporte, genere una matriz por categoría de producto y estado del pedido, y un gráfico de barras por método de pago.
11. Concluya qué tabla debe considerarse hecho principal: pedido o ítem de pedido, y justifique su respuesta.

## 5. Ahora hazlo tú (reto para el estudiante)
Construya una versión integrada orientada a marketing, donde la unidad de análisis sea el cliente y no la orden. Defina qué métricas se podrían calcular en esa vista.

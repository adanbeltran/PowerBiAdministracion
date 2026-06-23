# Taller: Segmentación de clientes mayoristas con K-Means

## Python y Power BI aplicados a decisiones comerciales

---

## Navegación

1. [Contexto del reto](#1-contexto-del-reto)
2. [Dataset](#2-dataset)
3. [Objetivo](#3-objetivo)
4. [Fase 1. Comprensión del negocio](#fase-1-comprensión-del-negocio)
5. [Fase 2. Comprensión de los datos](#fase-2-comprensión-de-los-datos)
6. [Fase 3. Preparación de los datos](#fase-3-preparación-de-los-datos)
7. [Fase 4. Modelado con K-Means](#fase-4-modelado-con-k-means)
8. [Fase 5. Evaluación](#fase-5-evaluación)
9. [Fase 6. Comunicación en Power BI](#fase-6-comunicación-en-power-bi)
10. [Ejercicio evaluable](#ejercicio-evaluable)
11. [Entregables](#entregables)
12. [Criterios de evaluación](#criterios-de-evaluación)

---

# 1. Contexto del reto

Una empresa distribuidora mayorista comercializa productos de consumo a hoteles, restaurantes, cafeterías y establecimientos minoristas. La gerencia comercial considera que tratar a todos los clientes de la misma forma reduce la efectividad de las campañas, dificulta la asignación de asesores y genera ofertas poco relevantes.

La organización desea construir una segmentación de clientes basada en sus patrones anuales de compra. Los segmentos deberán ser útiles para:

- diseñar campañas diferenciadas;
- priorizar clientes de alto valor;
- identificar patrones de consumo;
- orientar estrategias de venta cruzada;
- distribuir la atención de los asesores comerciales;
- diseñar portafolios y descuentos por tipo de cliente.

Usted asumirá el rol de una unidad de analítica comercial. Su tarea será construir, evaluar e interpretar una segmentación mediante **K-Means** y comunicar los resultados en Power BI.

---

# 2. Dataset

## Wholesale Customers

Fuente oficial:

[UCI Machine Learning Repository — Wholesale Customers](https://archive.ics.uci.edu/dataset/292/wholesale+customers)

El conjunto contiene información de **440 clientes** de una empresa distribuidora mayorista. Para cada cliente se registra su gasto anual en diferentes categorías de productos.

## Variables

| Variable | Tipo | Descripción |
|---|---|---|
| `Fresh` | Numérica | Gasto anual en productos frescos |
| `Milk` | Numérica | Gasto anual en productos lácteos |
| `Grocery` | Numérica | Gasto anual en productos de abarrotes |
| `Frozen` | Numérica | Gasto anual en productos congelados |
| `Detergents_Paper` | Numérica | Gasto anual en detergentes y productos de papel |
| `Delicassen` | Numérica | Gasto anual en productos de delicatessen |
| `Channel` | Categórica codificada | Canal Horeca o minorista |
| `Region` | Categórica codificada | Región del cliente |

> La variable aparece escrita como `Delicassen` en el archivo original.

<img width="422" height="209" alt="image" src="https://github.com/user-attachments/assets/483d3999-b7df-452f-bdb6-a6f5d79c7f5d" />


## Justificación del dataset

Este conjunto es apropiado para K-Means porque:

- cada fila representa un cliente;
- las variables principales son numéricas;
- las variables reflejan comportamiento de compra;
- existe suficiente variabilidad entre clientes;
- el problema tiene una interpretación comercial directa;
- los resultados pueden convertirse en estrategias de negocio.

El dataset es pedagógico y no debe utilizarse para describir el mercado mayorista actual. Su valor se encuentra en la aplicación metodológica del clustering.

---

# 3. Objetivo

## Objetivo general

Construir e interpretar una segmentación de clientes mayoristas mediante K-Means, utilizando Python para el procesamiento y modelado, y Power BI para la comunicación de resultados y recomendaciones comerciales.

## Resultados de aprendizaje

Al finalizar el taller, el estudiante estará en capacidad de:

1. Formular un problema de segmentación desde una necesidad empresarial.
2. Preparar variables numéricas para un algoritmo basado en distancias.
3. Reconocer el efecto de la escala, la asimetría y los valores atípicos.
4. Seleccionar un número razonable de clústeres.
5. Interpretar perfiles de consumo.
6. Proponer estrategias diferenciadas para cada segmento.
7. Comunicar resultados mediante un tablero de Power BI.
8. Reconocer las limitaciones del modelo.

---

# Fase 1. Comprensión del negocio

[Volver a navegación](#navegación)

## Pregunta empresarial central

**¿Qué grupos de clientes presentan patrones de compra similares y qué estrategia comercial debería aplicarse a cada grupo?**

## Actividad

Usted deberá definir:

- la necesidad empresarial;
- los usuarios de la segmentación;
- las decisiones que se desean apoyar;
- los criterios para considerar útil un segmento;
- los riesgos de una segmentación incorrecta.

## Preguntas orientadoras

- ¿Por qué no conviene ofrecer la misma campaña a todos los clientes?
- ¿Qué significa que dos clientes tengan un comportamiento de compra similar?
- ¿Qué características debería tener un segmento comercialmente útil?
- ¿Qué decisiones podrían tomarse a partir de los clústeres?
- ¿Qué información no está disponible en el dataset?
- ¿Qué conclusiones no deberían formularse?

## Producto esperado

Un planteamiento de máximo media página con:

1. problema empresarial;
2. objetivo de la segmentación;
3. usuarios del resultado;
4. decisiones que se apoyarán;
5. criterios de éxito.

---

# Fase 2. Comprensión de los datos

[Volver a navegación](#navegación)

## Trabajo en Python

Usted deberá explorar y documentar:

- cantidad de registros;
- tipos de datos;
- valores faltantes;
- duplicados;
- distribución de cada variable;
- asimetría;
- presencia de valores extremos;
- diferencias de escala;
- relaciones entre categorías de gasto.

## Variables para el clustering

El modelo K-Means deberá construirse inicialmente con:

- `Fresh`;
- `Milk`;
- `Grocery`;
- `Frozen`;
- `Detergents_Paper`;
- `Delicassen`.

Las variables `Channel` y `Region` no deberán incluirse en el modelo inicial porque representan categorías codificadas y no magnitudes continuas. Podrán utilizarse posteriormente para describir y contrastar los segmentos.

## Preguntas orientadoras

- ¿Todas las variables presentan escalas similares?
- ¿Qué categorías tienen mayor dispersión?
- ¿Existen clientes con gastos excepcionalmente altos?
- ¿Qué efecto tendrían estos valores sobre K-Means?
- ¿Qué variables parecen estar relacionadas?
- ¿Qué ocurriría si se utilizara la información sin estandarizar?

## Evidencia esperada

- tabla de estadísticos descriptivos;
- revisión de calidad;
- visualización de distribuciones;
- identificación de valores atípicos;
- interpretación preliminar.

---

# Fase 3. Preparación de los datos

[Volver a navegación](#navegación)

## Decisiones metodológicas

K-Means utiliza distancias entre observaciones. Por esta razón, Usted deberá decidir cómo tratar:

- diferencias de escala;
- distribuciones fuertemente asimétricas;
- valores atípicos;
- posibles variables redundantes.

## Transformaciones esperadas

Usted deberá comparar, como mínimo, dos escenarios:

### Escenario A. Estandarización directa

Variables originales transformadas mediante estandarización.

### Escenario B. Transformación logarítmica y estandarización

Aplicación de una transformación equivalente a:

```python
np.log1p(variable)
```

seguida de una estandarización.

## Preguntas orientadoras

- ¿Qué problema busca resolver la transformación logarítmica?
- ¿Cómo cambia la distribución de los gastos?
- ¿Qué sucede con los clientes de consumo extremo?
- ¿Cuál escenario produce segmentos más interpretables?
- ¿Debe eliminarse un valor atípico o conservarse?
- ¿Qué criterio empresarial podría justificar una exclusión?

## Restricción

No se deben eliminar registros únicamente porque se encuentran lejos de la media. Toda exclusión deberá justificarse técnica y empresarialmente.

## Producto esperado

Una tabla que documente:

| Decisión | Alternativas consideradas | Opción seleccionada | Justificación |
|---|---|---|---|
| Escala |  |  |  |
| Asimetría |  |  |  |
| Valores atípicos |  |  |  |
| Variables |  |  |  |

---

# Fase 4. Modelado con K-Means

[Volver a navegación](#navegación)

## Propósito

Identificar grupos de clientes con patrones de gasto semejantes.

## Trabajo en Python

Usted deberá construir varias soluciones de clustering y comparar valores de:

```text
k = 2, 3, 4, 5 y 6
```

Para cada valor de `k`, deberá registrar:

- inercia;
- coeficiente de silueta;
- cantidad de clientes por clúster;
- estabilidad de la solución;
- facilidad de interpretación empresarial.

## Configuración mínima

El modelo deberá incluir:

- semilla aleatoria;
- múltiples inicializaciones;
- variables estandarizadas;
- registro del clúster asignado a cada cliente.

Una configuración razonable puede partir de:

```python
KMeans(
    n_clusters=k,
    random_state=42,
    n_init=20
)
```

Este fragmento no constituye la solución completa. Usted deberá decidir cómo integrarlo al proceso.

## Selección del número de clústeres

La selección de `k` no debe depender de un único criterio.

Usted deberá considerar conjuntamente:

1. método del codo;
2. coeficiente de silueta;
3. tamaño de los grupos;
4. estabilidad;
5. interpretación empresarial;
6. utilidad para tomar decisiones.

## Preguntas orientadoras

- ¿Dónde aparece un cambio relevante en la inercia?
- ¿Qué valor de `k` tiene mejor silueta?
- ¿Alguna solución genera grupos demasiado pequeños?
- ¿Los perfiles son realmente diferentes?
- ¿La solución se mantiene al cambiar la semilla?
- ¿La alternativa con mejor métrica también es la más útil para la empresa?
- ¿Qué se gana y qué se pierde al aumentar `k`?

## Producto esperado

Una matriz comparativa:

| k | Inercia | Silueta | Tamaño mínimo | Tamaño máximo | Interpretabilidad | Decisión |
|---:|---:|---:|---:|---:|---|---|
| 2 |  |  |  |  |  |  |
| 3 |  |  |  |  |  |  |
| 4 |  |  |  |  |  |  |
| 5 |  |  |  |  |  |  |
| 6 |  |  |  |  |  |  |

---

# Fase 5. Evaluación

[Volver a navegación](#navegación)

## Perfilamiento de los clústeres

Una vez seleccionado el modelo, Usted deberá calcular para cada clúster:

- cantidad de clientes;
- gasto promedio por categoría;
- gasto mediano por categoría;
- gasto total promedio;
- categoría dominante;
- participación de cada canal;
- distribución regional.

## Nombres de los segmentos

Los nombres deberán surgir de los datos.

Ejemplos únicamente ilustrativos:

- clientes intensivos en frescos;
- clientes orientados a abarrotes y limpieza;
- compradores diversificados de alto valor;
- clientes de bajo consumo;
- clientes con énfasis en congelados.

No se deben asignar nombres antes de revisar los centroides y los perfiles.

## Validación empresarial

Las variables `Channel` y `Region` podrán utilizarse para analizar si los segmentos presentan diferencias comerciales relevantes.

Ejemplos:

- proporción de clientes Horeca por clúster;
- proporción de minoristas;
- distribución regional;
- concentración territorial.

Estas variables no determinan si un clúster es correcto, pero pueden ayudar a interpretarlo.

## Preguntas orientadoras

- ¿Qué variables caracterizan cada segmento?
- ¿Qué segmento concentra mayor gasto?
- ¿Qué clúster presenta mayor especialización?
- ¿Qué clúster tiene un consumo más equilibrado?
- ¿Qué grupo podría responder a ventas cruzadas?
- ¿Qué segmento requiere atención personalizada?
- ¿Qué segmento podría tener baja rentabilidad comercial?
- ¿Los segmentos coinciden parcialmente con los canales?
- ¿Qué limitaciones impiden conocer la rentabilidad real?

## Matriz de estrategia comercial

| Clúster | Perfil de compra | Necesidad probable | Estrategia | Canal recomendado | Indicador de seguimiento |
|---|---|---|---|---|---|
| 0 |  |  |  |  |  |
| 1 |  |  |  |  |  |
| 2 |  |  |  |  |  |
| 3 |  |  |  |  |  |

El número de filas deberá ajustarse al valor de `k` seleccionado.

---

# Fase 6. Comunicación en Power BI

[Volver a navegación](#navegación)

## Datos que se deben exportar desde Python

El archivo final deberá incluir, como mínimo:

- identificador de fila o cliente;
- variables originales;
- variables transformadas, cuando sea pertinente;
- clúster asignado;
- nombre comercial del segmento;
- gasto total;
- canal;
- región.

Nombre sugerido:

```text
clientes_segmentados.csv
```

## Página 1. Resumen de segmentación

Debe incluir:

- total de clientes;
- número de segmentos;
- gasto promedio;
- gasto total;
- cantidad de clientes por clúster;
- participación porcentual de cada segmento.

## Página 2. Perfil de segmentos

Debe permitir comparar:

- gasto promedio por categoría;
- gasto mediano;
- composición del gasto;
- categoría dominante;
- gasto total por segmento.

Visualizaciones posibles:

- barras agrupadas;
- matriz con formato condicional;
- gráfico de dispersión;
- perfil normalizado;
- pequeños múltiples.

## Página 3. Contexto comercial

Debe mostrar:

- distribución de canal por clúster;
- región por clúster;
- concentración de clientes;
- diferencias entre Horeca y minoristas;
- tabla de clientes y segmento.

## Página 4. Estrategias

Debe sintetizar:

- nombre del segmento;
- característica principal;
- oportunidad comercial;
- estrategia recomendada;
- indicador de seguimiento.

## Criterios de diseño

- Cada página debe responder una pregunta.
- Los nombres de segmentos deben ser comprensibles.
- Las escalas deben estar claramente indicadas.
- Los colores de los clústeres deben mantenerse consistentes.
- Las recomendaciones deben estar vinculadas con los datos.
- No se deben presentar gráficos sin interpretación.

---

# Responsabilidades por herramienta

## Python

Python se utilizará para:

- explorar los datos;
- transformar variables;
- estandarizar;
- evaluar valores atípicos;
- entrenar K-Means;
- calcular inercia y silueta;
- seleccionar `k`;
- perfilar clústeres;
- exportar resultados.

## Power BI

Power BI se utilizará para:

- importar los resultados;
- construir medidas;
- comparar perfiles;
- explorar canales y regiones;
- comunicar segmentos;
- presentar estrategias comerciales.

## Separación de responsabilidades

| Actividad | Python | Power BI |
|---|:---:|:---:|
| Calidad de datos | Sí | Verificación |
| Transformación logarítmica | Sí | No |
| Estandarización | Sí | No |
| Entrenamiento de K-Means | Sí | No |
| Evaluación del modelo | Sí | Visualización |
| Perfilamiento | Sí | Sí |
| Tablero gerencial | No | Sí |
| Recomendaciones | Sustento | Comunicación |

---

# Ejercicio evaluable

## Caso

La gerencia comercial solicita una propuesta de segmentación para diseñar el plan de ventas del siguiente periodo.

Usted deberá seleccionar el número de segmentos, describirlos y proponer una estrategia diferenciada.

## Actividades

### 1. Diagnóstico de variables

Determine:

- cuáles variables presentan mayor asimetría;
- cuáles tienen mayor dispersión;
- cuáles podrían dominar las distancias;
- qué valores requieren revisión.

### 2. Comparación de preparación

Compare:

- datos estandarizados sin transformación logarítmica;
- datos transformados con `log1p` y estandarizados.

Determine cuál alternativa genera clústeres más equilibrados e interpretables.

### 3. Selección de `k`

Evalúe valores entre 2 y 6 mediante:

- codo;
- silueta;
- tamaño de clústeres;
- interpretación comercial.

Seleccione una solución y justifique por qué es preferible a las demás.

### 4. Perfilamiento

Para cada clúster:

- calcule promedios y medianas;
- identifique categorías dominantes;
- compare gasto total;
- revise canal y región;
- asigne un nombre comercial.

### 5. Estrategia

Proponga para cada segmento:

- una campaña;
- una oportunidad de venta cruzada;
- un canal de contacto;
- una prioridad comercial;
- un indicador de seguimiento.

### 6. Tablero

Construya las cuatro páginas definidas para Power BI.

### 7. Conclusión ejecutiva

Responda en máximo 300 palabras:

- ¿qué segmentación fue seleccionada?;
- ¿qué diferencia a los grupos?;
- ¿qué acción debería ejecutar primero la gerencia?;
- ¿qué limitación debe considerarse?

---

# Preguntas de análisis

1. ¿Por qué K-Means no debería aplicarse directamente sin estandarizar?
2. ¿Qué efecto producen los valores extremos sobre los centroides?
3. ¿Por qué una transformación logarítmica puede mejorar el análisis?
4. ¿Qué diferencia existe entre inercia y silueta?
5. ¿Por qué el mejor valor de silueta no siempre produce la mejor solución empresarial?
6. ¿Qué clúster parece tener mayor potencial de venta cruzada?
7. ¿Qué segmento justificaría atención personalizada?
8. ¿Qué variables adicionales solicitaría a la empresa?
9. ¿Se puede concluir que un cliente con mayor gasto es más rentable?
10. ¿Cómo debería actualizarse la segmentación en una organización real?

---

# Entregables

[Volver a navegación](#navegación)

## 1. Notebook de Python

Nombre:

```text
segmentacion_clientes_kmeans.ipynb
```

Debe contener:

- comprensión del negocio;
- exploración;
- preparación;
- comparación de escenarios;
- selección de `k`;
- evaluación;
- perfilamiento;
- exportación.

## 2. Archivo de Power BI

Nombre:

```text
tablero_segmentacion_clientes.pbix
```

## 3. Archivo procesado

Nombre:

```text
clientes_segmentados.csv
```

## 4. Informe ejecutivo

Máximo tres páginas:

1. problema;
2. metodología;
3. segmentos;
4. estrategias;
5. limitaciones.

## 5. Sustentación

Duración máxima: 8 minutos.

---

# Criterios de evaluación

[Volver a navegación](#navegación)

| Criterio | Porcentaje |
|---|---:|
| Comprensión del problema empresarial | 10 % |
| Exploración y calidad de datos | 10 % |
| Preparación y justificación de transformaciones | 15 % |
| Aplicación de K-Means | 20 % |
| Selección y evaluación de `k` | 15 % |
| Interpretación de segmentos | 15 % |
| Tablero en Power BI | 10 % |
| Recomendaciones y limitaciones | 5 % |
| **Total** | **100 %** |

---

# Reglas metodológicas

1. No utilizar `Channel` y `Region` como variables numéricas continuas en el modelo inicial.
2. No seleccionar `k` únicamente mediante el método del codo.
3. No interpretar un clúster sin revisar sus centroides y estadísticas.
4. No asignar nombres comerciales antes de obtener los resultados.
5. No confundir gasto con rentabilidad.
6. No eliminar valores atípicos sin justificación.
7. No presentar resultados de Python y Power BI que sean inconsistentes.
8. No afirmar que los clústeres representan categorías naturales o definitivas.
9. Documentar todas las decisiones.
10. Formular recomendaciones asociadas con indicadores medibles.

---

# Lista de verificación

## Python

- [ ] Se verificaron tipos de datos.
- [ ] Se revisaron valores faltantes y duplicados.
- [ ] Se analizaron distribuciones y valores atípicos.
- [ ] Se compararon dos escenarios de preparación.
- [ ] Se evaluaron varios valores de `k`.
- [ ] Se calcularon inercia y silueta.
- [ ] Se justificó la solución final.
- [ ] Se perfilaron los clústeres.
- [ ] Se exportó el resultado.

## Power BI

- [ ] Se importó el archivo segmentado.
- [ ] Los clústeres tienen nombres interpretables.
- [ ] Se comparan los perfiles.
- [ ] Se muestran canal y región.
- [ ] Las estrategias están asociadas con los segmentos.
- [ ] Los colores son consistentes.
- [ ] Los filtros funcionan.
- [ ] El tablero es comprensible para la gerencia.

## Informe

- [ ] El problema está claramente formulado.
- [ ] La metodología está justificada.
- [ ] Los segmentos están interpretados.
- [ ] Las estrategias son viables.
- [ ] Se reconocen las limitaciones.

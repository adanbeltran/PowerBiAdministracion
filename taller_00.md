# Taller 00. Power BI y análisis de datos en Administración

## 1. Objetivo del taller

Aplicar un proceso básico de análisis de datos en Power BI usando la metodología CRISP-DM para cargar un dataset, comprender sus variables, preparar los datos, crear cálculos en DAX y construir un dashboard con hallazgos útiles para la toma de decisiones.

---

## 2. Marco teórico / conceptual

### 2.1 CRISP-DM

<img width="885" height="596" alt="image" src="https://github.com/user-attachments/assets/4955256d-04b0-4705-abfc-06ef49185865" />


**CRISP-DM** (*Cross-Industry Standard Process for Data Mining*) es una metodología para desarrollar proyectos de análisis de datos de manera ordenada.

Referencia oficial de IBM:

- https://www.ibm.com/docs/es/spss-modeler/saas?topic=dm-crisp-help-overview

Fases de CRISP-DM:

1. **Comprensión del negocio**  
   Definir el problema, el objetivo y las preguntas que se quieren responder.

2. **Comprensión de los datos**  
   Revisar qué variables existen, qué significan, qué tipo de dato tienen y si pueden servir para responder el problema.

3. **Preparación de los datos**  
   Corregir tipos de dato, limpiar valores, renombrar variables y dejar el archivo listo para analizar.

4. **Modelado**  
   Crear medidas, columnas calculadas y métricas que permitan interpretar el problema.

5. **Evaluación**  
   Verificar si los resultados obtenidos tienen sentido y realmente responden la pregunta planteada.

6. **Despliegue**  
   Comunicar los resultados en dashboards, reportes o presentaciones.

---

### 2.2 Qué es Power BI

**Power BI** es una herramienta de Microsoft para analizar, transformar y visualizar datos.

Sirve para:
- conectar archivos o bases de datos
- limpiar y organizar información
- crear cálculos con DAX
- construir gráficos interactivos
- presentar resultados en dashboards

Comparación breve:

| Herramienta | Uso principal | Diferencia principal |
|---|---|---|
| **Excel** | análisis básico y tablas dinámicas | más limitado para dashboards interactivos y modelado |
| **Tableau** | visualización avanzada | fuerte en visualización, pero menos integrado con ecosistema Microsoft |
| **Looker Studio** | reportes en línea | más orientado a reportes conectados a servicios web |
| **Power BI** | análisis, modelado y dashboards | integra transformación, DAX y visualización en un solo entorno |

---

### 2.3 DAX

**DAX** (*Data Analysis Expressions*) es el lenguaje de fórmulas de Power BI. Se usa para crear cálculos dentro del modelo de datos.

En Power BI, DAX se usa principalmente para:
- **medidas**: cálculos dinámicos que cambian según filtros y visualizaciones
- **columnas calculadas**: cálculos fila por fila que crean una nueva columna

#### Tipos de operación en DAX

| Tipo de operación | Qué hace | Uso típico |
|---|---|---|
| **Por fila** | evalúa cada registro individualmente | columnas calculadas |
| **Por agregación o columna** | resume varios registros en un solo valor | medidas |
| **Por contexto de filtro** | cambia el resultado según segmentadores, filtros o visuales | medidas con `CALCULATE`, `FILTER` |

#### Grupos de funciones DAX

| Categoría | Para qué sirve | Ejemplos | Tipo de operación más común |
|---|---|---|---|
| **Funciones de matemáticas y estadística** | resumir y calcular valores numéricos | `SUM`, `AVERAGE`, `MIN`, `MAX`, `MEDIAN`, `COUNT`, `DIVIDE` | agregación |
| **Funciones de lógica** | evaluar condiciones y clasificar | `IF`, `AND`, `OR`, `SWITCH` | por fila o por contexto |
| **Funciones de texto** | transformar o combinar cadenas de texto | `UPPER`, `LOWER`, `LEFT`, `RIGHT`, `LEN`, `CONCATENATE` | por fila |
| **Funciones de filtro** | cambiar el contexto del cálculo | `CALCULATE`, `FILTER`, `ALL` | por contexto |
| **Funciones de fecha y hora** | trabajar con fechas y tiempos | `TODAY`, `NOW`, `YEAR`, `MONTH`, `DAY` | por fila o contexto |

Referencia oficial de funciones DAX en Microsoft Learn: citeturn147349search1turn147349search3

---

## 3. Instrucciones de aplicación paso a paso

Dataset de trabajo en clase: **Housing**

Variables principales del archivo:
- `Home`
- `Price`
- `SqFt`
- `Bedrooms`
- `Bathrooms`
- `Offers`
- `Brick`
- `Neighborhood`

### Paso 1. Cargar el archivo

Ruta:

**Inicio → Obtener datos → Texto/CSV**

Acciones:
- importar el archivo CSV
- revisar que todas las columnas se carguen correctamente
- asignar a la tabla el nombre **Housing**

### Paso 2. Comprender las variables

Ruta:

**Vista → Datos**

Acciones:
- identificar variables numéricas: `Price`, `SqFt`, `Bedrooms`, `Bathrooms`, `Offers`
- identificar variables categóricas: `Brick`, `Neighborhood`
- reconocer `Home` como identificador del registro
- definir `Price` como variable principal del análisis

### Paso 3. Preparar los datos

Ruta:

**Inicio → Transformar datos**

Acciones:
- verificar tipos de dato
- corregir columnas mal interpretadas
- revisar valores vacíos
- renombrar columnas si se desea una lectura más clara

Renombrado sugerido:
- `Price` → Precio
- `SqFt` → Tamaño
- `Bedrooms` → Habitaciones
- `Bathrooms` → Baños
- `Offers` → Ofertas
- `Brick` → Ladrillo
- `Neighborhood` → Vecindario

### Paso 4. Crear cálculos DAX

Ruta:

**Modelado → Nueva medida** o **Nueva columna**

#### A. Matemáticas y estadística

Medidas:

```DAX
Precio Promedio = AVERAGE(Housing[Price])
```

```DAX
Precio Máximo = MAX(Housing[Price])
```

```DAX
Precio Mínimo = MIN(Housing[Price])
```

```DAX
Mediana Precio = MEDIAN(Housing[Price])
```

#### B. Lógica

Columna calculada:

```DAX
Nivel Precio = IF(Housing[Price] >= 200000, "Alto", "Medio o Bajo")
```

#### C. Texto

Columna calculada:

```DAX
Vecindario Mayúscula = UPPER(Housing[Neighborhood])
```

#### D. Filtro

Medida:

```DAX
Precio Promedio Casas Ladrillo =
CALCULATE(
    AVERAGE(Housing[Price]),
    Housing[Brick] = "Yes"
)
```

#### E. Fecha y hora

Columna calculada:

```DAX
Fecha Consulta = TODAY()
```

### Paso 5. Construir visualizaciones

Crear al menos las siguientes:

1. **Tarjetas**
   - Precio Promedio
   - Precio Máximo
   - Precio Mínimo
   - Mediana Precio

2. **Gráfico de barras**
   - Eje: `Bedrooms`
   - Valor: promedio de `Price`

3. **Gráfico de dispersión**
   - Eje X: `SqFt`
   - Eje Y: `Price`

4. **Gráfico de barras por categoría**
   - Eje: `Neighborhood`
   - Valor: promedio de `Price`

5. **Segmentadores**
   - `Brick`
   - `Neighborhood`
   - `Bathrooms`

### Paso 6. Interpretar resultados

Responder en clase preguntas como:
- ¿las viviendas con mayor tamaño tienden a tener mayor precio?
- ¿cambia el precio promedio según el número de habitaciones?
- ¿hay diferencias de precio entre vecindarios?
- ¿las viviendas de ladrillo muestran diferencias en precio promedio?

---

## 4. Reto en clase: ahora hazlo tú

Para el reto debes trabajar con el dataset adjunto **DirectMarketing.csv**.

Variables del archivo:
- `Age`
- `Gender`
- `OwnHome`
- `Married`
- `Location`
- `Salary`
- `Children`
- `History`
- `Catalogs`
- `AmountSpent`

Tu objetivo es aplicar los mismos conceptos trabajados en el paso 3, pero ahora en un contexto de **marketing directo**.

### Preguntas que debes responder

1. ¿Qué grupos de clientes gastan más dinero en promedio?
2. ¿Existen diferencias en gasto según ubicación, estado civil o tenencia de vivienda?
3. ¿El número de catálogos enviados parece relacionarse con el gasto?
4. ¿Los clientes con mayor salario muestran mayor gasto?
5. ¿Qué perfil de cliente parece más valioso para una campaña comercial?

### Operaciones DAX que debes realizar

Debes crear al menos una operación de cada categoría:

#### A. Matemáticas y estadística
Crear medidas como:
- gasto promedio
- gasto máximo
- gasto mínimo
- mediana de gasto
- conteo de clientes

Ejemplos:

```DAX
Gasto Promedio = AVERAGE(DirectMarketing[AmountSpent])
```

```DAX
Total Clientes = COUNT(DirectMarketing[AmountSpent])
```

#### B. Lógica
Crear una columna que clasifique clientes, por ejemplo:
- cliente de gasto alto / medio / bajo
- cliente con muchos o pocos catálogos

Ejemplo:

```DAX
Nivel Gasto = IF(DirectMarketing[AmountSpent] >= 1000, "Alto", "No Alto")
```

#### C. Texto
Crear una columna para transformar o estandarizar una categoría.

Ejemplo:

```DAX
Ubicación Mayúscula = UPPER(DirectMarketing[Location])
```

#### D. Filtro
Crear una medida que compare subconjuntos.

Ejemplos posibles:
- gasto promedio de clientes casados
- gasto promedio de quienes tienen vivienda propia
- gasto promedio de clientes con historial alto

Ejemplo:

```DAX
Gasto Promedio Casados =
CALCULATE(
    AVERAGE(DirectMarketing[AmountSpent]),
    DirectMarketing[Married] = "Married"
)
```

#### E. Fecha y hora
Crear una columna de referencia temporal para registrar la fecha del análisis.

Ejemplo:

```DAX
Fecha Analisis = TODAY()
```

### Visualizaciones mínimas del reto

Debes construir un dashboard de una página con:
- tarjetas de indicadores principales
- un gráfico de barras por categoría
- un gráfico de dispersión
- al menos dos segmentadores

### Entregables en Moodle

Debes subir:

1. **Archivo `.pbix`** con el dashboard completo.
2. **Documento corto** en PDF o Word con:
   - preguntas analizadas
   - medidas y columnas DAX creadas
   - hallazgos principales
   - interpretación gerencial breve

### Criterios mínimos esperados

Tu entrega debe mostrar:
- comprensión de variables
- preparación básica de datos
- uso de DAX en las cinco categorías trabajadas
- visualizaciones coherentes con las preguntas planteadas
- interpretación clara de los resultados

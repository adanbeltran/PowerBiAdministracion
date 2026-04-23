# Taller 05. Análisis financiero básico

## 1. Objetivo del taller

Aplicar analítica de datos en un caso de **Administración** e **Ingeniería Industrial** para resolver un problema de negocio con **Power BI**, incorporando progresivamente **DAX**, criterios de consulta tipo **SQL** y apoyo analítico con **Python**.

Caso de estudio del taller: La organización necesita interpretar ingresos, costos y utilidad con enfoque gerencial.

## 2. Marco teórico / conceptual

### 2.1 Concepto central del taller

**Tema del taller:** Transformación financiera y lectura gerencial.

Este taller se orienta a que el estudiante entienda cómo los datos se convierten en información útil para decidir. El foco está en interpretar el problema, comprender las variables, construir indicadores y comunicar hallazgos. Administración: lectura de rentabilidad. Ingeniería Industrial: análisis de costos, eficiencia y variación financiera de operaciones.

### 2.2 Conceptos estadísticos que se aplican

**Concepto principal:** intervalos de confianza.

El estudiante debe usar este concepto para describir, comparar o interpretar el comportamiento de los datos. No se trata solo de calcular un valor, sino de explicar qué significa para la organización y cómo apoya una decisión.

### 2.3 Herramientas analíticas del taller

- **Power BI:** despivotar columnas, modelado básico, tablas, líneas, tarjetas e indicadores.
- **DAX:** SUM, DIVIDE, VAR, margen, IF.
- **SQL:** SUM, AVG, cálculo de márgenes y consultas por periodo.
- **Python:** intervalos de confianza y análisis exploratorio financiero.

Progresión esperada en este punto del curso:

- **DAX** se expande hacia agregaciones, lógica y relaciones entre tablas.
- **SQL** se usa para pensar en uniones, agrupaciones y consultas de extracción.
- **Python** apoya limpieza, exploración y validación de resultados.

Referencias base del curso:

- CRISP-DM: https://www.ibm.com/docs/es/spss-modeler/saas?topic=dm-crisp-help-overview
- Power BI: https://learn.microsoft.com/power-bi/
- Funciones DAX: https://learn.microsoft.com/dax/

## 3. Instrucciones de aplicación paso a paso para realizar en forma asistida con el profesor

**Dataset sugerido para el trabajo en clase:** World Bank Data / Financial Sample

1. Abrir Power BI Desktop y registrar el problema de negocio que se va a resolver.
2. Cargar el dataset sugerido: **World Bank Data / Financial Sample**.
3. Revisar variables, tipos de dato, valores faltantes y posibles inconsistencias.
4. Aplicar transformaciones en Power Query cuando sea necesario.
5. Construir un modelo de datos coherente; si el taller tiene varias tablas, validar relaciones.
6. Crear medidas y/o columnas DAX enfocadas en: margen y participación, comparaciones por periodo, indicadores financieros básicos.
7. Diseñar visualizaciones con énfasis en: despivotar columnas, modelado básico, tablas, líneas, tarjetas e indicadores.
8. Interpretar los resultados en lenguaje gerencial y operacional.
9. Guardar avances y preparar el entregable para Moodle.

### Preguntas orientadoras del análisis en clase

- ¿cómo evolucionan ingresos, costos y utilidad?
- ¿qué unidades son más rentables?
- ¿qué tan estable es el comportamiento financiero?

### Operaciones mínimas que deben implementarse

- margen y participación
- comparaciones por periodo
- indicadores financieros básicos

### Componentes mínimos de DAX dentro del taller

- Medida base de agregación del indicador principal.
- Medida comparativa por segmento, región, canal, área o periodo.
- Columna calculada de clasificación cuando sea pertinente.

### Apoyo complementario con SQL y Python

- **SQL:** escribir o interpretar una consulta simple que permita entender cómo se extraería o resumiría la información del caso.
- **Python:** realizar una verificación corta del dataset o de un resultado clave mediante `pandas` y, cuando aplique, un gráfico descriptivo o una validación estadística simple.

## 4. Reto en clase: ahora hazlo tú

**Nuevo contexto:** evaluar rentabilidad por línea de negocio o proceso.

**Dataset del reto:** Financial Sample u otro archivo financiero.

### Lo que debes resolver

1. Definir un problema de negocio claro.
2. Formular entre 3 y 5 preguntas analíticas.
3. Identificar variables clave y preparar la base de datos.
4. Construir un dashboard de una página.
5. Aplicar DAX, SQL conceptual y una validación corta en Python.
6. Interpretar hallazgos con enfoque gerencial y, cuando aplique, operacional.

### Lo que debe responder el estudiante

- Qué variables explican mejor el comportamiento del fenómeno analizado.
- Qué segmentos, áreas, productos, clientes, procesos o periodos muestran mejor o peor desempeño.
- Qué decisión o recomendación se deriva del análisis.

### Entregables en Moodle

1. Archivo **.pbix** del taller o del reto.
2. Documento corto en **PDF o Word** con:
   - problema de negocio,
   - preguntas analíticas,
   - transformaciones realizadas,
   - medidas y columnas DAX creadas,
   - breve apoyo con SQL y/o Python,
   - hallazgos principales,
   - interpretación gerencial.
3. Cuando el docente lo solicite, archivo adicional con script **.sql** o **.py** breve de apoyo.

## 5. Rúbrica breve

| Criterio | Excelente | Aceptable | Insuficiente |
|---|---|---|---|
| Comprensión del problema | Define con claridad el caso y las preguntas | Define el caso, pero con preguntas parcialmente precisas | No define bien el problema |
| Preparación de datos | Limpia, transforma y documenta correctamente | Realiza preparación básica con algunos vacíos | Presenta errores de calidad o poca preparación |
| Uso de DAX | Crea medidas y columnas pertinentes y bien nombradas | Usa DAX básico, pero con alcance limitado | Usa poco DAX o presenta errores importantes |
| Visualización e interpretación | El dashboard responde preguntas y comunica hallazgos | El dashboard funciona, pero comunica parcialmente | El dashboard no responde bien al problema |
| Entrega en Moodle | Entrega completa, ordenada y puntual | Entrega con faltantes menores | Entrega incompleta o desordenada |

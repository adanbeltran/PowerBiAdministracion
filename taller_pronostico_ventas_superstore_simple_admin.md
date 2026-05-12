# Taller sencillo de pronóstico de ventas con Superstore

## Enfoque del taller

Este notebook está diseñado para estudiantes de Administración de Empresas, Ingeniería Industrial y áreas afines.

El objetivo es aprender a construir un pronóstico de ventas usando Python, con un código lo más simple posible para facilitar la replicación en clase.

Se trabajará con el archivo `SampleSuperstore.csv` adjunto por el docente. Este archivo contiene información comercial de una empresa tipo retail, con variables como ventas, utilidad, cantidad, segmento, región, categoría y subcategoría.

## Advertencia importante sobre el dataset

El archivo adjunto **no contiene una columna de fecha real**, como `Order Date` o `Fecha de pedido`.

Para enseñar el concepto de series de tiempo y pronóstico, este notebook crea una **fecha didáctica simulada** entre 2020 y 2023. Esto permite practicar los algoritmos de forecasting, pero debe entenderse como un recurso pedagógico.

En una empresa real, el pronóstico debe hacerse con una fecha real de venta, factura, pedido o despacho.

# 1. Conceptos básicos

## ¿Qué es Machine Learning?

Machine Learning es una forma de análisis de datos donde un algoritmo aprende patrones a partir de datos históricos.

En administración, puede usarse para apoyar decisiones como:

- Estimar ventas futuras.
- Identificar clientes con mayor probabilidad de compra.
- Clasificar pedidos con riesgo de retraso.
- Predecir utilidad o demanda.
- Detectar productos con bajo desempeño.

## Clasificación y regresión

En Machine Learning existen diferentes tipos de problemas.

### Clasificación

Se usa cuando se predice una categoría.

Ejemplos:

- Cliente comprador / no comprador.
- Pedido a tiempo / pedido tarde.
- Riesgo bajo / medio / alto.
- Cliente rentable / cliente no rentable.

### Regresión

Se usa cuando se predice un número.

Ejemplos:

- Ventas del próximo mes.
- Utilidad esperada.
- Cantidad demandada.
- Tiempo de entrega.
- Costo logístico.

El pronóstico de ventas es un problema de **regresión**, porque se intenta predecir un valor numérico.

# 2. ¿Qué es forecasting?

Forecasting significa pronóstico.

En términos administrativos, forecasting es estimar un valor futuro usando información histórica.

Ejemplo:

Si una empresa conoce sus ventas mensuales de los últimos años, puede usar esa información para estimar las ventas de los próximos meses.

## ¿Qué es una serie de tiempo?

Una serie de tiempo es una secuencia de datos ordenados por fecha.

Ejemplo:

| Mes | Ventas |
|---|---:|
| Enero | 100.000 |
| Febrero | 120.000 |
| Marzo | 115.000 |
| Abril | 130.000 |

En este taller construiremos una serie de tiempo mensual:

`mes` → `ventas totales del mes`

# 3. Algoritmos que se usarán

El taller usa algoritmos introductorios, útiles para entender el proceso de pronóstico sin requerir matemáticas avanzadas.

| Algoritmo | Idea intuitiva | Uso administrativo |
|---|---|---|
| Promedio histórico | El futuro se parece al promedio del pasado | Presupuesto conservador |
| Naive | El futuro se parece al último mes conocido | Punto de comparación básico |
| Promedio móvil | El futuro se parece al promedio de los últimos meses | Suavizar fluctuaciones |
| Seasonal Naive | Un mes se parece al mismo mes del año anterior | Negocios con estacionalidad |
| Holt-Winters | Usa nivel, tendencia y estacionalidad | Pronóstico formal de ventas |
| Regresión lineal | Ajusta una tendencia general | Ver si las ventas suben o bajan |
| Random Forest | Aprende relaciones no lineales con variables históricas | Modelo introductorio de Machine Learning |

# 4. Instalación de librerías

Esta celda instala las librerías necesarias para análisis de datos, gráficos, modelos estadísticos y modelos de Machine Learning.

```python
# Instalación de librerías necesarias para el taller.
# En Google Colab muchas ya vienen instaladas, pero esta celda ayuda a evitar errores.

!pip install -q pandas numpy matplotlib scikit-learn statsmodels
```

# 5. Importación de librerías

Se importan librerías con nombres estándar en ciencia de datos.

- `pandas`: manejo de tablas.
- `numpy`: operaciones numéricas.
- `matplotlib`: gráficos.
- `sklearn`: modelos y métricas de Machine Learning.
- `statsmodels`: modelos estadísticos de series de tiempo.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from google.colab import files

from sklearn.metrics import mean_absolute_error, mean_squared_error
from sklearn.linear_model import LinearRegression
from sklearn.ensemble import RandomForestRegressor

from statsmodels.tsa.holtwinters import ExponentialSmoothing
```

# 6. Carga del archivo

Sube el archivo `SampleSuperstore.csv`.

Este taller está ajustado a las columnas reales del archivo adjunto:

- `Ship Mode`
- `Segment`
- `Country`
- `City`
- `State`
- `Postal Code`
- `Region`
- `Category`
- `Sub-Category`
- `Sales`
- `Quantity`
- `Discount`
- `Profit`

```python
# Cargar manualmente el archivo CSV desde el computador.
# En la ventana que aparece, selecciona SampleSuperstore.csv.

uploaded = files.upload()

file_name = list(uploaded.keys())[0]

raw_sales = pd.read_csv(file_name, encoding="latin1")

raw_sales.head()
```

# 7. Normalización de nombres de columnas

Para trabajar con nombres más cómodos en Python, convertimos los nombres de columnas a formato simple:

- Minúsculas.
- Sin espacios.
- Sin guiones.

Ejemplo:

`Sub-Category` se convierte en `sub_category`.

```python
# Se normalizan los nombres de columnas para facilitar el trabajo en Python.
# Esta decisión mejora la legibilidad del código sin cambiar los datos originales.

raw_sales.columns = (
    raw_sales.columns
    .str.strip()
    .str.lower()
    .str.replace(" ", "_", regex=False)
    .str.replace("-", "_", regex=False)
)

raw_sales.columns
```

# 8. Validación de columnas necesarias

Antes de analizar, verificamos que el archivo tenga las columnas mínimas necesarias.

Para este taller se requieren:

- `sales`: ventas.
- `quantity`: cantidad vendida.
- `profit`: utilidad.
- `category`: categoría.
- `region`: región.
- `segment`: segmento de cliente.

```python
# Se crea una copia de trabajo para no modificar directamente la tabla original.

sales_data = raw_sales.copy()

required_columns = [
    "sales",
    "quantity",
    "profit",
    "category",
    "region",
    "segment"
]

missing_columns = [
    column for column in required_columns
    if column not in sales_data.columns
]

if len(missing_columns) > 0:
    raise ValueError(f"Faltan columnas necesarias en el archivo: {missing_columns}")

print("Todas las columnas necesarias están disponibles.")
```

# 9. Limpieza básica

La limpieza busca evitar errores en los cálculos.

Se realizarán acciones simples:

- Eliminar registros sin ventas.
- Eliminar registros sin cantidad.
- Eliminar registros sin utilidad.
- Conservar ventas mayores o iguales a cero.
- Crear margen de utilidad.

```python
# Se eliminan registros con valores nulos en variables clave.
# Esto evita errores al calcular ventas, utilidad y métricas.

sales_data = sales_data.dropna(subset=["sales", "quantity", "profit"])

# Se conservan registros con ventas no negativas.
# En este dataset las ventas deberían ser positivas.

sales_data = sales_data[sales_data["sales"] >= 0].copy()

# Se reinicia el índice para dejar la tabla ordenada después de la limpieza.

sales_data = sales_data.reset_index(drop=True)

# Se calcula el margen de utilidad.
# Margen = utilidad / ventas.
# Si sales es cero, se reemplaza por NaN para evitar división por cero.

sales_data["profit_margin"] = sales_data["profit"] / sales_data["sales"].replace(0, np.nan)

# Se eliminan valores infinitos en caso de divisiones problemáticas.

sales_data["profit_margin"] = sales_data["profit_margin"].replace([np.inf, -np.inf], np.nan)

sales_data.head()
```

# 10. Creación de fecha didáctica

El archivo real adjunto no tiene columna de fecha.

Como el pronóstico necesita una variable temporal, crearemos una fecha didáctica entre 2020 y 2023.

Esta fecha no representa una fecha real de venta. Su objetivo es permitir la práctica de:

- Series de tiempo.
- Ventas mensuales.
- Ventas de fin de semana.
- Modelos de pronóstico.
- Integración con Power BI.

En una empresa real, este paso debe reemplazarse por la columna real de fecha.

```python
# Se mezclan las filas de forma reproducible.
# random_state permite que todos obtengan el mismo resultado en clase.

sales_data = sales_data.sample(frac=1, random_state=42).reset_index(drop=True)

# Se crea un calendario diario entre 2020 y 2023.

calendar_days = pd.date_range(
    start="2020-01-01",
    end="2023-12-31",
    freq="D"
)

# Se asigna una fecha del calendario a cada registro.
# Si hay más registros que días, el calendario se repite.

date_positions = np.arange(len(sales_data)) % len(calendar_days)

sales_data["order_date"] = calendar_days[date_positions]

# Se crean variables de calendario útiles para análisis administrativo.

sales_data["year"] = sales_data["order_date"].dt.year
sales_data["month"] = sales_data["order_date"].dt.month
sales_data["month_start"] = sales_data["order_date"].values.astype("datetime64[M]")

sales_data["day_of_week_number"] = sales_data["order_date"].dt.dayofweek
sales_data["day_of_week_name"] = sales_data["order_date"].dt.day_name()

sales_data["is_weekend"] = sales_data["day_of_week_number"].isin([5, 6]).astype(int)

sales_data["day_type"] = np.where(
    sales_data["is_weekend"] == 1,
    "Fin de semana",
    "Día hábil"
)

sales_data.head()
```

# 11. Exploración inicial del dataset

Antes de pronosticar, conviene entender el comportamiento comercial general.

Revisaremos:

- Ventas totales.
- Utilidad total.
- Cantidad vendida.
- Número de registros.
- Ventas por categoría.
- Ventas por región.
- Ventas por segmento.

```python
# Resumen ejecutivo básico.

total_sales = sales_data["sales"].sum()
total_profit = sales_data["profit"].sum()
total_quantity = sales_data["quantity"].sum()
total_rows = len(sales_data)

print(f"Ventas totales: {total_sales:,.2f}")
print(f"Utilidad total: {total_profit:,.2f}")
print(f"Cantidad vendida: {total_quantity:,.0f}")
print(f"Número de registros: {total_rows:,.0f}")
```

```python
# Ventas por categoría.

sales_by_category = (
    sales_data
    .groupby("category", as_index=False)
    .agg(
        sales=("sales", "sum"),
        profit=("profit", "sum"),
        quantity=("quantity", "sum")
    )
    .sort_values("sales", ascending=False)
)

sales_by_category
```

```python
# Gráfico de ventas por categoría.

plt.figure(figsize=(8, 4))
plt.bar(sales_by_category["category"], sales_by_category["sales"])
plt.title("Ventas por categoría")
plt.xlabel("Categoría")
plt.ylabel("Ventas")
plt.xticks(rotation=45)
plt.grid(axis="y")
plt.show()
```

```python
# Ventas por región.

sales_by_region = (
    sales_data
    .groupby("region", as_index=False)
    .agg(
        sales=("sales", "sum"),
        profit=("profit", "sum"),
        quantity=("quantity", "sum")
    )
    .sort_values("sales", ascending=False)
)

sales_by_region
```

```python
# Gráfico de ventas por región.

plt.figure(figsize=(8, 4))
plt.bar(sales_by_region["region"], sales_by_region["sales"])
plt.title("Ventas por región")
plt.xlabel("Región")
plt.ylabel("Ventas")
plt.xticks(rotation=45)
plt.grid(axis="y")
plt.show()
```

```python
# Ventas por segmento de cliente.

sales_by_segment = (
    sales_data
    .groupby("segment", as_index=False)
    .agg(
        sales=("sales", "sum"),
        profit=("profit", "sum"),
        quantity=("quantity", "sum")
    )
    .sort_values("sales", ascending=False)
)

sales_by_segment
```

# 12. Análisis de ventas en fin de semana

Este análisis permite responder una pregunta administrativa sencilla:

¿Las ventas son diferentes entre días hábiles y fines de semana?

En este notebook la fecha es didáctica, por lo tanto este análisis también es pedagógico.

```python
# Ventas según tipo de día.

sales_by_day_type = (
    sales_data
    .groupby("day_type", as_index=False)
    .agg(
        sales=("sales", "sum"),
        profit=("profit", "sum"),
        quantity=("quantity", "sum"),
        transactions=("sales", "count")
    )
    .sort_values("sales", ascending=False)
)

sales_by_day_type
```

```python
# Gráfico de ventas en días hábiles y fines de semana.

plt.figure(figsize=(7, 4))
plt.bar(sales_by_day_type["day_type"], sales_by_day_type["sales"])
plt.title("Ventas por tipo de día")
plt.xlabel("Tipo de día")
plt.ylabel("Ventas")
plt.grid(axis="y")
plt.show()
```

```python
# Ventas por día de la semana.

sales_by_weekday = (
    sales_data
    .groupby(["day_of_week_number", "day_of_week_name"], as_index=False)
    .agg(
        sales=("sales", "sum"),
        profit=("profit", "sum"),
        transactions=("sales", "count")
    )
    .sort_values("day_of_week_number")
)

sales_by_weekday
```

# 13. Construcción de la serie mensual de ventas

Para pronosticar, necesitamos una tabla con una fila por periodo de tiempo.

En este caso construiremos una serie mensual:

`fecha mensual` → `ventas totales del mes`

También se conservan utilidad, cantidad y número de transacciones.

```python
# Se agrupan las ventas por mes.
# La variable objetivo del pronóstico será sales.

monthly_sales = (
    sales_data
    .groupby("month_start", as_index=False)
    .agg(
        sales=("sales", "sum"),
        profit=("profit", "sum"),
        quantity=("quantity", "sum"),
        transactions=("sales", "count")
    )
    .rename(columns={"month_start": "date"})
)

monthly_sales = monthly_sales.sort_values("date").reset_index(drop=True)

monthly_sales.head()
```

```python
# Gráfico de la serie mensual.

plt.figure(figsize=(12, 5))
plt.plot(monthly_sales["date"], monthly_sales["sales"], marker="o")
plt.title("Ventas mensuales")
plt.xlabel("Fecha")
plt.ylabel("Ventas")
plt.grid(True)
plt.show()
```

# 14. División entre entrenamiento y prueba

Para evaluar los algoritmos, se separan los datos en dos partes:

- Entrenamiento: meses usados para aprender el patrón.
- Prueba: últimos meses usados para evaluar qué tan bien predice el modelo.

En este taller usaremos los últimos 6 meses como periodo de prueba.

```python
# Número de meses que se usarán para validar los modelos.

test_months = 6

# Número de meses que se proyectarán hacia el futuro.

forecast_months = 6

# División entre entrenamiento y prueba.

train_data = monthly_sales.iloc[:-test_months].copy()
test_data = monthly_sales.iloc[-test_months:].copy()

# Fechas futuras para la proyección.

future_dates = pd.date_range(
    start=monthly_sales["date"].max() + pd.DateOffset(months=1),
    periods=forecast_months,
    freq="MS"
)

print("Meses de entrenamiento:", len(train_data))
print("Meses de prueba:", len(test_data))
print("Meses futuros a pronosticar:", forecast_months)
```

# 15. Métricas de evaluación

Para comparar modelos usaremos tres métricas.

## MAE

Error absoluto medio. Indica en promedio cuántas unidades monetarias se equivoca el modelo.

## RMSE

Raíz del error cuadrático medio. Penaliza más los errores grandes.

## MAPE

Error porcentual absoluto medio. Es muy útil para comunicar resultados a perfiles administrativos.

Ejemplo:

Un MAPE de 12% significa que el modelo se equivoca aproximadamente 12% en promedio.

# 16. Algoritmo 1: Promedio histórico

## Idea intuitiva

El futuro será similar al promedio de las ventas históricas.

## Lectura administrativa

Es un modelo muy simple. Puede servir como estimación conservadora cuando no se quiere asumir crecimiento ni caída.

## Instrucción estadística

Pronóstico = promedio de ventas del periodo de entrenamiento.

```python
# Se calcula el promedio histórico de ventas del periodo de entrenamiento.

historical_average = train_data["sales"].mean()

# Para todos los meses de prueba, el pronóstico será el mismo promedio histórico.

test_data["pred_average"] = historical_average

# Proyección futura con el mismo valor promedio.

future_average = pd.DataFrame({
    "date": future_dates,
    "forecast": historical_average
})

# Métricas del modelo.

mae_average = mean_absolute_error(
    test_data["sales"],
    test_data["pred_average"]
)

rmse_average = np.sqrt(
    mean_squared_error(
        test_data["sales"],
        test_data["pred_average"]
    )
)

mape_average = np.mean(
    np.abs(
        (test_data["sales"] - test_data["pred_average"]) / test_data["sales"]
    )
) * 100

print(f"MAE: {mae_average:,.2f}")
print(f"RMSE: {rmse_average:,.2f}")
print(f"MAPE: {mape_average:,.2f}%")
```

```python
# Gráfico estándar del modelo Promedio histórico.
# El mismo formato se repetirá en todos los algoritmos.

plt.figure(figsize=(13, 5))
plt.plot(train_data["date"], train_data["sales"], label="Histórico de entrenamiento")
plt.plot(test_data["date"], test_data["sales"], label="Ventas reales recientes")
plt.plot(test_data["date"], test_data["pred_average"], linestyle="--", label="Predicción sobre datos pasados")
plt.plot(future_average["date"], future_average["forecast"], linestyle="--", label="Proyección futura")
plt.title("Pronóstico de ventas - Promedio histórico")
plt.xlabel("Fecha")
plt.ylabel("Ventas")
plt.legend()
plt.grid(True)
plt.show()
```

# 17. Algoritmo 2: Naive

## Idea intuitiva

El próximo mes será igual al último mes conocido.

## Lectura administrativa

Es un modelo base. Cualquier modelo más elaborado debería superar este resultado.

## Instrucción estadística

Pronóstico del futuro = última venta observada.

```python
# Último valor de ventas del periodo de entrenamiento.

last_train_sales = train_data["sales"].iloc[-1]

# Pronóstico para los meses de prueba.

test_data["pred_naive"] = last_train_sales

# Proyección futura.

future_naive = pd.DataFrame({
    "date": future_dates,
    "forecast": last_train_sales
})

# Métricas.

mae_naive = mean_absolute_error(
    test_data["sales"],
    test_data["pred_naive"]
)

rmse_naive = np.sqrt(
    mean_squared_error(
        test_data["sales"],
        test_data["pred_naive"]
    )
)

mape_naive = np.mean(
    np.abs(
        (test_data["sales"] - test_data["pred_naive"]) / test_data["sales"]
    )
) * 100

print(f"MAE: {mae_naive:,.2f}")
print(f"RMSE: {rmse_naive:,.2f}")
print(f"MAPE: {mape_naive:,.2f}%")
```

```python
# Gráfico estándar del modelo Naive.

plt.figure(figsize=(13, 5))
plt.plot(train_data["date"], train_data["sales"], label="Histórico de entrenamiento")
plt.plot(test_data["date"], test_data["sales"], label="Ventas reales recientes")
plt.plot(test_data["date"], test_data["pred_naive"], linestyle="--", label="Predicción sobre datos pasados")
plt.plot(future_naive["date"], future_naive["forecast"], linestyle="--", label="Proyección futura")
plt.title("Pronóstico de ventas - Naive")
plt.xlabel("Fecha")
plt.ylabel("Ventas")
plt.legend()
plt.grid(True)
plt.show()
```

# 18. Algoritmo 3: Promedio móvil de 3 meses

## Idea intuitiva

El futuro se parece al promedio de los últimos 3 meses.

## Lectura administrativa

Ayuda a suavizar variaciones de corto plazo. Es útil cuando un solo mes puede ser atípico.

## Instrucción estadística

Pronóstico = promedio de las ventas de los últimos 3 meses.

```python
# Promedio de los últimos 3 meses del periodo de entrenamiento.

moving_average_3 = train_data["sales"].tail(3).mean()

# Pronóstico para los meses de prueba.

test_data["pred_moving_average_3"] = moving_average_3

# Proyección futura.

future_moving_average_3 = pd.DataFrame({
    "date": future_dates,
    "forecast": moving_average_3
})

# Métricas.

mae_moving_average_3 = mean_absolute_error(
    test_data["sales"],
    test_data["pred_moving_average_3"]
)

rmse_moving_average_3 = np.sqrt(
    mean_squared_error(
        test_data["sales"],
        test_data["pred_moving_average_3"]
    )
)

mape_moving_average_3 = np.mean(
    np.abs(
        (test_data["sales"] - test_data["pred_moving_average_3"]) / test_data["sales"]
    )
) * 100

print(f"MAE: {mae_moving_average_3:,.2f}")
print(f"RMSE: {rmse_moving_average_3:,.2f}")
print(f"MAPE: {mape_moving_average_3:,.2f}%")
```

```python
# Gráfico estándar del modelo Promedio móvil de 3 meses.

plt.figure(figsize=(13, 5))
plt.plot(train_data["date"], train_data["sales"], label="Histórico de entrenamiento")
plt.plot(test_data["date"], test_data["sales"], label="Ventas reales recientes")
plt.plot(test_data["date"], test_data["pred_moving_average_3"], linestyle="--", label="Predicción sobre datos pasados")
plt.plot(future_moving_average_3["date"], future_moving_average_3["forecast"], linestyle="--", label="Proyección futura")
plt.title("Pronóstico de ventas - Promedio móvil 3 meses")
plt.xlabel("Fecha")
plt.ylabel("Ventas")
plt.legend()
plt.grid(True)
plt.show()
```

# 19. Algoritmo 4: Seasonal Naive

## Idea intuitiva

Un mes se parece al mismo mes del año anterior.

Ejemplo:

- Enero de este año se parece a enero del año anterior.
- Diciembre de este año se parece a diciembre del año anterior.

## Lectura administrativa

Es útil cuando la empresa tiene temporadas comerciales: vacaciones, fin de año, regreso a clases, promociones o ciclos de compra.

## Instrucción estadística

Pronóstico del mes actual = ventas del mismo mes del año anterior.

```python
# Se crea una tabla auxiliar para calcular el mismo mes del año anterior.

seasonal_table = monthly_sales[["date", "sales"]].copy()

# shift(12) trae el valor de 12 meses atrás.

seasonal_table["pred_seasonal_naive"] = seasonal_table["sales"].shift(12)

# Se toman los últimos meses para evaluación.

test_seasonal = seasonal_table.iloc[-test_months:].copy()

# Proyección futura usando el mismo mes del año anterior.

future_seasonal_values = []

for future_date in future_dates:
    same_month_last_year = future_date - pd.DateOffset(years=1)

    last_year_sales = monthly_sales.loc[
        monthly_sales["date"] == same_month_last_year,
        "sales"
    ]

    if len(last_year_sales) > 0:
        future_seasonal_values.append(float(last_year_sales.iloc[0]))
    else:
        future_seasonal_values.append(float(train_data["sales"].mean()))

future_seasonal_naive = pd.DataFrame({
    "date": future_dates,
    "forecast": future_seasonal_values
})

# Métricas.

mae_seasonal_naive = mean_absolute_error(
    test_seasonal["sales"],
    test_seasonal["pred_seasonal_naive"]
)

rmse_seasonal_naive = np.sqrt(
    mean_squared_error(
        test_seasonal["sales"],
        test_seasonal["pred_seasonal_naive"]
    )
)

mape_seasonal_naive = np.mean(
    np.abs(
        (test_seasonal["sales"] - test_seasonal["pred_seasonal_naive"]) / test_seasonal["sales"]
    )
) * 100

print(f"MAE: {mae_seasonal_naive:,.2f}")
print(f"RMSE: {rmse_seasonal_naive:,.2f}")
print(f"MAPE: {mape_seasonal_naive:,.2f}%")
```

```python
# Gráfico estándar del modelo Seasonal Naive.

plt.figure(figsize=(13, 5))
plt.plot(train_data["date"], train_data["sales"], label="Histórico de entrenamiento")
plt.plot(test_seasonal["date"], test_seasonal["sales"], label="Ventas reales recientes")
plt.plot(test_seasonal["date"], test_seasonal["pred_seasonal_naive"], linestyle="--", label="Predicción sobre datos pasados")
plt.plot(future_seasonal_naive["date"], future_seasonal_naive["forecast"], linestyle="--", label="Proyección futura")
plt.title("Pronóstico de ventas - Seasonal Naive")
plt.xlabel("Fecha")
plt.ylabel("Ventas")
plt.legend()
plt.grid(True)
plt.show()
```

# 20. Algoritmo 5: Holt-Winters

## Idea intuitiva

Holt-Winters combina tres componentes:

- Nivel: valor base de las ventas.
- Tendencia: dirección general de las ventas.
- Estacionalidad: patrones que se repiten cada cierto número de meses.

## Lectura administrativa

Es un buen primer modelo formal para pronóstico de ventas porque considera crecimiento, caída y temporadas.

## Instrucción estadística

Ventas futuras = nivel + tendencia + estacionalidad.

```python
# Modelo Holt-Winters usando el periodo de entrenamiento.
# seasonal_periods=12 indica que se espera un patrón anual en datos mensuales.

holt_winters_model = ExponentialSmoothing(
    train_data["sales"],
    trend="add",
    seasonal="add",
    seasonal_periods=12
)

holt_winters_fit = holt_winters_model.fit(optimized=True)

# Pronóstico sobre el periodo de prueba.

test_data["pred_holt_winters"] = holt_winters_fit.forecast(test_months).values

# Para proyectar futuro, se entrena el modelo final con toda la serie disponible.

final_holt_winters_model = ExponentialSmoothing(
    monthly_sales["sales"],
    trend="add",
    seasonal="add",
    seasonal_periods=12
)

final_holt_winters_fit = final_holt_winters_model.fit(optimized=True)

future_holt_winters = pd.DataFrame({
    "date": future_dates,
    "forecast": final_holt_winters_fit.forecast(forecast_months).values
})

# Métricas.

mae_holt_winters = mean_absolute_error(
    test_data["sales"],
    test_data["pred_holt_winters"]
)

rmse_holt_winters = np.sqrt(
    mean_squared_error(
        test_data["sales"],
        test_data["pred_holt_winters"]
    )
)

mape_holt_winters = np.mean(
    np.abs(
        (test_data["sales"] - test_data["pred_holt_winters"]) / test_data["sales"]
    )
) * 100

print(f"MAE: {mae_holt_winters:,.2f}")
print(f"RMSE: {rmse_holt_winters:,.2f}")
print(f"MAPE: {mape_holt_winters:,.2f}%")
```

```python
# Gráfico estándar del modelo Holt-Winters.

plt.figure(figsize=(13, 5))
plt.plot(train_data["date"], train_data["sales"], label="Histórico de entrenamiento")
plt.plot(test_data["date"], test_data["sales"], label="Ventas reales recientes")
plt.plot(test_data["date"], test_data["pred_holt_winters"], linestyle="--", label="Predicción sobre datos pasados")
plt.plot(future_holt_winters["date"], future_holt_winters["forecast"], linestyle="--", label="Proyección futura")
plt.title("Pronóstico de ventas - Holt-Winters")
plt.xlabel("Fecha")
plt.ylabel("Ventas")
plt.legend()
plt.grid(True)
plt.show()
```

# 21. Algoritmo 6: Regresión lineal

## Idea intuitiva

La regresión lineal ajusta una línea para representar la tendencia general de las ventas.

## Lectura administrativa

Permite interpretar si las ventas tienden a subir o bajar.

- Pendiente positiva: tendencia creciente.
- Pendiente negativa: tendencia decreciente.

## Instrucción estadística

Ventas = intercepto + pendiente × tiempo.

```python
# Se crea una copia con una variable de tiempo numérica.

regression_data = monthly_sales.copy()

regression_data["time_index"] = np.arange(len(regression_data))

# División entre entrenamiento y prueba.

train_regression = regression_data.iloc[:-test_months].copy()
test_regression = regression_data.iloc[-test_months:].copy()

# Entrenamiento del modelo.

linear_model = LinearRegression()

linear_model.fit(
    train_regression[["time_index"]],
    train_regression["sales"]
)

# Predicción sobre el periodo de prueba.

test_regression["pred_linear_regression"] = linear_model.predict(
    test_regression[["time_index"]]
)

# Proyección futura.

future_time_index = np.arange(
    len(monthly_sales),
    len(monthly_sales) + forecast_months
)

future_linear_regression = pd.DataFrame({
    "date": future_dates,
    "time_index": future_time_index
})

future_linear_regression["forecast"] = linear_model.predict(
    future_linear_regression[["time_index"]]
)

# Métricas.

mae_linear_regression = mean_absolute_error(
    test_regression["sales"],
    test_regression["pred_linear_regression"]
)

rmse_linear_regression = np.sqrt(
    mean_squared_error(
        test_regression["sales"],
        test_regression["pred_linear_regression"]
    )
)

mape_linear_regression = np.mean(
    np.abs(
        (test_regression["sales"] - test_regression["pred_linear_regression"]) / test_regression["sales"]
    )
) * 100

print(f"Pendiente del modelo: {linear_model.coef_[0]:,.2f}")
print(f"Intercepto del modelo: {linear_model.intercept_:,.2f}")
print(f"MAE: {mae_linear_regression:,.2f}")
print(f"RMSE: {rmse_linear_regression:,.2f}")
print(f"MAPE: {mape_linear_regression:,.2f}%")
```

```python
# Gráfico estándar del modelo de Regresión lineal.

plt.figure(figsize=(13, 5))
plt.plot(train_regression["date"], train_regression["sales"], label="Histórico de entrenamiento")
plt.plot(test_regression["date"], test_regression["sales"], label="Ventas reales recientes")
plt.plot(test_regression["date"], test_regression["pred_linear_regression"], linestyle="--", label="Predicción sobre datos pasados")
plt.plot(future_linear_regression["date"], future_linear_regression["forecast"], linestyle="--", label="Proyección futura")
plt.title("Pronóstico de ventas - Regresión lineal")
plt.xlabel("Fecha")
plt.ylabel("Ventas")
plt.legend()
plt.grid(True)
plt.show()
```

# 22. Algoritmo 7: Random Forest

## Idea intuitiva

Random Forest crea muchos árboles de decisión y combina sus resultados.

Un árbol de decisión hace preguntas como:

- ¿El mes es diciembre?
- ¿Las ventas del mes anterior fueron altas?
- ¿El promedio de los últimos meses fue bajo?
- ¿Las ventas de hace 12 meses fueron similares?

## Lectura administrativa

Es un modelo introductorio de Machine Learning. Puede capturar relaciones no lineales, pero requiere crear variables explicativas.

En este taller se usarán variables simples:

- Mes.
- Número de periodo.
- Ventas del mes anterior.
- Ventas de hace 3 meses.
- Ventas de hace 12 meses.
- Promedio de los últimos 3 meses.

```python
# Se crea una tabla para Machine Learning con variables de calendario y rezagos.

ml_data = monthly_sales.copy()

ml_data["month"] = ml_data["date"].dt.month
ml_data["time_index"] = np.arange(len(ml_data))

# Rezagos: ventas observadas en periodos anteriores.

ml_data["lag_1"] = ml_data["sales"].shift(1)
ml_data["lag_3"] = ml_data["sales"].shift(3)
ml_data["lag_12"] = ml_data["sales"].shift(12)

# Promedio móvil de los últimos 3 meses.
# Se usa shift(1) para evitar usar el valor del mismo mes que se quiere predecir.

ml_data["rolling_mean_3"] = (
    ml_data["sales"]
    .shift(1)
    .rolling(3)
    .mean()
)

# Se eliminan filas con valores nulos generados por los rezagos.

ml_data = ml_data.dropna().reset_index(drop=True)

ml_data.head()
```

```python
# Variables explicativas del modelo.

feature_columns = [
    "month",
    "time_index",
    "lag_1",
    "lag_3",
    "lag_12",
    "rolling_mean_3"
]

# División entre entrenamiento y prueba.

train_ml = ml_data.iloc[:-test_months].copy()
test_ml = ml_data.iloc[-test_months:].copy()

# Entrenamiento del modelo.

random_forest_model = RandomForestRegressor(
    n_estimators=300,
    max_depth=5,
    random_state=42
)

random_forest_model.fit(
    train_ml[feature_columns],
    train_ml["sales"]
)

# Predicción sobre el periodo de prueba.

test_ml["pred_random_forest"] = random_forest_model.predict(
    test_ml[feature_columns]
)

# Proyección futura.
# Se hace de forma iterativa: cada pronóstico futuro se usa para construir el siguiente.

history_sales = monthly_sales["sales"].tolist()

future_random_forest_values = []

for step in range(forecast_months):
    next_date = future_dates[step]
    next_time_index = len(monthly_sales) + step

    next_row = pd.DataFrame({
        "month": [next_date.month],
        "time_index": [next_time_index],
        "lag_1": [history_sales[-1]],
        "lag_3": [history_sales[-3]],
        "lag_12": [history_sales[-12]],
        "rolling_mean_3": [np.mean(history_sales[-3:])]
    })

    next_forecast = random_forest_model.predict(next_row[feature_columns])[0]

    future_random_forest_values.append(float(next_forecast))

    history_sales.append(float(next_forecast))

future_random_forest = pd.DataFrame({
    "date": future_dates,
    "forecast": future_random_forest_values
})

# Métricas.

mae_random_forest = mean_absolute_error(
    test_ml["sales"],
    test_ml["pred_random_forest"]
)

rmse_random_forest = np.sqrt(
    mean_squared_error(
        test_ml["sales"],
        test_ml["pred_random_forest"]
    )
)

mape_random_forest = np.mean(
    np.abs(
        (test_ml["sales"] - test_ml["pred_random_forest"]) / test_ml["sales"]
    )
) * 100

print(f"MAE: {mae_random_forest:,.2f}")
print(f"RMSE: {rmse_random_forest:,.2f}")
print(f"MAPE: {mape_random_forest:,.2f}%")
```

```python
# Gráfico estándar del modelo Random Forest.

plt.figure(figsize=(13, 5))
plt.plot(train_ml["date"], train_ml["sales"], label="Histórico de entrenamiento")
plt.plot(test_ml["date"], test_ml["sales"], label="Ventas reales recientes")
plt.plot(test_ml["date"], test_ml["pred_random_forest"], linestyle="--", label="Predicción sobre datos pasados")
plt.plot(future_random_forest["date"], future_random_forest["forecast"], linestyle="--", label="Proyección futura")
plt.title("Pronóstico de ventas - Random Forest")
plt.xlabel("Fecha")
plt.ylabel("Ventas")
plt.legend()
plt.grid(True)
plt.show()
```

# 23. Comparación de modelos

Ahora se comparan todos los modelos con las mismas métricas.

El modelo con menor MAPE es, en principio, el más fácil de comunicar a un equipo administrativo porque expresa el error como porcentaje.

```python
# Tabla comparativa de modelos.

model_results = pd.DataFrame([
    {
        "model": "Promedio histórico",
        "MAE": mae_average,
        "RMSE": rmse_average,
        "MAPE": mape_average
    },
    {
        "model": "Naive",
        "MAE": mae_naive,
        "RMSE": rmse_naive,
        "MAPE": mape_naive
    },
    {
        "model": "Promedio móvil 3 meses",
        "MAE": mae_moving_average_3,
        "RMSE": rmse_moving_average_3,
        "MAPE": mape_moving_average_3
    },
    {
        "model": "Seasonal Naive",
        "MAE": mae_seasonal_naive,
        "RMSE": rmse_seasonal_naive,
        "MAPE": mape_seasonal_naive
    },
    {
        "model": "Holt-Winters",
        "MAE": mae_holt_winters,
        "RMSE": rmse_holt_winters,
        "MAPE": mape_holt_winters
    },
    {
        "model": "Regresión lineal",
        "MAE": mae_linear_regression,
        "RMSE": rmse_linear_regression,
        "MAPE": mape_linear_regression
    },
    {
        "model": "Random Forest",
        "MAE": mae_random_forest,
        "RMSE": rmse_random_forest,
        "MAPE": mape_random_forest
    }
])

model_results = model_results.sort_values("MAPE").reset_index(drop=True)

model_results
```

```python
# Gráfico de comparación de MAPE por modelo.

plt.figure(figsize=(11, 4))
plt.bar(model_results["model"], model_results["MAPE"])
plt.title("Comparación de modelos por MAPE")
plt.xlabel("Modelo")
plt.ylabel("MAPE")
plt.xticks(rotation=45, ha="right")
plt.grid(axis="y")
plt.show()
```

# 24. Interpretación administrativa

Después de comparar los modelos, se recomienda responder:

1. ¿Cuál modelo tiene menor MAPE?
2. ¿El error porcentual es aceptable para la empresa?
3. ¿El pronóstico muestra crecimiento, estabilidad o caída?
4. ¿Qué decisiones se podrían tomar con este pronóstico?
5. ¿Se necesita más información real para mejorar el modelo?

Ejemplos de decisiones:

- Ajustar metas comerciales.
- Planear compras.
- Definir inventario.
- Revisar campañas promocionales.
- Estimar capacidad operativa.

# 25. Preparación de datos para Power BI

Power BI funciona muy bien cuando la tabla de pronóstico está en formato largo.

Formato recomendado:

| date | value | model | series_type |
|---|---:|---|---|
| 2023-01-01 | 100000 | Real | Histórico |
| 2023-07-01 | 95000 | Holt-Winters | Predicción sobre datos pasados |
| 2024-01-01 | 110000 | Holt-Winters | Proyección futura |

Este formato permite usar:

- `date` en el eje X.
- `value` en el eje Y.
- `series_type` como leyenda.
- `model` como segmentador.

```python
# Histórico real para Power BI.

historical_powerbi = monthly_sales[["date", "sales"]].copy()
historical_powerbi = historical_powerbi.rename(columns={"sales": "value"})
historical_powerbi["model"] = "Real"
historical_powerbi["series_type"] = "Histórico"

# Predicciones sobre datos pasados.

average_validation = test_data[["date", "pred_average"]].copy()
average_validation = average_validation.rename(columns={"pred_average": "value"})
average_validation["model"] = "Promedio histórico"
average_validation["series_type"] = "Predicción sobre datos pasados"

naive_validation = test_data[["date", "pred_naive"]].copy()
naive_validation = naive_validation.rename(columns={"pred_naive": "value"})
naive_validation["model"] = "Naive"
naive_validation["series_type"] = "Predicción sobre datos pasados"

moving_average_validation = test_data[["date", "pred_moving_average_3"]].copy()
moving_average_validation = moving_average_validation.rename(columns={"pred_moving_average_3": "value"})
moving_average_validation["model"] = "Promedio móvil 3 meses"
moving_average_validation["series_type"] = "Predicción sobre datos pasados"

seasonal_validation = test_seasonal[["date", "pred_seasonal_naive"]].copy()
seasonal_validation = seasonal_validation.rename(columns={"pred_seasonal_naive": "value"})
seasonal_validation["model"] = "Seasonal Naive"
seasonal_validation["series_type"] = "Predicción sobre datos pasados"

holt_winters_validation = test_data[["date", "pred_holt_winters"]].copy()
holt_winters_validation = holt_winters_validation.rename(columns={"pred_holt_winters": "value"})
holt_winters_validation["model"] = "Holt-Winters"
holt_winters_validation["series_type"] = "Predicción sobre datos pasados"

linear_regression_validation = test_regression[["date", "pred_linear_regression"]].copy()
linear_regression_validation = linear_regression_validation.rename(columns={"pred_linear_regression": "value"})
linear_regression_validation["model"] = "Regresión lineal"
linear_regression_validation["series_type"] = "Predicción sobre datos pasados"

random_forest_validation = test_ml[["date", "pred_random_forest"]].copy()
random_forest_validation = random_forest_validation.rename(columns={"pred_random_forest": "value"})
random_forest_validation["model"] = "Random Forest"
random_forest_validation["series_type"] = "Predicción sobre datos pasados"

# Proyecciones futuras.

average_future = future_average.copy()
average_future = average_future.rename(columns={"forecast": "value"})
average_future["model"] = "Promedio histórico"
average_future["series_type"] = "Proyección futura"

naive_future = future_naive.copy()
naive_future = naive_future.rename(columns={"forecast": "value"})
naive_future["model"] = "Naive"
naive_future["series_type"] = "Proyección futura"

moving_average_future = future_moving_average_3.copy()
moving_average_future = moving_average_future.rename(columns={"forecast": "value"})
moving_average_future["model"] = "Promedio móvil 3 meses"
moving_average_future["series_type"] = "Proyección futura"

seasonal_future = future_seasonal_naive.copy()
seasonal_future = seasonal_future.rename(columns={"forecast": "value"})
seasonal_future["model"] = "Seasonal Naive"
seasonal_future["series_type"] = "Proyección futura"

holt_winters_future = future_holt_winters.copy()
holt_winters_future = holt_winters_future.rename(columns={"forecast": "value"})
holt_winters_future["model"] = "Holt-Winters"
holt_winters_future["series_type"] = "Proyección futura"

linear_regression_future = future_linear_regression[["date", "forecast"]].copy()
linear_regression_future = linear_regression_future.rename(columns={"forecast": "value"})
linear_regression_future["model"] = "Regresión lineal"
linear_regression_future["series_type"] = "Proyección futura"

random_forest_future = future_random_forest.copy()
random_forest_future = random_forest_future.rename(columns={"forecast": "value"})
random_forest_future["model"] = "Random Forest"
random_forest_future["series_type"] = "Proyección futura"

# Consolidación final para Power BI.

forecast_powerbi = pd.concat(
    [
        historical_powerbi,
        average_validation,
        naive_validation,
        moving_average_validation,
        seasonal_validation,
        holt_winters_validation,
        linear_regression_validation,
        random_forest_validation,
        average_future,
        naive_future,
        moving_average_future,
        seasonal_future,
        holt_winters_future,
        linear_regression_future,
        random_forest_future
    ],
    ignore_index=True
)

forecast_powerbi = forecast_powerbi[["date", "value", "model", "series_type"]]

forecast_powerbi.head()
```

# 26. Exportación de archivos

Se exportan tres archivos:

1. `superstore_clean_for_class.csv`: datos limpios con fecha didáctica.
2. `forecast_powerbi.csv`: histórico, predicciones y futuro en formato listo para Power BI.
3. `model_results.csv`: métricas de comparación de modelos.

```python
# Exportación de archivos CSV.

sales_data.to_csv("superstore_clean_for_class.csv", index=False)
forecast_powerbi.to_csv("forecast_powerbi.csv", index=False)
model_results.to_csv("model_results.csv", index=False)

# Descarga de archivos al computador.

files.download("superstore_clean_for_class.csv")
files.download("forecast_powerbi.csv")
files.download("model_results.csv")
```

# 27. Integración con Power BI

## Opción recomendada para clase

La forma más sencilla es:

1. Ejecutar este notebook en Google Colab.
2. Descargar los archivos CSV.
3. Abrir Power BI Desktop.
4. Ir a `Inicio` → `Obtener datos` → `Texto/CSV`.
5. Cargar:
   - `forecast_powerbi.csv`
   - `model_results.csv`
   - `superstore_clean_for_class.csv`

## Visualización principal de pronóstico

Usar un gráfico de líneas:

- Eje X: `date`
- Eje Y: `value`
- Leyenda: `series_type`
- Segmentador: `model`

Así se puede comparar el histórico, la predicción sobre datos pasados y la proyección futura.

## Visualización de comparación de modelos

Usar un gráfico de barras:

- Eje X: `model`
- Eje Y: `MAPE`

El modelo con menor MAPE tuvo menor error porcentual promedio.

## Medidas DAX opcionales

Ventas totales:

```DAX
Ventas Totales = SUM(forecast_powerbi[value])
```

MAPE promedio:

```DAX
MAPE Promedio = AVERAGE(model_results[MAPE])
```

## Nota sobre integración directa Python + Power BI

Power BI Desktop también permite ejecutar scripts de Python, pero para un curso introductorio suele ser más fácil trabajar con archivos CSV exportados desde Colab.

# 28. Preguntas para discusión en clase

1. ¿Cuál categoría tiene mayores ventas?
2. ¿Cuál región tiene mayor utilidad?
3. ¿Las ventas de fin de semana parecen relevantes en este ejercicio didáctico?
4. ¿Cuál modelo tuvo menor MAPE?
5. ¿Qué modelo es más fácil de explicar a un gerente?
6. ¿Qué modelo parece más sofisticado?
7. ¿Qué limitación tiene usar una fecha simulada?
8. ¿Qué columna faltaría en el dataset para hacer un pronóstico real?
9. ¿Cómo usaría este pronóstico un área comercial?
10. ¿Cómo lo usaría un área de operaciones o inventarios?

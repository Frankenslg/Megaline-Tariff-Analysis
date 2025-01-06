# Megaline Tariff Analysis

Este proyecto realiza un análisis detallado de las tarifas de prepago ofrecidas por la empresa de telecomunicaciones Megaline. El objetivo principal es determinar cuál de las dos tarifas, **Surf** y **Ultimate**, genera más ingresos, ayudando a ajustar el presupuesto de publicidad y optimizar estrategias comerciales.

## Descripción General

1. **Inicialización**: Carga de bibliotecas y conjuntos de datos necesarios para el análisis.
2. **Preparación y corrección de datos**: Inspección y limpieza de los datos, como la conversión de tipos, eliminación de duplicados y corrección de valores faltantes.
3. **Enriquecimiento de datos**: Cálculo de métricas adicionales, como la duración de la suscripción y el consumo total de servicios.
4. **Análisis exploratorio**: Exploración del comportamiento del cliente en llamadas, mensajes y uso de internet, con gráficos y estadísticas.
5. **Cálculo de ingresos**: Creación de funciones para calcular los ingresos mensuales considerando los límites de los planes y el consumo excedente.
6. **Pruebas de hipótesis**: Comparación estadística de los ingresos entre usuarios de ambos planes.
7. **Conclusiones**: Resumen de hallazgos clave sobre el uso del cliente y la rentabilidad de las tarifas.

## Estructura Paso a Paso

### 1. Inicialización
Carga de las bibliotecas necesarias:
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats
```

Carga de los datos desde archivos CSV:
```python
users = pd.read_csv('/datasets/megaline_users.csv')
calls = pd.read_csv('/datasets/megaline_calls.csv')
messages = pd.read_csv('/datasets/megaline_messages.csv')
internet = pd.read_csv('/datasets/megaline_internet.csv')
plans = pd.read_csv('/datasets/megaline_plans.csv')
```

### 2. Preparación y corrección de datos
- Conversión de tipos de datos (por ejemplo, `datetime`).
- Identificación y eliminación de duplicados.
- Renombramiento de columnas para mayor claridad.

Ejemplo:
```python
users['reg_date'] = pd.to_datetime(users['reg_date'])
users['churn_date'] = pd.to_datetime(users['churn_date'])
plans.rename(columns={
    'usd_monthly_pay': 'monthly_fee_usd',
    'usd_per_gb': 'additional_gb_fee_usd'
}, inplace=True)
```

### 3. Enriquecimiento de datos
- Conversión de MB a GB.
- Cálculo de duración de suscripción y otros indicadores clave.

### 4. Análisis exploratorio
- Visualización de distribuciones y tendencias por plan y mes:
```python
plt.figure(figsize=(10, 6))
sns.boxplot(data=merged_data, x='month', y='duration', hue='plan_name')
plt.title('Distribución de la duración de llamadas por plan y mes')
plt.show()
```

### 5. Cálculo de ingresos
Se desarrolló una función personalizada para calcular los ingresos mensuales:
```python
def calcular_ingresos_mensuales(row):
    income = row['monthly_fee_usd']
    if row['calls_count'] > row['minutes_included']:
        income += (row['calls_count'] - row['minutes_included']) * row['additional_minute_fee_usd']
    if row['messages_count'] > row['messages_included']:
        income += (row['messages_count'] - row['messages_included']) * row['additional_message_fee_usd']
    if row['mb_used_total'] > row['mb_per_month_included']:
        income += np.ceil((row['mb_used_total'] - row['mb_per_month_included']) / 1024) * row['additional_gb_fee_usd']
    return income

merged_data['monthly_revenue_usd'] = merged_data.apply(calcular_ingresos_mensuales, axis=1)
```

### 6. Pruebas de hipótesis
Se realizó una prueba t para comparar los ingresos de los usuarios de los planes:
```python
t_statistic, p_value = stats.ttest_ind(ultimate_revenue, surf_revenue, equal_var=False)
if p_value < 0.05:
    print("Rechazamos la hipótesis nula.")
else:
    print("No podemos rechazar la hipótesis nula.")
```

### 7. Conclusiones
- Los usuarios del plan **Ultimate** generan más ingresos en promedio que los del plan **Surf**.
- Existen patrones de consumo distintos que podrían guiar estrategias de marketing y optimización de tarifas.

## Archivos Disponibles
- **Megaline_Tariff_Analysis.ipynb**: Notebook con el código completo y visualizaciones.
- **Megaline_Tariff_Analysis.py**: Archivo Python con el código optimizado para ejecución.

## Herramientas y Tecnologías
- **Python**: Manipulación de datos y cálculos.
- **Pandas y NumPy**: Procesamiento de datos.
- **Matplotlib y Seaborn**: Visualización de datos.
- **Scipy**: Pruebas de hipótesis estadísticas.

---
© 2025 | Francisco |

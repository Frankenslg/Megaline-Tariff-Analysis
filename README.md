# 📊 **Megaline Tariff Analysis**

## 📝 **Descripción General**
Este proyecto analiza las tarifas "Surf" y "Ultimate" de la empresa de telecomunicaciones Megaline, con el objetivo de identificar cuál genera mayores ingresos. A partir de un análisis de datos detallado, se evaluaron el comportamiento del cliente, los ingresos generados y las diferencias entre las tarifas. El proyecto incluye limpieza, enriquecimiento, análisis estadístico y visualización de datos.

---

## 📂 **Paso a Paso**

### 📥 **1. Inicialización y Carga de Datos**
Se importaron las bibliotecas necesarias y se cargaron los datasets:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy import stats

# Cargar datasets
users = pd.read_csv('/datasets/megaline_users.csv')
calls = pd.read_csv('/datasets/megaline_calls.csv')
messages = pd.read_csv('/datasets/megaline_messages.csv')
internet = pd.read_csv('/datasets/megaline_internet.csv')
plans = pd.read_csv('/datasets/megaline_plans.csv')
```

---

### 🛠️ **2. Preparación y Corrección de Datos**

#### 📊 Información Inicial
```python
print(users.info())
print(calls.info())
print(messages.info())
print(internet.info())
print(plans.info())
```

#### 🔧 Limpieza y Conversión
Se corrigieron los tipos de datos y se eliminaron duplicados:
```python
users['reg_date'] = pd.to_datetime(users['reg_date'])
users['churn_date'] = pd.to_datetime(users['churn_date'])
users.fillna('2024-01-01', inplace=True)

calls['call_date'] = pd.to_datetime(calls['call_date'])
calls['duration'] = np.ceil(calls['duration'])
```

---

### ✨ **3. Enriquecimiento de Datos**

#### 📅 Adición de Columnas
Se agregaron columnas como el mes y el uso total en GB:
```python
calls['month'] = calls['call_date'].dt.month
internet['mb_used'] = np.ceil(internet['mb_used'] / 1024)
internet['month'] = internet['session_date'].dt.month
```

---

### 📊 **4. Integración y Análisis de Datos**

#### 🔗 Combinación de Tablas
Se creó un dataset unificado:
```python
merged_data = calls.merge(messages, on=['user_id', 'month'], how='outer')
merged_data = merged_data.merge(internet, on=['user_id', 'month'], how='outer')
merged_data.fillna(0, inplace=True)
```

#### 💰 Cálculo de Ingresos
Se calcularon los ingresos mensuales por usuario:
```python
def calcular_ingresos(row):
    income = row['monthly_fee_usd']
    if row['calls_count'] > row['minutes_included']:
        income += (row['calls_count'] - row['minutes_included']) * row['additional_minute_fee_usd']
    if row['messages_count'] > row['messages_included']:
        income += (row['messages_count'] - row['messages_included']) * row['additional_message_fee_usd']
    if row['mb_used_total'] > row['mb_per_month_included']:
        income += (row['mb_used_total'] - row['mb_per_month_included']) * row['additional_gb_fee_usd'] / 1024
    return income

merged_data['monthly_revenue_usd'] = merged_data.apply(calcular_ingresos, axis=1)
```

---

### 📈 **5. Visualización de Datos**

#### 📞 Análisis de Llamadas
Gráficos para analizar la duración y cantidad de llamadas:
```python
plt.figure(figsize=(10, 6))
sns.boxplot(data=merged_data, x='plan_name', y='calls_count')
plt.title('Distribution of Calls by Plan')
plt.show()
```

#### 📨 Análisis de Mensajes
```python
plt.figure(figsize=(10, 6))
sns.lineplot(data=merged_data, x='month', y='messages_count', hue='plan_name', marker='o')
plt.title('Monthly Messages by Plan')
plt.show()
```

#### 🌐 Análisis de Internet
```python
plt.figure(figsize=(10, 6))
sns.barplot(data=merged_data, x='month', y='mb_used_total', hue='plan_name')
plt.title('Internet Usage by Plan')
plt.show()
```

---

### 🧪 **6. Prueba de Hipótesis**

#### 📊 Comparación de Ingresos por Plan
Se realizó una prueba t:
```python
t_statistic, p_value = stats.ttest_ind(
    merged_data[merged_data['plan_name'] == 'ultimate']['monthly_revenue_usd'],
    merged_data[merged_data['plan_name'] == 'surf']['monthly_revenue_usd'],
    equal_var=False
)

print(f"Estadístico t: {t_statistic}, Valor p: {p_value}")
```

---

### 🏁 **7. Conclusión General**

1. **Limpieza y Preparación:** Se corrigieron tipos de datos, se eliminaron duplicados y se transformaron unidades.
2. **Análisis Exploratorio:** Se estudiaron las tendencias de llamadas, mensajes y uso de internet.
3. **Cálculo de Ingresos:** Se desarrolló una función para calcular ingresos basados en consumo.
4. **Visualización:** Se utilizaron gráficos para representar patrones de uso y tendencias.
5. **Prueba de Hipótesis:** Se rechazó la hipótesis nula, indicando diferencias significativas entre ingresos por plan.

¡Gracias por revisar este proyecto! 💡

---
© 2025 | Francisco SLG |

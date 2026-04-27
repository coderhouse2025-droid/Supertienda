# 🛒 Supertienda — Análisis de Ventas en Latinoamérica (2019–2023)

> 📊 Análisis de datos y modelado predictivo para Supertienda LatAm. Pipeline completo de Data Science: desde EDA hasta un modelo integrador de Machine Learning sobre 11.964 transacciones.

---

## 📋 Descripción

Este proyecto analiza las ventas de **Supertienda**, una cadena de artículos de oficina con presencia en 22 países de Latinoamérica, durante el período 2019–2023. A partir de un dataset de Kaggle, se construyó un pipeline completo que abarca desde la exploración y limpieza de datos hasta la implementación de cuatro modelos de Machine Learning encadenados en un modelo integrador final.

Los datos cubren ventas segmentadas por región, país, categoría de producto y tipo de cliente, con un volumen global de **USD 44.107.727**.

---

## 🎯 Preguntas de negocio respondidas

- 🌎 ¿En qué países opera Supertienda y cómo se distribuyen geográficamente?
- 👥 ¿Quiénes son los principales segmentos de clientes?
- 📦 ¿Cuáles son los productos con mayor volumen de ventas?
- 💰 ¿Qué región genera mayores ingresos y cuáles son los 3 países líderes por zona?

---

## 🛠️ Stack tecnológico

| Herramienta | Uso |
|---|---|
| 🐍 Python 3 | Lenguaje principal |
| 🐼 Pandas / NumPy | Limpieza y manipulación de datos |
| 📈 Matplotlib / Seaborn / Plotly | Visualizaciones estáticas e interactivas |
| 🤖 Scikit-learn | ML: Isolation Forest, K-Means, Random Forest |
| ⚡ XGBoost / LightGBM | Predicción de ventas y modelo integrador |
| 🔮 Prophet | Serie temporal — proyección de ventas |
| 🎯 implicit / mlxtend | ALS y reglas de asociación (Apriori) |
| 🗺️ GeoPandas / Folium / Geopy | Mapas interactivos de sucursales |
| 🗄️ SQLite3 | Almacenamiento relacional local |
| ☁️ Google Colab + Drive | Entorno de ejecución y fuente del dataset |

---

## 🔬 Estructura del análisis — 5 Ejes de Machine Learning

### 🚨 Eje 1 — Detección de Anomalías (Isolation Forest)
Identificación de transacciones con comportamiento inusual en Venta, Ganancia y Descuento sobre 11.964 registros.
- **598 anomalías detectadas** (5% del total) con `contamination = 0.05`
- Pérdida media en anomalías: **-$1.842** vs -$89 en transacciones normales
- Descuento medio en anomalías: **42%** vs 15% en normales
- Top subcategorías anómalas: Copiadoras (142), Teléfonos (98), Sillas (76)

### 🧩 Eje 2 — Segmentación Avanzada de Clientes (K-Means)
Microsegmentación de 794 clientes con features RFM + comportamiento (8 dimensiones).
- **K = 3** clusters, Silhouette = 0.2322
- 🏆 **Alto valor** (181 clientes): ticket $5.713, ganancia $8.496, ratio anomalías 21%
- 📊 **Estándar** (463 clientes): ticket $3.264, ganancia $1.799, ratio anomalías 7%
- 🌱 **Base** (150 clientes): ticket $2.520, ganancia $1.327, ratio anomalías 4%

### 📅 Eje 3 — Predicción de Ventas (Prophet + XGBoost)
Proyección de ventas mensuales 2024 con histórico 2019–2023. Split temporal: train 2019–2022 → test 2023.
- 🥇 **XGBoost gana** con MAPE < 10% — aceptable para planificación operativa
- Feature más importante: `lag_12` (mismo mes del año anterior) con 100% de importancia relativa
- Estacionalidad fuerte en Q4 (Nov/Dic como meses pico proyectados)

### 🤝 Eje 4 — Recomendación de Productos (ALS + Apriori)
Sistema de cross-selling con filtrado colaborativo implícito y reglas de asociación.
- **ALS** (implicit): 794 clientes × 17 subcategorías, factores latentes = 50
- **Apriori**: 12+ reglas encontradas, Lift ≥ 1.5
- Top par: **Papelería → Accesorios** (Lift 1.82, Confianza 68%)
- Mapa PCA de subcategorías: 3 clusters temáticos (Tecnología / Oficina / Mobiliario)

### 🏆 Eje 5 — Modelo Integrador (Random Forest + LightGBM)
Clasificador que estima la probabilidad de alta rentabilidad por cliente, combinando señales de los 4 ejes anteriores.
- **AUC = 1.00** en test set (RF y LGBM) — separación perfecta de clases en el período analizado
- Variable más importante: ganancia total histórica (100%), seguida de venta total (85%) y cluster K-Means (58%)
- 4 segmentos de acción: A-Premium / B-Potenciales / C-Riesgo / D-No rentables

---

## 🗃️ Dataset

- 📌 **Fuente:** Kaggle — "Supertienda" (artículos de oficina en LatAm)
- 📂 **Formato:** `.xlsx` (Google Drive via `gdown`)
- 🔢 **Registros:** 11.964 transacciones — 2019 a 2023
- 🌎 **Cobertura:** 22 países, 4 regiones (Norte, Centro, Sur, Caribe)
- 👥 **Clientes únicos:** 794
- 💵 **Volumen total:** USD 44.107.727

---

## 💡 Hallazgos principales del EDA

- 🥇 **Región Norte** lidera ingresos con $13.007.800 (impulsada 100% por México)
- 🏆 **Copiadoras y Teléfonos** son las subcategorías top, acumulando $8.639.145 cada una
- 👤 **Consumidor Final** representa el 52.97% del total de ventas ($23.362.708)
- ⚠️ El 34% de las transacciones tienen ganancia negativa — área crítica para optimización

---

## 📁 Archivos del repositorio

| Archivo | Descripción |
|---|---|
| 📓 `Supertienda (Final).ipynb` | Notebook principal — pipeline completo (5 ejes) |
| 📝 `Supertienda.ipynb` | Versión de desarrollo / borrador |
| 📄 `Supertienda.pdf` | Presentación original del dataset |
| 🤖 `Reporte Claude - Informe Supertienda.pdf` | Informe generado con asistencia de IA |
| 🗺️ `Roadmap - Modelos ML Supertienda.pdf` | Hoja de ruta de modelos ML |
| 📋 `README.md` | Este archivo |

---

## 👤 Autor

Proyecto desarrollado como trabajo final de Data Science — **Coderhouse 2025**.

---

> *"Este análisis no solo provee insights sobre el desempeño actual — sienta las bases para predicción de ventas, segmentación de clientes e implementación de sistemas de recomendación."* 🏆

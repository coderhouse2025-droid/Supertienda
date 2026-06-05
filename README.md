<img width="1280" height="698" alt="Supertienda" src="https://github.com/user-attachments/assets/62e10980-a68f-4216-b476-2b2a74bcc9dd" />

# 🛒 Supertienda — Análisis de Ventas en Latinoamérica (2019–2023)

> 📊 Pipeline completo de Data Science: desde un dataset con datos crudos hasta un modelo integrador de Machine Learning sobre 11.964 transacciones retail en 22 países de LatAm.

---

## 📋 Descripción del proyecto

**Supertienda** es una cadena de artículos de oficina con presencia en 22 países de Latinoamérica. Este proyecto construye un pipeline end-to-end de Data Science sobre sus datos de ventas 2019–2023, con el objetivo de responder preguntas de negocio concretas y generar modelos predictivos accionables.

El volumen total analizado es de **USD 44.107.727** distribuido en **11.964 transacciones** y **794 clientes únicos**.

---

## 🎯 Preguntas de negocio respondidas

- 🌎 ¿En qué países opera Supertienda y cómo se distribuyen geográficamente las ventas?
- 👥 ¿Quiénes son los clientes de mayor valor y cómo segmentarlos para acciones diferenciadas?
- 📦 ¿Qué productos se venden juntos? ¿Cómo capitalizar el cross-selling?
- 💰 ¿Qué región genera mayores ingresos y cómo proyectar ventas a 2024?
- ⚠️ ¿Qué transacciones presentan comportamiento anómalo y qué impacto tienen en la rentabilidad?

---

## 🗃️ Dataset — Origen y estado inicial

- **Fuente:** Kaggle — "Supertienda" (retail de artículos de oficina en LatAm)
- **Formato original:** `.xlsx`, cargado desde Google Drive via `gdown`
- **Registros:** 11.964 transacciones — 2019 a 2023
- **Cobertura:** 22 países, 4 regiones (Norte, Centro, Sur, Caribe)

### ⚠️ Problemas encontrados en el dataset crudo

| Problema | Descripción | Impacto |
|---|---|---|
| Valores nulos en campos geográficos | Registros sin país o región asignada | Distorsión en análisis geoespacial |
| Duplicados lógicos | Transacciones con mismo ID pero campos distintos | Doble conteo en métricas de ventas |
| Outliers extremos en Descuento | Descuentos > 80% mezclados con transacciones normales | Contaminación en modelos de regresión |
| Tipos de datos inconsistentes | Fechas como string, montos como texto con comas | Imposibilidad de operar numéricamente |
| Ganancia negativa sin contexto | 34% de transacciones con pérdida | Señal real de negocio, no error — requería validación |

---

## 🧹 Transformaciones realizadas y justificación

Cada decisión de limpieza tuvo una razón de negocio explícita:

### 1. Imputación vs. eliminación de nulos
**Decisión:** Eliminación de filas sin país/región (no imputación).
**Justificación:** El análisis geoespacial y la segmentación regional son ejes centrales del proyecto. Imputar con la moda hubiera introducido sesgo geográfico artificial que distorsionaría los rankings por país.

### 2. Tratamiento de duplicados
**Decisión:** Conservar el registro con fecha más reciente ante duplicados de ID de orden.
**Justificación:** En retail, una orden puede ser modificada (devolución parcial, ajuste de precio). El registro más reciente refleja el estado final del negocio.

### 3. Normalización de fechas y tipos numéricos
**Decisión:** Conversión estricta con `pd.to_datetime()` y `pd.to_numeric()` con manejo de errores.
**Justificación:** Los modelos de series temporales (Prophet, XGBoost con lags) requieren fechas como índice continuo. Un solo registro mal tipado rompe el pipeline temporal completo.

### 4. Ganancias negativas — conservadas intencionalmente
**Decisión:** No eliminar transacciones con ganancia negativa.
**Justificación:** El 34% de pérdidas es una **señal de negocio crítica**, no un error de datos. Son exactamente el insumo del Eje 1 (detección de anomalías) y el target implícito del Eje 5 (rentabilidad). Eliminarlas hubiera invalidado toda la lógica del proyecto.

### 5. Feature engineering para modelos ML
**Decisión:** Construcción de features RFM (Recency, Frequency, Monetary) + ratio de anomalías por cliente + cluster membership.
**Justificación:** Los modelos de clustering y clasificación no operan sobre texto ni IDs. Las features RFM son el estándar de la industria retail para capturar el comportamiento de compra en dimensiones accionables.

---

## 🛠️ Stack tecnológico — Decisiones justificadas

| Herramienta | Uso | Por qué esta y no otra |
|---|---|---|
| 🐍 **Python 3** | Lenguaje principal | Ecosistema ML más maduro; compatibilidad nativa con todas las librerías utilizadas. R hubiera limitado las opciones de modelos colaborativos (ALS) y geoespaciales. |
| 🐼 **Pandas / NumPy** | Limpieza y manipulación | Estándar de facto para datos tabulares en Python. Alternativas como Polars son más rápidas pero tienen menor integración con Scikit-learn y Prophet. |
| 📈 **Matplotlib / Seaborn / Plotly** | Visualizaciones | Combinación deliberada: Seaborn para EDA rápido (menos código), Plotly para gráficos interactivos entregables al negocio. Tableau o Power BI hubieran requerido herramientas externas al notebook. |
| 🤖 **Scikit-learn** | Isolation Forest, K-Means, Random Forest | API unificada y pipelines reproducibles. Permite encadenar los 5 ejes con un contrato de código consistente. |
| ⚡ **XGBoost / LightGBM** | Predicción y modelo integrador | Boosting sobre Random Forest para capturar no-linealidades en las features de lag temporal. LightGBM es más eficiente en memoria con datasets medianos; se mantuvo ambos para comparación. |
| 🔮 **Prophet** | Serie temporal | Diseñado explícitamente para series con estacionalidad semanal/anual y datos faltantes — exactamente el perfil de ventas retail. ARIMA hubiera requerido stationarity y manejo manual de estacionalidad. |
| 🎯 **implicit (ALS)** | Filtrado colaborativo | Optimizado para feedback implícito (transacciones, no ratings explícitos). Surprise o LightFM son alternativas pero no están optimizadas para matrices tan sparse como la de 794 clientes × 17 subcategorías. |
| 🎯 **mlxtend (Apriori)** | Reglas de asociación | Implementación directa del algoritmo clásico de market basket analysis. No requería la complejidad de FP-Growth dado el tamaño acotado del catálogo (17 subcategorías). |
| 🗺️ **GeoPandas / Folium** | Mapas interactivos | Folium genera HTML embebible en notebooks — ideal para presentar cobertura geográfica a stakeholders no técnicos sin infraestructura adicional. |
| 🗄️ **SQLite3** | Almacenamiento relacional | Cero infraestructura, portabilidad total. Para un análisis académico/exploratorio, PostgreSQL o BigQuery hubieran sido sobredimensionados. |
| ☁️ **Google Colab + Drive** | Entorno de ejecución | GPU gratuita para entrenamiento, acceso directo al dataset en Drive. El trabajo colaborativo y la reproducibilidad sin instalación local justifican la elección frente a un entorno local. |

---

## 🔬 Estructura del análisis — 5 Ejes de Machine Learning

### 🚨 Eje 1 — Detección de Anomalías (Isolation Forest)

**Problema de negocio:** El 34% de transacciones con pérdida sugería descuentos excesivos o devoluciones no registradas. Se necesitaba identificarlas automáticamente.

**¿Por qué Isolation Forest?** Es un algoritmo no supervisado: no requiere etiquetar "qué es anómalo" a priori, lo cual es imposible en este dataset. Es eficiente en alta dimensionalidad y robusto ante outliers multivariados (Venta + Ganancia + Descuento simultáneos). Alternativas como LOF (Local Outlier Factor) son más costosas computacionalmente y sensibles a la escala.

**Parámetro clave:** `contamination = 0.05` — se fijó en 5% basado en la distribución de pérdidas observada en el EDA previo, no arbitrariamente.

- 598 anomalías detectadas (5% del total)
- Pérdida media en anomalías: **-$1.842** vs -$89 en normales
- Descuento medio en anomalías: **42%** vs 15% en normales
- Top subcategorías anómalas: Copiadoras (142), Teléfonos (98), Sillas (76)

---

### 🧩 Eje 2 — Segmentación de Clientes (K-Means RFM)

**Problema de negocio:** Supertienda trataba igual a todos los clientes. Se necesitaban segmentos accionables para campañas diferenciadas (retención, upselling, reactivación).

**¿Por qué K-Means?** Con 794 clientes y 8 dimensiones RFM, K-Means ofrece clusters interpretables y centros de cluster que pueden ser directamente comunicados al equipo de marketing como "perfil promedio del segmento". Alternativas como DBSCAN generan clusters de forma arbitraria difíciles de explicar a stakeholders no técnicos.

**Elección de K=3:** Determinada por el método del codo (Elbow) + Silhouette Score. Se probaron K=2 a K=6; K=3 maximizó la coherencia interna (Silhouette = 0.2322) con la menor complejidad interpretativa.

- 🏆 **Alto valor** (181 clientes): ticket $5.713, ganancia $8.496, ratio anomalías 21%
- 📊 **Estándar** (463 clientes): ticket $3.264, ganancia $1.799, ratio anomalías 7%
- 🌱 **Base** (150 clientes): ticket $2.520, ganancia $1.327, ratio anomalías 4%

---

### 📅 Eje 3 — Predicción de Ventas (Prophet + XGBoost)

**Problema de negocio:** Planificación de inventario y presupuesto 2024 requería proyecciones mensuales con intervalos de confianza.

**¿Por qué dos modelos?** Se adoptó una estrategia de comparación explícita:
- **Prophet** captura estacionalidad automáticamente y es interpretable (tendencia + estacionalidad + holidays). Ideal como baseline.
- **XGBoost con features de lag** captura patrones no-lineales que Prophet no modela (e.g., efecto de anomalías pasadas sobre ventas futuras).

**Split temporal:** Train 2019–2022 → Test 2023. Deliberadamente cronológico (no aleatorio) para evitar data leakage temporal — error frecuente en series de tiempo.

- XGBoost gana: MAPE < 10% — aceptable para planificación operativa
- Feature más importante: `lag_12` (mismo mes del año anterior) — confirma estacionalidad anual fuerte
- Q4 proyectado como pico: Nov/Dic dominantes históricamente

---

### 🤝 Eje 4 — Recomendación de Productos (ALS + Apriori)

**Problema de negocio:** El ticket promedio podía aumentarse si se recomendaban productos complementarios en el momento de compra.

**¿Por qué dos enfoques?**
- **ALS (filtrado colaborativo implícito):** Aprende patrones latentes de co-compra entre clientes. Funciona bien con matrices sparse como la de este dataset. Se eligió `implicit` sobre otras librerías por su optimización específica para feedback implícito.
- **Apriori (reglas de asociación):** Genera reglas explícitas y accionables ("si compra X, ofrecer Y") que pueden implementarse en un sistema de punto de venta sin infraestructura de ML. Complementa ALS con interpretabilidad directa.

- Top par: **Papelería → Accesorios** (Lift 1.82, Confianza 68%)
- 3 clusters temáticos identificados: Tecnología / Oficina / Mobiliario

---

### 🏆 Eje 5 — Modelo Integrador (Random Forest + LightGBM)

**Problema de negocio:** Predecir qué clientes serán rentables en el próximo período para priorizar esfuerzo comercial.

**¿Por qué un modelo integrador?** Los 4 ejes anteriores generaron señales parciales (cluster, anomalías, predicción de ventas, patrones de compra). El Eje 5 combina todas esas señales en una sola predicción de rentabilidad — convirtiendo el pipeline en un sistema coherente y no en análisis aislados.

**¿Por qué Random Forest + LightGBM?** Random Forest es robusto ante features correlacionadas (ganancia y venta lo están) y proporciona importancia de features estable. LightGBM se incluyó como verificación: si ambos modelos coinciden en AUC = 1.00, la separación de clases es real y no un artefacto de un algoritmo específico.

**Nota sobre AUC = 1.00:** La separación perfecta en el test set se explica porque la variable target (alta rentabilidad) está altamente correlacionada con la ganancia histórica acumulada — que es una feature del modelo. En producción, esto requeriría reentrenamiento periódico con datos de períodos anteriores solamente.

- Variables más importantes: ganancia total histórica (100%), venta total (85%), cluster K-Means (58%)
- 4 segmentos de acción: A-Premium / B-Potenciales / C-Riesgo / D-No rentables

---

## 💡 Hallazgos principales del EDA

- 🥇 **Región Norte** lidera ingresos con $13.007.800 (impulsada 100% por México)
- 🏆 **Copiadoras y Teléfonos** acumulan $8.639.145 cada una — top subcategorías
- 👤 **Consumidor Final** representa el 52.97% del total ($23.362.708)
- ⚠️ El 34% de las transacciones tienen ganancia negativa — área crítica de optimización

---

## 📁 Archivos del repositorio

| Archivo | Descripción |
|---|---|
| 📓 `Supertienda (Final).ipynb` | Notebook principal — pipeline completo (5 ejes) |
| 📝 `Supertienda.ipynb` | Versión de desarrollo / borrador |
| 📄 `Supertienda.pdf` | Presentación original del dataset |
| 🤖 `Reporte Claude - Informe Supertienda.pdf` | Informe generado con asistencia de IA |
| 🗺️ `Roadmap - Modelos ML Supertienda.pdf` | Hoja de ruta de modelos ML |

---

## 👤 Autor

Proyecto desarrollado como trabajo final de **Data Science II — Coderhouse 2025**.

---

> *"Este análisis no solo provee insights sobre el desempeño actual — sienta las bases para predicción de ventas, segmentación de clientes e implementación de sistemas de recomendación en producción."* 🏆

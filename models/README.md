# Hospital Readmission Prediction System (MIMIC-IV)

![Python](https://img.shields.io/badge/Python-3.9%2B-blue) ![Library](https://img.shields.io/badge/Library-MIMIC--IV-green) ![Status](https://img.shields.io/badge/Status-Prototype-orange)

## Resumen Ejecutivo
Este proyecto aborda el problema de la "puerta giratoria" en hospitales: pacientes que son dados de alta y reingresan en menos de 30 días. Utilizando datos clínicos reales (**MIMIC-IV**) y Machine Learning, desarrollé un sistema capaz de estratificar el riesgo de reingreso al momento del alta para priorizar intervenciones de enfermería y gestión de casos.

> **Nota Técnica:** Este prototipo fue construido utilizando la versión **Demo de MIMIC-IV** para validar la arquitectura y el pipeline de datos. El código es totalmente escalable a la base de datos completa (400k+ pacientes).

---

## Hallazgos Clínicos Clave (Key Insights)
Tras entrenar un modelo **Random Forest** y aplicar interpretabilidad con **SHAP**, se identificaron los 3 factores más críticos que impulsan el reingreso en esta cohorte:

1.  **Días de Estancia Hospitalaria (`los`):** Variable #1. El modelo detectó que tanto estancias muy cortas (posibles altas prematuras) como estancias prolongadas (mayor complejidad clínica) aumentan drásticamente el riesgo.
2.  **Historial de Ingresos (`prior_admissions`):** Pacientes con ingresos recurrentes previos tienen la mayor probabilidad de fallar en su recuperación ambulatoria.
3.  **Edad del Paciente (`anchor_age`):** La fragilidad asociada a la edad avanzada reduce la capacidad de recuperación autónoma, convirtiéndose en un predictor clave.

---

## Tech Stack & Arquitectura
Este proyecto implementa una arquitectura eficiente que evita servidores SQL pesados, utilizando **DuckDB** para procesar datos clínicos directamente en memoria.

* **Ingeniería de Datos:** `DuckDB`, `SQL` (Window Functions para cálculos temporales).
* **Procesamiento:** `Pandas`, `NumPy`.
* **Modelado:** `Scikit-Learn` (Random Forest con balanceo de clases), `Imbalanced-learn`.
* **Validación:** `GroupShuffleSplit` (Prevención de *data leakage* a nivel de paciente).
* **Interpretabilidad:** `SHAP` (Análisis de impacto de variables).

---

## Estructura del Proyecto

```text
├── data/                  
│   ├── raw/                 # Archivos CSV de MIMIC-IV (Ignorados en git)
│   └── external/            # Mapeos de códigos ICD
├── notebooks/
│   ├── REINGRESO_MIMIC-IV.ipynb    # ETL con DuckDB: De CSV a DataFrame limpio
│                                   # Análisis exploratorio y limpieza
│                                   # Entrenamiento (RF), Evaluación y SHAP values
├── src/                     # Código modularizado (scripts .py)
├── .gitignore               # Configuración de seguridad de datos
├── requirements.txt         # Dependencias del entorno
└── README.md                # Documentación del proyecto



# Clonar repositorio
git clone https://github.com/juanMuelaSoto/reingreso-mimic-iv.git

# Crear entorno virtual
python -m venv venv
source venv/bin/activate  # En Windows: venv\Scripts\activate

# Instalar dependencias
pip install -r requirements.txt



Definición del Target: Diferencia temporal entre dischtime (alta) y siguiente admittime. Exclusión estricta de pacientes fallecidos.

Feature Engineering:

Detección de comorbilidades (Insuficiencia Cardíaca, EPOC, Diabetes) mediante mapeo de códigos ICD-9/10.

Cálculo de historial de ingresos y duración de estancia.

Evaluación:

Se priorizó la métrica Recall sobre la Exactitud (Accuracy), debido al alto costo clínico de los Falsos Negativos (enviar a casa a un paciente grave).

Ajuste de umbral de decisión para maximizar la detección de pacientes vulnerables.



Juan Antonio Muela Soto Data Scientist | Salud & IA www.linkedin.com/in/juan-antonio-muela-soto-09b22735a | juan.muelasoto@gmail.com
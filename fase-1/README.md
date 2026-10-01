# Predicción de adicción al smartphone — Fase 1

Proyecto integrador de Machine Learning basado en la competencia de Kaggle **Predicting Smartphone Addiction — Playground Series Season 6 Episode 8**, conjunto de datos previamente aprobado por el profesor.

## Integrantes

- **[Completar nombre del integrante 1]**
- **[Completar nombre del integrante 2]**
- **[Completar nombre del integrante 3, si aplica]**

## Descripción del problema

Se busca estimar la probabilidad de que una persona pertenezca a la clase `addicted_label = 1` utilizando hábitos de uso del smartphone y características personales. Es un problema de **clasificación binaria**.

- Observaciones de entrenamiento: **691.369**
- Predictores utilizados: **12** (se excluye `id`)
- Variable objetivo: `addicted_label`
- Clase positiva: **70,94 %**
- Métrica principal: **ROC-AUC**

## Fuente del conjunto de datos

Kaggle — **Predicting Smartphone Addiction — Playground Series Season 6 Episode 8**.

## Metodología

1. Descripción y exploración del conjunto de datos.
2. Identificación y cuantificación de valores faltantes.
3. Análisis de distribuciones, relaciones y correlaciones relevantes.
4. Exclusión de `id` como predictor por ser un identificador.
5. División estratificada 80/20 con `random_state=42`.
6. Preprocesamiento aprendido **solo con entrenamiento** para evitar fuga de información:
   - variables numéricas: imputación por mediana e indicadores de ausencia;
   - variables categóricas: categoría explícita `Missing` y One-Hot Encoding.
7. Modelo base: `DummyClassifier(strategy="prior")`.
8. Modelo de referencia: Regresión Logística.
9. Modelo principal: `XGBClassifier`.
10. Evaluación con ROC-AUC y métricas complementarias.
11. Reentrenamiento del pipeline final con todo `train.csv`, almacenamiento con Joblib y generación de predicciones.
12. Recarga del modelo almacenado y verificación de predicción.

## Resultados principales

Validación hold-out estratificada del 20 %:

| Modelo | ROC-AUC | Accuracy | Precision | Recall | F1 |
|---|---:|---:|---:|---:|---:|
| DummyClassifier | 0.5000 | 0.7094 | 0.7094 | 1.0000 | 0.8300 |
| Regresión Logística | 0.9123 | 0.8405 | 0.8720 | 0.9086 | 0.8899 |
| XGBoost | **0.9588** | **0.8945** | **0.9213** | **0.9307** | **0.9260** |

XGBoost fue seleccionado como modelo final porque obtuvo el mejor ROC-AUC en la validación local. Estas métricas corresponden a la partición de validación y no al leaderboard oficial de Kaggle.

## Estructura

```text
fase-1/
├── notebook.ipynb
├── modelo.joblib
├── README.md
├── data/
│   └── README.md
└── outputs/
    ├── metricas.json
    ├── submission.csv
    ├── feature_importance.csv
    └── graficos/
```

## Ejecución

Desde la raíz del repositorio:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook fase-1/notebook.ipynb
```

Para ejecutar el notebook desde cero, los archivos oficiales `train.csv`, `test.csv` y `sample_submission.csv` deben estar en `fase-1/data/`. Después se puede usar **Restart & Run All**.

## Archivos principales

- `fase-1/notebook.ipynb`: análisis, preparación, entrenamiento, evaluación e interpretación.
- `fase-1/modelo.joblib`: pipeline final entrenado, que incluye preprocesamiento y XGBoost.
- `fase-1/outputs/submission.csv`: probabilidades generadas sobre `test.csv`.
- `fase-1/outputs/metricas.json`: métricas de validación.
- `fase-1/outputs/feature_importance.csv`: importancias del modelo final.

## Reproducibilidad y prevención de fuga de información

Las transformaciones que aprenden parámetros están dentro de `Pipeline` y `ColumnTransformer`, por lo que durante la evaluación se ajustan exclusivamente con la partición de entrenamiento. El conjunto de validación no participa en la imputación, codificación ni entrenamiento. `id` no se utiliza como predictor.

## Interpretación

Las importancias del modelo indican qué variables fueron útiles para sus predicciones, pero **no demuestran relaciones causales**. Las conclusiones se limitan al conjunto de datos utilizado y no deben interpretarse como un diagnóstico clínico.

## Git Flow

El repositorio debe mantener el flujo solicitado para el proyecto:

- `main`: versiones estables y entregables.
- `develop`: integración.
- `feature/*`: desarrollo de tareas relevantes.

Las contribuciones, commits y Pull Requests deben corresponder al trabajo real de los integrantes.

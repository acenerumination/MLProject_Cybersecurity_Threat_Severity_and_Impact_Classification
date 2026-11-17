# ML Project on Cybersecurity Threat Severity and Impact Classification

Machine Learning Classification Project focused on the prediction of both severity and impact that a Cybersecurity threat imposes upon being detected.

Proyecto de aula del curso **Modelos y Simulación de Sistemas II** — Departamento de Ingeniería de Sistemas, Universidad de Antioquia.

## Estructura del repositorio

| Archivo                      | Descripción                                                                                                                                                                                                                                                                                                                                                                                |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `DEF_projectModelosII.ipynb` | **Notebook principal y oficial para reproducir todos los resultados del informe final.** Integra el EDA y preprocesamiento (Entregable I), el entrenamiento y evaluación de los 5 modelos (Sección 1), análisis de sensibilidad al balance de clases y prueba de estrés bajo desbalance artificial, reducción de dimensión con PCA y UMAP (Sección 2), y conclusiones generadas dinámicamente a partir de las variables calculadas. |
| `Entregable_Final.pdf`       | Informe final (formato IEEE) que integra los Entregables I y II: descripción del problema, estado del arte, entrenamiento y evaluación de modelos, reducción de dimensión, discusión y conclusiones.                                                                                                                                                                                       |
| `requirements.txt`           | Dependencias con las versiones usadas para generar los resultados.                                                                                                                                                                                                                                                                                                                                                                              |
| `README.md` | Este archivo. |

## Dataset

Se utiliza la base de datos pública [Global Cybersecurity Threats (2015-2024)](https://www.kaggle.com/datasets/atharvasoundankar/global-cybersecurity-threats-2015-2024), disponible en Kaggle (3000 registros de incidentes de ciberseguridad a nivel global).

## Cómo reproducir los resultados

### 1. Requisitos

- Python 3.11 o superior (probado en Google Colab)
- Jupyter Notebook / JupyterLab (o VS Code con la extensión de Jupyter)

### 2. Instalar dependencias

```bash
pip install -r requirements.txt
```

### 3. Carga del dataset

El notebook descarga el dataset **automáticamente** usando `kagglehub` en la primera celda. No es necesario descargarlo manualmente:

```python
import kagglehub
dataset_dir = kagglehub.dataset_download(
    "atharvasoundankar/global-cybersecurity-threats-2015-2024"
)
```

Si `kagglehub` no está instalado o falla la descarga automática, el notebook indica cómo continuar con una ruta manual: descargar el CSV desde Kaggle y colocarlo en el directorio del proyecto.

> Nota: para usar `kagglehub` se requiere una cuenta de Kaggle y tener configuradas las credenciales de la API (`~/.kaggle/kaggle.json`), o autenticarse según se solicite al ejecutar la celda.

### 4. Ejecutar el notebook

Abrir [`DEF_projectModelosII.ipynb`](DEF_projectModelosII.ipynb) y ejecutar las celdas en orden (de arriba hacia abajo). Es el notebook oficial del proyecto y reproduce, en un solo flujo, todos los resultados reportados en el informe final:

1. **EDA y preprocesamiento (Entregable I):** carga y verificación de la base de datos, análisis exploratorio, diagnóstico de calidad (faltantes, duplicados), construcción de la variable objetivo `Severity` (Low/Medium/High), codificación (One-Hot Encoding) y estandarización (Z-score ajustada solo sobre entrenamiento).
2. **Entrenamiento y evaluación de modelos (Sección 1, Entregable II):** validación cruzada estratificada K=5, malla de hiperparámetros para 5 familias de modelos (regresión logística, KNN, Random Forest, MLP y SVM), métricas con intervalos de confianza, y análisis de sensibilidad al balance de clases con prueba de estrés bajo desbalance artificial.
3. **Reducción de dimensión (Sección 2, Entregable II):** análisis individual de variables (ANOVA F e información mutua), extracción lineal con PCA y no lineal con UMAP, reevaluación de los mejores modelos en cada espacio de características, y conclusiones comparativas.

La ejecución completa tarda varios minutos (la malla de 51 configuraciones, la prueba de permutaciones con 1 000 permutaciones y UMAP son las partes más lentas). No interrumpa la ejecución: las figuras y tablas del informe salen de las celdas finales.

## Informe

El reporte final, que integra los Entregables I y II siguiendo la plantilla IEEE, se encuentra en [`Entregable_Final.pdf`](Entregable_Final.pdf).

## Equipo

- Luis Carlos Vanegas Zapata
- Alejandra Cano Espinosa
- Robinson Dario Henao Botero

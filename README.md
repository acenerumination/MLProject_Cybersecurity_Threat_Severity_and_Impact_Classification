# ML Project on Cybersecurity Threat Severity and Impact Classification

Machine Learning Classification Project focused on the prediction of both severity and impact that a Cybersecurity threat imposes upon being detected.

Proyecto de aula del curso **Modelos y Simulación de Sistemas II** — Departamento de Ingeniería de Sistemas, Universidad de Antioquia.

## Estructura del repositorio

| Archivo | Descripción |
|---|---|
| `EDA_Cybersecurity_Threats.ipynb` | Notebook con el análisis exploratorio de datos (EDA), diagnóstico de calidad, estrategia de imputación/codificación y preprocesamiento (One-Hot Encoding, estandarización Z-score). |
| `Entregable1_V1.2.pdf` | Informe del Entregable I (formato IEEE): descripción del problema, composición de la base de datos, paradigma de aprendizaje y estado del arte. |
| `README.md` | Este archivo. |

## Dataset

Se utiliza la base de datos pública [Global Cybersecurity Threats (2015-2024)](https://www.kaggle.com/datasets/atharvasoundankar/global-cybersecurity-threats-2015-2024), disponible en Kaggle (3000 registros de incidentes de ciberseguridad a nivel global).

## Cómo reproducir los resultados

### 1. Requisitos

- Python 3.9+
- Jupyter Notebook / JupyterLab (o VS Code con la extensión de Jupyter)

### 2. Instalar dependencias

```bash
pip install numpy pandas matplotlib seaborn scikit-learn kagglehub
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

Abrir `EDA_Cybersecurity_Threats.ipynb` y ejecutar las celdas en orden (de arriba hacia abajo). El notebook contiene:

1. Carga y verificación de la base de datos.
2. Análisis exploratorio (estadísticos descriptivos, distribución de variables, correlaciones).
3. Diagnóstico de calidad (valores faltantes, duplicados).
4. Construcción de la variable objetivo `Severity` (clasificación multiclase: Low/Medium/High).
5. Estrategia de codificación (One-Hot Encoding) y estandarización (`StandardScaler`).

## Informe

El reporte completo del Entregable I, siguiendo la plantilla IEEE, se encuentra en [`Entregable1_V1.2.pdf`](Entregable1_V1.2.pdf).

## Equipo

- Luis Carlos Vanegas Zapata
- Alejandra Cano Espinosa
- Robinson Dario Henao Botero

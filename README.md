
# ML Project on Cybersecurity Threat Severity and Impact Classification
 
Proyecto de aula del curso **Modelos y Simulación de Sistemas II** — Departamento de Ingeniería de Sistemas, Universidad de Antioquia.
 
Sistema de clasificación supervisada para el **triaje automático de incidentes de ciberseguridad**: dado un incidente en el momento de su detección ($t=0$), predecir su nivel de severidad $Y \in \{\text{Low}, \text{Medium}, \text{High}\}$ para priorizar la respuesta del equipo de seguridad.
 
Todo el proyecto (descripción del problema, análisis exploratorio, entrenamiento y evaluación de modelos, reducción de dimensión y conclusiones) está contenido en un único notebook reproducible.
 
## Estructura del repositorio
 
| Archivo | Descripción |
|---|---|
| `proyectoModelosII.ipynb` | Notebook del proyecto. Ejecuta de principio a fin el análisis completo y genera todas las tablas y figuras. |
| `requirements.txt` | Dependencias del proyecto (sin versiones fijadas). |
| `README.md` | Este archivo. |
 
Al ejecutar el notebook se crean dos carpetas con las salidas:
 
| Carpeta | Contenido |
|---|---|
| `figuras/` | Figuras en PNG (200 dpi): `fig_E1_eda`, `fig_E2_categoricas`, `fig_E3_perdida`, `fig1_hiperparametros`, `fig2_confusion`, `figP_permutacion`, `fig3_balance`, `fig4_variables`, `fig5_pca`, `fig6_umap`, `fig7_espacios`. |
| `tablas/` | Tablas en CSV: `tabla1` (malla de hiperparámetros), `tabla1B` (las 51 configuraciones), `tabla2` (desempeño de los 5 modelos), `tabla3` y `tabla3B` (gestión del balance), `tabla4` (análisis individual de variables), `tabla5` (PCA), `tabla6` (UMAP), `tabla7` (comparación de los tres espacios). |
 
## Dataset
 
Base de datos pública [Global Cybersecurity Threats (2015-2024)](https://www.kaggle.com/datasets/atharvasoundankar/global-cybersecurity-threats-2015-2024) (Kaggle): 3 000 incidentes y 10 variables (país, año, tipo de ataque, sector afectado, usuarios afectados, fuente del ataque, tipo de vulnerabilidad, mecanismo de defensa, pérdida financiera y tiempo de resolución). No tiene datos faltantes ni registros duplicados.
 
**Variable objetivo.** `Severity` no existe en el dataset: se construye como los terciles del índice de impacto $`\text{Impact} = \tfrac{1}{2}\left[\text{rank}_{pct}(\text{Financial Loss}) + \text{rank}_{pct}(\text{Resolution Time})\right]`$, lo que produce tres clases balanceadas (1 000 incidentes cada una).
 
**Restricción anti-fuga.** `Financial Loss`, `Incident Resolution Time` e `Impact_Score` definen la severidad y solo se conocen después de resolver el incidente, por lo que se excluyen de las variables de entrada. El modelo usa únicamente las 8 variables disponibles en $t=0$ (2 numéricas y 6 categóricas → $D = 32$ características tras One-Hot).
 
## Metodología
 
| Etapa | Implementación |
|---|---|
| Partición | Holdout estratificado 80/20 (2 400 entrenamiento / 600 prueba, `random_state=42`). La prueba solo se usa para la evaluación final. |
| Validación | Validación cruzada estratificada $K=5$ sobre el entrenamiento para todas las decisiones: hiperparámetros, dos mejores modelos y número de componentes de PCA y UMAP. |
| Preprocesamiento | Un único `ColumnTransformer` (imputación por mediana/moda, Z-score en numéricas, One-Hot con `drop='first'` en categóricas) dentro de un `Pipeline`, ajustado solo en el pliegue de entrenamiento. |
| Modelos | Regresión logística (paramétrico), KNN (no paramétrico), Random Forest (ensamble de árboles), MLP (red neuronal) y SVM; 51 configuraciones en total con `GridSearchCV`. |
| Métricas | F1-macro (criterio de selección), exactitud, exactitud balanceada y ROC-AUC macro (one-vs-rest). |
| Intervalos de confianza | Validación: $t$ de Student con $K-1$ grados de libertad. Prueba: bootstrap percentil con $B=1000$ réplicas. |
| Línea base | `DummyClassifier` estratificado y prueba de permutación (1 000 permutaciones) sobre el mejor modelo. |
| Balance de clases | Diagnóstico con la razón de desbalance $IR$. Como el dataset está balanceado ($IR=1$), no se aplica corrección; el efecto de `class_weight`, `RandomOverSampler` y `RandomUnderSampler` se demuestra con una prueba de estrés de $IR=5$. |
| Análisis de variables | ANOVA $F$ e información mutua por característica, con corrección de Bonferroni. |
| Reducción de dimensión | PCA (menor $K$ que retiene ≥ 90 % de la varianza) y UMAP ($K \in \{2,3,5,10,15\}$, elegido por parsimonia), reevaluando los dos mejores modelos. |
 
## Resultados principales
 
| Modelo | Espacio | Dimensión | % Reducción | F1-macro validación [IC 95 %] | F1-macro prueba [IC 95 %] |
|---|---|---|---|---|---|
| Random Forest | Original | 32 | 0 % | 0.3403 [0.3161, 0.3645] | 0.3450 [0.3065, 0.3844] |
| Random Forest | PCA | 22 | 31.2 % | 0.3325 [0.3252, 0.3397] | 0.3400 [0.3017, 0.3802] |
| Random Forest | UMAP | 2 | 93.8 % | 0.3314 [0.3098, 0.3529] | 0.3267 [0.2911, 0.3637] |
| KNN | Original | 32 | 0 % | 0.3364 [0.3157, 0.3571] | 0.3586 [0.3228, 0.3948] |
| KNN | PCA | 22 | 31.2 % | 0.3384 [0.3107, 0.3661] | 0.3493 [0.3128, 0.3875] |
| KNN | UMAP | 2 | 93.8 % | 0.3343 [0.3005, 0.3682] | 0.3355 [0.2990, 0.3720] |
 
- **Los dos mejores modelos** según la validación cruzada son Random Forest y KNN.
- **Ningún modelo supera al azar.** Los intervalos de confianza de los 5 modelos incluyen $1/3$ (el F1-macro de un clasificador aleatorio con tres clases balanceadas). El ROC-AUC está alrededor de 0.5 y la prueba de permutación no rechaza $H_0$ ($p = 0.27$).
- **El dataset no contiene señal predictiva en $t=0$.** Esto es coherente con el análisis exploratorio: las variables son aproximadamente uniformes e independientes de la severidad, lo que sugiere un origen sintético. Ninguna característica es discriminativa tras la corrección de Bonferroni.
- **La reducción de dimensión no causa pérdida distinguible.** PCA (32 → 22) y UMAP (32 → 2) dan intervalos que se solapan con los del espacio original. Con un desempeño al nivel del azar, esto refleja la ausencia de señal, no que pocas componentes capturen la estructura de la severidad.
## Cómo reproducir los resultados
 
### 1. Requisitos
 
- Python 3.11 o superior (resultados generados con Python 3.13; también funciona en Google Colab)
- Jupyter Notebook / JupyterLab, o VS Code con la extensión de Jupyter
### 2. Instalar dependencias
 
**En un entorno local**, instale las dependencias:
 
```bash
pip install -r requirements.txt
```
 
**En Google Colab** no hace falta este paso: la primera celda del notebook instala las librerías que Colab no trae (`imbalanced-learn`, `umap-learn`, `kagglehub`).
 
### 3. Carga del dataset
 
El notebook descarga el dataset **automáticamente** con `kagglehub`. Si la descarga falla, usa una copia local en la siguiente ruta:
 
```
data/Global_Cybersecurity_Threats_2015-2024.csv
```
 
Para usar la copia local, descargue el CSV desde Kaggle y colóquelo en una carpeta `data/` junto al notebook.
 
### 4. Ejecutar el notebook
 
Abra `proyectoModelosII.ipynb` y ejecute todas las celdas en orden (*Run All*). El notebook está organizado así:
 
1. **Descripción del problema y EDA:** contexto, carga y caracterización de los datos, construcción de `Severity`, pruebas de origen sintético, codificación y partición.
2. **Entrenamiento y evaluación de modelos:** metodología de validación y balance (1.1.A), malla de hiperparámetros (1.1.B), métricas (1.1.C), y resultados con intervalos de confianza, matrices de confusión, comparación con el azar y experimentos de balance (1.2).
3. **Reducción de dimensión:** análisis individual de variables (2.1), PCA (2.2), UMAP (2.3.A), y discusión y conclusiones (2.3.B).
La ejecución completa tarda varios minutos. Las partes más lentas son la malla de 51 configuraciones, la prueba de permutación con 1 000 permutaciones y la selección de $K$ en UMAP. Todas las semillas están fijadas (`random_state=42`), por lo que los resultados son reproducibles en un mismo entorno. Como `requirements.txt` no fija versiones, Random Forest y MLP pueden dar cifras ligeramente distintas con otras versiones de las librerías; las conclusiones no cambian, porque todos los intervalos se solapan con 1/3.
 
## Equipo
 
- Luis Carlos Vanegas Zapata
- Alejandra Cano Espinosa
- Robinson Dario Henao Botero
 

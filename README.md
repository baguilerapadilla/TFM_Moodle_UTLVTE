# Sistema de alerta temprana para la predicción del bajo rendimiento académico

Este repositorio contiene los componentes desarrollados para un sistema de alerta temprana orientado a estimar el riesgo de bajo rendimiento académico en estudiantes de cursos presenciales que utilizan Moodle como apoyo al aprendizaje.

El modelo utiliza indicadores de interacción registrados durante las primeras cuatro semanas del periodo académico y estima la probabilidad de que un estudiante obtenga una calificación inferior a 7/10 en el primer parcial.

## Objetivo

Desarrollar un sistema de alerta temprana basado en regresión logística que permita:

- Integrar y transformar información académica procedente de Moodle.
- Construir indicadores de participación durante las primeras cuatro semanas.
- Estimar la probabilidad de bajo rendimiento académico.
- Clasificar a los estudiantes por nivel de riesgo.
- Visualizar los resultados mediante un tablero desarrollado en Power BI.

## Arquitectura general

El proceso implementado comprende cuatro componentes:

1. **ETL en Pentaho Data Integration:** integra, depura y transforma la información previamente extraída.
2. **Archivo CSV:** contiene el conjunto final anonimizado utilizado para el análisis y entrenamiento.
3. **Jupyter Notebook:** ejecuta el preprocesamiento, entrenamiento, calibración y evaluación del modelo.
4. **Power BI:** presenta las probabilidades estimadas y los niveles de riesgo mediante un tablero interactivo.

```mermaid
flowchart LR
    A["Datos previamente extraídos"] --> B["ETL en Pentaho"]
    B --> C["CSV anonimizado"]
    C --> D["Modelo en JupyterLab"]
    D --> E["Resultados de predicción"]
    E --> F["Tablero Power BI"]
```

## Contenido del repositorio

```text
sistema-alerta-temprana/
├── README.md
├── LICENSE
├── etl/
│   └── proceso_etl.ktr
├── data/
│   └── dataset_anonimizado.csv
├── notebooks/
│   └── modelo_alerta_temprana.ipynb
└── powerbi/
    └── tablero_alerta_temprana.pbix
```

| Carpeta | Archivo | Descripción |
|---|---|---|
| `etl/` | `proceso_etl.ktr` | Transformación desarrollada en Pentaho Data Integration para integrar, depurar y preparar la información. |
| `data/` | `dataset_anonimizado.csv` | Conjunto final anonimizado utilizado como entrada del modelo. |
| `notebooks/` | `modelo_alerta_temprana.ipynb` | Notebook que contiene la preparación de variables, entrenamiento, calibración y evaluación del modelo. |
| `powerbi/` | `tablero_alerta_temprana.pbix` | Tablero utilizado para visualizar probabilidades, niveles de riesgo e indicadores académicos. |

> Los nombres indicados representan la estructura recomendada y deben sustituirse por los nombres reales de los archivos publicados.

## Archivos no incluidos

Por razones de confidencialidad, protección de datos y seguridad institucional, este repositorio no contiene:

- Bases de datos originales de Moodle.
- Datos personales o identificadores directos de estudiantes y docentes.
- Conjuntos de datos fuente.
- Conjuntos de datos intermedios generados durante el proceso ETL.
- Respaldos de la base de datos institucional.
- Credenciales o parámetros privados de conexión.
- Scripts SQL utilizados para extraer la información desde Moodle.

El CSV publicado corresponde exclusivamente al conjunto final preparado para el análisis. Sus identificadores se encuentran anonimizados y no permiten identificar directamente a los participantes.

Debido a que no se publican los scripts SQL ni las bases de datos originales, la reproducción del proceso comienza a partir del CSV anonimizado incluido en el repositorio.

## Unidad de análisis y variable objetivo

La unidad de análisis corresponde a cada combinación **estudiante–curso**.

La variable objetivo se definió de la siguiente manera:

- `riesgo = 1`: calificación del primer parcial inferior a 7/10.
- `riesgo = 0`: calificación del primer parcial igual o superior a 7/10.

Los registros que no disponían de una calificación válida del primer parcial fueron excluidos antes del entrenamiento y la evaluación del modelo.

## Indicadores utilizados

El conjunto de datos incorpora indicadores construidos a partir de las primeras cuatro semanas de actividad en Moodle, entre ellos:

- Número de sesiones y eventos registrados.
- Tiempo estimado de interacción.
- Recursos consultados.
- Participación en foros.
- Tareas disponibles y entregadas.
- Cuestionarios disponibles y realizados.
- Número de intentos en cuestionarios.
- Tasas de consulta de recursos.
- Indicadores de disponibilidad y uso de actividades.

La descripción exacta de las variables se encuentra documentada dentro del notebook.

## Modelo predictivo

El modelo principal corresponde a una **regresión logística**, seleccionada por su interpretabilidad y por su capacidad para estimar probabilidades de pertenencia a la clase de riesgo.

El flujo de modelado incluye:

1. Carga del CSV anonimizado.
2. Revisión y depuración de los datos.
3. Construcción y validación de variables.
4. Separación estratificada en entrenamiento y prueba.
5. Estandarización de los predictores.
6. Optimización de hiperparámetros mediante `GridSearchCV`.
7. Validación cruzada estratificada.
8. Calibración sigmoide de probabilidades.
9. Evaluación sobre el conjunto de prueba.
10. Exportación de probabilidades y niveles de riesgo para Power BI.

La implementación utiliza una tubería formada por `StandardScaler` y `LogisticRegression`. La calibración se realiza mediante `CalibratedClassifierCV` con el método sigmoide.

## Métricas de evaluación

El desempeño del modelo se evalúa mediante:

- Exactitud y exactitud balanceada.
- Precisión, sensibilidad y F1-score.
- F1 macro.
- ROC-AUC y Average Precision.
- Matriz de confusión.
- Brier score y curva de calibración.

También se incluye una comparación con un modelo de referencia para determinar si el modelo entrenado ofrece un desempeño superior a una clasificación básica.

## Requisitos

### Pentaho Data Integration

Se requiere Pentaho Data Integration para abrir y revisar la transformación almacenada en `etl/`.

### Python y JupyterLab

Se recomienda utilizar Python 3.10 o una versión compatible con las bibliotecas empleadas:

```text
pandas
numpy
matplotlib
seaborn
scikit-learn
joblib
jupyterlab
```

Instalación:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn joblib jupyterlab
```

### Power BI

Para abrir el tablero se requiere Power BI Desktop. Si el CSV cambia de ubicación, deberá actualizarse la ruta del origen de datos desde Power Query.

## Ejecución

1. Descargar o clonar el repositorio:

```bash
git clone URL_DEL_REPOSITORIO
cd sistema-alerta-temprana
```

2. Crear un entorno virtual:

```bash
python -m venv .venv
```

3. Activarlo en Windows:

```bash
.venv\Scripts\activate
```

En Linux o macOS:

```bash
source .venv/bin/activate
```

4. Instalar las dependencias e iniciar JupyterLab:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn joblib jupyterlab
jupyter lab
```

5. Abrir `notebooks/modelo_alerta_temprana.ipynb`, comprobar la ruta del CSV y ejecutar las celdas en el orden en que aparecen.

6. Abrir el archivo `.pbix` y actualizar la ruta del CSV de resultados cuando sea necesario.

## Uso del tablero

El tablero de Power BI permite consultar, según los filtros y visualizaciones implementados:

- Total de estudiantes analizados.
- Número de estudiantes por nivel de riesgo.
- Probabilidad individual de bajo rendimiento.
- Distribución de estudiantes con y sin riesgo.
- Indicadores de interacción con Moodle.
- Recursos, tareas y cuestionarios disponibles.
- Comparaciones entre cursos.
- Detalle anonimizado por estudiante–curso.

| Nivel de riesgo | Color |
|---|---|
| Bajo | Verde |
| Medio | Amarillo |
| Alto | Rojo |

Los puntos de corte utilizados para asignar estos niveles deben consultarse en el notebook y en las reglas aplicadas al archivo de resultados.

## Privacidad y uso de los datos

La información utilizada con fines de investigación fue sometida a un proceso de anonimización. El repositorio no contiene nombres, números de identificación, direcciones de correo electrónico ni otros identificadores personales directos.

El CSV no está cubierto por la licencia MIT aplicable al código. Su utilización queda limitada a fines académicos, científicos y de validación metodológica. No se autoriza intentar reidentificar a los participantes ni combinar los registros con otras fuentes para inferir su identidad.

## Limitaciones de reproducibilidad

La publicación permite reproducir el entrenamiento y la evaluación del modelo a partir del CSV anonimizado. No permite reproducir íntegramente la extracción desde la base de datos institucional porque los scripts SQL y los datos originales no forman parte del repositorio.

La transformación de Pentaho se incluye con fines de documentación técnica. Para ejecutarla en otro entorno puede ser necesario configurar nuevas conexiones, sustituir rutas, adaptar nombres de campos y proporcionar archivos de origen con una estructura equivalente.

## Tecnologías utilizadas

- Moodle 4.5 y MariaDB.
- Pentaho Data Integration.
- Python y JupyterLab.
- pandas, NumPy y scikit-learn.
- Matplotlib y Seaborn.
- Power BI.

## Autoría

**Susana Patiño**  
Máster en Análisis y Visualización de Datos Masivos  
Universidad Internacional de La Rioja (UNIR)

Proyecto desarrollado en el contexto de un Trabajo de Fin de Máster sobre analítica del aprendizaje y predicción temprana del bajo rendimiento académico.

## Citación

```text
Patiño, S. (2026). Sistema de alerta temprana basado en regresión logística para la predicción del bajo rendimiento académico en cursos presenciales mediante analítica de aprendizaje en Moodle [Trabajo de Fin de Máster, Universidad Internacional de La Rioja].
```

## Licencia

El código del repositorio se distribuye bajo la licencia MIT. Consulte el archivo [LICENSE](LICENSE). El CSV anonimizado y los demás datos quedan expresamente excluidos de esa licencia.

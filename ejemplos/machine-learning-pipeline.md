
# Ejemplo: Pipeline de Machine Learning Reproducible (DVC, Scikit-learn)

¡Hola, científicos de datos y entusiastas de MLOps! Este repositorio es una plantilla de ejemplo para estructurar un proyecto de Machine Learning enfocado en la **reproducibilidad, versionado y automatización** usando **DVC (Data Version Control)**.

En el mundo del ML, no basta con tener buen código; necesitamos versionar los datos con los que entrenamos, los modelos que producimos, los parámetros que usamos y las métricas que obtenemos. DVC, junto con Git, nos permite hacer exactamente eso, además de definir y ejecutar pipelines de forma eficiente. Este ejemplo usa **Scikit-learn** para las tareas de ML, pero la estructura es adaptable a otros frameworks como PyTorch o TensorFlow.

## Objetivo 🎯

* Proveer una **estructura estándar y lógica** para proyectos de ML basada en MLOps.
* Demostrar cómo usar **DVC** para:
    * **Versionar datos y modelos** grandes sin sobrecargar Git.
    * Definir y ejecutar un **pipeline de ML reproducible** (`dvc.yaml`).
    * Gestionar **parámetros** (`params.yaml`) y **métricas** (`metrics.json`).
* Fomentar el **código modular** para cada etapa del pipeline (`src/`).
* Servir como un **punto de partida práctico** que puedes clonar y adaptar.

## Tecnologías Clave 🛠️

* **Orquestación / Versionado Datos/Modelos:** [DVC (Data Version Control)](https://dvc.org/)
* **Control de Versiones Código/Params:** [Git](https://git-scm.com/)
* **ML Framework (Ejemplo):** [Scikit-learn](https://scikit-learn.org/)
* **Manipulación de Datos:** [Pandas](https://pandas.pydata.org/), [NumPy](https://numpy.org/)
* **Gestión de Dependencias:** [Poetry](https://python-poetry.org/) o [Hatch](https://hatch.pypa.io/) (vía `pyproject.toml`)
* **Configuración/Parámetros:** Archivos YAML (`params.yaml`)
* **Testing:** [Pytest](https://pytest.org/)
* **(Opcional) Seguimiento de Experimentos:** [MLflow](https://mlflow.org/) (se puede integrar en los scripts de `src/`)

## Estructura del Directorio 🗺️

Esta estructura separa claramente el código, los datos, los modelos, la configuración y los resultados:

* **`data/`**: Contiene los datos en diferentes estados (raw, processed, etc.). **Importante:** Los archivos grandes aquí no están en Git directamente, sino que son gestionados por DVC (verás archivos `.dvc` que son pequeños punteros de metadatos versionados por Git).
* **`models/`**: Similar a `data/`, almacena los modelos entrenados serializados, gestionados por DVC.
* **`src/ml_pipeline/`**: Código fuente Python modularizado. Cada script principal aquí (`make_dataset.py`, `train_model.py`, etc.) corresponde idealmente a una etapa definida en `dvc.yaml`.
* **`notebooks/`**: Para exploración y análisis inicial. ¡Evita poner lógica crítica de producción aquí! Transfiere el código útil a `src/`.
* **`tests/`**: Pruebas unitarias y de integración para el código en `src/`.
* **`dvc.yaml`**: **Archivo Central del Pipeline.** Define las etapas (stages) del flujo de trabajo (ej: `process_data`, `train`, `evaluate`). Cada etapa especifica:
    * `cmd`: El comando a ejecutar (ej: `python src/ml_pipeline/models/train_model.py`).
    * `deps`: Dependencias (scripts, datos de entrada).
    * `outs`: Salidas (datos procesados, modelos). DVC las cachea.
    * `params`: Parámetros de `params.yaml` que afectan esta etapa.
    * `metrics`: Archivos de métricas generados (ej: `metrics.json`).
* **`params.yaml`**: Archivo simple para definir parámetros (hiperparámetros del modelo, umbrales, nombres de características) que quieres versionar con Git y pasar a tus scripts. DVC rastrea cambios aquí.
* **`metrics.json`, `plots.json`**: Archivos generados por DVC que contienen las métricas y datos para visualizaciones, permitiendo comparar experimentos (`dvc metrics diff`, `dvc plots diff`).
* **`pyproject.toml`**: Define las dependencias Python del proyecto y configuración de herramientas.
* **`.dvc/`**: Directorio interno de DVC. No necesitas tocarlo usualmente, pero contiene la configuración local y punteros al cache.
* **`.dvcignore`**: Similar a `.gitignore`, pero le dice a DVC qué archivos ignorar al buscar dependencias o salidas.

## Cómo Usar esta Plantilla 🚀

1.  **Prerrequisitos:**
    * Python (3.8+)
    * Git
    * DVC: `pip install dvc` (o `pip install dvc[s3,gcs,azure,ssh]` para incluir soporte de almacenamiento remoto).
    * (Opcional) Un gestor de paquetes como Poetry (`pip install poetry`) o Hatch (`pip install hatch`).
2.  **Clonar:** `git clone <URL_DEL_REPO_UNIVERSAL> && cd ejemplos/machine-learning-pipeline`
3.  **Instalar Dependencias Python:**
    * *(Usando Poetry):* `poetry install`
    * *(Usando Hatch):* `hatch env create && hatch shell`
    * *(Usando pip):* `pip install -r requirements.txt` (si existe y está actualizado)
4.  **Obtener los Datos y Modelos Versionados:** DVC descargará los archivos necesarios desde el almacenamiento remoto configurado (o el cache local si ya existen).
    ```bash
    dvc pull
    ```
    *(Nota: Para el primer uso o si el remote no está configurado, podrías necesitar configurar un DVC remote: `dvc remote add ...`)*
5.  **Reproducir el Pipeline:** DVC ejecutará las etapas definidas en `dvc.yaml` cuyos inputs (datos, código, parámetros) hayan cambiado desde la última ejecución.
    ```bash
    dvc repro
    ```
    DVC es inteligente: si solo cambiaste los parámetros de entrenamiento, solo re-ejecutará las etapas `train` y `evaluate` (si `train` es dependencia de `evaluate`), usando los datos procesados cacheados de ejecuciones anteriores.
6.  **Ejecutar una Etapa Específica:** Si solo quieres re-ejecutar la evaluación:
    ```bash
    dvc repro evaluate  # 'evaluate' es el nombre de la etapa en dvc.yaml
    ```
7.  **Explorar Cambios (Ejemplo):**
    * Modifica un parámetro en `params.yaml` (ej: `model.n_estimators`).
    * Ejecuta `dvc repro`. Observa cómo DVC detecta el cambio y re-ejecuta las etapas afectadas.
    * Compara métricas con la versión anterior: `dvc metrics diff HEAD~1`.
8.  **Ejecutar Pruebas:**
    ```bash
    pytest
    # O usando Poetry/Hatch: poetry run pytest / hatch run test
    ```

## Conceptos Clave (MLOps) Implementados 💡

* **Reproducibilidad:** `dvc repro` + Git + DVC garantiza que cualquier persona con acceso pueda reproducir tus resultados y tu pipeline completo.
* **Versionado Integral:**
    * **Código y Parámetros:** Versionados con Git (`src/`, `params.yaml`, `dvc.yaml`).
    * **Datos y Modelos:** Versionados con DVC (`data/`, `models/`). Git solo guarda los pequeños archivos `.dvc`.
    * **Pipeline:** La definición del pipeline (`dvc.yaml`) y el estado exacto de las dependencias/salidas (`dvc.lock`) están versionados en Git.
* **Modularidad:** El pipeline se divide en etapas lógicas (`dvc.yaml`) que mapean a scripts modulares en `src/`.
* **Automatización:** `dvc repro` automatiza la ejecución del pipeline.
* **Experimentación:** Cambia `params.yaml` o código, ejecuta `dvc repro`, y usa `dvc metrics diff` / `dvc plots diff` para comparar experimentos fácilmente. Puedes integrar MLflow en los scripts de `src/` para un seguimiento más detallado.

## Siguientes Pasos y Adaptación 👣

* **Adapta los Scripts:** Modifica el código en `src/` para tu problema específico y tus datos.
* **Cambia el Framework:** Reemplaza Scikit-learn por PyTorch, TensorFlow, etc. Adapta los scripts de entrenamiento/evaluación.
* **Configura DVC Remote:** Para colaboración o datos/modelos grandes, configura un almacenamiento remoto de DVC (S3, GCS, Azure Blob, SSH, etc.) usando `dvc remote add`.
* **Integra Experiment Tracking:** Añade logging a MLflow o W&B en tus scripts de `train` y `evaluate`.
* **Añade Etapas:** Define más etapas en `dvc.yaml` (ej: despliegue de modelo, generación de reportes).
* **CI/CD para ML:** Configura GitHub Actions o similar para ejecutar `dvc repro` o `pytest` en cada PR, o incluso re-entrenar y desplegar modelos automáticamente. (¡Mira CML de Iterative AI!).

## Contribuciones a este Ejemplo ✨

Las mejoras a *esta plantilla específica* son bienvenidas. Sigue la guía general (`comunidad/como-contribuir.md`) indicando que tu aporte es para `ejemplos/machine-learning-pipeline`.

¡Esperamos que esta estructura te ayude a construir pipelines de ML más robustos y reproducibles! 🤖📈

``` bash
ejemplos/machine-learning-pipeline/
├── .dvc/                       # Directorio interno de DVC (config, cache local)
│   └── .gitignore              # Ignora el cache local en Git
│   └── config                  # Configuración local de DVC (remotes, etc.)
├── .dvcignore                  # Archivos/patrones a ignorar por DVC (ej: logs, notebooks ejecutados)
├── .gitignore                  # Archivos a ignorar por Git (entornos virtuales, __pycache__, .dvc/cache)
├── .pre-commit-config.yaml     # (Opcional) Hooks para calidad de código/formato
├── data/                       # Datos del proyecto (gestionados por DVC)
│   ├── external/               # Datos de fuentes externas
│   │   └── dataset.csv.dvc     # -> Archivo puntero de DVC al dato real
│   ├── interim/                # Datos intermedios transformados
│   │   └── features.pkl.dvc
│   ├── processed/              # Datos finales listos para modelado
│   │   ├── test.csv.dvc
│   │   └── train.csv.dvc
│   └── raw/                    # Datos originales inmutables
│       └── source_data.zip.dvc
├── docs/                       # (Opcional) Documentación del proyecto
│   └── index.md
├── dvc.yaml                    # ¡Define las etapas del pipeline DVC!
├── environment.yml             # (Opcional) Para entornos Conda
├── metrics.json                # (Generado por DVC) Métricas del modelo (versionado por Git/DVC)
├── models/                     # Modelos entrenados (gestionados por DVC)
│   └── model.pkl.dvc           # -> Archivo puntero de DVC al modelo serializado
├── notebooks/                  # Jupyter notebooks para exploración y prototipado
│   ├── 1-data-exploration.ipynb
│   └── 2-model-prototyping.ipynb
├── params.yaml                 # Parámetros y hiperparámetros (versionado por Git)
├── plots.json                  # (Generado por DVC) Métricas/plots para comparar experimentos
├── pyproject.toml              # Dependencias Python, build, metadata, config herramientas
├── README.md                   # Este archivo: explicación del ejemplo
├── references/                 # (Opcional) Diccionarios de datos, manuales, etc.
│   └── data_dictionary.md
├── reports/                    # (Opcional, usualmente en .gitignore) Reportes generados, figuras
│   └── figures/
│       └── confusion_matrix.png
├── requirements.txt            # (Alternativa a pyproject.toml o generado por Poetry/Hatch)
├── src/                        # Código fuente Python modularizado
│   └── ml_pipeline/            # Nombre del paquete principal
│       ├── __init__.py
│       ├── data/               # Módulos para procesamiento de datos
│       │   ├── __init__.py
│       │   └── make_dataset.py # Script para etapa DVC 'process_data'
│       ├── features/           # Módulos para ingeniería de características
│       │   ├── __init__.py
│       │   └── build_features.py # Script para etapa DVC 'featurize'
│       ├── models/             # Módulos para entrenamiento, evaluación, predicción
│       │   ├── __init__.py
│       │   ├── evaluate_model.py # Script para etapa DVC 'evaluate'
│       │   └── train_model.py    # Script para etapa DVC 'train'
│       ├── utils.py            # Funciones de utilidad compartidas
│       └── visualization.py    # (Opcional) Scripts para generar visualizaciones
└── tests/                      # Pruebas para el código en src/
    ├── conftest.py
    ├── data/                   # Datos pequeños para tests
    │   └── sample_raw.csv
    ├── test_data.py
    └── test_features.py
```
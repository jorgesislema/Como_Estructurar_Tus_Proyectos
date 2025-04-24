# Estructura y Buenas Prácticas para Proyectos de Ciencia de Datos

Los proyectos de Ciencia de Datos (Data Science - DS) presentan desafíos únicos en comparación con el desarrollo de software tradicional. La naturaleza exploratoria, la dependencia crítica de los datos, la gestión de entornos complejos y la necesidad imperiosa de **reproducibilidad** exigen un enfoque estructurado para la organización del repositorio.

Esta guía adapta los [Principios Fundamentales](./../_fundamentos/principios-basicos.md) y las [Buenas Prácticas de Git](./../_fundamentos/buenas-practicas-git.md) al contexto específico de la Ciencia de Datos, proponiendo una estructura y prácticas que faciliten la colaboración, el seguimiento de experimentos, la gestión de datos y modelos, y, sobre todo, la reproducibilidad de los resultados.

## Principios Clave para Ciencia de Datos

Además de los principios generales como Claridad, Consistencia y Separación de Conceptos, en Ciencia de Datos debemos poner un énfasis especial en:

1.  **Reproducibilidad:** La piedra angular. Otros (¡incluido tu yo futuro!) deben poder reproducir tus análisis, resultados y modelos. Esto implica versionar no solo el código, sino también el entorno, los datos (o el acceso a ellos) y las semillas aleatorias si aplica.
2.  **Versionado Explícito:**
    * **Código:** Git es la base.
    * **Datos:** Estrategias para versionar conjuntos de datos o, más comúnmente, para versionar *cómo acceder* a ellos (ver sección Gestión de Datos).
    * **Entorno:** Definiciones explícitas y versionadas de las dependencias (paquetes, versiones).
    * **Modelos:** Seguimiento de los modelos entrenados asociados a versiones específicas de código y datos.
    * **Experimentos:** Registro de parámetros, métricas y artefactos de cada ejecución experimental.
3.  **Modularidad:** Fomentar la escritura de código reutilizable en scripts (`.py`, `.R`) en lugar de depender exclusivamente de notebooks monolíticos.
4.  **Trazabilidad:** Poder rastrear un resultado o modelo hasta el código, los datos y los parámetros exactos que lo generaron.

## Estructura de Directorios Recomendada

Una estructura bien definida es crucial. Aquí proponemos una plantilla común y adaptable:
"""
mi-proyecto-ds/
├── .dvc/               # Metadatos de DVC (si se usa)
├── .github/            # Workflows de CI/CD (ej. para tests, linting)
│   └── workflows/
├── .gitignore          # Archivos a ignorar por Git
├── data/
│   ├── raw/            # Datos originales, inmutables (ej. data.csv.dvc)
│   ├── interim/        # Datos intermedios, transformados
│   ├── processed/      # Datos listos para modelado
│   └── external/       # Datos de fuentes externas
├── docs/               # Documentación del proyecto (más allá del README)
├── environments/       # Definiciones de entorno (alternativa a archivos raíz)
│   └── environment.yml # Ejemplo para Conda
├── models/             # Modelos entrenados serializados (ej. model.pkl.dvc)
├── notebooks/
│   ├── 0_setup/        # Configuración inicial, carga de datos
│   ├── 1_exploration/  # Análisis exploratorio (EDA)
│   ├── 2_preprocessing/ # Pasos de preprocesamiento (prototipado)
│   ├── 3_modeling/     # Desarrollo y evaluación de modelos (prototipado)
│   └── 4_reporting/    # Visualizaciones finales, informes
├── reports/
│   └── figures/        # Gráficos y visualizaciones generadas
├── src/                # Código fuente modular y reutilizable
│   ├── init.py
│   ├── data/           # Scripts para descarga y procesamiento de datos
│   ├── features/       # Scripts para ingeniería de características
│   ├── modeling/       # Scripts para entrenamiento, evaluación, predicción
│   └── visualization/  # Scripts para generar visualizaciones
├── tests/              # Pruebas unitarias/integración para el código en src/
│   └── ...
├── config.yml          # Archivo de configuración (parámetros, rutas)
├── dvc.yaml            # Pipeline de DVC (si se usa)
├── environment.yml     # Definición de entorno Conda (o requirements.txt, Pipfile)
├── LICENSE             # Licencia del proyecto
├── Makefile            # (Opcional) Comandos comunes (ej. make process-data)
├── README.md           # Descripción principal, instrucciones de setup y uso
└── requirements.txt    # Alternativa/Complemento a environment.yml para pip

"""

**Explicación de Directorios Clave:**

* **`data/`**: Fundamental. Organizado por etapas (raw, processed) ayuda a entender el flujo. **¡Crucial!** Evita commitear archivos de datos grandes directamente. Ver sección "Gestión de Datos".
* **`notebooks/`**: Para exploración, experimentación rápida y visualización. La numeración ayuda a seguir un orden lógico. Conviene mantenerlos limpios y enfocados.
* **`src/`**: Para el código Python/R refactorizado, modular y reutilizable. Funciones y clases que realizan tareas específicas (procesar datos, entrenar modelo). Este código *debería* tener pruebas en `tests/`.
* **`models/`**: Almacena los artefactos de modelos entrenados. A menudo versionados con herramientas como MLflow o DVC.
* **`environments/` (o archivos raíz):** Contiene los archivos que definen el entorno computacional (`environment.yml`, `requirements.txt`, `Dockerfile`). Absolutamente esencial para la reproducibilidad.
* **`tests/`**: Pruebas para el código en `src/`. Aseguran que las funciones de procesamiento, modelado, etc., funcionen como se espera.

## Gestión de Datos

Este es uno de los mayores desafíos en DS con Git. Git no está diseñado para archivos binarios grandes.

* **El Problema:** Commitear archivos CSV, imágenes, etc., de gran tamaño infla el repositorio `.git`, ralentiza las operaciones (clone, pull) y puede exceder límites de plataformas como GitHub.
* **Estrategias:**
    * **Datos Pequeños (< কয়েক MB):** Si son realmente pequeños y no cambian a menudo, *podrían* commitearse directamente (con precaución).
    * **Git LFS (Large File Storage):** Extensión de Git que reemplaza archivos grandes en el repositorio con punteros de texto, mientras almacena el contenido real en un servidor LFS aparte. Bueno para archivos de tamaño medio (MBs a ~1GB). [Ver Git LFS](https://git-lfs.github.com/)
    * **DVC (Data Version Control):** Herramienta open-source diseñada específicamente para flujos de trabajo de ML/DS. Versiona datos y modelos (similar a Git LFS pero con más funcionalidades), y permite crear *pipelines* reproducibles (ej. `dvc.yaml`). Se integra con almacenamientos externos (S3, GCS, Azure Blob, SSH, etc.). [Ver DVC](https://dvc.org/)
    * **Almacenamiento Externo + Configuración:** Guardar los datos en S3, GCS, Azure Blob, una base de datos, etc., y versionar en Git únicamente la *configuración* o los *scripts* necesarios para acceder a ellos. DVC facilita esto.
* **Versionar el Acceso:** A menudo, no versionas los datos *en sí*, sino la *forma* de obtener la versión correcta (un ID de DVC, una URL a S3 con versión, un query SQL específico).

**Recomendación:** Para la mayoría de los proyectos serios, usar **DVC** o **Git LFS** combinado con almacenamiento externo es la mejor práctica.

## Gestión del Entorno

¡Crítico para la reproducibilidad! Debes poder recrear el entorno exacto (librerías y sus versiones) utilizado en el análisis o entrenamiento.

* **Herramientas:**
    * **Conda:** `environment.yml` define el entorno. Se crea/actualiza con `conda env create -f environment.yml` / `conda env update -f environment.yml`.
    * **Pip:** `requirements.txt` define las librerías. Se genera con `pip freeze > requirements.txt` (idealmente en un entorno virtual). Se instala con `pip install -r requirements.txt`.
    * **Poetry / Pipenv:** Herramientas más modernas que gestionan dependencias y entornos virtuales de forma más robusta.
    * **Docker:** Define el entorno completo (OS, librerías, configuraciones) en un `Dockerfile`, permitiendo la máxima reproducibilidad y portabilidad.
* **Práctica:** Elige una herramienta, define el entorno explícitamente y **commitea el archivo de definición** (`environment.yml`, `requirements.txt`, `pyproject.toml`, `Dockerfile`) en el repositorio.

## Seguimiento de Experimentos y Versionado de Modelos

La experimentación es central en DS. Necesitas registrar qué hiciste, con qué parámetros, qué resultados obtuviste y qué modelo se generó.

* **Necesidad:** Evitar hojas de cálculo manuales, nombres de archivo confusos (`model_final_final_v2.pkl`) y la incapacidad de saber cómo se entrenó un modelo específico.
* **Herramientas:**
    * **MLflow:** Plataforma open-source para gestionar el ciclo de vida de ML, incluyendo seguimiento de experimentos (parámetros, métricas, artefactos), versionado de modelos y despliegue. [Ver MLflow](https://mlflow.org/)
    * **DVC:** Además de datos, puede versionar modelos y métricas, y definir pipelines experimentales.
    * **Weights & Biases (W&B):** Plataforma popular (SaaS con opción local) para seguimiento de experimentos, visualización y colaboración.
    * **Otros:** Neptune.ai, Comet ML, etc.
* **Integración con la Estructura:** Estas herramientas generan metadatos o archivos que *sí* se commitean en Git (ej. IDs de ejecución de MLflow, archivos `.dvc`, logs). El directorio `models/` puede almacenar modelos versionados por estas herramientas.

## Notebooks vs. Código Modular (`src/`)

* **Notebooks:** Excelentes para exploración interactiva, visualización y prototipado rápido. Son herramientas de *investigación*.
* **Scripts (`src/`):** Para lógica reutilizable, robusta y probable de ser usada en pipelines o producción. Funciones de preprocesamiento, clases de modelos, utilidades. Este código debe ser testeable.
* **Flujo Recomendado:**
    1.  Explora y prototipa en notebooks.
    2.  Una vez que una pieza de lógica es estable y útil (ej. una función de limpieza de datos, una rutina de entrenamiento), **refactorízala** moviéndola a un script `.py` o `.R` dentro de `src/`.
    3.  Importa y usa esa función/clase desde tu notebook (y desde otros scripts o pruebas).
    4.  Escribe pruebas para el código en `src/`.

## La Reproducibilidad como Norte

Para lograr un proyecto de DS reproducible, asegúrate de tener:

1.  ✅ **Código Versionado:** Usa Git diligentemente.
2.  ✅ **Datos Versionados (o Acceso Versionado):** Usa Git LFS, DVC o una estrategia clara con almacenamiento externo.
3.  ✅ **Entorno Versionado:** Commitea `environment.yml`, `requirements.txt` o `Dockerfile`.
4.  ✅ **Experimentos Registrados:** Usa herramientas como MLflow o DVC para rastrear parámetros y métricas.
5.  ✅ **Modelos Versionados:** Guarda los artefactos del modelo y vincúlalos al experimento/código/datos que los generó.
6.  ✅ **Documentación Clara:** El `README.md` debe explicar cómo configurar el entorno, obtener los datos y ejecutar los análisis/entrenamientos principales.
7.  ✅ **Semillas Aleatorias (si aplica):** Fija las semillas (random seeds) para operaciones estocásticas (ej. división train/test, inicialización de modelos) para obtener resultados idénticos entre ejecuciones.

## Ejemplo de `.gitignore` para Ciencia de Datos

```gitignore
# Entornos Virtuales
venv/
*.env
env/
ENV/

# Datos (¡Importante! Usar LFS/DVC para los reales)
# Descomentar si NO usas LFS/DVC y quieres asegurar que no se commitean por error
# data/raw/*
# data/interim/*
# data/processed/*
# models/*
# !data/.gitkeep # Permitir directorios vacíos si es necesario
# !models/.gitkeep

# Archivos de Python compilados y caches
__pycache__/
*.py[cod]
*$py.class
*.egg-info/
.ipynb_checkpoints/

# Archivos de configuración local/secretos
.env*
! .env.example # Permitir archivo de ejemplo

# Archivos de IDE / OS
.idea/
.vscode/
*.DS_Store
Thumbs.db

# Archivos de construcción/distribución Python
build/
dist/
*.egg
*.whl

# Logs y outputs temporales
*.log
*.tmp
*.swp

# Notebooks ejecutados (si contienen outputs grandes)
# A veces útil ignorar outputs, aunque dificulta revisión de resultados en PRs
# notebooks/*
# !notebooks/.gitkeep

# Reportes generados (a menos que quieras versionarlos)
reports/*
!reports/figures/.gitkeep

# Cache de DVC
.dvc/cache/

# Conclusión

Aplicar una estructura sólida y buenas prácticas de versionado a los proyectos de Ciencia de Datos transforma un proceso a menudo caótico en uno más sistemático, colaborativo y, fundamentalmente, reproducible. Si bien puede requerir una inversión inicial en aprender herramientas como DVC o MLflow, los beneficios a largo plazo en términos de confiabilidad, eficiencia y capacidad para construir sobre trabajos anteriores son inmensos. ¡Adopta estas prácticas y eleva la calidad y profesionalismo de tus proyectos de datos!
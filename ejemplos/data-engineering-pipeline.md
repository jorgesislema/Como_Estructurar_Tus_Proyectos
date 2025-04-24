
# Ejemplo: Pipeline de Ingeniería de Datos Moderna (Dagster, dbt, Python)

¡Hola, ingenieros/as de datos! Esta plantilla de ejemplo está diseñada para estructurar proyectos de ingeniería de datos modernos, enfocados en la creación de pipelines **confiables, observables y testeables** para procesos ETL (Extract, Transform, Load) y ELT (Extract, Load, Transform).

Utilizamos **Dagster** como orquestador principal. Dagster es un framework Python moderno que te permite definir tus pipelines como código, con un fuerte énfasis en los **activos de datos (Software-Defined Assets)**, el testing y una interfaz de usuario (Dagit) excelente para desarrollo y monitorización. Opcionalmente, integramos **dbt (data build tool)** para manejar las transformaciones basadas en SQL, un patrón muy común en arquitecturas ELT.

## Objetivo 🎯

* Proporcionar una **estructura organizada y escalable** para proyectos de ingeniería de datos.
* Demostrar el uso de **Dagster** para orquestación, definición de activos y observabilidad.
* Mostrar cómo integrar **dbt** (opcionalmente) para transformaciones SQL dentro de un pipeline Dagster.
* Fomentar **buenas prácticas** como la definición de assets, testing, y configuración basada en entorno.
* Servir como un **punto de partida práctico** y adaptable para tus pipelines.

## Tecnologías Clave 🛠️

* **Orquestador / Definición de Activos:** [Dagster](https://dagster.io/)
* **Transformación SQL (Opcional):** [dbt (data build tool)](https://www.getdbt.com/) (ej: `dbt-core`, `dbt-postgres`)
* **Transformación Python:** [Pandas](https://pandas.pydata.org/) o [Polars](https://pola.rs/)
* **Interacción con BD:** [SQLAlchemy](https://www.sqlalchemy.org/) (usado por Dagster/dbt para conectar)
* **Gestión de Dependencias:** [Poetry](https://python-poetry.org/) o [Hatch](https://hatch.pypa.io/) (vía `pyproject.toml`)
* **Testing:** [Pytest](https://pytest.org/)
* **(Opcional) Contenedores:** [Docker](https://www.docker.com/) & [Docker Compose](https://docs.docker.com/compose/)

## Estructura del Directorio 🗺️

La estructura está centrada alrededor del proyecto Dagster y opcionalmente un proyecto dbt:

* **`dagster_project/`**: El corazón de tu código Dagster. Contiene las definiciones de tus pipelines y activos.
    * **`repository.py`**: Punto de entrada clave. Define un `@repository` que agrupa tus activos, jobs, schedules, sensors, y recursos para que Dagster los descubra.
    * **`assets/`**: **Enfoque preferido en Dagster moderno.** Aquí defines tus *Software-Defined Assets* (SDAs). Cada asset representa una tabla, archivo, u otro objeto de datos persistente. Dagster entiende las dependencias entre assets y puede orquestar su materialización. Se pueden organizar por capa (staging, marts) o dominio.
    * **`ops/`**: (Alternativa/Complemento a Assets) Define *Ops*, que son unidades individuales de cómputo (funciones Python). Pueden usarse para construir *Jobs*.
    * **`jobs/`**: (Alternativa/Complemento a Assets) Define *Jobs*, que son grafos de *Ops* conectados, representando un pipeline ejecutable.
    * **`resources/`**: Define *Recursos*, que abstraen conexiones a sistemas externos como bases de datos, APIs, etc. Se inyectan en Assets u Ops.
    * **`schedules/` / `sensors/`**: Definen cómo se disparan tus pipelines (basado en tiempo o eventos).
* **`dbt_project/`**: (Opcional) Un proyecto dbt estándar. Contiene tus modelos SQL (`models/`), tests (`tests/`), macros (`macros/`), etc. Dagster tiene excelente integración para ejecutar `dbt run` o materializar modelos dbt como assets Dagster.
* **`sql/`**: (Alternativa a dbt) Si prefieres no usar dbt, puedes guardar aquí scripts SQL planos que tus Ops de Python ejecutarán.
* **`tests/`**: Pruebas unitarias y de integración para tus Ops, Assets, y Recursos de Dagster usando Pytest.
* **Archivos Raíz:** `pyproject.toml` para dependencias, `README.md`, Dockerfiles opcionales, y archivos de configuración (`.env.example`).

## Cómo Usar esta Plantilla 🚀

1.  **Prerrequisitos:**
    * Python (3.8+)
    * Git
    * (Recomendado) Docker y Docker Compose (para ejecutar Dagster UI y dependencias como DBs localmente).
    * (Opcional) Un gestor de paquetes como Poetry o Hatch.
    * (Opcional) dbt CLI si vas a desarrollar modelos dbt localmente (`pip install dbt-core dbt-postgres` o el adaptador que necesites).
2.  **Clonar:** `git clone <URL_DEL_REPO_UNIVERSAL> && cd ejemplos/data-engineering-pipeline`
3.  **Instalar Dependencias Python:**
    * *(Usando Poetry):* `poetry install`
    * *(Usando Hatch):* `hatch env create && hatch shell`
    * *(Usando pip):* `pip install -r requirements.txt` (si existe)
4.  **Variables de Entorno:** Copia `.env.example` a `.env` y configura las variables (credenciales de base de datos, API keys, etc.). Dagster puede cargar variables de `.env`.
5.  **(Opcional - dbt) Configurar Perfil:** Si usas dbt, asegúrate de tener un `profiles.yml` configurado (usualmente en `~/.dbt/`) o configura el recurso Dagster para dbt para que lo maneje.
6.  **Iniciar Dagster UI (Dagit) Localmente:** La forma más fácil suele ser con Docker Compose si se proporciona un `docker-compose.yml`. Si no, puedes ejecutarlo directamente:
    ```bash
    # Asegúrate de estar en el entorno virtual si no usas Hatch/Docker
    # Ejecuta Dagit apuntando al repositorio definido en dagster_project/repository.py
    dagster dev
    ```
    Abre tu navegador en `http://localhost:3000`.
7.  **Explorar y Ejecutar:**
    * Navega por los **Assets** en la UI de Dagit. Verás el grafo de dependencias.
    * Selecciona assets y haz clic en **"Materialize"** para ejecutar las Ops o comandos dbt necesarios para crearlos o actualizarlos.
    * Explora los **Jobs** (si están definidos) y ejecútalos.
    * Revisa los **Schedules** y **Sensors**.
    * Inspecciona los **Runs** para ver logs y resultados.
8.  **Ejecutar Pruebas:**
    ```bash
    pytest
    # O usando Poetry/Hatch: poetry run pytest / hatch run test
    ```
    * Si usas dbt, también puedes ejecutar: `dbt test` (dentro del directorio `dbt_project/` o vía un Op de Dagster).

## Conceptos Clave (Data Engineering & Dagster) 💡

* **ETL vs ELT:** ETL (Extract, Transform, Load) transforma los datos *antes* de cargarlos al destino final (ej: data warehouse). ELT (Extract, Load, Transform) carga los datos crudos o semi-procesados al destino y *luego* los transforma allí (a menudo usando SQL/dbt). Esta estructura puede soportar ambos.
* **Orquestación:** Herramientas como Dagster gestionan dependencias entre tareas, scheduling, reintentos, alertas y monitorización, crucial para pipelines complejos.
* **Software-Defined Assets (SDAs):** El enfoque principal de Dagster. Modelas tus pipelines declarando los *activos de datos* que producen (tablas, archivos, reportes, modelos ML) y cómo se generan. Dagster infiere las dependencias y orquesta su creación/actualización.
* **Observabilidad y Linaje:** Dagster UI proporciona visibilidad sobre ejecuciones, estado de assets, y linaje de datos (cómo se generó un asset).
* **Testing:** Es vital testear la lógica de transformación (tests unitarios en Ops Python, `dbt test` para modelos SQL) y la calidad de los datos (Great Expectations, Soda Core, dbt tests, Dagster Asset Checks).

## Siguientes Pasos y Adaptación 👣

* **Conecta tus Fuentes y Destinos:** Adapta los `resources/` y las Ops/Assets de ingestión/carga para tus bases de datos, APIs, o data lakes específicos.
* **Desarrolla tus Transformaciones:** Escribe tu lógica en Python dentro de `ops/` o `assets/`, o crea/modifica modelos en `dbt_project/`.
* **Implementa Chequeos de Calidad:** Usa `dbt test` o integra librerías como Great Expectations o Soda Core usando Ops de Dagster, o usa los `Asset Checks` nativos de Dagster.
* **Configura Despliegue:** Containeriza tu código de usuario Dagster (`Dockerfile`) y despliégalo en la nube (Kubernetes, ECS, Dagster Cloud) usando CI/CD.
* **Define Schedules/Sensors:** Configura tus pipelines para que se ejecuten automáticamente.

## Contribuciones a este Ejemplo ✨

Si tienes sugerencias para mejorar *esta plantilla específica de data engineering*, por favor sigue la guía de contribución general (`comunidad/como-contribuir.md`) indicando que tu sugerencia es para `ejemplos/data-engineering-pipeline`.

¡Construye pipelines de datos robustos y observables con esta base! 💧⚙️

```bash

ejemplos/data-engineering-pipeline/
├── .env.example                # Variables de entorno (conexiones DB, API keys)
├── .gitignore                  # Ignorar Python cache, virtualenvs, secrets, logs
├── .dockerignore               # (Opcional) Archivos a ignorar en el build de Docker
├── Dockerfile                  # (Opcional) Para containerizar el código de usuario Dagster
├── docker-compose.yml          # (Opcional) Para correr Dagster UI, daemon, DB, etc. localmente
├── dagster_project/            # Directorio principal del proyecto Dagster (o tu nombre preferido)
│   ├── __init__.py
│   ├── assets/                 # Definición de Software-Defined Assets (Tablas, Archivos)
│   │   ├── __init__.py
│   │   ├── core/               # Ej: Activos de datos 'core'
│   │   │   └── users.py
│   │   ├── marts/              # Ej: Activos de data marts finales
│   │   │   └── daily_activity.py
│   │   └── staging/            # Ej: Activos de staging (limpieza inicial)
│   │       └── stg_orders.py
│   ├── jobs/                   # (Opcional) Definición explícita de Jobs (grafos de Ops)
│   │   ├── __init__.py
│   │   └── daily_etl_job.py
│   ├── ops/                    # (Opcional) Definición explícita de Ops (unidades de cómputo)
│   │   ├── __init__.py
│   │   ├── ingestion/
│   │   │   └── fetch_api_data.py
│   │   └── transformation/
│   │       └── clean_data.py
│   ├── resources/              # Definición de Recursos (conexiones DB, clientes API)
│   │   ├── __init__.py
│   │   └── db_resource.py
│   ├── schedules/              # Definición de Schedules para ejecutar Jobs/Assets
│   │   ├── __init__.py
│   │   └── daily_schedule.py
│   ├── sensors/                # (Opcional) Definición de Sensors (ej: reaccionar a archivos S3)
│   │   └── ...
│   └── repository.py           # Punto de entrada: Define el repositorio de código Dagster
├── dbt_project/                # (Opcional) Proyecto dbt estándar para transformaciones SQL
│   ├── dbt_project.yml         # Configuración del proyecto dbt
│   ├── models/                 # Modelos dbt (archivos .sql)
│   │   ├── marts/
│   │   │   └── core/
│   │   │       └── dim_users.sql
│   │   └── staging/
│   │       ├── schema.yml      # Documentación y tests de schema dbt
│   │       └── base/
│   │           └── stg_orders.sql
│   ├── profiles.yml            # (Usualmente en ~/.dbt/ o gestionado por Dagster resource)
│   ├── snapshots/              # (Opcional) Snapshots dbt
│   └── tests/                  # (Opcional) Tests singulares dbt
├── notebooks/                  # (Opcional) Exploración y análisis ad-hoc
│   └── data_profiling.ipynb
├── pyproject.toml              # Dependencias Python (dagster, pandas, dbt-core, etc.), build, tools
├── README.md                   # Este archivo: explicación del ejemplo
├── scripts/                    # (Opcional) Scripts auxiliares (setup, limpieza)
│   └── setup_db.sh
├── sql/                        # (Alternativa a dbt) Scripts SQL planos usados por Ops Python
│   └── transformations/
│       └── aggregate_orders.sql
└── tests/                      # Pruebas Python para Ops, Assets, Recursos
    ├── conftest.py
    ├── test_assets.py
    └── test_ops.py

```
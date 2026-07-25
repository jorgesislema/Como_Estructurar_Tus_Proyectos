# Ejemplo: Backend Python Moderno con FastAPI

¡Bienvenido/a a la plantilla de ejemplo para un backend Python moderno! Este repositorio sirve como un punto de partida práctico y bien estructurado para tus propios proyectos de API.

Esta construido usando **FastAPI**, aprovechando sus caracteristicas modernas como la **asincronia**, la **inyeccion de dependencias**, y la validacion de datos con **Pydantic**. Tambien integra **SQLAlchemy** (para la base de datos, con soporte async) y **Alembic** (para migraciones), siguiendo las mejores practicas recomendadas en `Como_Estructurar_Tus_Proyectos`.

## Objetivo 🎯

El propósito de este ejemplo es:

* Mostrar una **estructura de proyecto modular y escalable** para un backend Python.
* Implementar **buenas prácticas** como el layout `src/`, gestión de dependencias con `pyproject.toml`, configuración basada en entorno, y testing.
* Servir como un **esqueleto funcional** que puedas clonar y adaptar fácilmente a tus necesidades.

**Disclaimer:** Esto es una plantilla/ejemplo. Para un entorno de producción real, necesitarás añadir más elementos como logging robusto, monitorización, manejo de errores más detallado, hardening de seguridad, etc.

## Tecnologías Clave 🛠️

* **Framework API:** [FastAPI](https://fastapi.tiangolo.com/)
* **Servidor ASGI:** [Uvicorn](https://www.uvicorn.org/)
* **Validación de Datos:** [Pydantic](https://pydantic-docs.helpmanual.io/) (V2+)
* **Base de Datos ORM:** [SQLAlchemy](https://www.sqlalchemy.org/) (2.0+ con soporte `async`)
* **Migraciones DB:** [Alembic](https://alembic.sqlalchemy.org/)
* **Gestión de Dependencias/Build:** [Poetry](https://python-poetry.org/) o [Hatch](https://hatch.pypa.io/) (definido en `pyproject.toml`)
* **Testing:** [Pytest](https://pytest.org/) con `httpx` para el cliente async.
* **(Opcional) Contenedores:** [Docker](https://www.docker.com/) & [Docker Compose](https://docs.docker.com/compose/)

## Estructura del Directorio 🗺️

La estructura sigue el layout `src/` y organiza el código por funcionalidad y capa:

* **`src/backend_app/`**: El corazón de tu aplicación. Es el paquete Python que se instalará.
    * **`main.py`**: Punto de entrada ASGI. Crea la instancia de FastAPI e incluye los routers principales.
    * **`api/`**: Contiene los endpoints de la API, organizados por versión (ej: `v1/`) y luego por recurso (`endpoints/`). Aquí viven tus `APIRouter`.
        * `deps.py`: Funciones de dependencia reutilizables de FastAPI (ej: obtener sesión de BD, verificar usuario actual).
    * **`core/`**: Lógica de negocio central que no es específica de la API ni de la BD. Incluye la carga de configuración (`config.py` usando Pydantic Settings) y la lógica de seguridad (`security.py`).
    * **`db/`**: Capa de acceso a datos.
        * `session.py`: Configuración de SQLAlchemy (engine, sessionmaker async).
        * `models/`: Define tus modelos ORM (tablas de la BD).
        * `crud/`: Funciones que realizan operaciones CRUD sobre los modelos, interactuando con la sesión de BD. Abstrae la lógica de BD de la API.
    * **`schemas/`**: Define los modelos Pydantic usados para validar los datos de entrada/salida de la API y para la serialización. Separa los modelos de la API de los modelos de la BD.
* **`tests/`**: Pruebas automatizadas usando `pytest`. La estructura refleja la de `src/` para facilitar la localización de tests.
* **`migrations/`**: Gestionado por Alembic. Contiene scripts para evolucionar el esquema de tu base de datos de forma versionada.
* **`pyproject.toml`**: Define todo sobre el proyecto: metadatos, dependencias (producción y desarrollo), sistema de build, y configuración de herramientas (pytest, ruff, mypy, etc.).
* **`Dockerfile`, `docker-compose.yml`**: (Opcional) Para facilitar el desarrollo y despliegue usando contenedores.
* **`.env.example`, `.gitignore`, `.pre-commit-config.yaml`**: Archivos de configuración y calidad de código.

## Cómo Usar esta Plantilla 🚀

1.  **Clonar:** `git clone <URL_DEL_REPO_UNIVERSAL> && cd ejemplos/backend-python`
2.  **Entorno Virtual y Dependencias:**
    * *(Usando Poetry):* `poetry install`
    * *(Usando Hatch):* `hatch env create && hatch shell` (o usa `hatch run ...` para ejecutar comandos)
3.  **Configuración:** Copia `.env.example` a `.env` y ajusta las variables (¡especialmente la URL de la base de datos!).
    ```bash
    cp .env.example .env
    # Edita .env con tus valores (ej: DATABASE_URL, SECRET_KEY)
    ```
4.  **Base de Datos y Migraciones:**
    * Asegúrate de que tu base de datos (ej: PostgreSQL con soporte async como `asyncpg`) esté corriendo. Si usas Docker Compose: `docker-compose up -d db` (o similar).
    * Aplica las migraciones:
        ```bash
        # (Asegúrate de estar en el shell del entorno virtual si no usas Hatch)
        alembic upgrade head
        ```
5.  **Ejecutar el Servidor de Desarrollo:**
    ```bash
    # (Con Poetry)
    poetry run uvicorn backend_app.main:app --reload --host 0.0.0.0 --port 8000

    # (Con Hatch)
    hatch run dev
    # (Asumiendo un script 'dev' en pyproject.toml: "uvicorn backend_app.main:app --reload...")
    ```
    Ahora deberías poder acceder a la API en `http://localhost:8000` y a la documentación interactiva en `http://localhost:8000/docs`.
6.  **Ejecutar Pruebas:**
    ```bash
    # (Con Poetry)
    poetry run pytest

    # (Con Hatch)
    hatch run test
    # (Asumiendo un script 'test' en pyproject.toml: "pytest")
    ```

## Siguientes Pasos y Adaptación 👣

* **Modifica los Modelos:** Adapta `db/models/` y `schemas/` a tus propias entidades de datos.
* **Crea Nuevos Endpoints:** Añade nuevos archivos en `api/v1/endpoints/` y regístralos en `main.py` o routers intermedios.
* **Implementa tu Lógica:** Añade lógica de negocio en `core/` o servicios dedicados.
* **Cambia la Base de Datos:** Adapta `db/session.py` y las dependencias si prefieres otra BD (ej: MongoDB con `motor`).
* **Autenticación:** El ejemplo incluye bases para seguridad (`security.py`, `schemas/token.py`), pero necesitarás implementar flujos de login, registro, protección de rutas, etc.
* **Añade Funcionalidades:** Cache (Redis), tareas en background (Celery), WebSockets, etc.

## Contribuciones a este Ejemplo ✨

Si tienes sugerencias para mejorar *esta plantilla de ejemplo especifica*, por favor sigue la guia de contribucion general (`plantillas/CONTRIBUTING.md`) indicando que tu sugerencia es para `ejemplos/backend-python`.

¡Esperamos que esta plantilla te sea de gran utilidad para empezar tu próximo proyecto backend en Python!
``` bash

ejemplos/backend-python/
├── .env.example                # Ejemplo de variables de entorno necesarias
├── .gitignore                  # Archivos a ignorar por Git (estándar Python + específicos)
├── .pre-commit-config.yaml     # (Opcional) Configuración para pre-commit hooks (calidad código)
├── Dockerfile                  # (Opcional) Para containerizar la aplicación
├── docker-compose.yml          # (Opcional) Para orquestar servicios (app, DB) en local
├── migrations/                 # (Si usa BD relacional con Alembic) Migraciones de base de datos
│   ├── README                  # Info sobre Alembic
│   ├── env.py                  # Configuración de entorno de Alembic
│   ├── script.py.mako          # Plantilla para nuevas migraciones
│   └── versions/               # Archivos de migración generados
│       └── xxxxx_initial.py
├── pyproject.toml              # ¡Archivo central! Define dependencias, build, metadata, tools
├── README.md                   # Este archivo: explicación del ejemplo
├── src/                        # Código fuente de la aplicación (layout src/)
│   └── backend_app/            # Nombre del paquete Python principal
│       ├── __init__.py
│       ├── api/                # Módulos de la API (endpoints/routers)
│       │   ├── __init__.py
│       │   └── v1/             # Versionado de la API
│       │       ├── __init__.py
│       │       ├── deps.py     # Funciones de dependencia (ej: obtener sesión DB, usuario actual)
│       │       └── endpoints/  # Routers específicos por recurso
│       │           ├── __init__.py
│       │           ├── items.py
│       │           └── users.py
│       ├── core/               # Lógica central, configuración, seguridad
│       │   ├── __init__.py
│       │   ├── config.py       # Carga de configuración (ej: con Pydantic Settings)
│       │   └── security.py     # Funciones de autenticación/autorización (hashing, JWT)
│       ├── db/                 # Capa de acceso a datos
│       │   ├── __init__.py
│       │   ├── base.py         # (Opcional) Modelo base ORM, metadatos
│       │   ├── crud/           # Operaciones CRUD (Create, Read, Update, Delete)
│       │   │   ├── __init__.py
│       │   │   ├── crud_item.py
│       │   │   └── crud_user.py
│       │   ├── models/         # Modelos ORM (ej: SQLAlchemy) o de BD NoSQL
│       │   │   ├── __init__.py
│       │   │   ├── item.py
│       │   │   └── user.py
│       │   └── session.py      # Configuración y gestión de la sesión de BD (ej: SQLAlchemy async)
│       ├── main.py             # Punto de entrada de la app (instancia FastAPI, routers principales)
│       └── schemas/            # Esquemas Pydantic (validación de datos API, serialización)
│           ├── __init__.py
│           ├── item.py
│           ├── token.py        # Esquemas para tokens JWT
│           └── user.py
└── tests/                      # Pruebas automatizadas
    ├── conftest.py             # Fixtures globales de pytest (ej: cliente HTTP, sesión DB de test)
    ├── api/                    # Pruebas para la capa API
    │   └── v1/
    │       ├── test_items.py
    │       └── test_users.py
    ├── core/                   # Pruebas para la lógica central
    │   └── test_security.py
    ├── crud/                   # Pruebas para las operaciones CRUD
    │   └── test_crud_item.py
    └── utils/                  # Utilidades específicas para tests
        └── ...
```


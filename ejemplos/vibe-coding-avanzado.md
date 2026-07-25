# Ejemplo: Proyecto Vibe Coding Avanzado (Nivel 3)

Proyecto de ejemplo para el Nivel 3 de Vibe Coding: ingeniero de software que dirige agentes de IA autonomos usando arquitectura limpia, CI/CD completo, multiples entornos y despliegue profesional.

Proyecto de ejemplo: una API de asesor financiero con IA que analiza gastos, genera reportes y responde preguntas usando RAG.

---

## Arbol de Carpetas Completo

```bash
asesor-financiero-ia/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── cd.yml
│   │   ├── eval-semanal.yml
│   │   └── dependabot.yml
│   └── copilot-instructions.md
├── .cursor/
│   └── rules
├── .gitignore
├── .env.example
├── .pre-commit-config.yaml
├── README.md
├── RULES.md
├── CONTEXTO.md
├── AGENTS.md
├── CLAUDE.md
├── pyproject.toml
├── Dockerfile
├── docker-compose.yml
├── Makefile
├── src/
│   ├── __init__.py
│   ├── main.py                      # FastAPI app factory
│   ├── config.py                     # Pydantic Settings
│   ├── api/
│   │   ├── __init__.py
│   │   ├── v1/
│   │   │   ├── __init__.py
│   │   │   ├── router.py
│   │   │   ├── gastos.py            # Endpoints de gastos
│   │   │   ├── reportes.py          # Endpoints de reportes
│   │   │   └── chat.py              # Endpoint de chat con IA
│   │   └── deps.py                   # Dependencias FastAPI
│   ├── core/
│   │   ├── __init__.py
│   │   ├── security.py              # JWT, autenticacion
│   │   ├── exceptions.py            # Excepciones personalizadas
│   │   └── events.py                # Eventos de ciclo de vida
│   ├── domain/
│   │   ├── __init__.py
│   │   ├── entities.py              # Entidades: Usuario, Gasto, Reporte
│   │   ├── value_objects.py         # Value Objects: Dinero, Categoria, Periodo
│   │   └── repositories.py          # Interfaces de repositorios
│   ├── infrastructure/
│   │   ├── __init__.py
│   │   ├── database.py              # SQLAlchemy engine y session
│   │   ├── models.py                # Modelos ORM
│   │   ├── repositories.py          # Implementaciones concretas
│   │   ├── llm/
│   │   │   ├── __init__.py
│   │   │   ├── base.py
│   │   │   ├── openai_client.py
│   │   │   └── anthropic_client.py
│   │   ├── rag/
│   │   │   ├── __init__.py
│   │   │   ├── embeddings.py
│   │   │   ├── vector_store.py
│   │   │   ├── retriever.py
│   │   │   └── document_loader.py
│   │   └── cache/
│   │       ├── __init__.py
│   │       └── redis_cache.py
│   ├── services/
│   │   ├── __init__.py
│   │   ├── gastos_service.py       # Logica de gastos
│   │   ├── reportes_service.py     # Generacion de reportes
│   │   ├── chat_service.py         # Servicio de chat con IA
│   │   └── analisis_service.py     # Analisis financiero con LLM
│   ├── prompts/
│   │   ├── __init__.py
│   │   ├── manager.py
│   │   └── templates/
│   │       ├── system/
│   │       │   ├── asesor_financiero.txt
│   │       │   ├── analisis_gastos.txt
│   │       │   └── generador_reporte.txt
│   │       └── user/
│   │           ├── pregunta_financiera.txt
│   │           └── resumen_gastos.txt
│   └── schemas/
│       ├── __init__.py
│       ├── gastos.py                # Pydantic schemas
│       ├── reportes.py
│       └── chat.py
├── tests/
│   ├── conftest.py
│   ├── unit/
│   │   ├── test_domain/
│   │   ├── test_services/
│   │   └── test_infrastructure/
│   ├── integration/
│   │   ├── test_api/
│   │   ├── test_database/
│   │   └── test_llm/
│   └── e2e/
│       ├── test_chat_flow.py
│       └── test_report_generation.py
├── evals/
│   ├── test_cases/
│   │   ├── preguntas_financieras.json
│   │   └── escenarios_analisis.json
│   ├── runner.py
│   └── metrics.py
├── alembic/
│   ├── versions/
│   └── env.py
├── scripts/
│   ├── seed_data.py
│   ├── index_documents.py
│   └── run_evals.py
├── deployment/
│   ├── docker/
│   │   └── Dockerfile.prod
│   ├── kubernetes/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── ingress.yaml
│   └── terraform/
│       ├── main.tf
│       └── variables.tf
├── monitoring/
│   └── grafana/
│       └── dashboard.json
└── docs/
    ├── architecture.md
    ├── api.md
    ├── prompts.md
    └── adr/
        ├── 001-eleccion-llm-provider.md
        └── 002-estrategia-rag.md
```

---

## Contenido de Archivos Clave

### CONTEXTO.md

```markdown
# Contexto del Proyecto: Asesor Financiero IA

## Descripcion General
API REST que proporciona analisis financiero personalizado usando inteligencia artificial.
Los usuarios registran sus gastos, y el sistema genera reportes, detecta patrones de gasto,
y responde preguntas financieras usando RAG sobre documentacion de educacion financiera.

## Stack Tecnologico
- **Lenguaje:** Python 3.12
- **Framework:** FastAPI 0.115+
- **Base de datos:** PostgreSQL 16 + Redis 7
- **ORM:** SQLAlchemy 2.0 + Alembic
- **Validacion:** Pydantic v2
- **LLMs:** OpenAI (GPT-4o) + Anthropic (Claude Sonnet) via abstraccion propia
- **RAG:** ChromaDB + OpenAI Embeddings
- **Testing:** Pytest + pytest-asyncio + pytest-cov
- **CI/CD:** GitHub Actions
- **Despliegue:** Docker + Kubernetes (AWS EKS)
- **Monitoreo:** Grafana + Prometheus + Sentry
- **Gestion de paquetes:** uv

## Arquitectura
Clean Architecture con cuatro capas:
1. **api/** - Presentacion: endpoints FastAPI versionados (v1)
2. **services/** - Aplicacion: casos de uso, orquestacion
3. **domain/** - Dominio: entidades, value objects, interfaces de repositorios
4. **infrastructure/** - Infraestructura: SQLAlchemy, OpenAI, ChromaDB, Redis

Las dependencias siempre apuntan hacia adentro: api -> services -> domain <- infrastructure.

## Estructura de Carpetas
(Ver arbol completo arriba)

## Principios de Diseno
- SOLID, especialmente Dependency Inversion (domain define interfaces, infrastructure implementa)
- CQRS ligero: separar operaciones de lectura y escritura cuando sea necesario
- Repository pattern para acceso a datos
- LLM abstraction layer para cambiar de proveedor sin cambiar logica de negocio

## Dependencias Clave
- FastAPI: framework web asincrono
- SQLAlchemy 2.0: ORM con soporte asincrono
- Alembic: migraciones de base de datos
- Pydantic v2: validacion y serializacion
- OpenAI + Anthropic SDKs: clientes de LLM
- ChromaDB: vector store para RAG
- Redis: cache de respuestas de LLM
- Structlog: logging estructurado
- Tenacity: reintentos con backoff

## Servicios Externos
- OpenAI API (GPT-4o para analisis complejo, GPT-4o-mini para consultas simples)
- Anthropic API (Claude Sonnet para generacion de reportes)
- PostgreSQL en AWS RDS
- Redis en AWS ElastiCache
- Sentry para error tracking

## Variables de Entorno Requeridas
Ver .env.example. Principales: DATABASE_URL, REDIS_URL, OPENAI_API_KEY, ANTHROPIC_API_KEY, SECRET_KEY.

## Flujo de Trabajo
1. Crear rama desde main: feat/nombre-feature
2. Desarrollar siguiendo RULES.md
3. La IA genera codigo siguiendo este CONTEXTO.md
4. Ejecutar tests: pytest
5. CI: lint -> type-check -> test -> coverage
6. PR review (humano + agente IA revisor)
7. Merge -> despliegue automatico a staging
8. Validacion en staging -> deploy a produccion
```

### AGENTS.md

```markdown
# AGENTS.md - Asesor Financiero IA

## Rol
Eres un asistente de ingenieria de software trabajando en Asesor Financiero IA,
una API de analisis financiero con inteligencia artificial.

## Stack y Arquitectura
- FastAPI + SQLAlchemy 2.0 + PostgreSQL + Redis
- Clean Architecture: api/ -> services/ -> domain/ <- infrastructure/
- Ver CONTEXTO.md para el detalle completo

## Al Escribir Codigo
- Usa tipos estrictos (mypy --strict)
- Sigue el patron repositorio definido en domain/repositories.py
- Usa Pydantic v2 para schemas (model_validate, no .parse_obj)
- Usa SQLAlchemy 2.0 style (select() en lugar de Model.query)
- Inyecta dependencias via FastAPI Depends()
- NUNCA accedas directamente a la DB desde servicios; usa repositorios
- Usa el PromptManager para cargar templates, no hardcodees prompts
- Maneja errores con excepciones personalizadas de core/exceptions.py
- Usa async/await para todo (FastAPI, SQLAlchemy, clientes LLM)

## Al Escribir Tests
- pytest + pytest-asyncio
- Usa fixtures de conftest.py
- Mockea clientes LLM en tests unitarios
- Usa testcontainers para tests de integracion con DB
- Nombre de tests: test_[modulo]_[funcionalidad]_[escenario]

## Prohibiciones
- NO hagas commit de .env, .venv, __pycache__
- NO uses Any como tipo; usa el tipo concreto o Generic
- NO uses print(); usa structlog
- NO modifiques pyproject.toml sin consultar
- NO introduzcas nuevas dependencias sin consultar
- NO modifiques las migraciones de Alembic manualmente
- NO uses eval(), exec(), o similares
```

### RULES.md

```markdown
# RULES.md - Asesor Financiero IA

## Python
- Python 3.12, tipos estrictos (mypy --strict)
- Ruff para linting y formato (config en pyproject.toml)
- Max 100 caracteres por linea
- Docstrings en formato Google para todas las funciones publicas
- snake_case: variables, funciones, archivos
- PascalCase: clases, excepciones
- UPPER_SNAKE_CASE: constantes

## FastAPI
- Endpoints versionados: /api/v1/
- Schemas Pydantic en src/schemas/
- Dependencias en src/api/deps.py
- Usar async def para todos los endpoints
- Status codes explicitos (nunca 200 para errores)

## Base de Datos
- SQLAlchemy 2.0 estilo declarativo
- Modelos en infrastructure/models.py
- Repositorios implementan interfaces de domain/repositories.py
- Migraciones con Alembic (alembic revision --autogenerate)
- Nunca hacer drop en produccion

## LLMs
- Usar abstraction layer de infrastructure/llm/
- Prompts en src/prompts/templates/ (Jinja2 o f-strings)
- Versionar prompts (carpeta v1/, v2/)
- Cachear respuestas frecuentes en Redis (infrastructure/cache/)
- Medir costos y latencia de cada llamada

## Git
- Conventional Commits
- No commits de .env, .venv, __pycache__, node_modules
- No commits de archivos mayores a 5MB (usar Git LFS si es necesario)
- Branches: main, staging, feat/*, fix/*, docs/*

## CI/CD
- CI en cada PR: ruff, mypy, pytest, coverage (min 80%)
- CD: despliegue automatico a staging desde main, a prod con approval
- Evaluacion de LLM semanal (workflow eval-semanal.yml)
```

### .github/workflows/ci.yml

```yaml
name: CI

on:
  pull_request:
    branches: [main, staging]
  push:
    branches: [main]

jobs:
  lint-and-type:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v3
      - run: uv sync
      - run: uv run ruff check .
      - run: uv run ruff format --check .
      - run: uv run mypy src/

  test:
    needs: lint-and-type
    runs-on: ubuntu-latest
    services:
      postgres:
        image: pgvector/pgvector:pg16
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: test_db
        ports:
          - 5432:5432
      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v3
      - run: uv sync
      - run: uv run pytest --cov=src --cov-report=xml
        env:
          DATABASE_URL: postgresql+asyncpg://test:test@localhost:5432/test_db
          REDIS_URL: redis://localhost:6379
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
          SECRET_KEY: test-secret-key
      - uses: codecov/codecov-action@v4
```

### pyproject.toml

```toml
[project]
name = "asesor-financiero-ia"
version = "1.0.0"
description = "API de asesor financiero con inteligencia artificial"
requires-python = ">=3.12"
dependencies = [
    "fastapi>=0.115.0",
    "uvicorn[standard]>=0.30.0",
    "sqlalchemy[asyncio]>=2.0.35",
    "asyncpg>=0.30.0",
    "alembic>=1.14.0",
    "pydantic>=2.9.0",
    "pydantic-settings>=2.6.0",
    "redis>=5.2.0",
    "openai>=1.55.0",
    "anthropic>=0.39.0",
    "chromadb>=0.5.0",
    "python-jose[cryptography]>=3.3.0",
    "passlib[bcrypt]>=1.7.4",
    "structlog>=24.4.0",
    "tenacity>=9.0.0",
    "httpx>=0.28.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=8.3.0",
    "pytest-asyncio>=0.24.0",
    "pytest-cov>=6.0.0",
    "ruff>=0.7.0",
    "mypy>=1.13.0",
    "testcontainers>=4.8.0",
]

[tool.ruff]
line-length = 100
target-version = "py312"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP", "B", "SIM", "C4"]

[tool.ruff.format]
quote-style = "double"

[tool.mypy]
strict = true
python_version = "3.12"

[tool.pytest.ini_options]
asyncio_mode = "auto"
testpaths = ["tests"]
addopts = "-v --strict-markers --cov=src --cov-report=term-missing"
```

### Makefile

```makefile
.PHONY: install dev test lint format type-check migrate run build

install:
	uv sync

dev:
	uv run uvicorn src.main:app --reload --host 0.0.0.0 --port 8000

test:
	uv run pytest

lint:
	uv run ruff check .

format:
	uv run ruff format .

type-check:
	uv run mypy src/

migrate:
	uv run alembic upgrade head

run:
	uv run uvicorn src.main:app --host 0.0.0.0 --port 8000

build:
	docker build -t asesor-financiero-ia .

docker-up:
	docker compose up -d

docker-down:
	docker compose down

evals:
	uv run python scripts/run_evals.py

seed:
	uv run python scripts/seed_data.py
```

### Dockerfile

```dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential curl \
    && rm -rf /var/lib/apt/lists/*

COPY pyproject.toml uv.lock ./
RUN pip install uv && uv sync --frozen --no-dev

COPY . .

EXPOSE 8000

CMD ["uv", "run", "uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## Prompt Para Iniciar el Proyecto con IA

Usa este prompt con Cursor, Claude Code, o cualquier asistente IA para generar el esqueleto del proyecto:

```
Necesito crear un proyecto de API REST para un asesor financiero con IA.
Stack: Python 3.12, FastAPI, SQLAlchemy 2.0 async, PostgreSQL, Redis, OpenAI, Anthropic.

Adjunto CONTEXTO.md, RULES.md, y AGENTS.md como referencia.

Tarea: generar la estructura completa del proyecto con:
1. Todos los directorios y __init__.py
2. Configuracion de pyproject.toml con dependencias
3. Dockerfile y docker-compose.yml
4. Workflows de CI/CD en .github/workflows/
5. src/config.py con Pydantic Settings
6. src/main.py con FastAPI app factory
7. src/core/exceptions.py con excepciones personalizadas
8. src/domain/entities.py con Usuario, Gasto, Categoria
9. src/domain/repositories.py con interfaces abstractas
10. src/infrastructure/database.py con engine async de SQLAlchemy
11. src/infrastructure/models.py con modelos ORM
12. src/schemas/ con schemas Pydantic v2
13. tests/conftest.py con fixtures base
14. .env.example con todas las variables requeridas
15. Makefile con comandos comunes

Sigue estrictamente RULES.md para estilo y arquitectura.
Usa tipos en todas las funciones, docstrings Google, y async/await consistente.
```

---

## Referencias Cruzadas

- [Guia de Vibe Coding](../docs/guia-vibe-coding.md)
- [Vibe Coding Basico](./vibe-coding-basico.md)
- [Dominio Vibe Coding](../por-dominio/vibe-coding.md)
- [Archivos IA Esenciales](../docs/archivos-ia-esenciales.md)

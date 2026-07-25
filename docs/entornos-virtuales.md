# Entornos Virtuales, .venv y .env: La Guia Definitiva

Uno de los errores mas comunes en proyectos --especialmente en vibe coding-- es no entender como funcionan los entornos virtuales, las variables de entorno y por que son criticos para la reproducibilidad. Esta guia cubre todo lo que un ingeniero de IA necesita saber sobre `.venv`, `.env`, gestion de entornos y secretos.

---

## 1. .venv -- El Entorno Virtual de Python

### Que es un entorno virtual

Un entorno virtual es una carpeta que contiene una instalacion aislada de Python con sus propias dependencias. **Cada proyecto debe tener su propio `.venv`**. Esto evita conflictos de versiones entre proyectos.

```
mi-proyecto/
├── .venv/                   <-- Entorno virtual (NUNCA se commitea)
│   ├── Lib/                 <-- Librerias instaladas (Windows)
│   │   └── site-packages/   <-- Paquetes pip instalados
│   ├── bin/                 <-- Ejecutables (Linux/Mac)
│   ├── Include/
│   └── pyvenv.cfg           <-- Configuracion del entorno
├── src/
├── pyproject.toml
└── .gitignore
```

### Crear un entorno virtual

```bash
# Opcion 1: venv (incluido en Python 3.3+)
python -m venv .venv

# Opcion 2: uv (mas rapido, recomendado)
uv venv

# Opcion 3: virtualenv (mas funcionalidades)
pip install virtualenv
virtualenv .venv

# Opcion 4: Conda (para ciencia de datos)
conda create -n mi-proyecto python=3.12
conda activate mi-proyecto
```

### Activar el entorno

```bash
# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# Windows (CMD)
.venv\Scripts\activate.bat

# Linux / macOS
source .venv/bin/activate

# Con uv (no necesita activacion)
uv run python script.py
uv run pytest
```

### Importante: NUNCA commitear .venv

El directorio `.venv/` **siempre** va en `.gitignore`. Las dependencias se declaran en `pyproject.toml` o `requirements.txt`, no se commitea el entorno completo.

```gitignore
# Siempre en .gitignore
.venv/
venv/
env/
ENV/
env.bak/
venv.bak/
```

### Comandos esenciales

```bash
# Ver que paquetes estan instalados
pip list
pip freeze > requirements.txt    # Exportar dependencias

# Instalar desde archivo de dependencias
pip install -r requirements.txt

# Con uv (recomendado para 2024+)
uv pip install -r requirements.txt
uv pip install fastapi sqlalchemy pydantic
```

### Node.js: node_modules

El equivalente en el ecosistema Node.js es `node_modules/`, que tambien va en `.gitignore`:

```gitignore
node_modules/
```

Las dependencias se declaran en `package.json` y se instalan con `npm install`, `yarn`, o `pnpm install`.

---

## 2. .env -- Variables de Entorno

### Que es un archivo .env

Un archivo `.env` contiene variables de entorno en formato `CLAVE=VALOR`. Se usa para configuracion que cambia entre entornos (desarrollo, staging, produccion) y para **secretos** que jamas deben estar en el codigo fuente.

### Ejemplo de .env

```bash
# Base de datos
DATABASE_URL=postgresql://usuario:password@localhost:5432/mi_db

# API Keys (NUNCA committear este archivo)
OPENAI_API_KEY=sk-proj-abc123...
ANTHROPIC_API_KEY=sk-ant-xyz789...
GEMINI_API_KEY=AIza...

# Configuracion de la app
APP_ENV=development
DEBUG=true
LOG_LEVEL=DEBUG
SECRET_KEY=clave-super-secreta-cambiar-en-produccion

# Servicios externos
REDIS_URL=redis://localhost:6379
AWS_ACCESS_KEY_ID=AKIA...
AWS_SECRET_ACCESS_KEY=...
AWS_REGION=us-east-1

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=mi-correo@gmail.com
SMTP_PASSWORD=...

# URL de la app
API_BASE_URL=http://localhost:8000
FRONTEND_URL=http://localhost:3000
```

### .env NUNCA se commitea

El archivo `.env` **siempre, sin excepcion, va en `.gitignore`**. Contiene secretos y credenciales que si se exponen en un repositorio publico pueden ser explotados en minutos.

```gitignore
.env
.env.local
.env.*.local
```

### .env.example: la alternativa segura

En su lugar, se commitea un archivo `.env.example` que muestra las variables necesarias pero **sin valores reales**:

```bash
# .env.example -- TIENE valores de ejemplo, NO secretos reales
# Copiar a .env y completar con valores reales: cp .env.example .env

# Base de datos
DATABASE_URL=postgresql://usuario:password@localhost:5432/nombre_db

# API Keys -- Obtener de la consola del proveedor
OPENAI_API_KEY=sk-proj-...
ANTHROPIC_API_KEY=sk-ant-...
GEMINI_API_KEY=AIza...

# Configuracion
APP_ENV=development
DEBUG=true
LOG_LEVEL=DEBUG
SECRET_KEY=cambiar-por-valor-aleatorio-seguro

# Servicios
REDIS_URL=redis://localhost:6379
AWS_ACCESS_KEY_ID=...
AWS_SECRET_ACCESS_KEY=...
AWS_REGION=us-east-1

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=tu-correo@gmail.com
SMTP_PASSWORD=...

# URLs
API_BASE_URL=http://localhost:8000
FRONTEND_URL=http://localhost:3000
```

### Como cargar .env en el codigo

**Python con python-dotenv:**

```python
# Instalar: pip install python-dotenv
from dotenv import load_dotenv
import os

load_dotenv()  # Carga .env en os.environ

api_key = os.getenv("OPENAI_API_KEY")
database_url = os.getenv("DATABASE_URL")
debug = os.getenv("DEBUG", "false").lower() == "true"
```

**Python con Pydantic Settings (recomendado para proyectos serios):**

```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    database_url: str
    openai_api_key: str
    anthropic_api_key: str
    app_env: str = "development"
    debug: bool = False
    log_level: str = "INFO"
    secret_key: str
    redis_url: str = "redis://localhost:6379"
    api_base_url: str = "http://localhost:8000"
    frontend_url: str = "http://localhost:3000"

    model_config = {"env_file": ".env", "env_file_encoding": "utf-8"}

settings = Settings()  # Lee .env automaticamente
```

**TypeScript/Node.js:**

```typescript
// Instalar: npm install dotenv
import "dotenv/config";

const apiKey = process.env.OPENAI_API_KEY;
const databaseUrl = process.env.DATABASE_URL;
```

### Buenas practicas con .env

1. **Jamas** commitear `.env`. Revisa `git status` antes de cada commit.
2. **Siempre** mantener `.env.example` actualizado con todas las variables necesarias.
3. Usar `python-dotenv` o `pydantic-settings` en Python; `dotenv` en Node.js.
4. En produccion, usar secretos del sistema (AWS Secrets Manager, Azure Key Vault, GitHub Secrets, Vercel Env).
5. No hacer `print(os.getenv("API_KEY"))` en logs o outputs publicos.
6. Rotar claves periodicamente.
7. Si accidentalmente commiteaste un `.env` con secretos, **rota las claves inmediatamente** y usa `git filter-branch` o `BFG Repo-Cleaner` para eliminarlo del historial.

---

## 3. Gestion de Entornos por Herramienta

### Python: Tabla Comparativa

| Herramienta | Archivo de Dependencias | Crear Entorno | Instalar Paquetes | Velocidad |
|-------------|------------------------|---------------|-------------------|-----------|
| pip + venv | `requirements.txt` | `python -m venv .venv` | `pip install -r requirements.txt` | Lento |
| Poetry | `pyproject.toml` | `poetry install` | `poetry add fastapi` | Medio |
| uv (recomendado) | `pyproject.toml` | `uv venv` | `uv pip install -r requirements.txt` | Muy rapido |
| Hatch | `pyproject.toml` | `hatch env create` | `hatch add fastapi` | Rapido |
| PDM | `pyproject.toml` | `pdm install` | `pdm add fastapi` | Rapido |
| Conda | `environment.yml` | `conda env create -f environment.yml` | `conda install` | Lento |
| Pipenv | `Pipfile` | `pipenv install` | `pipenv install fastapi` | Medio |

### Node.js / TypeScript

| Herramienta | Archivo de Dependencias | Instalar Paquetes | Velocidad |
|-------------|------------------------|-------------------|-----------|
| npm | `package.json` | `npm install` | Medio |
| yarn | `package.json` | `yarn` | Rapido |
| pnpm | `package.json` | `pnpm install` | Muy rapido |
| bun | `package.json` | `bun install` | Muy rapido |

---

## 4. Docker: El Entorno Maximo

Cuando la reproducibilidad es critica, Docker encapsula TODO el entorno (SO, Python, dependencias, variables). Un `Dockerfile` basico para un proyecto Python con IA:

```dockerfile
FROM python:3.12-slim

# Prevenir que Python genere .pyc y active unbuffered output
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Instalar dependencias del sistema
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Instalar dependencias Python (capa cacheable)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copiar codigo fuente
COPY . .

# Puerto
EXPOSE 8000

# Comando de inicio
CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### .env con Docker

Con Docker Compose, las variables pueden venir de `.env` o definirse en `docker-compose.yml`:

```yaml
# docker-compose.yml
services:
  app:
    build: .
    ports:
      - "8000:8000"
    env_file:
      - .env
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/app
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
      POSTGRES_DB: app
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

---

## 5. Resumen: El Checklist del Entorno

Al iniciar un proyecto, verifica:

```
[ ] .venv/ esta en .gitignore
[ ] node_modules/ esta en .gitignore (si aplica)
[ ] .env esta en .gitignore
[ ] .env.example existe y esta commiteado
[ ] requirements.txt / pyproject.toml / package.json existe y esta commiteado
[ ] El README explica como crear el entorno virtual
[ ] El README explica como instalar dependencias
[ ] El README explica como configurar .env desde .env.example
[ ] No hay claves API hardcodeadas en el codigo fuente
[ ] Las variables de entorno se cargan con dotenv / pydantic-settings
[ ] Dockerfile disponible si se requiere reproducibilidad total
```

---

## Referencias Cruzadas

- [Archivos IA Esenciales](./archivos-ia-esenciales.md) -- RULES.md, CONTEXTO.md, AGENTS.md
- [.gitignore Definitivo](./gitignore-definitivo.md) -- guia completa de exclusiones
- [Guia de Vibe Coding](./guia-vibe-coding.md) -- flujos de trabajo con IA

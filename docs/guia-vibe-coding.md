# Guia de Vibe Coding: Del Nivel Basico al Avanzado

Vibe coding es el paradigma de desarrollo donde un programador --con o sin experiencia previa-- utiliza asistentes de inteligencia artificial (Cursor, Copilot, Claude, ChatGPT, Aider, OpenCode) para generar, modificar y mantener codigo mediante lenguaje natural. Esta guia cubre como estructurar un repositorio para que la IA trabaje de forma eficiente, desde el proyecto mas simple hasta sistemas profesionales.

---

## Indice de Niveles

| Nivel | Perfil | Herramientas | Tiempo tipico de proyecto |
|-------|--------|--------------|---------------------------|
| **Nivel 0: Chispa** | Nunca ha programado | ChatGPT, Claude (web) | Horas |
| **Nivel 1: Principiante** | Sabe lo basico, usa IA para todo | Cursor, Copilot | Dias |
| **Nivel 2: Intermedio** | Programa, la IA acelera | Cursor + reglas, Aider | Semanas |
| **Nivel 3: Avanzado** | Ingeniero, la IA es un par | Agentes, multi-agente | Meses |
| **Nivel 4: Experto** | Arquitecto, dirige agentes | Orquestacion de agentes | Continuo |

---

## Nivel 0: Chispa ("Nunca he programado")

Perfil: persona sin experiencia en programacion que quiere crear algo usando IA conversacional.

### Estructura minima

```
mi-proyecto/
├── .gitignore
├── .env.example
├── README.md              # La IA ayuda a escribirlo
├── main.py                # (o app.py, index.html, etc.)
└── requirements.txt
```

### Archivos esenciales

**.gitignore minimo:**
```gitignore
.venv/
.env
__pycache__/
```

**.env.example:**
```bash
OPENAI_API_KEY=sk-...
```

**README.md generado por IA:**
```markdown
# Mi Proyecto

Generado con [ChatGPT | Claude]. Un [descripcion breve].

## Como ejecutar
1. Crear entorno: `python -m venv .venv`
2. Activar: `.venv\Scripts\activate` (Windows) o `source .venv/bin/activate` (Mac/Linux)
3. Instalar: `pip install -r requirements.txt`
4. Copiar `.env.example` a `.env` y poner tu API key
5. Ejecutar: `python main.py`
```

### Flujo de trabajo
1. Describir que se quiere construir en ChatGPT/Claude
2. Pegar el codigo generado en el archivo
3. Ejecutar, ver errores, pegarlos de vuelta a la IA
4. Iterar hasta que funcione
5. Pedir a la IA que genere README.md

### Limitaciones de este nivel
- Sin tests
- Sin control de versiones riguroso
- Sin separacion de responsabilidades
- Dificil de mantener a largo plazo

---

## Nivel 1: Principiante Vibe Coder

Perfil: ya entiende que es un repositorio, Git, entornos virtuales, pero la IA escribe la mayoria del codigo.

### Estructura del proyecto

```
mi-proyecto/
├── .gitignore
├── .env.example
├── README.md
├── RULES.md                   # Reglas basicas que el LLM debe seguir
├── requirements.txt
├── src/
│   ├── __init__.py
│   ├── main.py                # Punto de entrada
│   ├── config.py              # Configuracion desde .env
│   ├── utils.py               # Funciones de utilidad
│   └── [feature]/
│       ├── __init__.py
│       └── [modulo].py
├── tests/
│   └── test_main.py
└── docs/
    └── README.md
```

### Contenido de RULES.md (version principiante)

```markdown
# Reglas del Proyecto

## Lenguaje
- Python 3.11+
- Usar tipos en todas las funciones (def funcion(x: str) -> int:)
- Formatear con Ruff

## Convenciones
- Nombres de archivos: snake_case.py
- Nombres de funciones: snake_case()
- Nombres de clases: PascalCase

## Prohibido
- NO uses print() para debugging (usa logging)
- NO hardcodees claves API
- NO hagas commit de .env ni .venv/

## Testing
- Escribir un test basico para cada funcion nueva
- Ejecutar pytest antes de considerar el codigo como terminado

## Al Pedirle a la IA
- Pedir explicaciones de lo que genera
- Pedir que incluya tipos y docstrings
- Pedir que maneje errores con try/except
```

### Prompt template para este nivel

```
Necesito que generes un [script/modulo/funcion] en Python que haga [descripcion].

Reglas del proyecto (RULES.md):
- Usar tipos en todas las funciones
- snake_case para variables y funciones
- Manejar errores con try/except
- Incluir docstrings
- Usar las librerias: [listar librerias instaladas]

El codigo debe ir en src/[ruta].py
Las variables de entorno se leen desde config.py

Ademas, genera un test basico en tests/test_[nombre].py
```

---

## Nivel 2: Intermedio Vibe Coder

Perfil: programador que usa IA como acelerador. Entiende arquitectura, patrones, testing. La IA genera el codigo pero el diseno general es humano.

### Estructura del proyecto

```
mi-proyecto/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml             # CI: lint, test, type-check
│   │   └── cd.yml             # CD: deploy
│   └── copilot-instructions.md
├── .cursor/
│   └── rules                  # Reglas para Cursor AI
├── .gitignore
├── .env.example
├── .pre-commit-config.yaml
├── README.md
├── RULES.md
├── CONTEXTO.md
├── AGENTS.md
├── pyproject.toml             # Dependencias, config de Ruff/mypy/pytest
├── Dockerfile
├── docker-compose.yml
├── src/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py
│   ├── api/                   # Endpoints / rutas
│   │   ├── __init__.py
│   │   └── router.py
│   ├── core/                  # Logica compartida, seguridad
│   │   ├── __init__.py
│   │   ├── security.py
│   │   └── dependencies.py
│   ├── domain/                # Entidades, value objects
│   │   ├── __init__.py
│   │   └── models.py
│   ├── infrastructure/        # DB, clientes externos
│   │   ├── __init__.py
│   │   ├── database.py
│   │   └── llm_client.py
│   └── services/              # Casos de uso, logica de negocio
│       ├── __init__.py
│       └── [servicio].py
├── tests/
│   ├── conftest.py
│   ├── test_api/
│   ├── test_services/
│   └── test_domain/
├── scripts/
│   └── seed_data.py
├── docs/
│   ├── arquitectura.md
│   └── api.md
└── alembic/                   # Migraciones de BD
    └── ...
```

### CONTEXTO.md esencial

Ver [Archivos IA Esenciales](./archivos-ia-esenciales.md#2-contextomd----el-cerebro-del-proyecto-para-la-ia) para la plantilla completa.

### Flujo de trabajo con IA en nivel intermedio

1. **Disenar** la arquitectura (humano o humano+IA)
2. **Escribir CONTEXTO.md** para que la IA entienda el proyecto
3. **Crear RULES.md** con convenciones
4. **Crear AGENTS.md** con comportamiento esperado
5. Para cada feature:
   - Describir el requerimiento en lenguaje natural
   - La IA genera el codigo siguiendo el contexto y las reglas
   - Revisar el codigo generado (el humano valida)
   - La IA genera los tests
   - Ejecutar tests y CI
   - Merge

---

## Nivel 3: Avanzado Vibe Coder

Perfil: ingeniero de software que dirige agentes de IA autonomos. Los agentes ejecutan tareas completas: analisis, implementacion, testing, documentacion.

### Estructura del proyecto

```
mi-proyecto/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── cd.yml
│   │   ├── agent-review.yml   # Agente revisor automatico de PRs
│   │   └── release-drafter.yml
│   └── copilot-instructions.md
├── .cursor/
│   └── rules/
│       ├── global.md
│       ├── python.md
│       ├── testing.md
│       ├── database.md
│       └── api.md
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
├── Makefile                   # Comandos comunes
├── src/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py
│   ├── api/                   # Endpoints versionados
│   │   ├── v1/
│   │   └── v2/
│   ├── core/
│   ├── domain/
│   ├── infrastructure/
│   ├── services/
│   └── workers/               # Tareas asincronas (Celery, etc.)
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── e2e/
│   └── fixtures/
├── scripts/
├── docs/
│   ├── adr/                   # Architecture Decision Records
│   ├── api/
│   └── diagrams/
├── alembic/
├── deployment/                # Configuraciones de despliegue
│   ├── kubernetes/
│   └── terraform/
├── monitoring/                # Dashboards, alertas
│   └── grafana/
└── .aider.conf.yml
```

### AGENTS.md para nivel avanzado

Ver [Archivos IA Esenciales](./archivos-ia-esenciales.md#3-agentsmd----instrucciones-para-agentes-de-ia-autonomos) para la plantilla completa con permisos y restricciones.

### Orquestacion multi-agente (nivel avanzado)

En este nivel se pueden usar multiples agentes especializados:

```
agentes/
├── arquitecto/       # Disena la arquitectura, toma decisiones de diseno
├── desarrollador/    # Implementa features
├── revisor/          # Revisa PRs, verifica reglas
├── tester/           # Escribe tests, verifica cobertura
├── documentador/     # Mantiene docs actualizadas
└── devops/           # Gestiona CI/CD, infraestructura
```

---

## Nivel 4: Experto (Ingeniero de IA)

Perfil: arquitecto de software que orquesta equipos de agentes de IA. El repositorio es la fuente de verdad para humanos y agentes.

### Caracteristicas distintivas

- **Monorepo** con multiples paquetes/servicios
- **Reglas por dominio** (`.cursor/rules/` con archivos especializados)
- **ADRs** para decisiones arquitectonicas
- **Evaluacion automatizada** de calidad de codigo generado por IA
- **Metrics** de productividad con IA
- **Pipeline de entrenamiento** de modelos propios
- **Feature flags** para despliegues progresivos
- **Observabilidad** completa (logs, metrics, traces)

---

## Tabla Comparativa: Archivos por Nivel

| Archivo | Nivel 0 | Nivel 1 | Nivel 2 | Nivel 3 | Nivel 4 |
|---------|---------|---------|---------|---------|---------|
| `.gitignore` | X | X | X | X | X |
| `.env.example` | X | X | X | X | X |
| `README.md` | X | X | X | X | X |
| `RULES.md` | -- | X | X | X | X |
| `CONTEXTO.md` | -- | -- | X | X | X |
| `AGENTS.md` | -- | -- | X | X | X |
| `.cursor/rules` | -- | -- | X | X | X |
| `.github/copilot-instructions.md` | -- | -- | X | X | X |
| `CLAUDE.md` | -- | -- | -- | X | X |
| `pyproject.toml` | -- | X | X | X | X |
| `.pre-commit-config.yaml` | -- | -- | X | X | X |
| `Dockerfile` | -- | -- | X | X | X |
| `Makefile` | -- | -- | -- | X | X |
| `tests/` | -- | X | X | X | X |
| `docs/` | -- | -- | X | X | X |
| `scripts/` | -- | -- | X | X | X |
| CI/CD workflows | -- | -- | X | X | X |
| `monitoring/` | -- | -- | -- | -- | X |
| `deployment/` | -- | -- | -- | X | X |
| `alembic/` o migraciones | -- | -- | X | X | X |
| ADRs | -- | -- | -- | X | X |

---

## Ejemplos Completos

Ver directorio `ejemplos/`:
- [Proyecto Vibe Coding Basico (Nivel 1)](../ejemplos/vibe-coding-basico.md)
- [Proyecto Vibe Coding Avanzado (Nivel 3)](../ejemplos/vibe-coding-avanzado.md)
- [Proyecto Agente IA](../ejemplos/agente-ia-proyecto.md)
- [Proyecto Fine-tuning LLM](../ejemplos/llm-finetuning.md)

## Referencias Cruzadas

- [Archivos IA Esenciales](./archivos-ia-esenciales.md) -- detalle de cada archivo
- [Entornos Virtuales](./entornos-virtuales.md) -- .venv, .env, gestion de entornos
- [.gitignore Definitivo](./gitignore-definitivo.md) -- guia completa de exclusiones
- [Dominio: Vibe Coding](../por-dominio/vibe-coding.md) -- guia por dominio
- [Dominio: IA Generativa](../por-dominio/ia-generativa.md)
- [Dominio: Agentes IA](../por-dominio/agentes-ia.md)

# Estructura y Buenas Practicas para Proyectos de Vibe Coding

Vibe coding es el paradigma de desarrollo de software donde un programador utiliza asistentes de inteligencia artificial (Cursor, Copilot, Claude, ChatGPT, Aider, OpenCode, Windsurf) como herramienta principal para escribir codigo, delegando la sintaxis y los detalles de implementacion a la IA mientras mantiene la direccion del proyecto.

Esta guia cubre como estructurar un repositorio optimizado para el flujo de trabajo humano+IA, desde el nivel mas basico hasta el mas avanzado.

---

## Principios del Vibe Coding

1. **La IA es una herramienta, no un reemplazo**: el humano define que construir, la IA ayuda con el como.
2. **Instrucciones explicitas**: cuanto mas claro sea el contexto que le das a la IA, mejor sera el resultado.
3. **Iteracion rapida**: generar, probar, refinar. El ciclo es mas corto que en desarrollo tradicional.
4. **El repositorio es el prompt de sistema**: RULES.md, CONTEXTO.md y AGENTS.md definen como la IA debe comportarse.
5. **Testing asistido**: la IA puede generar tests, pero el humano valida que prueban lo correcto.
6. **Documentacion viva**: la IA ayuda a mantener la documentacion sincronizada con el codigo.

---

## Estructura de Directorios Recomendada

### Nivel Basico (Proyecto Personal / Prototipo)

```
mi-proyecto-vibe/
├── .gitignore
├── .env.example
├── README.md
├── RULES.md                   # Reglas que la IA debe seguir
├── requirements.txt           # Dependencias
├── main.py                    # Punto de entrada principal
├── utils.py                   # Funciones auxiliares
└── tests/
    └── test_main.py
```

### Nivel Intermedio (Proyecto Profesional con IA)

```
mi-proyecto-vibe/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   └── cd.yml
│   └── copilot-instructions.md
├── .cursor/
│   └── rules                   # Reglas para Cursor AI
├── .gitignore
├── .env.example
├── .pre-commit-config.yaml
├── README.md
├── RULES.md
├── CONTEXTO.md                 # Contexto amplio para la IA
├── AGENTS.md                   # Comportamiento esperado de agentes
├── pyproject.toml              # Dependencias + config de herramientas
├── src/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py               # Configuracion desde .env
│   ├── api/                    # Endpoints (si es API)
│   ├── core/                   # Logica compartida
│   ├── domain/                 # Modelos de dominio
│   ├── infrastructure/         # Base de datos, clientes externos
│   └── services/               # Logica de negocio
├── tests/
│   ├── conftest.py
│   ├── unit/
│   └── integration/
├── prompts/                    # Historial de prompts utiles (opcional)
│   └── prompts-utiles.md
├── scripts/                    # Scripts de utilidad
└── docs/
```

### Nivel Avanzado (Multi-agente / Produccion)

Incluye todo lo del nivel intermedio mas:

```
mi-proyecto-vibe/
├── ...
├── CLAUDE.md
├── .aider.conf.yml
├── .windsurfrules
├── Makefile                    # Comandos comunes documentados
├── src/
│   ├── ...
│   └── workers/                # Tareas asincronas
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── deployment/
│   ├── docker/
│   └── kubernetes/
├── monitoring/
├── docs/
│   ├── adr/                    # Decisiones arquitectonicas
│   └── api/
├── alembic/                    # Migraciones
└── agentes/                    # Definiciones de agentes IA
    ├── arquitecto.md           # Prompt del agente arquitecto
    ├── desarrollador.md        # Prompt del agente desarrollador
    └── revisor.md              # Prompt del agente revisor
```

---

## Convenciones para Vibe Coding

### Nombrado de Archivos y Carpetas

```
src/
├── api/                        # Endpoints y rutas
├── core/                       # Configuracion, seguridad, utilidades centrales
├── domain/                     # Entidades y reglas de negocio
├── infrastructure/             # Implementaciones concretas (DB, APIs)
├── services/                   # Casos de uso, orquestacion
├── models/                     # Modelos de ML (si aplica)
└── utils/                      # Utilidades compartidas
```

### Como Dar Instrucciones a la IA

**Prompt base para generar codigo:**

```
Trabaja en el proyecto descrito en CONTEXTO.md.
Sigue las reglas definidas en RULES.md.

Tarea: [descripcion de lo que necesitas]

Stack: Python 3.12, FastAPI, SQLAlchemy 2.0, Pydantic v2
Patron: Clean Architecture (api -> services -> domain -> infrastructure)

El codigo debe incluir:
- Tipos en todas las funciones
- Docstrings en formato Google
- Manejo de errores con excepciones personalizadas
- Tests unitarios

No uses:
- print() para debugging (usa el logger)
- Valores hardcodeados
- Dependencias que no estan en pyproject.toml
```

### Reglas Clave para la IA (RULES.md minimo)

```markdown
# RULES.md

## Estilo
- Python 3.11+, tipos estrictos
- Ruff para formateo y linting (config en pyproject.toml)
- Docstrings en formato Google
- Max 88 caracteres por linea
- snake_case para funciones y variables
- PascalCase para clases

## Arquitectura
- Clean Architecture: api -> services -> domain -> infrastructure
- Inyeccion de dependencias via FastAPI Depends()
- Repositorios para acceso a datos
- Servicios para logica de negocio

## Testing
- pytest con --strict-markers
- Cobertura minima: 80%
- Fixtures en conftest.py
- Tests AAA: Arrange, Act, Assert

## Git
- Conventional Commits (feat:, fix:, refactor:, test:, docs:, chore:)
- No commits de .env, .venv, __pycache__

## Prohibiciones
- NO secrets en el codigo fuente
- NO dependencias circulares
- NO print() para debugging
- NO imports relativos (usa imports absolutos desde src/)
```

---

## Flujo de Trabajo con IA

### Ciclo de Desarrollo Diario

1. **Planificar**: define la tarea del dia en lenguaje natural
2. **Contextualizar**: la IA lee CONTEXTO.md para entender el proyecto
3. **Generar**: la IA produce el codigo siguiendo RULES.md
4. **Validar**: ejecuta tests, linting, type-check
5. **Iterar**: si hay errores, la IA los corrige
6. **Commit**: una vez que pasa todo, commit con mensaje convencional

### Workflow de PR con Agente IA

1. Crear rama de feature: `feat/nombre-feature`
2. La IA implementa siguiendo RULES.md y AGENTS.md
3. La IA genera tests
4. CI ejecuta: lint -> type-check -> tests -> coverage
5. Agente revisor de IA revisa el PR (verifica reglas, patrones, seguridad)
6. Desarrollador humano aprueba
7. Merge a main, despliegue automatico

---

## Herramientas y Configuraciones Especificas

### Cursor

```bash
# .cursor/rules
# Ver plantilla completa en plantillas/.cursorrules
```

### GitHub Copilot

```markdown
# .github/copilot-instructions.md
# Ver plantilla completa en plantillas/github-copilot-instructions.md
```

### Aider

```yaml
# .aider.conf.yml
model: anthropic/claude-sonnet-4-20250514
read: [RULES.md, CONTEXTO.md, AGENTS.md]
auto-commits: false
lint-cmd: ruff check .
test-cmd: pytest
```

### OpenCode / Claude Code

```markdown
# AGENTS.md
# Ver plantilla completa en plantillas/AGENTS.md
```

---

## Errores Comunes en Vibe Coding

1. **No tener RULES.md**: la IA genera codigo inconsistente.
2. **No tener CONTEXTO.md**: la IA no entiende el proposito del proyecto y genera codigo que no encaja.
3. **No usar tipos**: la IA genera codigo sin tipos, dificil de mantener.
4. **No escribir tests**: confiar en que "la IA lo hizo bien". Siempre verificar con tests.
5. **No usar .gitignore correctamente**: terminar committeando .env, .venv o node_modules.
6. **Delegar demasiado**: la IA escribe codigo, pero las decisiones arquitectonicas las toma el humano.
7. **No revisar el codigo generado**: siempre leer y entender lo que la IA produce.
8. **Prompts vagos**: "hazme una app" vs "crea un endpoint GET /users que devuelva usuarios paginados desde PostgreSQL usando SQLAlchemy".

---

## Checklist para Iniciar un Proyecto Vibe Coding

```
[ ] Crear repositorio en GitHub/GitLab
[ ] Inicializar Git localmente
[ ] Crear .gitignore (segun tipo de proyecto)
[ ] Crear .env.example (documentar variables requeridas)
[ ] Crear README.md (descripcion del proyecto)
[ ] Crear RULES.md (reglas de desarrollo)
[ ] Crear CONTEXTO.md (stack, arquitectura, dependencias)
[ ] Crear AGENTS.md (comportamiento esperado de agentes IA)
[ ] Configurar pyproject.toml / package.json
[ ] Instalar dependencias en entorno virtual
[ ] Configurar CI/CD (.github/workflows/)
[ ] Configurar .cursor/rules si usas Cursor
[ ] Configurar .github/copilot-instructions.md si usas Copilot
[ ] Primer commit con la estructura base
[ ] Empezar a desarrollar con IA
```

---

## Referencias Cruzadas

- [Guia General de Vibe Coding](../docs/guia-vibe-coding.md) -- niveles 0 a 4 en detalle
- [Archivos IA Esenciales](../docs/archivos-ia-esenciales.md) -- RULES.md, CONTEXTO.md, AGENTS.md, etc.
- [Entornos Virtuales y .env](../docs/entornos-virtuales.md) -- .venv, .env, gestion de entornos
- [.gitignore Definitivo](../docs/gitignore-definitivo.md) -- exclusiones por tipo de proyecto
- [Ejemplo: Vibe Coding Basico](../ejemplos/vibe-coding-basico.md)
- [Ejemplo: Vibe Coding Avanzado](../ejemplos/vibe-coding-avanzado.md)

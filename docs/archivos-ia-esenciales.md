# Archivos Esenciales para Proyectos con IA: RULES.md, CONTEXTO.md, AGENTS.md y Mas

En la era del desarrollo asistido por inteligencia artificial, el repositorio no solo alberga codigo: tambien contiene **instrucciones para los asistentes de IA** que colaboran en el proyecto. Estos archivos son el "prompt de sistema" del repositorio y determinan como herramientas como Cursor, Copilot, Claude Code, Codex, Aider, OpenCode y otras entienden y trabajan con tu codigo.

Esta guia cubre todos los archivos de instruccion para IA que un proyecto profesional debe incluir, desde el nivel mas basico (vibe coding) hasta el mas avanzado (ingenieria de software con agentes).

---

## Indice de Archivos por Nivel

| Nivel | Archivo | Proposito | Herramienta |
|-------|---------|-----------|-------------|
| Basico | `.gitignore` | Exclusion de archivos del control de versiones | Git (universal) |
| Basico | `.env.example` | Variables de entorno de ejemplo | Cualquier app |
| Basico | `README.md` | Documentacion de entrada del proyecto | Universal |
| Intermedio | `.cursor/rules` o `.cursorrules` | Reglas de comportamiento para Cursor AI | Cursor |
| Intermedio | `.github/copilot-instructions.md` | Instrucciones para GitHub Copilot | Copilot |
| Avanzado | `AGENTS.md` | Instrucciones para agentes de IA (OpenCode, Claude Code) | OpenCode, Claude |
| Avanzado | `CONTEXTO.md` | Contexto amplio del proyecto para asistentes IA | Universal |
| Avanzado | `RULES.md` | Reglas globales de desarrollo del repositorio | Multi-agente |
| Avanzado | `CLAUDE.md` | Instrucciones especificas para Claude Code | Claude Code |
| Avanzado | `.aider.conf.yml` | Configuracion para Aider | Aider |
| Avanzado | `.windsurfrules` | Reglas para Windsurf | Windsurf |

---

## 1. RULES.md -- El Archivo de Reglas Universal

`RULES.md` es el estandar emergente para definir reglas de desarrollo que aplican a todo el repositorio, tanto para desarrolladores humanos como para asistentes de IA. Define el contrato de como se trabaja en este proyecto.

### Contenido de RULES.md

```markdown
# Reglas del Proyecto [NOMBRE_DEL_PROYECTO]

## Reglas de Estilo de Codigo
- Usar [Python 3.11+ | TypeScript 5.x | etc.]
- Formatear con [Ruff | Prettier | Black]
- Longitud maxima de linea: [88 | 100 | 120] caracteres
- Nombres de variables en [snake_case | camelCase | PascalCase]
- Nombres de clases en [PascalCase]
- Constantes en [UPPER_SNAKE_CASE]
- Funciones: maximo [20 | 30] lineas. Si excede, refactorizar.
- Un archivo por clase/componente (excepto utilidades pequenas)

## Reglas de Tipado
- Activar modo estricto de tipos ([mypy --strict | TypeScript strict: true])
- Prohibir `any` en TypeScript (usar `unknown` si es necesario)
- Todas las funciones publicas deben tener anotaciones de tipo
- Usar `Optional[T]` en lugar de `T | None` (segun convencion del proyecto)

## Reglas de Commits
- Usar Conventional Commits (`feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`)
- No hacer commit de secretos ni claves API
- No hacer commit de archivos generados (build, dist, .next, __pycache__)
- No hacer commit de entornos virtuales (.venv, venv, env)
- Cada commit debe ser atomico (un solo cambio logico)

## Reglas de Documentacion
- Todo modulo publico debe tener docstring
- Toda funcion publica debe tener docstring con parametros y retorno
- Usar formato [Google | NumPy | Sphinx] para docstrings
- Mantener README.md actualizado con setup y uso
- Documentar decisiones arquitectonicas en ADRs (si aplica)

## Reglas de Testing
- Cobertura minima de tests: [70% | 80%]
- Toda nueva funcionalidad debe incluir tests
- No hacer commit si los tests no pasan
- Ejecutar `pytest` (o equivalente) antes de cada commit

## Reglas de Seguridad
- Nunca commitear archivos .env
- Usar .env.example para documentar variables requeridas
- Las claves API y secretos van en variables de entorno o gestor de secretos
- No incluir tokens, passwords, ni credenciales en el codigo fuente
- Revisar dependencias con [pip-audit | npm audit | safety] periodicamente

## Reglas de Performance
- Evitar consultas N+1 en bases de datos
- Usar paginacion para listados grandes
- Cerrar conexiones y archivos explicitamente o con context managers
- No cargar datasets completos en memoria si no es necesario

## Reglas de Accesibilidad (frontend)
- Usar etiquetas semanticas HTML
- Proveer textos alternativos en imagenes
- Asegurar contraste de color adecuado
```

---

## 2. CONTEXTO.md -- El Cerebro del Proyecto para la IA

`CONTEXTO.md` proporciona a los asistentes de IA la informacion necesaria para entender el proyecto sin tener que leer todo el codigo fuente. Es el documento que responde "de que va este proyecto y como esta construido".

### Contenido de CONTEXTO.md

```markdown
# Contexto del Proyecto: [NOMBRE_DEL_PROYECTO]

## Descripcion General
[2-3 parrafos explicando que hace el proyecto, para quien, y por que existe]

## Stack Tecnologico
- **Lenguaje:** [Python 3.12 | TypeScript 5.4 | etc.]
- **Framework principal:** [FastAPI | Next.js | React | etc.]
- **Base de datos:** [PostgreSQL 16 | MongoDB 7 | SQLite | etc.]
- **ORM:** [SQLAlchemy 2.0 | Prisma | Drizzle | etc.]
- **Testing:** [Pytest | Vitest | Jest | etc.]
- **CI/CD:** [GitHub Actions | GitLab CI | etc.]
- **Despliegue:** [Docker | Kubernetes | Vercel | AWS Lambda | etc.]
- **Gestion de paquetes:** [Poetry | uv | pip | npm | pnpm | yarn]

## Arquitectura
[Aqui describe el patron arquitectonico y la estructura de alto nivel]

Este proyecto sigue una arquitectura [Clean Architecture | Hexagonal | MVC | Microservicios].
Las capas principales son:
1. **Capa de presentacion** (`src/api/` o `src/routes/`): endpoints HTTP
2. **Capa de aplicacion** (`src/services/` o `src/use_cases/`): logica de negocio
3. **Capa de dominio** (`src/domain/` o `src/models/`): entidades y reglas de negocio
4. **Capa de infraestructura** (`src/infrastructure/` o `src/db/`): acceso a datos, clientes externos

## Estructura de Carpetas
```
mi-proyecto/
├── src/                # Codigo fuente principal
│   ├── api/            # Endpoints y controladores
│   ├── core/           # Configuracion, seguridad, dependencias
│   ├── domain/         # Entidades, value objects, repositorios (interfaces)
│   ├── infrastructure/ # Implementaciones concretas (DB, APIs externas)
│   └── services/       # Casos de uso, servicios de aplicacion
├── tests/              # Tests automatizados
├── docs/               # Documentacion
├── scripts/            # Scripts de utilidad
├── .github/            # CI/CD workflows
├── RULES.md            # Reglas de desarrollo
├── CONTEXTO.md         # Este archivo
├── AGENTS.md           # Instrucciones para asistentes IA
├── .gitignore          # Exclusiones de Git
├── .env.example        # Variables de entorno de ejemplo
├── pyproject.toml      # Configuracion del proyecto Python
└── README.md           # Documentacion de entrada
```

## Principios de Diseno
- [SOLID | DRY | KISS | YAGNI]
- Composicion sobre herencia
- Inmutabilidad donde sea posible
- Favorecer funciones puras

## Flujo de Trabajo
1. Crear rama desde `main`: `git checkout -b feat/nombre-feature`
2. Desarrollar siguiendo RULES.md
3. Escribir tests
4. Ejecutar tests localmente: `pytest` (o equivalente)
5. Crear PR contra `main`
6. CI ejecuta tests, linting, type-check
7. Review de codigo
8. Merge a `main`, despliegue automatico

## Dependencias Clave
[Lista de las dependencias mas importantes y para que se usan]

- **FastAPI**: framework web para la API REST
- **SQLAlchemy**: ORM para PostgreSQL
- **Alembic**: migraciones de base de datos
- **Pydantic**: validacion de datos y schemas
- **httpx**: cliente HTTP asincrono para llamadas externas
- **Tenacity**: reintentos con backoff exponencial
- **Structlog**: logging estructurado

## Servicios Externos
- **Base de datos:** PostgreSQL en [Railway | Supabase | AWS RDS]
- **Cache:** Redis en [Upstash | AWS ElastiCache]
- **Almacenamiento:** S3 en [AWS | Cloudflare R2]
- **Email:** [Resend | SendGrid | AWS SES]
- **Monitoreo:** [Sentry | Datadog | Grafana]
- **IA/LLM:** [OpenAI API | Anthropic API | Azure OpenAI | modelo local]

## Variables de Entorno Requeridas
Ver `.env.example` para la lista completa. Las principales:
- `DATABASE_URL`: cadena de conexion a base de datos
- `SECRET_KEY`: clave secreta para JWT/sesiones
- `OPENAI_API_KEY`: clave de API de OpenAI
- `REDIS_URL`: URL de conexion a Redis

## Notas Importantes
- Este proyecto usa [async/await extensivamente | programacion sincrona]
- La base de datos usa [UUIDs | IDs autoincrementales] como claves primarias
- Los errores se manejan con [excepciones personalizadas | Result types]
- El versionado de API se hace via [URL prefix | header | query param]
```

---

## 3. AGENTS.md -- Instrucciones para Agentes de IA Autonomos

`AGENTS.md` es el archivo especifico para agentes de IA que ejecutan tareas en el repositorio de forma autonoma (OpenCode, Claude Code, etc.). Define permisos, flujos de trabajo y restricciones.

### Contenido de AGENTS.md

```markdown
# AGENTS.md -- Instrucciones para Asistentes de IA

## Rol del Agente
Eres un asistente de ingenieria de software trabajando en el proyecto **[NOMBRE]**.
Tu objetivo es ayudar al desarrollador a escribir, revisar, refactorizar y mantener codigo de alta calidad.

## Comportamiento General
- Se conciso y directo en tus respuestas
- Antes de escribir codigo, lee el contexto relevante (archivos cercanos, imports, tests)
- Sigue el estilo y convenciones existentes en el codigo
- No generes documentacion a menos que se te pida explicitamente
- No hagas commits a menos que se te pida explicitamente
- Si no entiendes algo, pregunta antes de asumir
- No modifiques archivos de configuracion del proyecto sin consultar
- Siempre verifica que los tests pasan despues de hacer cambios

## Al Escribir Codigo
- Usa las mismas librerias y patrones que el resto del proyecto
- Prioriza legibilidad sobre brevedad extrema
- Incluye manejo de errores apropiado
- Usa tipos estrictos
- Sigue el principio de minimo asombro
- No introduzcas nuevas dependencias sin consultar
- Escribe docstrings en el formato del proyecto
- Manten las funciones pequenas y enfocadas

## Al Revisar Codigo
- Verifica tipos, manejo de errores, y casos borde
- Revisa seguridad (inyeccion SQL, XSS, secretos expuestos)
- Revisa performance (consultas N+1, memory leaks)
- Sugiere mejoras pero no seas dogmatico

## Prohibiciones
- NO hagas commit de secretos, tokens o claves API
- NO modifiques .gitignore sin consultar
- NO uses `eval()`, `exec()`, o similares
- NO introduzcas codigo no determinista sin documentarlo
- NO generes codigo que viole el principio de responsabilidad unica
- NO elimines tests existentes sin consultar
- NO hagas force push a ramas compartidas
- NO uses emojis en el codigo o documentacion (a menos que el proyecto los use)
```

---

## 4. .cursor/rules -- Reglas para Cursor AI

Los archivos `.cursor/rules` o `.cursorrules` (en la raiz del proyecto) definen como se comporta Cursor AI al generar y editar codigo en este repositorio.

### Archivo: `.cursor/rules` (o `.cursorrules` en raiz)

```markdown
# Reglas de Cursor para [NOMBRE_DEL_PROYECTO]

## Lenguaje y Estilo
- Lenguaje: [Python | TypeScript | etc.]
- Framework: [FastAPI | React | Next.js | etc.]
- Estilo de codigo: [Ruff | ESLint | Prettier | Black]
- Siempre usa tipos estrictos
- Prefiere composicion sobre herencia
- Usa async/await donde sea apropiado

## Estructura
- Un archivo por clase/componente
- Agrupar por funcionalidad, no por tipo de archivo
- Los tests van en `tests/` reflejando la estructura de `src/`

## Al Generar Codigo
- Usa las librerias ya instaladas en el proyecto
- Sigue los mismos patrones de codigo existentes
- Nombra variables y funciones descriptivamente
- Incluye tipos en todas las firmas de funciones
- Maneja errores explicitamente
- No uses `print()` para debugging; usa el logger del proyecto
- Usa los modelos/schemas existentes para validacion de datos
- No introduzcas nuevas dependencias

## Al Editar Codigo
- Lee el contexto primero (archivos relacionados)
- Minimiza cambios innecesarios
- Preserva el estilo existente
- No reformatees archivos completos a menos que se pida

## Convenciones del Proyecto
- Nombres de archivos: [snake_case.py | kebab-case.ts | PascalCase.tsx]
- Tests: `test_*.py` con `pytest`
- Docstrings: formato [Google | NumPy | Sphinx]
- Imports: [ordenados alfabeticamente | isort | ruff]

## Contexto del Proyecto
[Aqui pega un resumen de CONTEXTO.md adaptado para el formato de reglas de Cursor]
```

### Archivo: `.cursor/rules` (estructura recomendada por dominio)

Para proyectos grandes, Cursor permite organizar reglas por dominio en la carpeta `.cursor/rules/`:

```
.cursor/
└── rules/
    ├── global.md           # Reglas que aplican a todo el proyecto
    ├── python.md           # Reglas especificas para codigo Python
    ├── typescript.md       # Reglas especificas para TypeScript/React
    ├── testing.md          # Reglas para escritura de tests
    ├── database.md         # Reglas para acceso a datos y migraciones
    └── api.md              # Reglas para diseno de APIs y endpoints
```

Ejemplo de regla por dominio para Python (`.cursor/rules/python.md`):

```markdown
# Reglas para Codigo Python

- Usar Python 3.11+ con todas las anotaciones de tipo
- Formatear con Ruff (configurado en pyproject.toml)
- Docstrings en formato Google
- Usar Pydantic para validacion de datos
- Usar SQLAlchemy 2.0+ con estilo declarativo
- Preferir dataclasses sobre diccionarios para datos estructurados
- Usar `pathlib.Path` en lugar de `os.path`
- Usar `httpx` en lugar de `requests` para codigo asincrono
- Usar context managers (`with`) para recursos que deben cerrarse
- Evitar `import *`; importar solo lo necesario
- Ordenar imports: stdlib, terceros, locales (separados por linea en blanco)
```

---

## 5. .github/copilot-instructions.md -- Instrucciones para GitHub Copilot

GitHub Copilot lee este archivo para personalizar sus sugerencias en el repositorio.

```markdown
# Instrucciones para GitHub Copilot

## Estilo de Codigo
- [Python | TypeScript | etc.] con tipos estrictos
- Formatear segun la configuracion del proyecto ([Ruff | Prettier | etc.])
- Mantener funciones pequenas (max 20 lineas)
- Usar nombres descriptivos

## Testing
- Framework: [Pytest | Vitest | Jest]
- Nombrar tests: `test_[funcionalidad]_[escenario]`
- Cada test debe ser independiente
- Usar fixtures/factories para datos de prueba

## Patrones
- [FastAPI]: usar dependencias para inyeccion
- [React]: usar hooks personalizados para logica reutilizable
- [Base de datos]: usar repositorios para abstraer acceso a datos
- [Errores]: usar excepciones personalizadas, no valores de retorno especiales

## No Hacer
- No uses `any` (TypeScript) o omitas tipos (Python)
- No generes codigo sin tipos
- No uses `console.log` / `print` para debugging
- No hardcodees valores de configuracion
```

---

## 6. CLAUDE.md -- Instrucciones para Claude Code

Especifico para usuarios de Claude Code de Anthropic.

```markdown
# CLAUDE.md -- Instrucciones para Claude Code

## Comandos del Proyecto
- Ejecutar tests: `pytest` (o `npm test`)
- Linting: `ruff check .` (o `npm run lint`)
- Formateo: `ruff format .` (o `npm run format`)
- Type check: `mypy src/` (o `npx tsc --noEmit`)
- Construir: `[comando de build]`
- Ejecutar en dev: `[comando de dev server]`

## Arquitectura
[Aqui describe brevemente la arquitectura, stack y patrones]

## Notas para Claude
- Leer RULES.md y CONTEXTO.md antes de trabajar
- No modificar archivos de configuracion sin preguntar
- Mantener el estilo de codigo consistente con lo existente
- Siempre verificar que los tests pasan despues de cambios
- No hacer commits sin autorizacion explicita
```

---

## 7. .aider.conf.yml -- Configuracion para Aider

Para usuarios de Aider (herramienta de pair programming con IA en terminal).

```yaml
# .aider.conf.yml
model: anthropic/claude-sonnet-4-20250514
# o model: openai/gpt-4o
# o model: openrouter/deepseek/deepseek-chat

read: [RULES.md, CONTEXTO.md]
auto-commits: false
lint-cmd: ruff check .
test-cmd: pytest
```

---

## 8. .windsurfrules -- Reglas para Windsurf

```markdown
# Reglas para Windsurf

## Estilo
- [Mismas reglas que RULES.md adaptadas]

## Testing
- Ejecutar tests con `pytest` antes de considerar un cambio completo

## Git
- No hacer commit si los tests fallan
- Usar Conventional Commits
```

---

## Flujo de Decision: Que Archivos Necesita Mi Proyecto

### Nivel Basico (Vibe Coding / Prototipo Rapido)
```
mi-proyecto/
├── .gitignore
├── .env.example
├── README.md
└── RULES.md          (reglas minimas)
```

### Nivel Intermedio (Proyecto Personal / Portfolio)
```
mi-proyecto/
├── .gitignore
├── .env.example
├── README.md
├── RULES.md
├── CONTEXTO.md
├── AGENTS.md
└── .cursor/rules     (o .cursorrules)
```

### Nivel Avanzado (Produccion / Equipo)
```
mi-proyecto/
├── .gitignore
├── .env.example
├── README.md
├── RULES.md
├── CONTEXTO.md
├── AGENTS.md
├── CLAUDE.md
├── .cursor/
│   └── rules/
│       ├── global.md
│       ├── python.md
│       ├── testing.md
│       └── api.md
├── .github/
│   └── copilot-instructions.md
└── .aider.conf.yml
```

---

## Nota Importante sobre el Contenido de Estos Archivos

Todos estos archivos deben ser **tratados como codigo fuente**: se versionan en Git, se revisan en PRs, y se mantienen actualizados. Un `CONTEXTO.md` desactualizado es peor que no tenerlo, porque la IA tomara decisiones basadas en informacion incorrecta.

El contenido especifico de cada archivo debe adaptarse al proyecto real. Las plantillas de esta guia son puntos de partida que debes personalizar.

## Referencias Cruzadas

- [Guia de Vibe Coding](./guia-vibe-coding.md) -- como usar estos archivos en flujos de IA
- [Entornos Virtuales](./entornos-virtuales.md) -- .venv, .env, gestion de entornos
- [.gitignore Definitivo](./gitignore-definitivo.md) -- guia completa de exclusiones
- [Estructura de Carpetas](./estructura-carpetas.md) -- principios de organizacion
- [Documentacion Viva](./documentacion-viva.md) -- mantener documentacion sincronizada

## Plantillas Disponibles

En `plantillas/` encontraras versiones listas para copiar de:
- [RULES.md](../plantillas/RULES.md)
- [CONTEXTO.md](../plantillas/CONTEXTO.md)
- [AGENTS.md](../plantillas/AGENTS.md)
- [.cursorrules](../plantillas/.cursorrules)
- [Instrucciones para Copilot](../plantillas/github-copilot-instructions.md)
- [.env de ejemplo](../plantillas/env.ejemplo)
- [.gitignore completo](../plantillas/gitignore-completo.md)

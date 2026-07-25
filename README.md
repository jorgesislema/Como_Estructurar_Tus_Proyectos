# Guia Definitiva para la Arquitectura y Estructura de Repositorios

Este repositorio es la referencia obligatoria para estructurar proyectos de software, desde el nivel mas basico (vibe coding) hasta el mas avanzado (ingenieria de IA profesional). Cubre principios arquitectonicos, infraestructura y despliegue (VPS, cloud, serverless), CI/CD, estandares de colaboracion, desarrollo asistido por IA, agentes autonomos, LLM Ops, bases de datos, networking, observabilidad, costos y seguridad del codigo.

**Objetivo:** Convertirse en el estandar de oro para la organizacion profesional de cualquier repositorio, sin importar el stack, dominio, tamano del equipo o nivel de experiencia.

---

## Indice General

- [Documentacion Modular (`docs/`)](#documentacion-modular-docs)
- [Guias por Dominio (`por-dominio/`)](#guias-por-dominio-por-dominio)
- [Ejemplos Practicos (`ejemplos/`)](#ejemplos-practicos-ejemplos)
- [Plantillas Profesionales (`plantillas/`)](#plantillas-profesionales-plantillas)
- [Workflows de Automatizacion (`workflows/`)](#workflows-de-automatizacion-workflows)
- [Casos de Estudio (`casos-estudio/`)](#casos-de-estudio-casos-estudio)
- [Como Usar Esta Guia](#como-usar-esta-guia)
- [Contribuciones](#contribuciones)
- [Licencia](#licencia)

---

## Documentacion Modular (`docs/`)

Guias y explicaciones detalladas sobre fundamentos del desarrollo de software profesional.

### Fundamentos de Arquitectura y Estilo
- `principios-basicos.md`: vision general y objetivos de la guia.
- `principios-arquitectonicos.md`: SOLID, DRY, KISS, YAGNI, SoC, Cohesion/Acoplamiento, Ley de Demeter.
- `estructura-carpetas.md`: diseno logico y fisico de carpetas. Estrategias por capa, por feature, hibridas.
- `convenciones-nombres.md`: nombres consistentes y legibles para ramas, archivos, commits.
- `versionado-semver.md`: control y semantica de versiones (SemVer).

### Git, Equipo y Procesos
- `flujos-ramas-git.md`: GitHub Flow, GitFlow, trunk-based development.
- `buenas-practicas-git.md`: commits atomicos, mensajes significativos, PR hygiene.
- `flujos-trabajo-equipo.md`: flujo de ramas y gestion de cambios en equipo.
- `gestion-issues-y-prs.md`: colaboracion efectiva via issues y pull requests.
- `control-acceso.md`: practicas de seguridad y permisos en repositorios.

### Calidad, Seguridad y Mantenibilidad
- `seguridad-codigo.md`: estandares de seguridad de codigo fuente.
- `modularidad-y-escalabilidad.md`: diseno extensible, escalado vertical vs horizontal.
- `documentacion-viva.md`: docstrings, Sphinx, MkDocs, BDD, diagramas como codigo.
- `mantenimiento-largo-plazo.md`: sostenibilidad del proyecto a largo plazo.
- `patrones-repo-industria.md`: monorepo vs polyrepo, ejemplos reales analizados.

### CI/CD y Automatizacion
- `workflows-github.md`: uso profesional de GitHub Actions.

### Ingenieria de IA y Vibe Coding

- `guia-vibe-coding.md`: guia completa de desarrollo asistido por IA. Cubre los 5 niveles: desde "nunca he programado" hasta "arquitecto dirigiendo agentes". Incluye tabla comparativa de archivos por nivel, prompts template, y flujos de trabajo.
- `archivos-ia-esenciales.md`: guia exhaustiva de todos los archivos que un proyecto con IA debe tener: RULES.md, CONTEXTO.md, AGENTS.md, `.cursor/rules`, `.github/copilot-instructions.md`, CLAUDE.md, `.aider.conf.yml`. Cada archivo con su contenido detallado y flujo de decision de cual necesitas segun tu nivel.
- `entornos-virtuales.md`: guia definitiva de `.venv`, `.env`, gestion de entornos (uv, Poetry, Conda, pip), Docker. Incluye como cargar `.env` en Python (dotenv, Pydantic Settings), Node.js, y checklist de verificacion.
- `gitignore-definitivo.md`: guia completa de `.gitignore` por tipo de proyecto: Python, Ciencia de Datos/ML, Node.js/TypeScript, IA Generativa/LLMs, Fullstack Monorepo, Vibe Coding. Incluye principios fundamentales y checklist de archivos que SI deben committearse.

### Infraestructura, Despliegue y Operaciones

- `decision-infraestructura.md`: guia para decidir donde ejecutar tu proyecto. Cubre todas las opciones: localhost, tunel (ngrok), VPS (con tabla de proveedores y precios), PaaS, serverless, cloud completo, edge computing. Incluye arbol de decision y lo que el vibe coder no considera (DNS, HTTPS, backups, cold starts, rate limits).
- `arquitecturas-despliegue.md`: patrones de despliegue: monolito, monolito modular, microservicios, event-driven, serverless, edge, y patrones hibridos (los que realmente se usan). Pros/contras, criterios de decision, y mapa de arquitectura por tipo de proyecto.
- `entornos-y-stages.md`: dev, staging, produccion y entornos efimeros por PR. Pipeline de promocion entre entornos. Configuracion por entorno, errores comunes. Entornos para proyectos de IA (evaluacion, fine-tuning).
- `bases-datos-almacenamiento.md`: SQL (PostgreSQL, SQLite, MySQL), NoSQL (MongoDB, Redis), Vector Stores (pgvector, Chroma, Pinecone, Qdrant), Cache, Object Storage. Arbol de decision completo. Lo que el vibe coder no sabe: migraciones, indices, conexiones, N+1 queries, backups.
- `networking-seguridad.md`: DNS, HTTPS/SSL, CDN, WAF, firewalls, VPN (Tailscale), gestion de secretos en produccion. Checklist de seguridad basica imprescindible.
- `cicd-gitops.md`: CI/CD por nivel (personal a enterprise). Estrategias de despliegue (big bang, rolling, blue/green, canary, feature flags). GitOps (ArgoCD, Flux). IaC (Terraform, Pulumi). Lo que el vibe coder ignora: secretos en CI, cache de dependencias, smoke tests, rollback.
- `observabilidad.md`: logs, metrics, traces, alertas. Las 4 metricas de oro. Metricas especificas para LLMs. Herramientas (Sentry, Grafana, Prometheus, LangFuse). Dashboard minimo recomendado. Alerta por sintoma, no por causa.
- `costos-finops.md`: estimacion de costos por tipo de proyecto ($0 a $26K+/mes). Costos especificos de LLMs por modelo. Estrategias de reduccion (cache semantica, model routing). Errores clasicos que cuestan dinero. FinOps: presupuestos, tags, revision mensual. Recomendacion por presupuesto.

### Referencia Completa

- `glosario.md`: glosario exhaustivo con mas de 120 terminos tecnicos organizados alfabeticamente. Cubre arquitectura, infraestructura, IA, seguridad, testing, Git y mas. Cada termino incluye definicion, contexto y referencia al documento donde se profundiza.
- `tipos-licencias.md`: guia completa de licencias de software: MIT, Apache 2.0, GPL v3, AGPL, LGPL, MPL, BSD, SSPL, BUSL, Creative Commons. Incluye arbol de decision, ejemplos de proyectos famosos que usan cada una, texto completo de MIT, y recomendacion por tipo de proyecto.
- `catalogo-archivos.md`: referencia exhaustiva de TODOS los tipos de carpetas y archivos que existen en proyectos de software. Cubre mas de 200 entradas organizadas por categoria: control de versiones, entornos, IDE, codigo fuente, testing, documentacion, datos/ML, infraestructura, build, configuracion, linters, despliegue, monitoreo, IA/LLMs. Cada entrada indica si se commitea o no.
- `mcp-protocolo.md`: guia del Model Context Protocol (Anthropic, 2024). Arquitectura cliente-servidor, tipos de transporte (stdio/HTTP+SSE), capacidades (tools, resources, prompts), ejemplos de servidores MCP (base de datos, filesystem, Git, RAG), configuracion en clientes, estructura de proyectos MCP y comparacion con alternativas (OpenAI function calling, LangChain, A2A).

---

## Guias por Dominio (`por-dominio/`)

Estructuras de repositorio especializadas por area de ingenieria.

### Desarrollo Tradicional
- `backend.md`: APIs con Node.js, Python, Java, Go.
- `frontend.md`: aplicaciones React, Vue, Angular.
- `fullstack.md`: monorepos fullstack.
- `devtools-python.md`: herramientas de desarrollo Python.
- `cli-herramientas.md`: herramientas de linea de comandos.
- `proyectos-desktop.md`: aplicaciones de escritorio.
- `proyectos-documentacion.md`: proyectos de documentacion.

### Datos e Inteligencia Artificial
- `ciencia-datos.md`: proyectos de Data Science con DVC, notebooks, gestion de experimentos, reproducibilidad.
- `ml-ops.md`: MLOps con CI/CD/CT, pipelines de entrenamiento, despliegue, monitoreo de modelos.
- `ingenieria-datos.md`: pipelines ETL/ELT, data engineering.

### Ingenieria de IA (NUEVO)
- `vibe-coding.md`: guia completa para proyectos desarrollados con asistentes de IA. Cubre desde el nivel basico hasta multi-agente. Incluye estructura de directorios por nivel, RULES.md minimo, prompts template para IA, y checklist de inicio.
- `ia-generativa.md`: proyectos con LLMs (GPT-4, Claude, Gemini, Llama). Cubre capa de abstraccion de LLMs, gestion de prompts como codigo, RAG, cadenas de procesamiento, evaluacion de outputs, middleware (caching, rate limiting, cost tracking). Con ejemplos de codigo de cada componente.
- `agentes-ia.md`: agentes de IA autonomos. Cubre el bucle ReAct, sistema de herramientas (function calling), tipos de memoria (corto plazo, largo plazo, trabajo, entidades), seguridad y guardrails, multi-agente, evaluacion de agentes. Con implementacion de referencia del bucle del agente.
- `llm-ops.md`: operaciones de LLMs. Cubre API Gateway multi-proveedor, enrutamiento inteligente, cache semantica, fine-tuning pipeline, evaluacion continua, monitoreo (costos, latencia, calidad), guardrails. Con dashboards y alerts.

---

## Ejemplos Practicos (`ejemplos/`)

Arboles de proyecto completos con explicaciones y codigo de referencia.

### Desarrollo Tradicional
- `backend-python.md`: API con FastAPI, SQLAlchemy, Alembic, Poetry.
- `frontend-react.md`: frontend con React, Vite, TypeScript, Vitest.
- `fullstack-monorepo.MD`: monorepo con Turborepo, React + Express.
- `data-engineering-pipeline.md`: pipelines de datos ETL/ELT.
- `terraform-infra.md`: infraestructura como codigo con Terraform.
- `estructura-microservicios.md`: arquitectura de microservicios.

### Machine Learning y Datos
- `machine-learning-pipeline.md`: pipeline ML reproducible con DVC y Scikit-learn.

### Vibe Coding e Ingenieria de IA (NUEVO)
- `vibe-coding-basico.md`: proyecto completo Nivel 1 (principiante). Resumidor de texto con OpenAI. Incluye TODOS los archivos con su contenido: .gitignore, .env.example, README.md, RULES.md, requirements.txt, main.py, src/config.py, src/llm.py, src/resumidor.py, src/utils.py, prompts/, tests/. Con prompt inicial para que la IA genere el proyecto.
- `vibe-coding-avanzado.md`: proyecto completo Nivel 3 (profesional). API de asesor financiero con IA usando FastAPI, Clean Architecture, PostgreSQL, Redis, OpenAI, Anthropic, RAG. Incluye CONTEXTO.md, AGENTS.md, RULES.md, CI/CD workflows, pyproject.toml, Dockerfile, Makefile. Con prompt para iniciar el proyecto con IA.
- `agente-ia-proyecto.md`: proyecto de agente analista de datos autonomo. Incluye implementacion del bucle ReAct, sistema de herramientas con function calling, sandbox Docker, evaluacion, seguridad. Con codigo de referencia del agente y suite de evaluacion.
- `llm-finetuning.md`: proyecto de fine-tuning de LLMs. Pipeline completo: preparacion de datos, validacion, entrenamiento (OpenAI/Hugging Face), evaluacion, registro y despliegue. Con DVC, W&B, configuraciones YAML, y CI/CD para fine-tuning.
- `rag-end-to-end.md`: proyecto RAG completo paso a paso. Cubre ingestion de documentos, estrategias de chunking, embeddings multi-proveedor, recuperacion hibrida (dense + BM25 + re-ranking), generacion contextual y evaluacion RAGAS-style. Con arbol de decision para chunk size, vector store y modelo de embeddings.
- `ai-microservicio.md`: microservicio de IA listo para produccion. Combina streaming SSE, rate limiting por usuario, circuit breaker, multi-proveedor fallback, cache semantica (exacta + embeddings), auth, guardrails y observabilidad completa. Con dashboard Grafana y metricas Prometheus.
- `multi-agente-colaborativo.md`: sistema multi-agente donde un equipo de agentes IA (arquitecto, desarrollador, tester, revisor) colabora via pizarra compartida y bus de mensajes. Orquestador descompone tareas, asigna agentes, y sintetiza resultados. Incluye metricas de equipo.
- `prompt-engineering.md`: laboratorio de ingenieria de prompts con evaluacion sistematica. Versionado de prompts, suite de evaluacion multi-metodo, A/B testing estadistico, pipeline CI/CD para prompts, reportes de regresion y recomendaciones automatizadas.
- `ollama-local.md`: despliegue local de LLMs con Ollama. Instalacion, Docker Compose (Ollama + Open WebUI + PostgreSQL), seleccion de modelos por GPU/tarea, Modelfile personalizado, cliente Python, enrutador local-vs-cloud y comparacion de costos.

---

## Plantillas Profesionales (`plantillas/`)

Archivos listos para copiar, adaptar y usar en cualquier proyecto.

### Estandar de Repositorio
- `README.md`: plantilla completa de README profesional.
- `LICENSE.md`: plantilla de licencia de software.
- `CHANGELOG.md`: plantilla de changelog (Keep a Changelog).
- `CONTRIBUTING.md`: guia de contribucion para colaboradores.
- `CODE_OF_CONDUCT.md`: codigo de conducta comunitario.
- `SECURITY.md`: politica de seguridad.
- `ISSUE_TEMPLATE.md`: plantilla para issues de GitHub.
- `PULL_REQUEST_TEMPLATE.md`: plantilla para pull requests.
- `.gitignore.md`: plantilla de .gitignore por tipo de proyecto.

### Configuracion de Proyecto
- `config/pyproject.toml`: configuracion moderna de proyecto Python (Hatchling, Ruff, Mypy, Pytest).
- `config/setup.cfg`: configuracion legacy de Python (con notas de deprecacion).
- `config/requirements.txt`: plantilla de dependencias Python.
- `config/mkdocs.yml`: configuracion de documentacion con MkDocs Material.

### Ingenieria de IA (NUEVO)
- `RULES.md`: plantilla de reglas de desarrollo para humanos e IA. Cubre estilo, tipos, arquitectura, documentacion, testing, Git, seguridad, performance y prohibiciones. Formato lista para copiar y personalizar.
- `CONTEXTO.md`: plantilla de contexto del proyecto para asistentes de IA. Cubre stack, arquitectura, estructura de carpetas, principios de diseno, flujo de trabajo, dependencias, servicios externos, variables de entorno. La IA usa este archivo para entender el proyecto sin leer todo el codigo.
- `AGENTS.md`: plantilla de instrucciones para agentes de IA autonomos (OpenCode, Claude Code). Define rol, comportamiento al escribir y revisar codigo, comandos del proyecto, y prohibiciones.
- `.cursorrules`: plantilla de reglas para Cursor AI. Incluye formato para `.cursorrules` (un solo archivo) y para `.cursor/rules/` (multiple archivos por dominio).
- `github-copilot-instructions.md`: plantilla de instrucciones para GitHub Copilot. Define estilo, testing, patrones del proyecto, imports, y que NO debe sugerir.
- `env.ejemplo`: plantilla completa de `.env.example`. Cubre base de datos, LLMs (OpenAI, Anthropic, Gemini, Azure, DeepSeek, Groq), AWS, Azure, GCP, vector stores, email, monitoreo, URLs, feature flags, rate limiting.
- `gitignore-completo.md`: plantilla unificada de `.gitignore`. Cubre Python, Node.js, TypeScript, Java, Go, Rust, secretos, IDEs, OS, Docker, Terraform, ML/Datos, LLMs, herramientas de IA.

---

## Workflows de Automatizacion (`workflows/`)

GitHub Actions listos para usar en cualquier proyecto.

- `ci-python.yml`: CI completa para Python (Ruff, Mypy, Pytest, coverage, matrix builds).
- `ci-node.yml`: CI completa para Node.js/TypeScript (lint, type-check, test, build, matrix builds).
- `lint-frontend.yml`: lint y formato para frontend (ESLint, Prettier).
- `build-docs.yml`: build y despliegue de documentacion a GitHub Pages.
- `release-tagging.yml`: versionado automatico y GitHub Releases.
- `test-notebooks.yml`: ejecucion de Jupyter notebooks en CI.

---

## Casos de Estudio (`casos-estudio/`)

Analisis profundo de estructuras de repositorios en empresas lideres. Cada caso incluye arbol de carpetas, analisis tecnico, puntos fuertes y aprendizajes.

- `Netflix.md`: arquitectura de microservicios, Gradle, Helm, CI/CD.
- `airbnb.md`: monorepo fullstack (React + Python), Docker Compose.
- `microsoft.md`: documentacion, gobernanza y herramientas de desarrollo.
- `OpenIA.md`: estructuras para investigacion reproducible y ML.
- `Google Dataflow .md`: pipelines de datos a escala.
- `spotify-data-platform.md`: plataforma de datos moderna.
- `open-source-foundation.md`: gobernanza en Apache, Linux Foundation.
- `analisis-comparativo-monorepos.md`: monorepo vs polyrepo con tabla comparativa.

---

## Como Usar Esta Guia

1. **Clona** este repositorio o copia las carpetas que necesites.
2. **Lee** `docs/` para entender los principios detras de cada decision estructural.
3. **Elige tu nivel** en `docs/guia-vibe-coding.md` (Nivel 0 a 4) y sigue la estructura recomendada.
4. **Copia** los archivos de IA esenciales desde `plantillas/`: RULES.md, CONTEXTO.md, AGENTS.md.
5. **Inspirate** en `ejemplos/` para ver implementaciones concretas de cada tipo de proyecto.
6. **Adapta** las `plantillas/` para profesionalizar tu flujo de trabajo.
7. **Activa** los `workflows/` para CI/CD desde el primer dia.
8. **Aprende** de los `casos-estudio/` para aplicar decisiones arquitectonicas informadas.
9. **Usa los prompts** incluidos en los ejemplos para generar proyectos completos con IA.

---

## Mapa de Navegacion Rapida

### Si eres nuevo programando y usas IA (Nivel 0-1)
1. `docs/guia-vibe-coding.md` -- Entiende los niveles
2. `docs/entornos-virtuales.md` -- Aprende .venv y .env
3. `docs/gitignore-definitivo.md` -- Configura tu .gitignore
4. `plantillas/RULES.md` -- Copia las reglas basicas
5. `ejemplos/vibe-coding-basico.md` -- Mira el ejemplo completo
6. `por-dominio/vibe-coding.md` -- Referencia de la estructura

### Si eres programador y quieres incorporar IA (Nivel 2-3)
1. `docs/archivos-ia-esenciales.md` -- Configura todos los archivos IA
2. `docs/entornos-virtuales.md` -- Domina los entornos
3. `plantillas/CONTEXTO.md` -- Crea tu contexto de proyecto
4. `plantillas/AGENTS.md` -- Define comportamiento de agentes
5. `ejemplos/vibe-coding-avanzado.md` -- Estructura profesional
6. `por-dominio/vibe-coding.md` -- Convenciones y flujos

### Si trabajas con IA Generativa / LLMs
1. `por-dominio/ia-generativa.md` -- Estructura base para LLMs
2. `por-dominio/agentes-ia.md` -- Agentes autonomos
3. `por-dominio/llm-ops.md` -- Operaciones de LLM
4. `ejemplos/rag-end-to-end.md` -- RAG completo paso a paso
5. `ejemplos/ai-microservicio.md` -- Microservicio IA profesional
6. `ejemplos/prompt-engineering.md` -- Evaluacion sistematica de prompts
7. `ejemplos/multi-agente-colaborativo.md` -- Equipo de agentes IA
8. `ejemplos/ollama-local.md` -- LLMs locales con Ollama
9. `docs/mcp-protocolo.md` -- Model Context Protocol
10. `ejemplos/agente-ia-proyecto.md` -- Ejemplo de agente
11. `ejemplos/llm-finetuning.md` -- Ejemplo de fine-tuning

### Si trabajas con ML / Data Science
1. `por-dominio/ciencia-datos.md` -- Proyectos de Data Science
2. `por-dominio/ml-ops.md` -- MLOps en produccion
3. `ejemplos/machine-learning-pipeline.md` -- Pipeline con DVC
4. `docs/gitignore-definitivo.md` -- Exclusiones para ML
5. `casos-estudio/OpenIA.md` -- Caso OpenAI

### Si eres ingeniero de software tradicional
1. `docs/principios-arquitectonicos.md` -- Fundamentos
2. `docs/estructura-carpetas.md` -- Diseno de carpetas
3. `docs/patrones-repo-industria.md` -- Monorepo vs Polyrepo
4. `ejemplos/` -- Ejemplos por stack
5. `por-dominio/` -- Guias por dominio

### Si quieres desplegar tu proyecto a produccion
1. `docs/decision-infraestructura.md` -- Decide donde ejecutar
2. `docs/arquitecturas-despliegue.md` -- Elige el patron de despliegue
3. `docs/entornos-y-stages.md` -- Configura entornos
4. `docs/bases-datos-almacenamiento.md` -- Elige donde guardar datos
5. `docs/networking-seguridad.md` -- DNS, HTTPS, firewalls
6. `docs/cicd-gitops.md` -- Automatiza el despliegue
7. `docs/observabilidad.md` -- Monitorea y alerta
8. `docs/costos-finops.md` -- Controla el presupuesto
9. `ejemplos/despliegue-plataformas.md` -- Ejemplos por plataforma

---

## Estructura del Repositorio

```
Como_Estructurar_Tus_Proyectos/
├── README.md                           # Este archivo: indice general
├── docs/                               # Documentacion modular (32 archivos)
│   ├── principios-basicos.md
│   ├── principios-arquitectonicos.md
│   ├── estructura-carpetas.md
│   ├── convenciones-nombres.md
│   ├── versionado-semver.md
│   ├── flujos-ramas-git.md
│   ├── buenas-practicas-git.md
│   ├── flujos-trabajo-equipo.md
│   ├── gestion-issues-y-prs.md
│   ├── control-acceso.md
│   ├── seguridad-codigo.md
│   ├── modularidad-y-escalabilidad.md
│   ├── documentacion-viva.md
│   ├── mantenimiento-largo-plazo.md
│   ├── patrones-repo-industria.md
│   ├── workflows-github.md
│   ├── guia-vibe-coding.md            # NUEVO: niveles 0-4 de vibe coding
│   ├── archivos-ia-esenciales.md      # NUEVO: RULES.md, CONTEXTO.md, AGENTS.md, etc.
│   ├── entornos-virtuales.md          # NUEVO: .venv, .env, Docker, entornos
│   ├── gitignore-definitivo.md        # NUEVO: .gitignore por tipo de proyecto
│   ├── decision-infraestructura.md    # NUEVO: VPS, Cloud, Serverless, PaaS, Edge
│   ├── arquitecturas-despliegue.md    # NUEVO: monolito, microservicios, serverless
│   ├── entornos-y-stages.md           # NUEVO: dev, staging, prod, efimeros
│   ├── bases-datos-almacenamiento.md  # NUEVO: SQL, NoSQL, vectores, cache, storage
│   ├── networking-seguridad.md        # NUEVO: DNS, HTTPS, CDN, WAF, firewalls
│   ├── cicd-gitops.md                 # NUEVO: CI/CD, GitOps, estrategias de deploy
│   ├── observabilidad.md              # NUEVO: logs, metrics, traces, alertas
│   ├── costos-finops.md               # NUEVO: estimacion, optimizacion, FinOps
│   ├── glosario.md                     # NUEVO: glosario de 120+ terminos tecnicos
│   ├── tipos-licencias.md              # NUEVO: guia de licencias con ejemplos
│   └── catalogo-archivos.md            # NUEVO: todos los archivos/carpetas posibles
├── por-dominio/                        # Guias por dominio (14 archivos)
│   ├── backend.md
│   ├── frontend.md
│   ├── fullstack.md
│   ├── ciencia-datos.md
│   ├── ml-ops.md
│   ├── ingenieria-datos.md
│   ├── devtools-python.md
│   ├── cli-herramientas.md
│   ├── proyectos-desktop.md
│   ├── proyectos-documentacion.md
│   ├── vibe-coding.md                 # NUEVO: dominio vibe coding
│   ├── ia-generativa.md               # NUEVO: dominio IA generativa/LLMs
│   ├── agentes-ia.md                  # NUEVO: dominio agentes IA
│   └── llm-ops.md                     # NUEVO: dominio LLM Ops
├── ejemplos/                           # Ejemplos practicos (17 archivos)
│   ├── backend-python.md
│   ├── frontend-react.md
│   ├── fullstack-monorepo.MD
│   ├── machine-learning-pipeline.md
│   ├── data-engineering-pipeline.md
│   ├── terraform-infra.md
│   ├── estructura-microservicios.md
│   ├── vibe-coding-basico.md          # NUEVO: proyecto vibe code principiante
│   ├── vibe-coding-avanzado.md        # NUEVO: proyecto vibe code profesional
│   ├── agente-ia-proyecto.md          # NUEVO: proyecto agente IA
│   ├── llm-finetuning.md              # NUEVO: proyecto fine-tuning LLM
│   └── despliegue-plataformas.md      # NUEVO: ejemplos de despliegue por plataforma
├── workflows/                          # GitHub Actions (6 archivos)
│   ├── ci-python.yml
│   ├── ci-node.yml
│   ├── lint-frontend.yml
│   ├── build-docs.yml
│   ├── release-tagging.yml
│   └── test-notebooks.yml
├── casos-estudio/                      # Casos de estudio (8 archivos)
│   ├── Netflix.md
│   ├── airbnb.md
│   ├── microsoft.md
│   ├── OpenIA.md
│   ├── Google Dataflow .md
│   ├── spotify-data-platform.md
│   ├── open-source-foundation.md
│   └── analisis-comparativo-monorepos.md
└── plantillas/                         # Plantillas copy-paste (19 archivos)
    ├── README.md
    ├── LICENSE.md
    ├── CHANGELOG.md
    ├── CONTRIBUTING.md
    ├── CODE_OF_CONDUCT.md
    ├── SECURITY.md
    ├── ISSUE_TEMPLATE.md
    ├── PULL_REQUEST_TEMPLATE.md
    ├── .gitignore.md
    ├── RULES.md                        # NUEVO: plantilla de reglas
    ├── CONTEXTO.md                     # NUEVO: plantilla de contexto IA
    ├── AGENTS.md                       # NUEVO: plantilla de agente IA
    ├── .cursorrules                    # NUEVO: plantilla reglas Cursor
    ├── github-copilot-instructions.md  # NUEVO: plantilla Copilot
    ├── env.ejemplo                     # NUEVO: plantilla .env.example
    ├── gitignore-completo.md           # NUEVO: plantilla .gitignore unificada
    ├── CLAUDE.md                        # NUEVO: plantilla para Claude Code
├── .aider.conf.yml                  # NUEVO: plantilla para Aider
└── config/
        ├── pyproject.toml
        ├── setup.cfg
        ├── requirements.txt
        └── mkdocs.yml
```

---

## Contribuciones

Toda mejora, correccion o ejemplo adicional es bienvenida. Lee el archivo `CONTRIBUTING.md` para mas detalles.

## Licencia

Este proyecto esta licenciado bajo los terminos de la licencia incluida en `LICENSE`.

---

Creditos y Reconocimientos

Inspirado por las mejores practicas de comunidades como GitHub, GitLab, Google, Microsoft, Netflix, Airbnb, OpenAI, Anthropic y fundaciones como Apache, Linux y OpenJS. Construido como recurso educativo para la comunidad de ingenieria de software e inteligencia artificial.

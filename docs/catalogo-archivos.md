# Catalogo Universal de Archivos y Carpetas en Proyectos de Software

Referencia exhaustiva de TODOS los tipos de carpetas y archivos que una persona puede encontrarse al realizar un proyecto de software, desde desarrollo basico hasta produccion empresarial con IA. Cada entrada incluye: nombre, proposito, si se commitea o no, y en que tipo de proyectos aparece.

---

## Carpetas Universales

### Control de Versiones

| Carpeta | Proposito | Se commitea? | Aparece en |
|---------|-----------|--------------|------------|
| `.git/` | Repositorio Git local (historial, ramas, config) | NO (local) | Todo proyecto con Git |
| `.github/` | Configuraciones de GitHub (workflows, templates, bots) | SI | Proyectos en GitHub |
| `.github/workflows/` | Workflows de GitHub Actions (CI/CD) | SI | Proyectos con CI/CD en GitHub |
| `.github/ISSUE_TEMPLATE/` | Plantillas para nuevos issues | SI | Proyectos con gestion de issues |
| `.github/PULL_REQUEST_TEMPLATE.md` o `.github/PULL_REQUEST_TEMPLATE/` | Plantillas para PRs | SI | Proyectos con revision de PRs |
| `.gitlab/` | Configuraciones de GitLab (CI/CD) | SI | Proyectos en GitLab |
| `.gitlab-ci.yml` | Pipeline de CI/CD de GitLab | SI | Proyectos en GitLab |
| `.circleci/` | Configuraciones de CircleCI | SI | Proyectos con CircleCI |
| `.dvc/` | Metadatos de DVC (cache, config local) | NO (local, cache) | Proyectos con DVC |
| `.svn/` | Subversion metadata | NO (local) | Legacy SVN |

### Entornos y Dependencias

| Carpeta | Proposito | Se commitea? | Aparece en |
|---------|-----------|--------------|------------|
| `.venv/` o `venv/` o `env/` | Entorno virtual de Python | NUNCA | Proyectos Python |
| `.conda/` | Entorno Conda local | NUNCA | Proyectos con Conda |
| `node_modules/` | Dependencias de Node.js instaladas | NUNCA | Proyectos Node.js/JavaScript |
| `.pnpm-store/` | Cache de paquetes de pnpm | NUNCA | Proyectos con pnpm |
| `.yarn/` | Cache y configuracion de Yarn | NO | Proyectos con Yarn |
| `__pycache__/` | Bytecode compilado de Python | NUNCA | Proyectos Python |
| `vendor/` | Dependencias vendoreadas (Go, PHP antiguo) | Depende (Go: SI, PHP: NO) | Go, PHP legacy |
| `target/` | Artefactos de compilacion (Java, Rust) | NUNCA | Java, Rust |
| `bin/` o `obj/` | Binarios intermedios (.NET) | NUNCA | .NET/C# |
| `.tox/` | Entornos de testing de Tox | NUNCA | Proyectos Python con Tox |

### IDE y Editores

| Carpeta | Proposito | Se commitea? | Aparece en |
|---------|-----------|--------------|------------|
| `.vscode/` | Configuracion de VS Code/Cursor (settings, launch, extensions) | Depende (recomendado SI para config compartida) | VS Code, Cursor |
| `.idea/` | Configuracion de JetBrains (PyCharm, IntelliJ, WebStorm) | NO (normalmente en .gitignore) | JetBrains IDEs |
| `.cursor/` | Configuracion de Cursor AI (rules, settings) | SI (rules compartidas) | Cursor IDE |
| `.eclipse/` o `.settings/` | Configuracion de Eclipse | NO | Eclipse |
| `.project` y `.classpath` | Archivos de proyecto Eclipse | Depende | Eclipse legacy |

### Codigo Fuente

| Carpeta | Proposito | Aparece en |
|---------|-----------|------------|
| `src/` | Codigo fuente principal | Casi todo proyecto |
| `app/` o `lib/` | Codigo fuente (alternativa a src/) | JavaScript, Ruby, Python |
| `api/` | Definiciones de API (Protobuf, OpenAPI, GraphQL) | Proyectos con APIs |
| `pkg/` | Codigo reutilizable (patron Go) | Go, a veces Python |
| `cmd/` | Puntos de entrada ejecutables (patron Go) | Go |
| `internal/` | Codigo no exportable fuera del modulo (patron Go) | Go |
| `components/` | Componentes de UI reutilizables | React, Vue, Angular |
| `pages/` o `views/` o `routes/` | Paginas o rutas de la aplicacion | Frontend |
| `hooks/` o `composables/` | Hooks/Custom hooks (logica reutilizable sin UI) | React, Vue 3 |
| `store/` o `state/` | Gestion de estado global | Frontend |
| `services/` | Capa de servicios / logica de negocio | Backend, fullstack |
| `domain/` o `core/` | Entidades y reglas de negocio (DDD) | Backend |
| `infrastructure/` o `infra/` | Implementaciones de acceso a datos, APIs externas | Backend |
| `middleware/` | Middleware HTTP / capa intermedia | Backend |
| `utils/` o `helpers/` o `common/` | Funciones de utilidad compartidas | Todo proyecto |
| `shared/` | Codigo compartido entre modulos en monorepos | Monorepos |
| `constants/` | Valores constantes | Todo proyecto |
| `types/` o `interfaces/` | Definiciones de tipos/interfaces | TypeScript |
| `assets/` | Recursos estaticos (imagenes, fuentes, iconos) | Frontend |
| `public/` o `static/` | Archivos publicos servidos directamente | Frontend (React, Vue) |
| `styles/` o `css/` o `scss/` | Hojas de estilo | Frontend |
| `themes/` | Temas visuales de la aplicacion | Frontend, CMS |
| `locales/` o `i18n/` o `translations/` | Archivos de traduccion / internacionalizacion | Aplicaciones multi-idioma |
| `plugins/` | Extensiones o plugins | CMS, herramientas extensibles |
| `workers/` | Procesos en segundo plano / workers | Aplicaciones con tareas async |

### Testing

| Carpeta | Proposito | Aparece en |
|---------|-----------|------------|
| `tests/` o `test/` | Tests unitarios y de integracion | Todo proyecto serio |
| `__tests__/` | Tests co-localizados (convencion Jest) | JavaScript/TypeScript |
| `spec/` | Especificaciones de tests (convencion Ruby, Angular) | Ruby, Angular |
| `e2e/` | Tests end-to-end | Proyectos con E2E testing |
| `cypress/` | Tests y configuracion de Cypress | Proyectos con Cypress |
| `playwright/` | Tests y configuracion de Playwright | Proyectos con Playwright |
| `features/` | Archivos Gherkin (BDD: Cucumber, Behave, pytest-bdd) | Proyectos con BDD |
| `fixtures/` o `__fixtures__/` | Datos de prueba | Tests |
| `mocks/` o `__mocks__/` | Mocks de modulos | Tests (JavaScript) |
| `conftest.py` | Fixtures compartidas de pytest | Tests Python |
| `coverage/` | Reportes de cobertura de codigo | NUNCA se commitea |
| `.nyc_output/` | Reportes de cobertura (Istanbul/NYC) | NUNCA se commitea |

### Documentacion

| Carpeta | Proposito | Se commitea? |
|---------|-----------|--------------|
| `docs/` o `documentation/` | Documentacion del proyecto | SI |
| `docs/adr/` | Architecture Decision Records | SI |
| `docs/api/` | Documentacion de API | SI |
| `docs/diagrams/` | Diagramas de arquitectura (PlantUML, Mermaid) | SI |
| `examples/` | Ejemplos de uso del proyecto | SI |
| `.readthedocs.yaml` | Configuracion de ReadTheDocs | SI |
| `mkdocs.yml` | Configuracion de MkDocs | SI |
| `_build/` | Output de Sphinx/MkDocs (HTML generado) | NO |
| `site/` | Documentacion generada (MkDocs) | NO |

### Datos y Machine Learning

| Carpeta | Proposito | Se commitea? |
|---------|-----------|--------------|
| `data/` | Datos del proyecto | Depende (DVC o LFS para grandes) |
| `data/raw/` | Datos originales inmutables | Con DVC/Git LFS |
| `data/processed/` | Datos procesados | Con DVC/Git LFS |
| `data/interim/` | Datos intermedios | Con DVC/Git LFS |
| `data/external/` | Datos de fuentes externas | Con DVC/Git LFS |
| `datasets/` | Datasets de entrenamiento/evaluacion | Con DVC/Git LFS |
| `models/` | Modelos entrenados serializados | Con DVC/Git LFS |
| `checkpoints/` | Checkpoints de entrenamiento de ML | NO (muy grandes) |
| `notebooks/` | Jupyter Notebooks | SI (sin outputs grandes) |
| `runs/` | Logs de entrenamiento (TensorBoard, etc.) | NO |
| `mlruns/` | Registros de MLflow | NO |
| `wandb/` | Cache de Weights & Biases | NO |
| `embeddings/` | Embeddings precomputados | Con DVC/Git LFS |
| `vector_store/` o `chroma_db/` | Indices de vector store | NO (local) |
| `features/` | Definiciones y logica de feature engineering | SI |
| `evals/` o `evaluations/` | Datasets y scripts de evaluacion de LLMs | SI |
| `experiments/` | Resultados de experimentos de ML | SI (metadatos, no binarios) |
| `reports/` | Reportes generados y figuras | NO (generados) |
| `figures/` | Graficos y visualizaciones | NO (generados) |
| `prompts/` | Templates de prompts versionados | SI |
| `.cache/` o `~/.cache/` | Cache de datasets HuggingFace | NO |
| `.huggingface/` o `huggingface/` | Cache de modelos HuggingFace | NO |

### Infraestructura y Despliegue

| Carpeta | Proposito | Se commitea? |
|---------|-----------|--------------|
| `docker/` | Dockerfiles organizados | SI |
| `deployment/` | Configuraciones de despliegue | SI |
| `deployment/kubernetes/` o `k8s/` | Manifiestos de Kubernetes | SI |
| `deployment/helm/` o `charts/` | Charts de Helm | SI |
| `deployment/terraform/` o `terraform/` | Configuraciones de Terraform | SI |
| `deployment/tofu/` | Configuraciones de OpenTofu | SI |
| `deployment/ansible/` | Playbooks de Ansible | SI |
| `deployment/serverless/` | Configuraciones serverless | SI |
| `infrastructure/` o `infra/` | IaC del proyecto | SI |
| `iac/` | Infraestructura como codigo | SI |
| `.terraform/` | Modulos y plugins de Terraform (generado) | NO |
| `volumes/` | Volumenes Docker persistentes | NO (local) |
| `certs/` o `ssl/` | Certificados SSL/TLS locales | NO (solo en .gitignore) |
| `logs/` | Archivos de log | NO (generados) |
| `monitoring/` | Configuraciones de monitoreo (Grafana, Prometheus) | SI |
| `dashboards/` | Dashboards de monitoreo (JSON de Grafana) | SI |
| `alerts/` | Reglas de alertas | SI |
| `runbooks/` | Procedimientos operativos | SI |

### Build y Distribucion

| Carpeta | Proposito | Se commitea? |
|---------|-----------|--------------|
| `dist/` o `build/` | Artefactos de build listos para distribucion | NUNCA |
| `.next/` | Build de Next.js | NUNCA |
| `out/` | Output de export estatica (Next.js, SvelteKit) | NUNCA |
| `.nuxt/` | Build de Nuxt | NUNCA |
| `.output/` | Output de Nuxt 3 | NUNCA |
| `.svelte-kit/` | Build de SvelteKit | NUNCA |
| `.turbo/` | Cache de Turborepo | NUNCA |
| `storybook-static/` | Build de Storybook | NUNCA |
| `public/build/` | Assets compilados (Remix, etc.) | NUNCA |
| `eggs/` | Paquetes Python (legacy) | NUNCA |
| `*.egg-info/` | Metadatos de paquete Python | NUNCA |
| `wheelhouse/` | Wheels compilados | NO |

### Scripts y Utilidades

| Carpeta | Proposito | Aparece en |
|---------|-----------|------------|
| `scripts/` o `bin/` | Scripts de automatizacion, deploy, utilidades | Todo proyecto |
| `tools/` | Herramientas auxiliares del proyecto | Proyectos grandes |
| `tasks/` | Tareas automatizadas (Taskfile, Just) | Proyectos con task runners |
| `.husky/` | Git hooks (husky) | Proyectos con husky |
| `.githooks/` | Git hooks personalizados | Cualquier proyecto Git |

### Configuracion y Secretos

| Carpeta | Proposito | Se commitea? |
|---------|-----------|--------------|
| `config/` o `conf/` o `cfg/` | Archivos de configuracion de la aplicacion | SI (sin secretos) |
| `configs/` | Configuraciones de experimentos ML | SI |
| `environments/` | Definiciones de entorno (Docker, Conda) | SI |
| `secrets/` | Secretos locales | NUNCA |
| `.streamlit/` | Configuracion de Streamlit | SI |
| `.github/` (raiz) | Configuraciones de GitHub | SI |
| `devcontainer/` o `.devcontainer.json` | Configuracion de Dev Containers (VS Code) | SI |
| `.gitpod.yml` | Configuracion de Gitpod | SI |

### Runtime y Cache de Herramientas

| Carpeta | Proposito | Se commitea? |
|---------|-----------|--------------|
| `.pytest_cache/` | Cache de pytest | NUNCA |
| `.mypy_cache/` | Cache de mypy | NUNCA |
| `.ruff_cache/` | Cache de Ruff | NUNCA |
| `.pyre/` | Servidor Pyre | NUNCA |
| `.eslintcache/` | Cache de ESLint | NUNCA |
| `.prettierrc` (cache) | Cache de Prettier | NUNCA |
| `.parcel-cache/` | Cache de Parcel bundler | NUNCA |
| `.cache/` | Cache generico | NUNCA |
| `tmp/` o `temp/` | Archivos temporales | NUNCA |
| `.direnv/` | Cache de direnv | NUNCA |

### Monorepo Tools

| Carpeta | Proposito | Se commitea? |
|---------|-----------|--------------|
| `packages/` | Paquetes compartidos en monorepo | SI |
| `apps/` | Aplicaciones en monorepo | SI |
| `libs/` | Librerias compartidas (Nx) | SI |
| `.nx/` | Cache de Nx | NUNCA |
| `.lerna/` | Metadatos de Lerna | NO |

---

## Archivos Universales

### Raiz del Proyecto: Gestion del Repositorio

| Archivo | Proposito | Requerido? |
|---------|-----------|------------|
| `.gitignore` | Exclusiones de Git | SI - obligatorio |
| `.gitattributes` | Atributos de Git (finales de linea, LFS) | Opcional |
| `.gitkeep` | Mantener carpetas vacias en Git | Hack comun |
| `README.md` | Documentacion principal del proyecto | SI - obligatorio |
| `LICENSE` o `LICENSE.md` o `LICENSE.txt` | Licencia del software | SI - muy recomendado |
| `CONTRIBUTING.md` | Guia para contribuidores | Recomendado |
| `CODE_OF_CONDUCT.md` | Codigo de conducta | Recomendado |
| `SECURITY.md` | Politica de seguridad | Recomendado |
| `CHANGELOG.md` | Registro de cambios | Recomendado |
| `SUPPORT.md` | Informacion de soporte | Opcional |
| `FUNDING.yml` | Informacion de financiamiento (GitHub Sponsors) | Opcional |
| `CODEOWNERS` | Propietarios de codigo por path (GitHub) | Opcional |
| `ACKNOWLEDGMENTS.md` o `AUTHORS.md` | Creditos y autores | Opcional |
| `.mailmap` | Mapeo de nombres para Git | Opcional |
| `.editorconfig` | Configuracion de estilo entre editores | Recomendado |

### Raiz del Proyecto: Reglas y Contexto para IA

| Archivo | Proposito | Aparece en |
|---------|-----------|------------|
| `RULES.md` | Reglas de desarrollo del proyecto | Proyectos con IA |
| `CONTEXTO.md` | Contexto del proyecto para asistentes IA | Proyectos con IA |
| `AGENTS.md` | Instrucciones para agentes de IA (OpenCode, Claude Code) | Proyectos con IA |
| `CLAUDE.md` | Instrucciones especificas para Claude Code | Proyectos con Claude Code |
| `.cursorrules` o `.cursor/rules` | Reglas para Cursor AI | Proyectos con Cursor |
| `.github/copilot-instructions.md` | Instrucciones para GitHub Copilot | Proyectos con Copilot |
| `.windsurfrules` | Reglas para Windsurf | Proyectos con Windsurf |
| `.aider.conf.yml` | Configuracion para Aider | Proyectos con Aider |

### Raiz del Proyecto: Lenguajes y Frameworks

| Archivo | Lenguaje/Ecosistema | Proposito |
|---------|---------------------|-----------|
| `pyproject.toml` | Python | Dependencias, build, config herramientas |
| `setup.py` | Python legacy | Script de instalacion (obsoleto, usar pyproject.toml) |
| `setup.cfg` | Python legacy | Configuracion declarativa (obsoleto) |
| `requirements.txt` | Python | Dependencias pip |
| `requirements-dev.txt` | Python | Dependencias de desarrollo |
| `Pipfile` / `Pipfile.lock` | Python (Pipenv) | Dependencias con lock |
| `environment.yml` | Python (Conda) | Entorno Conda |
| `package.json` | Node.js/JavaScript | Dependencias, scripts, metadata |
| `package-lock.json` | Node.js (npm) | Lock de dependencias exactas |
| `yarn.lock` | Node.js (Yarn) | Lock de dependencias exactas |
| `pnpm-lock.yaml` | Node.js (pnpm) | Lock de dependencias exactas |
| `bun.lockb` | Node.js (Bun) | Lock de dependencias (binario) |
| `tsconfig.json` | TypeScript | Configuracion del compilador TypeScript |
| `jsconfig.json` | JavaScript | Configuracion de paths (VS Code) |
| `Cargo.toml` | Rust | Dependencias, metadata, build |
| `Cargo.lock` | Rust | Lock de dependencias exactas |
| `go.mod` | Go | Modulo y dependencias |
| `go.sum` | Go | Checksums de dependencias |
| `Gemfile` / `Gemfile.lock` | Ruby | Dependencias con lock |
| `composer.json` / `composer.lock` | PHP | Dependencias con lock |
| `pom.xml` | Java (Maven) | Build, dependencias |
| `build.gradle` / `build.gradle.kts` | Java/Kotlin (Gradle) | Build, dependencias |
| `settings.gradle` | Java/Kotlin (Gradle) | Configuracion de proyecto multi-modulo |
| `pubspec.yaml` | Dart/Flutter | Dependencias |
| `mix.exs` | Elixir | Build, dependencias |
| `rebar.config` | Erlang | Build, dependencias |
| `CMakeLists.txt` | C/C++ (CMake) | Configuracion de build |
| `Makefile` | C/C++/General | Tareas de build automatizadas |
| `meson.build` | C/C++ (Meson) | Sistema de build |

### Raiz del Proyecto: Linters y Formateadores

| Archivo | Herramienta | Proposito |
|---------|------------|-----------|
| `.prettierrc` / `.prettierrc.json` / `prettier.config.js` | Prettier | Formateo de codigo |
| `.prettierignore` | Prettier | Archivos a ignorar |
| `eslint.config.js` / `.eslintrc.js` / `.eslintrc.json` | ESLint | Linting JavaScript/TypeScript |
| `.eslintignore` | ESLint | Archivos a ignorar |
| `.stylelintrc` | Stylelint | Linting CSS/SCSS |
| `.markdownlint.json` | markdownlint | Linting Markdown |
| `.yamllint` | yamllint | Linting YAML |
| `.shellcheckrc` | ShellCheck | Linting shell scripts |
| `.vale.ini` | Vale | Linting de prosa/documentacion |
| `.codespellrc` | codespell | Corrector ortografico |
| `.hadolint.yaml` | Hadolint | Linting Dockerfiles |
| `.browserslistrc` | Browserslist | Configuracion de navegadores soportados |
| `.npmignore` | npm | Archivos a excluir del paquete npm |
| `.dockerignore` | Docker | Archivos a excluir del contexto de build |
| `.gitmessage` | Git | Plantilla de mensaje de commit |

### Raiz del Proyecto: Build y Bundlers

| Archivo | Herramienta | Proposito |
|---------|------------|-----------|
| `vite.config.ts` / `vite.config.js` | Vite | Configuracion de Vite |
| `next.config.js` / `next.config.ts` | Next.js | Configuracion de Next.js |
| `webpack.config.js` | Webpack | Configuracion de Webpack |
| `rollup.config.js` | Rollup | Configuracion de Rollup |
| `esbuild.config.js` | esbuild | Configuracion de esbuild |
| `turbo.json` | Turborepo | Pipeline de tareas |
| `nx.json` | Nx | Configuracion de Nx |
| `lerna.json` | Lerna | Configuracion de monorepo |
| `babel.config.js` / `.babelrc` | Babel | Transpilacion JavaScript |
| `postcss.config.js` | PostCSS | Procesamiento CSS |
| `tailwind.config.js` / `tailwind.config.ts` | Tailwind CSS | Configuracion de Tailwind |
| `astro.config.mjs` | Astro | Configuracion de Astro |
| `svelte.config.js` | Svelte | Configuracion de Svelte/SvelteKit |
| `nuxt.config.ts` | Nuxt | Configuracion de Nuxt |
| `remix.config.js` | Remix | Configuracion de Remix |
| `gatsby-config.js` | Gatsby | Configuracion de Gatsby |

### Raiz del Proyecto: Testing y Calidad

| Archivo | Herramienta | Proposito |
|---------|------------|-----------|
| `pytest.ini` / `tox.ini` / `pyproject.toml [tool.pytest]` | pytest | Configuracion de pytest |
| `jest.config.ts` / `jest.config.js` | Jest | Configuracion de Jest |
| `vitest.config.ts` | Vitest | Configuracion de Vitest |
| `playwright.config.ts` | Playwright | Configuracion de Playwright |
| `cypress.config.ts` | Cypress | Configuracion de Cypress |
| `.pre-commit-config.yaml` | pre-commit | Git hooks automaticos |
| `.commitlintrc.js` | commitlint | Validacion de mensajes de commit |
| `conftest.py` | pytest | Fixtures compartidas |
| `noxfile.py` | Nox | Automatizacion de testing multi-entorno |
| `tox.ini` | Tox | Testing multi-entorno Python |
| `sonar-project.properties` | SonarQube | Analisis de calidad de codigo |
| `.snyk` | Snyk | Escaneo de vulnerabilidades |
| `.trivyignore` | Trivy | Exclusiones de escaneo de seguridad |

### Raiz del Proyecto: Despliegue y Hosting

| Archivo | Plataforma | Proposito |
|---------|-----------|-----------|
| `Dockerfile` | Docker | Definicion de imagen de contenedor |
| `docker-compose.yml` / `docker-compose.yaml` | Docker | Orquestacion de servicios |
| `docker-compose.override.yml` | Docker | Overrides locales |
| `Caddyfile` | Caddy | Configuracion de reverse proxy |
| `nginx.conf` | Nginx | Configuracion de servidor web |
| `Procfile` | Heroku | Definicion de procesos |
| `app.json` | Heroku | Metadata de aplicacion |
| `netlify.toml` | Netlify | Configuracion de despliegue |
| `vercel.json` | Vercel | Configuracion de despliegue |
| `fly.toml` | Fly.io | Configuracion de despliegue |
| `render.yaml` | Render | Configuracion de despliegue |
| `railway.json` / `railway.toml` | Railway | Configuracion de despliegue |
| `wrangler.toml` / `wrangler.json` | Cloudflare Workers | Configuracion de workers |
| `serverless.yml` | Serverless Framework | Configuracion serverless |
| `samconfig.toml` | AWS SAM | Configuracion serverless |
| `cdk.json` | AWS CDK | Configuracion de infraestructura |
| `terraform.tfvars` | Terraform | Variables de Terraform |
| `pulumi.yaml` / `Pulumi.yaml` | Pulumi | Proyecto de infraestructura |
| `Pulumi.stack.yaml` | Pulumi | Configuracion de stack |
| `ansible.cfg` | Ansible | Configuracion de Ansible |
| `helmfile.yaml` | Helmfile | Orquestacion de charts Helm |
| `.helmignore` | Helm | Exclusiones de chart |

### Raiz del Proyecto: Variables de Entorno y Secretos

| Archivo | Se commitea? | Proposito |
|---------|--------------|------------|
| `.env` | NUNCA | Variables de entorno locales |
| `.env.example` | SI | Plantilla de variables requeridas |
| `.env.development` | NO | Variables de desarrollo |
| `.env.staging` | NO | Variables de staging |
| `.env.production` | NO | Variables de produccion |
| `.env.local` | NUNCA | Variables locales (sobrescribe .env) |
| `.env.test` | NO | Variables de testing |
| `.envrc` | NO | Variables de entorno direnv |
| `service-account.json` | NUNCA | Credenciales de Google Cloud |
| `credentials.json` | NUNCA | Credenciales genericas |

### Raiz del Proyecto: Configuracion del Entorno de Desarrollo

| Archivo | Herramienta | Proposito |
|---------|------------|-----------|
| `.nvmrc` | nvm (Node Version Manager) | Version de Node.js requerida |
| `.node-version` | nodenv / fnm | Version de Node.js requerida |
| `.python-version` | pyenv | Version de Python requerida |
| `.ruby-version` | rbenv / rvm | Version de Ruby requerida |
| `.terraform-version` | tfenv | Version de Terraform requerida |
| `.tool-versions` | asdf | Versiones de multiples herramientas |
| `.java-version` | jenv | Version de Java requerida |
| `.sdkmanrc` | SDKMAN | Versiones de SDKs |
| `devcontainer.json` | VS Code DevContainers | Configuracion de contenedor de desarrollo |
| `flake.nix` / `flake.lock` | Nix | Entorno de desarrollo reproducible |
| `shell.nix` | Nix | Shell de desarrollo |
| `.air.toml` | Air (Go) | Hot reload para Go |
| `Taskfile.yml` | Task | Task runner (alternativa a Makefile) |
| `justfile` | Just | Command runner (alternativa a Makefile) |

### Raiz del Proyecto: Monitoreo y Observabilidad

| Archivo | Herramienta | Proposito |
|---------|------------|-----------|
| `sentry.properties` | Sentry | Configuracion de Sentry |
| `opentelemetry.yml` | OpenTelemetry | Configuracion de telemetria |
| `log_config.yaml` | Varios | Configuracion de logging |

### Raiz del Proyecto: DVC y ML

| Archivo | Herramienta | Proposito |
|---------|------------|-----------|
| `dvc.yaml` | DVC | Pipeline de datos/ML |
| `dvc.lock` | DVC | Lock de estado del pipeline |
| `params.yaml` | DVC | Parametros del pipeline |
| `metrics.json` | DVC | Metricas generadas |
| `MLproject` | MLflow | Definicion de proyecto MLflow |
| `conda.yaml` | MLflow | Entorno Conda para MLflow |
| `Dockerfile.ml` | Proyectos ML | Dockerfile para entrenamiento |

### Otros Archivos de Raiz

| Archivo | Proposito |
|---------|-----------|
| `Makefile` | Tareas automatizadas (build, test, deploy, clean) |
| `.env` | Variables de entorno locales (NUNCA committeada) |
| `NOTICE` | Avisos de terceros (requerido por Apache 2.0 en algunos casos) |
| `CITATION.cff` | Informacion de citacion academica |
| `.all-contributorsrc` | Configuracion de All Contributors |
| `renovate.json` | Configuracion de Renovate (actualizacion de dependencias) |
| `.github/dependabot.yml` | Configuracion de Dependabot |
| `.release-it.json` | Configuracion de release-it |
| `.cz.toml` / `.czrc` | Configuracion de Commitizen |
| `pyoxidizer.toml` | PyOxidizer (empaquetado Python) |
| `briefcase.toml` | Briefcase (apps nativas Python) |
| `manifest.json` | Extensiones de navegador, PWA |
| `robots.txt` | Exclusiones de crawlers web |
| `sitemap.xml` | Mapa del sitio |
| `favicon.ico` | Icono del sitio |
| `.htaccess` | Configuracion de Apache HTTP Server |
| `_redirects` | Redirecciones (Netlify) |
| `_headers` | Cabeceras HTTP (Netlify) |
| `.slugignore` | Archivos a excluir en Heroku |

---

## Archivos Especificos de IA y LLMs

| Archivo | Proposito |
|---------|-----------|
| `prompts/*.txt` / `prompts/*.yaml` | Prompts versionados como codigo |
| `evals/*.json` / `evals/*.yaml` | Datasets de evaluacion de LLMs |
| `guardrails/config.yml` | Configuracion de guardrails de contenido |
| `model_card.md` | Documentacion del modelo (Hugging Face) |
| `inference_config.yaml` | Configuracion de inferencia (temperatura, max_tokens) |
| `finetuning_config.yaml` | Configuracion de fine-tuning |
| `.openai_cache/` | Cache de respuestas de OpenAI (NO commitear) |

---

## Resumen: Que SIEMPRE Debe Estar en un Proyecto

### Minimo Absoluto
```
mi-proyecto/
├── .gitignore          # Obligatorio
├── README.md           # Obligatorio
├── LICENSE             # Muy recomendado
└── [codigo fuente]     # Obviamente
```

### Proyecto Profesional
```
mi-proyecto/
├── .gitignore
├── .editorconfig
├── .pre-commit-config.yaml
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── SECURITY.md
├── CHANGELOG.md
├── [manifiesto de dependencias]
├── src/ o app/
├── tests/
├── docs/
├── .github/workflows/ci.yml
└── Dockerfile (opcional)
```

### Proyecto con IA / Vibe Coding
Todo lo de "Proyecto Profesional" mas:
```
├── RULES.md
├── CONTEXTO.md
├── AGENTS.md
├── .cursor/rules (o .cursorrules)
├── .env.example
├── prompts/
└── evals/
```

---

## Referencias Cruzadas

- [.gitignore Definitivo](./gitignore-definitivo.md) -- exclusiones por tipo de proyecto
- [Entornos Virtuales](./entornos-virtuales.md) -- .venv, .env, gestion de entornos
- [Archivos IA Esenciales](./archivos-ia-esenciales.md) -- RULES.md, CONTEXTO.md, AGENTS.md
- [Estructura de Carpetas](./estructura-carpetas.md) -- principios de organizacion
- [Glosario](./glosario.md) -- definiciones de todos los terminos

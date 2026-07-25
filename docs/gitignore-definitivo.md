# .gitignore Definitivo: La Guia Completa por Tipo de Proyecto

El archivo `.gitignore` es la primera linea de defensa contra commits accidentales de archivos que no deben estar en el repositorio. Esta guia cubre configuraciones completas para cada tipo de proyecto de ingenieria de IA.

---

## Principios Fundamentales del .gitignore

### Que SIEMPRE se ignora

1. **Secretos y credenciales**: `.env`, `.env.local`, `*.pem`, `*.key`, `credentials.json`
2. **Entornos virtuales**: `.venv/`, `venv/`, `env/`, `node_modules/`
3. **Artefactos de build**: `dist/`, `build/`, `*.pyc`, `.next/`, `target/`
4. **Archivos de IDE**: `.vscode/`, `.idea/`, `*.swp`, `*.swo`, `.DS_Store`
5. **Cache y temporales**: `__pycache__/`, `.pytest_cache/`, `.mypy_cache/`, `*.log`
6. **Datos y modelos grandes**: datasets, checkpoints, modelos serializados (a menos que uses DVC/Git LFS)

### Que NUNCA se ignora

1. `README.md`, `CONTEXTO.md`, `RULES.md`, `AGENTS.md`
2. `pyproject.toml`, `requirements.txt`, `package.json`, `Cargo.toml`
3. `.gitignore` (el propio archivo)
4. `.env.example` (el de ejemplo, sin secretos reales)
5. Workflows de CI/CD (`.github/workflows/*.yml`)
6. Archivos de configuracion del proyecto (`.pre-commit-config.yaml`, `Dockerfile`)

---

## 1. .gitignore para Python (Proyecto General)

```gitignore
# === Entornos Virtuales ===
.venv/
venv/
env/
ENV/
env.bak/
venv.bak/

# === Variables de Entorno y Secretos ===
.env
.env.local
.env.*.local
*.pem
*.key
credentials.json
service-account.json
secrets/

# === Python Cache y Compilados ===
__pycache__/
*.py[cod]
*$py.class
*.so
*.egg
*.egg-info/
dist/
build/
eggs/
*.whl

# === Testing y Coverage ===
.pytest_cache/
.coverage
htmlcov/
.coverage.*
*.cover

# === Type Checking ===
.mypy_cache/
.ruff_cache/
.pyre/

# === Jupyter Notebooks ===
.ipynb_checkpoints/
*.ipynb_checkpoints

# === IDE y Editor ===
.vscode/
.idea/
*.swp
*.swo
*~
.DS_Store
Thumbs.db
Desktop.ini

# === Logs ===
*.log
logs/

# === Datos y Modelos (si no usas DVC/Git LFS) ===
data/raw/*
data/interim/*
data/processed/*
models/*
!data/.gitkeep
!models/.gitkeep

# === Configuracion de Herramientas ===
.envrc
.direnv/

# === Vagrant / Docker (opcional) ===
.vagrant/
```

---

## 2. .gitignore para Ciencia de Datos y Machine Learning

```gitignore
# === Entornos Virtuales ===
.venv/
venv/
env/
conda-env/

# === Secretos ===
.env
.env.local
*.pem
secrets/
credentials.json

# === Python ===
__pycache__/
*.py[cod]
*.egg-info/
dist/
build/

# === Type Checking y Linting ===
.mypy_cache/
.ruff_cache/

# === Jupyter ===
.ipynb_checkpoints/
*.ipynb_checkpoints

# === Datos (si NO usas DVC) ===
data/raw/*
data/interim/*
data/processed/*
data/external/*
!data/raw/.gitkeep
!data/interim/.gitkeep
!data/processed/.gitkeep
!data/external/.gitkeep

# === Modelos Entrenados (si NO usas DVC) ===
models/*
!models/.gitkeep
*.pkl
*.joblib
*.h5
*.keras
*.pt
*.pth
*.onnx
checkpoints/
runs/

# === Datasets Grandes ===
*.csv  # Descomenta si tus CSVs son grandes
# *.parquet
# *.arrow

# === Outputs y Reportes Generados ===
reports/*
!reports/.gitkeep
outputs/
figures/

# === Logs de Entrenamiento ===
*.log
lightning_logs/
mlruns/
wandb/

# === Cache de Datasets ===
.cache/
datasets/
huggingface/

# === Tools ===
.dvc/cache/
.mlflow/
.neptune/

# === IDE ===
.vscode/
.idea/
.DS_Store
Thumbs.db
```

---

## 3. .gitignore para Node.js / TypeScript / Frontend

```gitignore
# === Dependencias ===
node_modules/
.pnpm-store/

# === Secretos ===
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# === Build y Output ===
dist/
build/
.next/
out/
.nuxt/
.output/
.svelte-kit/

# === TypeScript ===
*.tsbuildinfo
tsconfig.tsbuildinfo

# === Testing y Coverage ===
coverage/
.nyc_output/

# === Linting y Cache ===
.eslintcache
.prettierrc
.stylelintcache

# === Logs ===
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

# === IDE y Editor ===
.vscode/
.idea/
*.swp
*.swo
*~
.DS_Store
Thumbs.db

# === Turbo (monorepo) ===
.turbo/

# === Runtime ===
.pnpm-debug.log*

# === OS ===
Desktop.ini
```

---

## 4. .gitignore para Proyecto de IA Generativa / LLMs / Agentes

```gitignore
# === Entornos Virtuales ===
.venv/
venv/
env/

# === Secretos (CRITICO: nunca commitear claves de API) ===
.env
.env.local
*.key
*.pem
credentials.json
service-account.json
secrets/
api_keys.txt

# === Python ===
__pycache__/
*.py[cod]
*.egg-info/
dist/
build/

# === Type Checking y Linting ===
.mypy_cache/
.ruff_cache/

# === Modelos Locales (grandes) ===
models/
*.gguf
*.bin
*.safetensors
*.onnx
llama.cpp/models/
checkpoints/
loras/

# === Datos de Fine-tuning ===
training_data/
datasets/
data/raw/*
data/processed/*

# === Logs de Experimentos ===
*.log
mlruns/
wandb/
lightning_logs/

# === Vector Stores y Embeddings ===
chroma_db/
faiss_index/
vector_store/
*.index
embeddings/

# === Caches de LLM ===
.cache/
~/.cache/
huggingface/
openai_cache/

# === Outputs de Evaluacion ===
evals/
evaluation_results/
benchmark_outputs/

# === Jupyter ===
.ipynb_checkpoints/

# === IDE ===
.vscode/
.idea/
.DS_Store
Thumbs.db
```

---

## 5. .gitignore para Proyecto Fullstack (Monorepo)

```gitignore
# === Entornos ===
.venv/
venv/
env/
node_modules/
.pnpm-store/

# === Secretos ===
.env
.env.local
.env.*.local
*.pem
secrets/

# === Python ===
__pycache__/
*.py[cod]
*.egg-info/
dist/
build/

# === TypeScript / Frontend ===
.next/
dist/
build/
*.tsbuildinfo

# === Testing ===
.pytest_cache/
coverage/
.nyc_output/

# === Linting ===
.mypy_cache/
.ruff_cache/
.eslintcache

# === Logs ===
*.log

# === IDE ===
.vscode/
.idea/
.DS_Store
Thumbs.db

# === Turbo (si usas Turborepo) ===
.turbo/

# === Docker ===
.docker/

# === Datos (si aplica) ===
data/raw/*
models/*
!data/raw/.gitkeep
!models/.gitkeep
```

---

## 6. .gitignore para Vibe Coding (Proyecto con IA)

Ademas del `.gitignore` especifico del lenguaje, los proyectos de vibe coding deben incluir:

```gitignore
# === Archivos de IA (NUNCA commitear si tienen datos sensibles) ===
# .aider.chat.history.md  # Conversaciones con Aider
# .aider.suggestions.md   # Sugerencias de Aider

# === Outputs de IA que no deben versionarse ===
*.generated.*
ai_outputs/
llm_responses/

# === Reglas locales de IA (si las hay) ===
# .cursor/rules/local/   # Reglas que solo aplican a tu maquina

# === Cachés de herramientas de IA ===
.openai_cache/
.anthropic_cache/
.llm_cache/
```

---

## 7. Archivos que DEBEN Committearse (Checklist)

Lista de verificacion de lo que SI debe estar en el repositorio:

```
[ ] .gitignore
[ ] README.md
[ ] RULES.md                    (si existe)
[ ] CONTEXTO.md                 (si existe)
[ ] AGENTS.md                   (si existe)
[ ] .cursor/rules               (si existe, reglas compartidas del equipo)
[ ] .github/copilot-instructions.md  (si existe)
[ ] .github/workflows/*.yml     (CI/CD)
[ ] .env.example                (plantilla de variables, SIN secretos)
[ ] pyproject.toml / package.json / Cargo.toml
[ ] requirements.txt (si se usa)
[ ] Dockerfile
[ ] docker-compose.yml
[ ] .pre-commit-config.yaml
[ ] src/                        (todo el codigo fuente)
[ ] tests/                      (todos los tests)
[ ] docs/                       (documentacion)
[ ] LICENSE
```

---

## Referencias Cruzadas

- [Entornos Virtuales](./entornos-virtuales.md) -- .venv, .env, gestion de entornos
- [Archivos IA Esenciales](./archivos-ia-esenciales.md) -- RULES.md, CONTEXTO.md, AGENTS.md
- [Guia de Vibe Coding](./guia-vibe-coding.md) -- flujos de trabajo con IA

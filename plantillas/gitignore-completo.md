# .gitignore Completo -- Plantilla Unificada

Esta es una plantilla de .gitignore exhaustiva que cubre practicamente todos los casos. Copiar las secciones que apliquen a tu proyecto.

---

```gitignore
# =============================================================================
# .gitignore -- Plantilla Unificada
# =============================================================================
# Copiar solo las secciones que aplican a tu proyecto.
# Regla general: si es generado, cache, secreto o especifico de maquina, va aqui.
# =============================================================================

# === Secretos y Credenciales (CRITICO) ===
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
.env.*.local
*.pem
*.key
*.p12
*.pfx
credentials.json
service-account.json
secrets/
api_keys.txt
config/secrets.yml

# === Entornos Virtuales Python ===
.venv/
venv/
env/
ENV/
env.bak/
venv.bak/
.python-version
.conda/

# === Dependencias Node.js ===
node_modules/
.pnpm-store/
.yarn/

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
pip-wheel-metadata/
.pip-cache/

# === TypeScript / Frontend Build ===
.next/
out/
.nuxt/
.output/
.svelte-kit/
dist/
build/
*.tsbuildinfo
tsconfig.tsbuildinfo

# === Testing y Coverage ===
.pytest_cache/
.coverage
htmlcov/
.coverage.*
*.cover
coverage/
.nyc_output/
.coverage

# === Type Checking y Linting ===
.mypy_cache/
.ruff_cache/
.dmypy.json
.pyre/
.pytype/
.eslintcache
.stylelintcache
.prettierrc

# === Jupyter Notebooks ===
.ipynb_checkpoints/
*.ipynb_checkpoints

# === IDE y Editores ===
.vscode/
.idea/
*.swp
*.swo
*~
.project
.classpath
.settings/
*.sublime-workspace
*.sublime-project

# === Sistema Operativo ===
.DS_Store
.AppleDouble
.LSOverride
Thumbs.db
Desktop.ini
ehthumbs.db
$RECYCLE.BIN/

# === Logs ===
*.log
logs/
log/
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*
.pnpm-debug.log*

# === Bases de Datos Locales ===
*.sqlite
*.sqlite3
*.db
*.sql
# !migrations/*.sql  # Descomentar si tienes migraciones SQL

# === Docker ===
.docker/
docker-compose.override.yml

# === Monorepo Tools ===
.turbo/
.nx/
.lerna/
.tmp/

# === Datos y Modelos (si no usas DVC/Git LFS) ===
data/raw/*
data/interim/*
data/processed/*
data/external/*
models/*
*.pkl
*.joblib
*.h5
*.keras
*.pt
*.pth
*.onnx
*.gguf
*.bin
*.safetensors
checkpoints/
runs/
!data/raw/.gitkeep
!data/interim/.gitkeep
!data/processed/.gitkeep
!data/external/.gitkeep
!models/.gitkeep

# === Datasets (archivos grandes) ===
*.csv
*.parquet
*.arrow
# !sample.csv  # Descomentar para incluir datasets de ejemplo

# === ML / Experiment Tracking ===
mlruns/
wandb/
lightning_logs/
.neptune/
.dvc/cache/
.mlflow/

# === LLM / Vector Store ===
chroma_db/
faiss_index/
vector_store/
*.index
embeddings/

# === Outputs Generados ===
reports/*
figures/
outputs/
!reports/.gitkeep

# === Configuracion Local ===
.envrc
.direnv/
*.local
*.override.yml

# === Caches de LLM y Datasets ===
.cache/
~/.cache/
huggingface/
datasets/
openai_cache/

# === Herramientas de IA ===
.aider.chat.history.md
.aider.suggestions.md
.openai_cache/
.anthropic_cache/
.llm_cache/

# === Vagrant ===
.vagrant/

# === Terraform ===
*.tfstate
*.tfstate.*
.terraform/
.terraform.lock.hcl
override.tf
override.tf.json
*_override.tf
*_override.tf.json
crash.log
crash.*.log

# === Archivos de Respaldo ===
*.bak
*.backup
*.old
*.orig
```

---

## Como Usar

1. Copiar el bloque completo a `.gitignore`
2. Eliminar secciones que no apliquen a tu proyecto
3. Agregar patrones especificos de tu proyecto si es necesario
4. Para proyectos con datos grandes, considerar DVC o Git LFS en lugar de gitignore para datos (asi los versionas)

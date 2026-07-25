# Ejemplo: Proyecto de Fine-Tuning de LLM

Ejemplo de estructura de repositorio para un proyecto de fine-tuning de modelos de lenguaje. Cubre el ciclo completo: preparacion de datos, entrenamiento, evaluacion, registro y despliegue del modelo fine-tuned.

Stack: Python 3.12, OpenAI Fine-Tuning API, Hugging Face (datasets, transformers), Weights & Biases (tracking), DVC (datos), MLflow (modelos).

---

## Arbol de Carpetas Completo

```bash
fine-tuning-llm/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── validate-dataset.yml
│   │   ├── fine-tune.yml
│   │   └── eval-model.yml
│   └── copilot-instructions.md
├── .cursor/
│   └── rules
├── .dvc/
│   └── config
├── .gitignore
├── .dvcignore
├── .env.example
├── .pre-commit-config.yaml
├── README.md
├── RULES.md
├── CONTEXTO.md
├── AGENTS.md
├── pyproject.toml
├── Dockerfile
├── Makefile
├── src/
│   ├── __init__.py
│   ├── config.py                  # Pydantic Settings
│   ├── data/                      # Preparacion de datos
│   │   ├── __init__.py
│   │   ├── collector.py           # Recoleccion de datos de fuentes
│   │   ├── cleaner.py             # Limpieza y normalizacion
│   │   ├── formatter.py           # Formateo al schema del proveedor
│   │   ├── splitter.py            # Train/val/test split
│   │   └── validator.py           # Validacion de schema y calidad
│   ├── fine_tuning/               # Fine-tuning
│   │   ├── __init__.py
│   │   ├── providers/
│   │   │   ├── __init__.py
│   │   │   ├── base.py            # Interfaz comun
│   │   │   ├── openai_ft.py       # OpenAI Fine-Tuning API
│   │   │   ├── huggingface_ft.py  # Hugging Face (LoRA, QLoRA)
│   │   │   └── together_ft.py     # Together AI
│   │   ├── job_manager.py         # Gestion de jobs de FT
│   │   └── hyperparameters.py     # Configuraciones de HP
│   ├── evaluation/                # Evaluacion
│   │   ├── __init__.py
│   │   ├── metrics/
│   │   │   ├── __init__.py
│   │   │   ├── accuracy.py
│   │   │   ├── quality.py         # Evaluacion LLM-as-judge
│   │   │   ├── safety.py          # Toxicity, bias
│   │   │   └── format_compliance.py
│   │   ├── runner.py
│   │   └── reporter.py            # Generacion de reportes
│   ├── deployment/                # Despliegue del modelo
│   │   ├── __init__.py
│   │   ├── registry.py            # Registro en MLflow / Hugging Face Hub
│   │   └── deployer.py            # Despliegue a produccion
│   ├── prompts/                   # Prompts para evaluacion
│   │   ├── __init__.py
│   │   ├── manager.py
│   │   └── templates/
│   │       └── judge/
│   │           └── quality_judge.txt
│   └── tracking/                  # Experiment tracking
│       ├── __init__.py
│       └── wandb_logger.py
├── tests/
│   ├── conftest.py
│   ├── unit/
│   │   ├── test_data/
│   │   ├── test_evaluation/
│   │   └── test_deployment/
│   └── integration/
│       └── test_pipeline_e2e.py
├── configs/                       # Configuraciones de experimentos
│   ├── base.yaml
│   ├── lora_qwen_7b.yaml
│   └── full_gpt_4o_mini.yaml
├── data/
│   ├── raw/                       # Datos originales (DVC)
│   │   ├── conversations.jsonl.dvc
│   │   └── instructions.jsonl.dvc
│   ├── processed/                 # Datos procesados
│   │   ├── train.jsonl
│   │   ├── val.jsonl
│   │   └── test.jsonl
│   └── README.md
├── evals/                         # Datasets de evaluacion
│   ├── test_suites/
│   │   ├── held_out.jsonl
│   │   ├── generalization.jsonl
│   │   └── safety.jsonl
│   └── baselines/
│       └── base_model_results.json
├── experiments/                   # Resultados de experimentos
│   ├── exp_001_baseline/
│   ├── exp_002_lora_r16/
│   └── exp_003_full_ft/
├── models/                        # Punteros a modelos (DVC)
│   └── README.md
├── notebooks/                     # Analisis exploratorio
│   ├── 01_data_exploration.ipynb
│   ├── 02_error_analysis.ipynb
│   └── 03_results_visualization.ipynb
├── scripts/
│   ├── prepare_data.py
│   ├── submit_ft_job.py
│   ├── run_evaluation.py
│   ├── deploy_model.py
│   └── compare_experiments.py
├── docs/
│   ├── data_schema.md
│   ├── evaluation_protocol.md
│   └── experiments/
│       └── exp_001_baseline.md
├── Makefile
└── dvc.yaml                       # Pipeline DVC
```

---

## Pipeline de Fine-Tuning

```yaml
# dvc.yaml
stages:
  prepare_data:
    cmd: python scripts/prepare_data.py
    deps:
      - src/data/
      - data/raw/
    outs:
      - data/processed/

  validate_dataset:
    cmd: python -m src.data.validator --dataset data/processed/train.jsonl
    deps:
      - src/data/validator.py
      - data/processed/train.jsonl

  fine_tune:
    cmd: python scripts/submit_ft_job.py --config configs/base.yaml
    deps:
      - src/fine_tuning/
      - data/processed/train.jsonl
      - data/processed/val.jsonl
    outs:
      - models/
    metrics:
      - experiments/latest/metrics.json

  evaluate:
    cmd: python scripts/run_evaluation.py
    deps:
      - src/evaluation/
      - evals/test_suites/
      - models/
    metrics:
      - experiments/latest/eval_metrics.json
```

---

## Configuracion de Experimento (configs/base.yaml)

```yaml
# Configuracion base de fine-tuning

provider: openai
base_model: gpt-4o-mini-2024-07-18

training:
  n_epochs: 3
  batch_size: 4
  learning_rate_multiplier: 1.0
  seed: 42

dataset:
  train_file: data/processed/train.jsonl
  validation_file: data/processed/val.jsonl

evaluation:
  test_file: evals/test_suites/held_out.jsonl
  metrics:
    - accuracy
    - quality_score
    - format_compliance
  quality_threshold: 0.8

tracking:
  project: fine-tuning-llm
  entity: mi-equipo

deployment:
  registry: huggingface_hub
  repo_id: mi-org/mi-modelo-fine-tuned
```

---

## Validacion de Datos (src/data/validator.py)

```python
"""Validador de datasets de fine-tuning."""
import json
from pathlib import Path
from typing import Any

from pydantic import BaseModel, ValidationError


class OpenAIFineTuningMessage(BaseModel):
    role: str  # "system" | "user" | "assistant"
    content: str


class OpenAIFineTuningExample(BaseModel):
    messages: list[OpenAIFineTuningMessage]


class DatasetValidator:
    """Valida la estructura y calidad de un dataset de fine-tuning."""

    MIN_EXAMPLES = 10
    MAX_EXAMPLES = 100_000

    def __init__(self, filepath: Path) -> None:
        self.filepath = filepath
        self.errors: list[str] = []
        self.warnings: list[str] = []

    def validate(self) -> bool:
        """Valida el dataset completo. Devuelve True si es valido."""
        examples = self._load_data()

        if len(examples) < self.MIN_EXAMPLES:
            self.errors.append(
                f"Solo {len(examples)} ejemplos. Minimo requerido: {self.MIN_EXAMPLES}"
            )

        if len(examples) > self.MAX_EXAMPLES:
            self.warnings.append(
                f"Dataset grande ({len(examples)} ejemplos). Considerar sampling."
            )

        for i, example in enumerate(examples):
            try:
                OpenAIFineTuningExample.model_validate(example)
            except ValidationError as e:
                self.errors.append(f"Ejemplo {i}: {e}")

            self._check_content_quality(i, example)

        return len(self.errors) == 0

    def _load_data(self) -> list[dict[str, Any]]:
        data = []
        with open(self.filepath, encoding="utf-8") as f:
            for line in f:
                line = line.strip()
                if line:
                    data.append(json.loads(line))
        return data

    def _check_content_quality(self, index: int, example: dict) -> None:
        for msg in example.get("messages", []):
            content = msg.get("content", "")
            if len(content) < 5:
                self.warnings.append(
                    f"Ejemplo {index}: mensaje muy corto ({len(content)} chars)"
                )
            if len(content) > 32768:
                self.errors.append(
                    f"Ejemplo {index}: mensaje excede limite de tokens"
                )

    def report(self) -> str:
        return f"Validacion completada.\nErrores: {len(self.errors)}\nWarnings: {len(self.warnings)}"
```

---

## GitHub Actions (Fine-Tune)

```yaml
# .github/workflows/fine-tune.yml
name: Fine-Tune Model

on:
  workflow_dispatch:
    inputs:
      config:
        description: 'Config file path'
        required: true
        default: 'configs/base.yaml'

jobs:
  validate-data:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v3
      - run: uv sync
      - name: Validate dataset
        run: uv run python -m src.data.validator --dataset data/processed/train.jsonl

  fine-tune:
    needs: validate-data
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v3
      - run: uv sync
      - name: Submit fine-tuning job
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
          WANDB_API_KEY: ${{ secrets.WANDB_API_KEY }}
        run: |
          uv run python scripts/submit_ft_job.py \
            --config ${{ github.event.inputs.config }}
      - name: Wait for completion and evaluate
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: uv run python scripts/run_evaluation.py
      - name: Deploy if quality threshold met
        env:
          HF_TOKEN: ${{ secrets.HF_TOKEN }}
        run: uv run python scripts/deploy_model.py
```

---

## Prompt Para Iniciar el Proyecto

```
Quiero crear un pipeline de fine-tuning para modelos de lenguaje.
Necesito ajustar GPT-4o-mini (o Llama 3.1 8B) para una tarea especifica.

El proyecto debe cubrir el ciclo completo:
- Preparacion de datos (limpieza, formateo, split)
- Validacion automatica del dataset
- Envio de jobs de fine-tuning a OpenAI API o Hugging Face
- Evaluacion del modelo resultante (accuracy, quality, safety)
- Registro en Hugging Face Hub
- Despliegue a produccion

Stack: Python 3.12, OpenAI API, Hugging Face, W&B, DVC, MLflow

Estructura deseada:
- src/data/ con collector, cleaner, formatter, splitter, validator
- src/fine_tuning/ con abstraccion de proveedores (OpenAI, HF, Together)
- src/evaluation/ con metricas y LLM-as-judge
- src/deployment/ con registro y despliegue
- configs/ con archivos YAML de configuracion de experimentos
- data/ con DVC para versionado de datasets
- evals/ con datasets de evaluacion
- notebooks/ para analisis exploratorio
- Workflows CI/CD: validate-dataset, fine-tune, eval-model

Sigue RULES.md, CONTEXTO.md, AGENTS.md.
Usa tipos estrictos, async/await, Pydantic v2, manejo de errores robusto.
```

---

## Referencias Cruzadas

- [Dominio: LLM Ops](../por-dominio/llm-ops.md)
- [Dominio: IA Generativa](../por-dominio/ia-generativa.md)
- [Dominio: MLOps](../por-dominio/ml-ops.md)
- [Guia de Vibe Coding](../docs/guia-vibe-coding.md)

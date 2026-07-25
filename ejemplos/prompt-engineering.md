# Ejemplo: Prompt Engineering Sistematico con Evaluacion

Ejemplo de un proyecto dedicado a la ingenieria y evaluacion sistematica de prompts. Cubre versionado, testing, A/B comparacion, optimizacion iterativa y despliegue controlado de prompts a produccion.

---

## Arbol de Carpetas Completo

```bash
prompt-lab/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── eval-prompts.yml         # Evaluacion en cada cambio de prompt
│       └── deploy-prompt.yml        # Despliegue con canary
├── .cursor/
│   └── rules
├── .gitignore
├── .env.example
├── README.md
├── RULES.md
├── CONTEXTO.md
├── pyproject.toml
├── Makefile
├── src/
│   ├── __init__.py
│   ├── config.py
│   ├── prompts/                     # Prompts como codigo fuente
│   │   ├── __init__.py
│   │   ├── registry.py             # Registro central de prompts versionados
│   │   ├── loader.py               # Carga prompts desde archivos
│   │   └── versions/
│   │       ├── v1/
│   │       │   ├── system_prompt.txt
│   │       │   └── metadata.yaml    # Version, fecha, autor, cambios
│   │       ├── v2/
│   │       │   ├── system_prompt.txt
│   │       │   └── metadata.yaml
│   │       └── production.yaml      # Apunta a la version en produccion
│   ├── evaluation/                  # Sistema de evaluacion
│   │   ├── __init__.py
│   │   ├── runner.py               # Ejecutor de evaluaciones
│   │   ├── test_cases/
│   │   │   ├── accuracy.json        # ¿La respuesta es factualmente correcta?
│   │   │   ├── format.json          # ¿La respuesta sigue el formato esperado?
│   │   │   ├── tone.json            # ¿El tono es el deseado?
│   │   │   ├── safety.json          # ¿Respuestas seguras ante inputs maliciosos?
│   │   │   ├── edge_cases.json      # Casos borde y situaciones raras
│   │   │   └── regression.json      # Casos donde fallaban versiones anteriores
│   │   ├── metrics/
│   │   │   ├── __init__.py
│   │   │   ├── exact_match.py       # Match exacto o patron
│   │   │   ├── semantic_match.py    # Similitud semantica con expected
│   │   │   ├── llm_judge.py         # Evaluacion con LLM como juez
│   │   │   ├── constraint_check.py  # Verifica constraints (longitud, formato, keywords)
│   │   │   └── latency_cost.py      # Mide latencia y costo por prompt
│   │   └── reporter.py             # Generador de reportes de evaluacion
│   ├── optimization/                # Optimizacion de prompts
│   │   ├── __init__.py
│   │   ├── ab_tester.py            # A/B testing entre versiones de prompt
│   │   ├── variation_generator.py  # Genera variaciones para probar
│   │   └── optimizer.py            # Algoritmo de optimizacion (DSPy-style)
│   ├── deployment/                  # Despliegue de prompts
│   │   ├── __init__.py
│   │   ├── canary.py               # Despliegue canario (X% trafico a nuevo prompt)
│   │   ├── rollback.py             # Rollback a version anterior
│   │   └── registry_sync.py        # Sincroniza registro local con produccion
│   ├── llm/
│   │   ├── __init__.py
│   │   └── client.py               # Cliente LLM abstracto
│   └── api/
│       ├── __init__.py
│       ├── router.py               # API para gestionar prompts
│       └── schemas.py
├── tests/
│   ├── conftest.py
│   ├── test_prompt_registry.py
│   ├── test_evaluation_runner.py
│   └── test_canary.py
├── experiments/                     # Resultados de experimentos
│   ├── exp_001_initial/
│   │   ├── config.yaml
│   │   └── report.md
│   ├── exp_002_add_examples/
│   │   ├── config.yaml
│   │   └── report.md
│   └── exp_003_chain_of_thought/
│       ├── config.yaml
│       └── report.md
├── configs/
│   ├── evaluation/
│   │   ├── strict.yaml              # Evaluacion estricta (todos los tests)
│   │   └── quick.yaml               # Evaluacion rapida (solo regresion + safety)
│   └── deployment/
│       ├── canary_10.yaml           # 10% trafico al nuevo prompt
│       └── canary_50.yaml           # 50% trafico
├── notebooks/
│   ├── 01_baseline_evaluation.ipynb
│   ├── 02_human_review.ipynb
│   └── 03_ab_results_analysis.ipynb
├── scripts/
│   ├── run_eval.py                  # Ejecutar suite de evaluacion
│   ├── compare_versions.py          # Comparar dos versiones de prompt
│   ├── deploy_prompt.py             # Desplegar prompt a produccion
│   └── rollback_prompt.py           # Rollback de prompt
└── docs/
    ├── prompt_guidelines.md          # Guia de estilo para escribir prompts
    ├── evaluation_protocol.md        # Protocolo de evaluacion
    └── version_history.md            # Historial de versiones y sus resultados
```

---

## Flujo de Trabajo de Prompt Engineering

### Ciclo de Mejora de un Prompt

```
[Version actual del prompt en produccion]
    |
    | Recolectar feedback (errores, quejas, casos donde falla)
    v
[Hipotesis de mejora]
    | Ej: "Agregar 3 ejemplos few-shot mejorara la precision en X%"
    v
[Crear nueva version del prompt] -> src/prompts/versions/v{N}/
    |
[Ejecutar suite de evaluacion completa]
    ├── Accuracy:    v1: 85% -> v2: 91% ✓
    ├── Format:      v1: 98% -> v2: 97% ✗ (regresion!)
    ├── Safety:      v1: 100% -> v2: 100% ✓
    ├── Latency:     v1: 1.2s -> v2: 1.8s ✗ (mas lento por los ejemplos)
    └── Cost:        v1: $0.003 -> v2: $0.008 ✗ (mas tokens)
    |
[Decision: ¿vale la pena la mejora en accuracy a costa de formato y costo?]
    ├── Si: proceder a A/B test
    └── No: iterar de nuevo
    |
[A/B Test en produccion]
    ├── 10% trafico con v2, 90% con v1
    ├── Metricas monitoreadas: satisfaccion de usuario, re-rate, errores
    └── Despues de 48h con resultados positivos -> promover a 50%
    |
[Promover a produccion completa]
    └── v2 ahora es el prompt por defecto
```

---

## Componentes Clave

### 1. Registro de Prompts (`src/prompts/registry.py`)

```python
# Sistema de versionado de prompts
class PromptRegistry:
    def __init__(self, versions_dir: Path):
        self.versions_dir = versions_dir
        self.production_version = self._load_production_version()
    
    def get(self, version: str | None = None) -> PromptVersion:
        """Obtiene una version especifica o la de produccion"""
        target = version or self.production_version
        prompt_text = (self.versions_dir / target / "system_prompt.txt").read_text()
        metadata = yaml.safe_load(
            (self.versions_dir / target / "metadata.yaml").read_text()
        )
        return PromptVersion(name=target, text=prompt_text, metadata=metadata)
    
    def promote_to_production(self, version: str, reason: str):
        """Promueve una version a produccion"""
        # 1. Validar que la version paso todas las evaluaciones
        # 2. Actualizar production.yaml
        # 3. Registrar en el historial
        # 4. Notificar al equipo
```

### 2. Suite de Evaluacion (`src/evaluation/test_cases/`)

Ejemplo de test case:

```json
{
  "test_suite": "accuracy",
  "version": "1.0",
  "cases": [
    {
      "id": "acc_001",
      "category": "factual_qa",
      "input": {
        "system": "{{system_prompt}}",
        "user": "Cual es el comando para listar archivos en Linux?"
      },
      "expected": {
        "must_contain": ["ls"],
        "must_not_contain": ["dir", "I don't know"],
        "max_length": 200,
        "format": "plain_text"
      },
      "evaluation": {
        "metrics": ["must_contain", "must_not_contain", "max_length"],
        "llm_judge_prompt": "La respuesta explica correctamente el comando ls? Responde SI o NO."
      }
    },
    {
      "id": "acc_002",
      "category": "code_generation",
      "input": {
        "system": "{{system_prompt}}",
        "user": "Escribe una funcion Python que calcule el factorial de un numero"
      },
      "expected": {
        "must_contain": ["def factorial", "return"],
        "must_not_contain": ["import math"],
        "can_execute": true,
        "test_input": 5,
        "test_expected_output": 120
      },
      "evaluation": {
        "metrics": ["must_contain", "must_not_contain", "can_execute", "execution_match"]
      }
    }
  ]
}
```

### 3. Metricas de Evaluacion (`src/evaluation/metrics/`)

| Metrica | Que mide | Cuando usarla |
|---------|---------|---------------|
| `exact_match` | Coincidencia exacta de texto o patron | Respuestas deterministicas, formato fijo |
| `must_contain` | Presencia de keywords requeridas | Hechos, comandos, nombres |
| `must_not_contain` | Ausencia de contenido prohibido | Seguridad, alucinaciones |
| `semantic_match` | Similitud semantica con respuesta esperada | Respuestas abiertas, resumenes |
| `llm_judge` | Evaluacion cualitativa por otro LLM | Calidad, tono, utilidad |
| `constraint_check` | Verifica restricciones (longitud, formato, idioma) | Formatos estructurados |
| `can_execute` | El codigo generado ejecuta sin errores | Generacion de codigo |
| `latency_p99` | Percentil 99 de latencia | Optimizacion de velocidad |
| `cost_per_call` | Costo promedio por llamada | Optimizacion de costos |

### 4. A/B Testing (`src/optimization/ab_tester.py`)

```python
class ABTester:
    def __init__(self, registry: PromptRegistry):
        self.registry = registry
        self.active_tests: dict[str, ABTest] = {}
    
    async def start_test(self, 
        control_version: str,
        treatment_version: str,
        traffic_split: float = 0.1,  # 10% al nuevo prompt
        min_sample_size: int = 1000,
        metrics: list[str] = ["user_satisfaction", "error_rate", "latency"]
    ) -> str:
        """Inicia un A/B test entre dos versiones de prompt"""
        test_id = str(uuid4())
        self.active_tests[test_id] = ABTest(
            control=control_version,
            treatment=treatment_version,
            split=traffic_split,
            metrics=metrics
        )
        return test_id
    
    async def get_results(self, test_id: str) -> ABTestResults:
        """Obtiene resultados estadisticos del test"""
        test = self.active_tests[test_id]
        results = await self._compute_statistics(test)
        
        return ABTestResults(
            control_vs_treatment={
                metric: {
                    "control_mean": control_mean,
                    "treatment_mean": treatment_mean,
                    "p_value": p_value,
                    "significant": p_value < 0.05,
                    "winner": "treatment" if treatment_mean > control_mean and p_value < 0.05 else "control"
                }
                for metric in test.metrics
            },
            sample_size=results.sample_size,
            recommendation=self._generate_recommendation(results)
        )
```

### 5. Reporte de Evaluacion

Al ejecutar la suite completa se genera un reporte como:

```
=================================================================
PROMPT EVALUATION REPORT
Date: 2025-07-25 14:30 UTC
Prompt Version: v3 (added_chain_of_thought)
Compared to: v2 (production)
=================================================================

TEST SUITE RESULTS:
┌─────────────────┬──────────┬──────────┬─────────┬──────────┐
│ Suite           │ v2 Score │ v3 Score │ Change  │ Status   │
├─────────────────┼──────────┼──────────┼─────────┼──────────┤
│ accuracy        │ 85.2%    │ 91.4%    │ +6.2%   │ PASS     │
│ format          │ 97.8%    │ 94.1%    │ -3.7%   │ REGRESSION│
│ tone            │ 88.0%    │ 90.5%    │ +2.5%   │ PASS     │
│ safety          │ 100%     │ 100%     │ 0%      │ PASS     │
│ edge_cases      │ 72.3%    │ 78.9%    │ +6.6%   │ PASS     │
│ regression      │ 100%     │ 100%     │ 0%      │ PASS     │
├─────────────────┼──────────┼──────────┼─────────┼──────────┤
│ OVERALL         │ 89.0%    │ 91.5%    │ +2.5%   │ PASS*    │
└─────────────────┴──────────┴──────────┴─────────┴──────────┘
* Contains 1 regression. Review before promoting.

LATENCY AND COST:
┌─────────────────┬──────────┬──────────┐
│ Metric          │ v2       │ v3       │
├─────────────────┼──────────┼──────────┤
│ P50 Latency     │ 0.8s     │ 1.1s     │
│ P99 Latency     │ 2.1s     │ 2.9s     │
│ Avg Cost/Call   │ $0.004   │ $0.007   │
└─────────────────┴──────────┴──────────┘

REGRESSION DETAIL (format):
- Case fmt_012: v3 incluye markdown extra no solicitado
- Case fmt_023: v3 excede longitud maxima en 15 chars
- Case fmt_045: v3 devuelve lista cuando se esperaba JSON

RECOMMENDATION: Fix format regressions before promoting to production.
```

---

## Pipeline de CI/CD para Prompts

```yaml
# .github/workflows/eval-prompts.yml
name: Evaluate Prompts

on:
  pull_request:
    paths:
      - 'src/prompts/versions/**'
      - 'src/prompts/**'

jobs:
  evaluate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Run evaluation suite
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: |
          python scripts/run_eval.py \
            --new-version $(cat src/prompts/versions/latest.txt) \
            --baseline production \
            --config configs/evaluation/strict.yaml
      
      - name: Check for regressions
        run: python scripts/check_regressions.py
      
      - name: Comment results on PR
        uses: actions/github-script@v7
        with:
          script: |
            const report = require('./eval_report.json');
            await github.rest.issues.createComment({
              ...context.repo,
              issue_number: context.issue.number,
              body: `## Prompt Evaluation Results\n${report.summary}`
            });
```

---

## Referencias Cruzadas

- [IA Generativa](../por-dominio/ia-generativa.md) -- gestion de prompts
- [LLM Ops](../por-dominio/llm-ops.md) -- despliegue y evaluacion continua
- [RAG End-to-End](./rag-end-to-end.md) -- evaluacion RAGAS-style

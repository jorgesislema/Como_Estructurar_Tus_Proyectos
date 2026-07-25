# Estructura y Buenas Practicas para Proyectos de LLM Ops

LLM Ops (Large Language Model Operations) es la disciplina que aplica principios de DevOps y MLOps al ciclo de vida de modelos de lenguaje: fine-tuning, despliegue, monitoreo, evaluacion continua, gestion de prompts y optimizacion de costos.

A diferencia de MLOps tradicional, LLM Ops lidia con desafios unicos: prompts como artefactos versionables, evaluacion cualitativa de outputs, gestion de tokens/costos, multiples proveedores de LLM y latencia de inferencia.

---

## Principios Clave de LLM Ops

1. **Prompts como codigo**: versionado, testing, CI/CD, rollback de prompts.
2. **Evaluacion continua**: no solo en deploy, sino monitoreo constante de calidad de outputs.
3. **Multi-proveedor**: abstraccion para usar OpenAI, Anthropic, Google, Azure, modelos locales.
4. **Observabilidad**: tracing de llamadas, costos, latencia, tasa de errores.
5. **Fine-tuning gestionado**: datasets versionados, jobs reproducibles, evaluacion pre/post.
6. **Guardrails**: filtros de contenido, validacion de outputs, prevencion de jailbreaks.
7. **Eficiencia de costos**: caching, model distillation, model routing (modelo pequeno para tareas simples).

---

## Estructura de Directorios Recomendada

```
mi-llm-ops/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml                 # Tests, linting
│   │   ├── eval-continua.yml      # Evaluacion programada
│   │   ├── deploy-model.yml       # Despliegue de modelo fine-tuned
│   │   ├── deploy-prompt.yml      # Despliegue de prompts
│   │   └── cost-alert.yml         # Alertas de costos
│   └── copilot-instructions.md
├── .cursor/
│   └── rules
├── .gitignore
├── .env.example
├── README.md
├── RULES.md
├── CONTEXTO.md
├── AGENTS.md
├── pyproject.toml
├── src/
│   ├── __init__.py
│   ├── config.py
│   ├── gateway/                   # API Gateway de LLMs
│   │   ├── __init__.py
│   │   ├── router.py             # Enrutamiento entre proveedores/modelos
│   │   ├── load_balancer.py      # Balanceo de carga entre deployments
│   │   └── fallback.py           # Fallback si un proveedor falla
│   ├── providers/                 # Adaptadores para cada proveedor
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── openai.py
│   │   ├── anthropic.py
│   │   ├── google.py
│   │   ├── azure.py
│   │   ├── aws_bedrock.py
│   │   ├── together.py
│   │   ├── fireworks.py
│   │   ├── groq.py
│   │   ├── deepseek.py
│   │   └── local.py              # Ollama, vLLM, llama.cpp
│   ├── prompts/                   # Gestion de prompts
│   │   ├── __init__.py
│   │   ├── registry.py           # Registro central de prompts
│   │   ├── templates/            # Templates Jinja2
│   │   │   ├── v1/
│   │   │   └── v2/
│   │   ├── versioning.py         # Versionado de prompts
│   │   └── deploy.py             # Script de despliegue de prompts
│   ├── fine_tuning/              # Fine-tuning de modelos
│   │   ├── __init__.py
│   │   ├── dataset_builder.py   # Construccion de datasets
│   │   ├── dataset_validator.py # Validacion de datasets
│   │   ├── job_submitter.py     # Envio de jobs de fine-tuning
│   │   ├── job_monitor.py       # Monitoreo de jobs
│   │   └── evaluator.py         # Evaluacion post fine-tuning
│   ├── evaluation/               # Evaluacion de modelos y prompts
│   │   ├── __init__.py
│   │   ├── metrics/
│   │   │   ├── accuracy.py
│   │   │   ├── faithfulness.py  # Fidelidad (no alucinaciones)
│   │   │   ├── relevance.py
│   │   │   ├── toxicity.py
│   │   │   └── cost_efficiency.py
│   │   ├── judges/
│   │   │   └── llm_judge.py     # LLM-as-judge
│   │   ├── test_suites/
│   │   │   ├── regression.json  # Tests de regresion
│   │   │   └── safety.json      # Tests de seguridad
│   │   └── runner.py
│   ├── observability/            # Monitoreo y tracing
│   │   ├── __init__.py
│   │   ├── logger.py
│   │   ├── tracer.py            # OpenTelemetry tracing
│   │   ├── cost_monitor.py      # Monitoreo de costos
│   │   ├── latency_monitor.py   # Monitoreo de latencia
│   │   └── dashboard.py         # Dashboards (Grafana, etc.)
│   ├── guardrails/               # Guardrails y seguridad
│   │   ├── __init__.py
│   │   ├── input_guard.py       # Filtro de inputs
│   │   ├── output_guard.py      # Filtro de outputs
│   │   ├── pii_detector.py      # Deteccion de PII
│   │   └── jailbreak_detector.py
│   ├── cache/                    # Cache de respuestas
│   │   ├── __init__.py
│   │   ├── semantic_cache.py    # Cache semantica (Redis + embeddings)
│   │   └── exact_cache.py       # Cache exacta (Redis)
│   ├── routing/                  # Enrutamiento inteligente
│   │   ├── __init__.py
│   │   └── model_router.py      # Seleccion de modelo por tarea
│   └── api/                      # API del servicio
│       ├── __init__.py
│       ├── router.py
│       └── schemas.py
├── tests/
│   ├── conftest.py
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── datasets/                     # Datasets para fine-tuning y evaluacion
│   ├── raw/
│   ├── processed/
│   └── README.md
├── experiments/                  # Seguimiento de experimentos
│   └── README.md
├── deployment/                   # Configuraciones de despliegue
│   ├── docker/
│   ├── kubernetes/
│   └── terraform/
├── monitoring/                   # Config de monitoreo
│   ├── grafana/
│   │   └── dashboards/
│   └── alerts/
│       └── rules.yml
├── docs/
│   ├── architecture.md
│   ├── prompts.md
│   ├── models.md                # Catalogo de modelos disponibles
│   └── runbooks/                # Procedimientos operativos
│       ├── incident_response.md
│       └── model_rollback.md
├── scripts/
│   ├── deploy_prompts.py
│   ├── run_evals.py
│   ├── submit_finetune_job.py
│   └── cost_report.py
├── Makefile
├── Dockerfile
└── docker-compose.yml
```

---

## Componentes Clave Explicados

### 1. API Gateway de LLMs (`src/gateway/`)

Centraliza todas las llamadas a LLMs, permitiendo enrutamiento, load balancing y fallback:

```python
# src/gateway/router.py
class LLMGateway:
    def __init__(self, providers: dict[str, BaseLLMClient], config: GatewayConfig):
        self.providers = providers
        self.config = config
        self.router = ModelRouter(config.routing_rules)
        self.cost_monitor = CostMonitor()
    
    async def generate(self, messages: list[dict], **kwargs) -> LLMResponse:
        # 1. Seleccionar mejor modelo/proveedor
        provider_name, model = self.router.select(messages, **kwargs)
        
        # 2. Intentar con primario
        try:
            response = await self.providers[provider_name].generate(
                messages, model=model, **kwargs
            )
            self.cost_monitor.track(response)
            return response
        except (RateLimitError, TimeoutError, APIError) as e:
            # 3. Fallback a secundario
            logger.warning(f"Provider {provider_name} failed: {e}")
            fallback = self.router.get_fallback(provider_name)
            response = await self.providers[fallback].generate(
                messages, model=fallback_model, **kwargs
            )
            self.cost_monitor.track(response)
            return response
```

### 2. Enrutamiento Inteligente (`src/routing/`)

Decide que modelo usar segun la tarea:

```python
# src/routing/model_router.py
class ModelRouter:
    ROUTING_RULES = {
        "simple": {        # Tareas simples (clasificacion, extraccion)
            "primary": ("openai", "gpt-4o-mini"),
            "fallback": ("groq", "llama-3.1-8b"),
        },
        "complex": {       # Tareas complejas (razonamiento, codigo)
            "primary": ("anthropic", "claude-sonnet-4-20250514"),
            "fallback": ("openai", "gpt-4o"),
        },
        "creative": {      # Tareas creativas (escritura, ideacion)
            "primary": ("anthropic", "claude-opus-4-20250514"),
            "fallback": ("openai", "gpt-4o"),
        },
        "fast": {          # Tareas que requieren baja latencia
            "primary": ("groq", "llama-3.1-70b"),
            "fallback": ("together", "mixtral-8x7b"),
        },
    }
    
    def select(self, task_complexity: str) -> tuple[str, str]:
        rule = self.ROUTING_RULES.get(task_complexity, self.ROUTING_RULES["simple"])
        return rule["primary"]
```

### 3. Cache Semantica (`src/cache/`)

Evita llamadas repetidas al LLM para preguntas similares:

```python
# src/cache/semantic_cache.py
class SemanticCache:
    def __init__(self, vector_store, embedding_model, threshold=0.95):
        self.vector_store = vector_store
        self.embedding_model = embedding_model
        self.threshold = threshold
    
    async def get(self, query: str) -> str | None:
        query_embedding = await self.embedding_model.embed(query)
        results = await self.vector_store.search(query_embedding, top_k=1)
        
        if results and results[0].score >= self.threshold:
            logger.info("Cache hit: semantic similarity")
            return results[0].metadata["response"]
        return None
    
    async def set(self, query: str, response: str):
        embedding = await self.embedding_model.embed(query)
        await self.vector_store.add(
            embedding=embedding,
            metadata={"query": query, "response": response}
        )
```

### 4. Fine-tuning Pipeline (`src/fine_tuning/`)

```python
# src/fine_tuning/job_submitter.py
class FineTuningPipeline:
    def __init__(self, provider: str, config: FineTuningConfig):
        self.provider = provider
        self.config = config
    
    async def run(self, dataset_path: str, base_model: str) -> str:
        # 1. Validar dataset
        validator = DatasetValidator()
        validator.validate(dataset_path)
        
        # 2. Subir dataset al proveedor
        dataset_id = await self._upload_dataset(dataset_path)
        
        # 3. Crear job de fine-tuning
        job_id = await self._create_job(
            base_model=base_model,
            dataset_id=dataset_id,
            hyperparameters=self.config.hyperparameters
        )
        
        # 4. Monitorear job
        monitor = JobMonitor()
        await monitor.wait_for_completion(job_id)
        
        # 5. Evaluar modelo resultante
        evaluator = FineTuningEvaluator()
        results = await evaluator.evaluate(job_id, self.config.eval_dataset)
        
        # 6. Registrar resultados
        self._log_results(job_id, results)
        
        return job_id
```

### 5. Evaluacion Continua (`src/evaluation/`)

```python
# src/evaluation/runner.py
class ContinuousEvaluator:
    def __init__(self, gateway: LLMGateway, test_suites: list[TestSuite]):
        self.gateway = gateway
        self.test_suites = test_suites
    
    async def run_all(self) -> EvalReport:
        report = EvalReport()
        
        for suite in self.test_suites:
            for test_case in suite.cases:
                response = await self.gateway.generate(
                    messages=test_case.messages,
                    model=suite.model,
                    temperature=0  # Deterministico para evaluacion
                )
                
                scores = {}
                for metric in suite.metrics:
                    score = await metric.evaluate(test_case.expected, response)
                    scores[metric.name] = score
                
                report.add_result(test_case, scores)
        
        # Alertar si hay degradacion
        if report.is_degrading():
            self._alert(report)
        
        return report
```

---

## Monitoreo y Alertas

### Metricas Clave a Monitorear

```
LLM-Specific:
├── latencia_p99_ms          # Percentil 99 de latencia
├── tokens_por_llamada       # Tokens usados por llamada
├── costo_por_llamada_usd    # Costo en USD por llamada
├── costo_total_diario_usd   # Costo total diario
├── tasa_de_error            # Errores (rate limit, timeout, 5xx)
├── tasa_de_cache_hit        # Porcentaje de cache hits
├── puntuacion_evaluacion    # Score de calidad de outputs
└── tasa_de_guardrail_activado  # Veces que se activo un guardrail

Sistema:
├── cpu_usage
├── memory_usage
├── requests_per_second
└── active_connections
```

### Dashboard Grafana (ejemplo)

```json
{
  "dashboard": {
    "title": "LLM Operations",
    "panels": [
      {
        "title": "Costos por Proveedor (24h)",
        "type": "barchart",
        "targets": [{"expr": "sum(llm_cost_usd) by (provider)"}]
      },
      {
        "title": "Latencia P99 por Modelo",
        "type": "timeseries",
        "targets": [{"expr": "histogram_quantile(0.99, llm_latency_ms) by (model)"}]
      },
      {
        "title": "Evaluacion de Calidad (ultimos 7 dias)",
        "type": "timeseries",
        "targets": [{"expr": "llm_eval_score"}],
        "thresholds": [{"value": 0.7, "color": "red"}]
      }
    ]
  }
}
```

---

## CI/CD para LLM Ops

```yaml
# .github/workflows/deploy-prompt.yml
name: Deploy Prompt

on:
  push:
    paths:
      - 'src/prompts/templates/**'

jobs:
  validate-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Validate prompt syntax
        run: python scripts/validate_prompts.py
      
      - name: Run evaluation suite
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: python scripts/run_evals.py --prompts-changed
      
      - name: Check for regressions
        run: python scripts/check_regressions.py
      
      - name: Deploy prompts
        if: success()
        env:
          PROMPT_REGISTRY_URL: ${{ secrets.PROMPT_REGISTRY_URL }}
        run: python scripts/deploy_prompts.py
```

---

## Referencias Cruzadas

- [IA Generativa y LLMs](./ia-generativa.md) -- desarrollo de aplicaciones LLM
- [Agentes IA](./agentes-ia.md) -- agentes autonomos
- [MLOps](./ml-ops.md) -- fundamentos de MLOps
- [Ciencia de Datos](./ciencia-datos.md) -- gestion de datos y experimentos
- [Guia de Vibe Coding](../docs/guia-vibe-coding.md)
- [Archivos IA Esenciales](../docs/archivos-ia-esenciales.md)
- [Ejemplo: Fine-tuning LLM](../ejemplos/llm-finetuning.md)

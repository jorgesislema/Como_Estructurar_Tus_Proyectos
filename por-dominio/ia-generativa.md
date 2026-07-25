# Estructura y Buenas Practicas para Proyectos de IA Generativa y LLMs

La IA Generativa abarca proyectos que utilizan modelos de lenguaje de gran escala (LLMs) como GPT-4, Claude, Gemini, Llama, Mistral y otros para generar texto, codigo, imagenes u otros contenidos. Estos proyectos tienen necesidades unicas de estructura: gestion de prompts, cadenas de llamadas, memoria conversacional, RAG (Retrieval-Augmented Generation), evaluacion de outputs y gestion de costos/tokens.

---

## Principios Clave para Proyectos de IA Generativa

1. **Prompts como codigo**: los prompts deben versionarse, testearse y mantenerse como cualquier otro artefacto de software.
2. **Reproducibilidad**: mismo prompt + mismos parametros (temperatura, seed) debe producir resultados predecibles.
3. **Evaluacion sistematica**: no basta con "mirar si la respuesta es buena". Se necesitan metricas automaticas y evaluacion humana estructurada.
4. **Gestion de costos**: cada llamada a la API tiene costo en tokens. El codigo debe ser eficiente.
5. **Seguridad de API keys**: las claves de API nunca en el codigo fuente, siempre en variables de entorno.
6. **Manejo de errores**: las APIs de LLM fallan (rate limits, timeouts, errores 500). El codigo debe ser resiliente.

---

## Estructura de Directorios Recomendada

```
mi-proyecto-llm/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml             # Tests, linting
│   │   ├── eval.yml           # Evaluacion periodica de prompts
│   │   └── cd.yml             # Despliegue
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
├── Dockerfile
├── docker-compose.yml
├── src/
│   ├── __init__.py
│   ├── main.py                # Punto de entrada (API o CLI)
│   ├── config.py              # Configuracion desde .env
│   ├── llm/                   # Capa de abstraccion de LLMs
│   │   ├── __init__.py
│   │   ├── base.py            # Clase base abstracta para LLMs
│   │   ├── openai_client.py   # Cliente OpenAI
│   │   ├── anthropic_client.py # Cliente Anthropic
│   │   ├── google_client.py   # Cliente Google (Gemini)
│   │   ├── azure_client.py    # Cliente Azure OpenAI
│   │   ├── local_client.py    # Cliente para modelos locales (Ollama, vLLM)
│   │   └── factory.py         # Factory para instanciar el cliente correcto
│   ├── prompts/               # Gestion de prompts
│   │   ├── __init__.py
│   │   ├── templates/         # Plantillas de prompts (Jinja2, f-strings)
│   │   │   ├── system/
│   │   │   │   ├── asistente_general.txt
│   │   │   │   ├── generador_codigo.txt
│   │   │   │   └── analista_datos.txt
│   │   │   └── user/
│   │   │       ├── resumen.txt
│   │   │       ├── traduccion.txt
│   │   │       └── extraccion.txt
│   │   ├── manager.py         # Carga, versiona, y aplica templates
│   │   └── versioning.py      # Versionado de prompts
│   ├── chains/                # Cadenas de procesamiento (orquestacion)
│   │   ├── __init__.py
│   │   ├── base_chain.py
│   │   ├── rag_chain.py       # Retrieval-Augmented Generation
│   │   ├── agent_chain.py     # Cadena de agente con herramientas
│   │   └── router_chain.py    # Enrutamiento entre cadenas
│   ├── memory/                # Memoria conversacional
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── buffer.py          # Memoria de buffer simple
│   │   ├── summary.py         # Memoria con resumen
│   │   └── persistent.py      # Memoria persistente (Redis, Postgres)
│   ├── tools/                 # Herramientas para agentes (function calling)
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── web_search.py
│   │   ├── code_executor.py
│   │   ├── database_query.py
│   │   └── api_caller.py
│   ├── rag/                   # Retrieval-Augmented Generation
│   │   ├── __init__.py
│   │   ├── embeddings.py      # Generacion de embeddings
│   │   ├── vector_store.py    # Almacen vectorial (Chroma, Pinecone, etc.)
│   │   ├── retriever.py       # Recuperacion de documentos
│   │   ├── chunker.py         # Division de documentos en chunks
│   │   └── loader.py          # Carga de documentos (PDF, web, etc.)
│   ├── evaluation/            # Evaluacion de outputs
│   │   ├── __init__.py
│   │   ├── metrics.py         # Metricas automaticas (BLEU, ROUGE, etc.)
│   │   ├── llm_judge.py       # Evaluacion LLM-as-judge
│   │   ├── test_cases.py      # Casos de prueba
│   │   └── runner.py          # Ejecutor de evaluaciones
│   ├── middleware/             # Middleware de procesamiento
│   │   ├── __init__.py
│   │   ├── logging.py         # Logging de llamadas
│   │   ├── caching.py         # Cache de respuestas
│   │   ├── rate_limiter.py    # Control de rate limits
│   │   ├── cost_tracker.py    # Seguimiento de costos/tokens
│   │   └── fallback.py        # Fallback entre proveedores
│   ├── api/                   # API REST (si aplica)
│   │   ├── __init__.py
│   │   ├── router.py
│   │   ├── schemas.py         # Schemas Pydantic
│   │   └── streaming.py       # Streaming de respuestas
│   └── ui/                    # Interfaz web (si aplica)
│       ├── streamlit_app.py   # o Gradio, Chainlit, etc.
│       └── components/
├── tests/
│   ├── conftest.py
│   ├── test_llm/
│   ├── test_prompts/
│   ├── test_chains/
│   ├── test_rag/
│   ├── test_evaluation/
│   └── test_tools/
├── data/
│   ├── documents/             # Documentos para RAG
│   ├── datasets/              # Datasets de evaluacion
│   └── prompts_history/       # Historial de prompts (opcional)
├── docs/
│   ├── prompts.md             # Documentacion de prompts
│   ├── evaluation.md          # Resultados de evaluacion
│   └── architecture.md
├── scripts/
│   ├── index_documents.py     # Indexar documentos en vector store
│   ├── run_evaluation.py      # Ejecutar suite de evaluacion
│   └── migrate_prompts.py     # Migrar prompts entre versiones
└── alembic/                   # Migraciones (si hay DB)
```

---

## Componentes Clave Explicados

### 1. Capa de Abstraccion de LLMs (`src/llm/`)

Centraliza el acceso a diferentes proveedores de LLM detras de una interfaz comun:

```python
# src/llm/base.py
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import AsyncIterator

@dataclass
class LLMResponse:
    content: str
    model: str
    usage: dict  # tokens usados
    finish_reason: str

@dataclass  
class LLMMessage:
    role: str  # "system" | "user" | "assistant"
    content: str

class BaseLLMClient(ABC):
    @abstractmethod
    async def generate(self, messages: list[LLMMessage], **kwargs) -> LLMResponse:
        ...

    @abstractmethod
    async def generate_stream(self, messages: list[LLMMessage], **kwargs) -> AsyncIterator[str]:
        ...
```

- Beneficio: cambiar de OpenAI a Anthropic o a un modelo local solo requiere cambiar la configuracion, no el codigo.
- Permite A/B testing entre modelos.
- Facilita el fallback automatico entre proveedores.

### 2. Gestion de Prompts (`src/prompts/`)

Los prompts deben tratarse como **codigo fuente**: versionados, testeados, con revision por pares.

```python
# src/prompts/manager.py
from jinja2 import Environment, FileSystemLoader

class PromptManager:
    def __init__(self, templates_dir: str = "src/prompts/templates"):
        self.env = Environment(loader=FileSystemLoader(templates_dir))
    
    def render(self, template_name: str, **variables) -> str:
        template = self.env.get_template(template_name)
        return template.render(**variables)

# Uso:
pm = PromptManager()
system_prompt = pm.render("system/asistente_general.txt")
user_prompt = pm.render("user/resumen.txt", texto=texto_largo, max_palabras=100)
```

### 3. RAG (`src/rag/`)

Retrieval-Augmented Generation: aumentar las respuestas del LLM con informacion recuperada de una base de conocimiento.

```
Flujo RAG:
1. Usuario hace pregunta
2. Retriever busca documentos relevantes en vector store
3. Los documentos se inyectan en el prompt del LLM como contexto
4. LLM genera respuesta basada en el contexto recuperado
```

### 4. Evaluacion (`src/evaluation/`)

Evaluar LLMs es fundamentalmente diferente a evaluar codigo tradicional:

```python
# src/evaluation/metrics.py
class LLMEvaluation:
    def __init__(self, test_cases: list[dict]):
        self.test_cases = test_cases
    
    async def run_all(self, client: BaseLLMClient) -> dict:
        results = {"passed": 0, "failed": 0, "details": []}
        for case in self.test_cases:
            response = await client.generate(case["messages"])
            score = await self.llm_judge(case["expected"], response.content)
            # score = self.semantic_similarity(case["expected"], response.content)
            results["details"].append({...})
        return results
```

### 5. Middleware (`src/middleware/`)

Capa transversal que maneja concerns como logging, caching, rate limiting, cost tracking:

```python
# src/middleware/cost_tracker.py
class CostTracker:
    def __init__(self):
        self.total_cost = 0.0
        self.total_tokens = 0
    
    def track(self, response: LLMResponse):
        cost = self._calculate_cost(response.model, response.usage)
        self.total_cost += cost
        self.total_tokens += response.usage.get("total_tokens", 0)
    
    def _calculate_cost(self, model: str, usage: dict) -> float:
        # Precios por 1K tokens (actualizar periodicamente)
        pricing = {
            "gpt-4o": {"input": 0.005, "output": 0.015},
            "gpt-4o-mini": {"input": 0.00015, "output": 0.0006},
            "claude-sonnet-4-20250514": {"input": 0.003, "output": 0.015},
        }
        # ...calculo...
```

---

## Gestion de API Keys y Secretos

```bash
# .env.example
# LLM Providers
OPENAI_API_KEY=sk-proj-...
ANTHROPIC_API_KEY=sk-ant-...
GEMINI_API_KEY=AIza...
AZURE_OPENAI_API_KEY=...
AZURE_OPENAI_ENDPOINT=https://...
DEEPSEEK_API_KEY=sk-...
GROQ_API_KEY=gsk_...

# Vector Store (si aplica)
PINECONE_API_KEY=...
CHROMA_HOST=localhost
CHROMA_PORT=8000

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/llm_app
REDIS_URL=redis://localhost:6379
```

---

## Evaluacion de LLMs: Estrategia

```
evaluacion/
├── test_cases/
│   ├── factualidad.json        # Casos que requieren precision factual
│   ├── creatividad.json        # Casos que requieren creatividad
│   ├── seguridad.json          # Casos de jailbreak / contenido peligroso
│   ├── formato.json            # Casos que requieren formato especifico (JSON, etc.)
│   └── idioma.json             # Casos multilingues
├── judges/
│   └── judge_prompt.txt        # Prompt del LLM juez
└── results/
    └── YYYY-MM-DD/
        └── evaluation_report.json
```

Tipos de evaluacion:
- **Metodo 1 - Assert-based**: verificar que la respuesta contenga ciertas palabras, no contenga otras, cumpla formato.
- **Metodo 2 - LLM-as-judge**: otro LLM evalua si la respuesta es correcta segun criterios definidos.
- **Metodo 3 - Embedding similarity**: comparar embeddings de la respuesta vs respuesta esperada.
- **Metodo 4 - Evaluacion humana**: muestra de outputs revisada por personas.

---

## CI/CD Especifico para LLMs

```yaml
# .github/workflows/eval.yml
name: Prompt Evaluation

on:
  schedule:
    - cron: '0 6 * * 1'  # Cada lunes a las 6 AM
  workflow_dispatch:

jobs:
  evaluate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install -r requirements.txt
      - name: Run evaluations
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: python scripts/run_evaluation.py
      - name: Upload results
        uses: actions/upload-artifact@v4
        with:
          name: eval-results
          path: evaluation_results/
```

---

## Referencias Cruzadas

- [Guia de Vibe Coding](../docs/guia-vibe-coding.md)
- [Archivos IA Esenciales](../docs/archivos-ia-esenciales.md)
- [MLOps](../por-dominio/ml-ops.md)
- [Agentes IA](../por-dominio/agentes-ia.md)
- [LLM Ops](../por-dominio/llm-ops.md)
- [Ejemplo: Agente IA](../ejemplos/agente-ia-proyecto.md)
- [Ejemplo: Fine-tuning LLM](../ejemplos/llm-finetuning.md)

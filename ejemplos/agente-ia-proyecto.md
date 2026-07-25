# Ejemplo: Proyecto de Agente de IA

Ejemplo de estructura de repositorio para un agente de IA autonomo. Este proyecto implementa un agente que puede ejecutar tareas de analisis de datos: recibir un dataset, explorarlo, generar visualizaciones y producir un informe.

Stack: Python 3.12, LangChain/LangGraph (o implementacion propia), OpenAI/Anthropic, PostgreSQL, Redis, Docker.

---

## Arbol de Carpetas Completo

```bash
agente-analista-datos/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── eval.yml
│   │   └── cd.yml
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
├── Makefile
├── src/
│   ├── __init__.py
│   ├── main.py                    # CLI principal
│   ├── config.py
│   ├── agent/
│   │   ├── __init__.py
│   │   ├── core.py                # Bucle ReAct del agente
│   │   ├── state.py               # Estado del agente
│   │   ├── planner.py             # Planificador de tareas
│   │   └── reflector.py           # Autoevaluacion
│   ├── llm/
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── openai_client.py
│   │   └── anthropic_client.py
│   ├── tools/
│   │   ├── __init__.py
│   │   ├── base.py                # BaseTool abstracta
│   │   ├── registry.py            # Registro de herramientas
│   │   ├── python_executor.py     # Ejecutar codigo Python en sandbox
│   │   ├── file_reader.py         # Leer archivos CSV, JSON, Excel
│   │   ├── data_analyzer.py       # Analisis estadistico basico
│   │   ├── chart_generator.py     # Generar graficos (matplotlib)
│   │   ├── web_search.py          # Busqueda web
│   │   └── report_writer.py       # Generar informe Markdown
│   ├── memory/
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── conversation.py        # Memoria de conversacion
│   │   └── working.py             # Memoria de trabajo (resultados)
│   ├── sandbox/
│   │   ├── __init__.py
│   │   └── docker_executor.py     # Ejecucion en contenedor Docker
│   ├── safety/
│   │   ├── __init__.py
│   │   ├── code_validator.py      # Validar codigo antes de ejecutar
│   │   └── rate_limiter.py        # Limitar llamadas a API
│   ├── observability/
│   │   ├── __init__.py
│   │   ├── tracer.py              # Tracing de pasos del agente
│   │   └── logger.py              # Logging estructurado
│   └── prompts/
│       ├── __init__.py
│       ├── manager.py
│       └── templates/
│           ├── system/
│           │   ├── data_analyst.txt
│           │   └── planner.txt
│           └── reflection/
│               └── self_critique.txt
├── tests/
│   ├── conftest.py
│   ├── unit/
│   │   ├── test_agent_core.py
│   │   ├── test_tools.py
│   │   └── test_safety.py
│   ├── integration/
│   │   └── test_agent_e2e.py
│   └── fixtures/
│       ├── sample_data.csv
│       └── mock_llm.py
├── evals/
│   ├── tasks/
│   │   ├── basic_analysis.json
│   │   ├── visualization.json
│   │   └── complex_report.json
│   ├── runner.py
│   └── metrics.py
├── sandbox/
│   └── Dockerfile                  # Imagen Docker para sandbox
├── scripts/
│   ├── run_agent.py
│   └── run_evals.py
├── docs/
│   ├── architecture.md
│   ├── tools.md
│   └── safety.md
└── data/
    └── README.md                   # Datasets de ejemplo no se commitean
```

---

## Archivos Clave

### src/agent/core.py -- El Corazon del Agente

```python
"""Bucle principal del agente usando el patron ReAct (Reasoning + Acting)."""
import json
import logging
from dataclasses import dataclass, field
from typing import Any

from src.agent.state import AgentState, AgentStep
from src.llm.base import BaseLLMClient
from src.tools.registry import ToolRegistry
from src.memory.conversation import ConversationMemory
from src.memory.working import WorkingMemory
from src.safety.code_validator import CodeValidator
from src.observability.tracer import Tracer

logger = logging.getLogger(__name__)


@dataclass
class AgentConfig:
    max_steps: int = 15
    max_tool_calls: int = 50
    temperature: float = 0.0


class DataAnalystAgent:
    """Agente autonomo para analisis de datos."""

    def __init__(
        self,
        llm: BaseLLMClient,
        tools: ToolRegistry,
        conversation_memory: ConversationMemory,
        working_memory: WorkingMemory,
        code_validator: CodeValidator,
        tracer: Tracer,
        config: AgentConfig | None = None,
    ) -> None:
        self.llm = llm
        self.tools = tools
        self.conversation = conversation_memory
        self.working = working_memory
        self.code_validator = code_validator
        self.tracer = tracer
        self.config = config or AgentConfig()

    async def run(self, task: str, dataset_path: str) -> dict[str, Any]:
        """Ejecuta el agente en una tarea de analisis de datos."""
        state = AgentState(task=task, dataset_path=dataset_path)

        with self.tracer.span("agent_run") as span:
            span.set_attribute("task", task)

            while state.current_step < self.config.max_steps:
                step_result = await self._execute_step(state)

                if step_result.is_final:
                    return {
                        "answer": step_result.answer,
                        "report_path": step_result.report_path,
                        "steps_taken": state.current_step + 1,
                        "tools_used": state.tools_used,
                        "total_cost_usd": state.total_cost,
                    }

                state.current_step += 1

        return {"answer": "No se pudo completar la tarea en el maximo de pasos."}

    async def _execute_step(self, state: AgentState) -> AgentStep:
        """Ejecuta un paso del ciclo ReAct."""
        messages = self._build_messages(state)

        response = await self.llm.generate(
            messages=messages,
            tools=self.tools.get_openai_schemas(),
            temperature=self.config.temperature,
        )

        action, action_input, thought = self._parse_tool_call(response)

        if action is None:
            return AgentStep(is_final=True, answer=thought)

        observation = await self.tools.execute(action, **action_input)

        step = AgentStep(
            thought=thought,
            action=action,
            action_input=action_input,
            observation=str(observation)[:5000],
        )
        state.steps.append(step)
        state.tools_used.append(action)

        self.conversation.add_assistant_message(thought)
        self.conversation.add_tool_result(action, observation)

        return step

    def _build_messages(self, state: AgentState) -> list[dict[str, str]]:
        system_prompt = self._load_system_prompt()
        task_prompt = f"Tarea: {state.task}\nDataset: {state.dataset_path}"
        return [
            {"role": "system", "content": system_prompt},
            *self.conversation.get_messages(),
            {"role": "user", "content": task_prompt},
        ]

    def _load_system_prompt(self) -> str:
        from pathlib import Path
        prompt_path = Path(__file__).parent.parent / "prompts/templates/system/data_analyst.txt"
        return prompt_path.read_text()
```

### src/tools/base.py -- Sistema de Herramientas

```python
"""Clase base para herramientas del agente."""
from abc import ABC, abstractmethod
from typing import Any, get_type_hints
from pydantic import BaseModel


class BaseTool(ABC):
    """Herramienta que el agente puede usar via function calling."""

    name: str
    description: str

    @abstractmethod
    async def execute(self, **kwargs: Any) -> str:
        """Ejecuta la herramienta y devuelve resultado como string."""
        ...

    def get_openai_schema(self) -> dict[str, Any]:
        """Genera el schema en formato OpenAI function calling."""
        hints = get_type_hints(self.execute)
        parameters = self._build_parameters(hints)

        return {
            "type": "function",
            "function": {
                "name": self.name,
                "description": self.description,
                "parameters": parameters,
            },
        }

    def _build_parameters(self, hints: dict) -> dict:
        # Construye JSON Schema a partir de type hints
        properties = {}
        required = []

        for param_name, param_type in hints.items():
            if param_name in ("return",):
                continue
            json_type = self._python_to_json_type(param_type)
            properties[param_name] = {"type": json_type}
            required.append(param_name)

        return {
            "type": "object",
            "properties": properties,
            "required": required,
        }

    @staticmethod
    def _python_to_json_type(py_type: type) -> str:
        mapping = {str: "string", int: "integer", float: "number", bool: "boolean"}
        return mapping.get(py_type, "string")
```

### evals/tasks/basic_analysis.json

```json
{
  "suite_name": "basic_analysis",
  "description": "Tareas basicas de analisis de datos",
  "tasks": [
    {
      "id": "ba_001",
      "input": "Analiza el archivo ventas_2024.csv. Cual es el producto mas vendido?",
      "expected_output_pattern": ["producto mas vendido", "unidades"],
      "required_tools": ["python_executor", "file_reader"],
      "max_steps": 5,
      "threshold": 0.7
    },
    {
      "id": "ba_002",
      "input": "Genera un grafico de barras de ventas por mes del archivo ventas_2024.csv",
      "expected_output_pattern": ["grafico", "barras", "ventas", "mes"],
      "required_tools": ["python_executor", "chart_generator", "file_reader"],
      "max_steps": 8,
      "threshold": 0.6
    },
    {
      "id": "ba_003",
      "input": "Calcula el promedio, mediana y desviacion estandar de la columna 'precio' en productos.csv",
      "expected_output_pattern": ["promedio", "mediana", "desviacion"],
      "required_tools": ["python_executor", "file_reader"],
      "max_steps": 5,
      "threshold": 0.8
    }
  ]
}
```

---

## Prompt Para Iniciar el Proyecto

```
Quiero construir un agente de IA autonomo para analisis de datos.
El agente recibe un dataset y una tarea en lenguaje natural, y debe:

1. Leer el archivo de datos (CSV, JSON, Excel)
2. Analizar los datos (estadisticas descriptivas)
3. Generar visualizaciones (matplotlib/seaborn)
4. Producir un informe Markdown con hallazgos

Stack: Python 3.12, OpenAI API, Docker (sandbox)

Arquitectura del agente:
- Patron ReAct (Reasoning + Acting) con function calling de OpenAI
- Herramientas registradas en ToolRegistry
- Memoria de conversacion y memoria de trabajo separadas
- Sandbox Docker para ejecutar codigo Python de forma segura
- CodeValidator para verificar codigo antes de ejecutar

Estructura deseada:
- src/agent/core.py con clase DataAnalystAgent
- src/agent/state.py con AgentState y AgentStep
- src/tools/ con sistema de herramientas (file_reader, python_executor, data_analyzer, chart_generator, report_writer)
- src/memory/ con ConversationMemory y WorkingMemory
- src/sandbox/ con DockerExecutor
- src/safety/ con CodeValidator
- src/observability/ con Tracer
- evals/ con suite de evaluacion

Reglas:
- Sigue RULES.md, CONTEXTO.md, AGENTS.md
- Tipos estrictos, docstrings Google, async/await
- Manejo de errores robusto (timeouts, rate limits, API errors)
- El agente debe tener max_steps configurable
- Cada paso debe registrarse para debugging
```

---

## Referencias Cruzadas

- [Dominio: Agentes IA](../por-dominio/agentes-ia.md)
- [Dominio: IA Generativa](../por-dominio/ia-generativa.md)
- [Guia de Vibe Coding](../docs/guia-vibe-coding.md)
- [Ejemplo: Vibe Coding Avanzado](./vibe-coding-avanzado.md)

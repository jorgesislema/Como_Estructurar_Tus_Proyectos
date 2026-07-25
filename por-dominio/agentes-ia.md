# Estructura y Buenas Practicas para Proyectos de Agentes de IA

Un agente de IA es un sistema que utiliza un LLM como "cerebro" para razonar, planificar, usar herramientas y ejecutar acciones de forma autonoma o semi-autonoma para cumplir objetivos. A diferencia de una simple llamada a un LLM, un agente mantiene un ciclo de: percibir -> razonar -> actuar -> observar -> repetir.

Ejemplos: asistentes de codigo (Devin, OpenCode), agentes de atencion al cliente, agentes de investigacion, agentes de automatizacion de tareas.

---

## Principios Clave para Agentes de IA

1. **Ciclo de razonamiento**: el agente itera: recibe entrada, razona (LLM), elige accion, ejecuta herramienta, observa resultado, repite.
2. **Herramientas tipadas**: cada herramienta tiene un schema claro de entrada/salida (function calling).
3. **Memoria**: el agente recuerda el contexto de la conversacion y los resultados de acciones previas.
4. **Planificacion**: el agente descompone tareas complejas en sub-tareas.
5. **Seguridad y limites**: el agente debe tener restricciones claras (sandbox, permisos, budget).
6. **Observabilidad**: registrar cada paso del agente para debugging y auditoria.
7. **Manejo de errores**: si una herramienta falla, el agente debe recuperarse o escalar.

---

## Estructura de Directorios Recomendada

```
mi-agente-ia/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── eval.yml             # Evaluacion del agente
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
├── src/
│   ├── __init__.py
│   ├── main.py                  # CLI o API principal
│   ├── config.py
│   ├── agent/                   # Nucleo del agente
│   │   ├── __init__.py
│   │   ├── core.py              # Bucle principal del agente (reasoning loop)
│   │   ├── state.py             # Maquina de estados del agente
│   │   ├── planner.py           # Planificacion de tareas
│   │   ├── executor.py          # Ejecucion de acciones
│   │   └── reflector.py         # Autoevaluacion y reflexion
│   ├── llm/                     # Capa de LLM (heredado de ia-generativa)
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── openai_client.py
│   │   ├── anthropic_client.py
│   │   └── factory.py
│   ├── tools/                   # Herramientas del agente (function calling)
│   │   ├── __init__.py
│   │   ├── base.py              # Clase base para herramientas
│   │   ├── registry.py          # Registro de herramientas disponibles
│   │   ├── file_system.py       # Leer/escribir archivos
│   │   ├── web_search.py        # Busqueda web
│   │   ├── web_scraper.py       # Extraer contenido web
│   │   ├── code_executor.py     # Ejecutar codigo en sandbox
│   │   ├── terminal.py          # Ejecutar comandos de terminal
│   │   ├── database_query.py    # Consultar base de datos
│   │   ├── api_caller.py        # Llamar APIs externas
│   │   ├── email_sender.py      # Enviar emails
│   │   └── human_input.py       # Pedir input al humano
│   ├── memory/                  # Memoria del agente
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── short_term.py        # Memoria de corto plazo (conversacion actual)
│   │   ├── long_term.py         # Memoria de largo plazo (vector store)
│   │   ├── working.py           # Memoria de trabajo (scratchpad)
│   │   └── entity.py            # Memoria de entidades (personas, lugares)
│   ├── prompts/                 # Prompts del agente
│   │   ├── __init__.py
│   │   ├── system/
│   │   │   ├── base_agent.txt   # Prompt de sistema base
│   │   │   ├── coder_agent.txt  # Prompt para agente programador
│   │   │   ├── researcher.txt   # Prompt para agente investigador
│   │   │   └── assistant.txt    # Prompt para asistente general
│   │   └── reflection/
│   │       └── self_critique.txt # Prompt para autoevaluacion
│   ├── sandbox/                 # Entorno de ejecucion seguro
│   │   ├── __init__.py
│   │   ├── docker_sandbox.py    # Sandbox con Docker
│   │   └── restricted_exec.py   # Exec restringido
│   ├── safety/                  # Seguridad y limites
│   │   ├── __init__.py
│   │   ├── guardrails.py        # Guardrails (NeMo, etc.)
│   │   ├── content_filter.py    # Filtro de contenido
│   │   ├── budget.py            # Control de presupuesto (tokens, $)
│   │   └── permissions.py       # Control de permisos por herramienta
│   ├── observability/           # Monitoreo y tracing
│   │   ├── __init__.py
│   │   ├── tracer.py            # Tracing de pasos del agente
│   │   ├── logger.py            # Logging estructurado
│   │   └── metrics.py           # Metricas (latencia, tasa de exito)
│   ├── api/                     # API REST (si aplica)
│   │   ├── __init__.py
│   │   ├── router.py
│   │   ├── schemas.py
│   │   └── websocket.py         # WebSocket para streaming
│   └── ui/                      # Interfaz (si aplica)
│       ├── streamlit_app.py
│       └── components/
├── tests/
│   ├── conftest.py
│   ├── unit/
│   │   ├── test_agent_core.py
│   │   ├── test_tools.py
│   │   ├── test_memory.py
│   │   └── test_safety.py
│   ├── integration/
│   │   ├── test_agent_e2e.py    # Test end-to-end del agente
│   │   └── test_tool_chains.py
│   └── fixtures/
│       ├── mock_llm.py          # Mock de LLM para tests
│       └── test_tasks.json      # Tareas de prueba
├── evals/                       # Evaluacion del agente
│   ├── tasks/
│   │   ├── coding.json          # Tareas de programacion
│   │   ├── reasoning.json       # Tareas de razonamiento
│   │   ├── web_tasks.json       # Tareas de navegacion web
│   │   └── tool_use.json        # Tareas de uso de herramientas
│   ├── metrics.py               # Metricas de evaluacion
│   ├── runner.py                # Ejecutor de evaluacion
│   └── dashboard.py             # Dashboard de resultados
├── scripts/
│   ├── run_agent.py             # Ejecutar agente desde terminal
│   ├── run_evals.py             # Ejecutar suite de evaluacion
│   └── deploy.sh
└── docs/
    ├── architecture.md
    ├── tools.md                  # Documentacion de herramientas
    └── safety.md                 # Documentacion de seguridad
```

---

## El Bucle del Agente (Reasoning Loop)

El corazon de cualquier agente es su bucle de razonamiento. Implementacion de referencia:

```python
# src/agent/core.py
import asyncio
from dataclasses import dataclass, field
from typing import Any

@dataclass
class AgentStep:
    thought: str           # Razonamiento del LLM
    action: str | None     # Herramienta seleccionada (None = respuesta final)
    action_input: dict     # Parametros para la herramienta
    observation: str       # Resultado de la herramienta
    step_number: int

@dataclass  
class AgentState:
    task: str
    steps: list[AgentStep] = field(default_factory=list)
    final_answer: str | None = None
    max_steps: int = 15
    current_step: int = 0

class Agent:
    def __init__(self, llm, tools, memory, safety):
        self.llm = llm
        self.tools = tools
        self.memory = memory
        self.safety = safety

    async def run(self, task: str) -> str:
        state = AgentState(task=task)
        
        while state.current_step < state.max_steps:
            # 1. Construir contexto (system prompt + historial + herramientas)
            context = self._build_context(state)
            
            # 2. LLM razona y decide accion
            response = await self.llm.generate(context)
            thought, action, action_input = self._parse_response(response)
            
            # 3. Check de seguridad
            self.safety.check(action, action_input)
            
            # 4. Si es respuesta final, terminar
            if action is None or action == "final_answer":
                state.final_answer = thought
                break
            
            # 5. Ejecutar herramienta
            observation = await self.tools.execute(action, **action_input)
            
            # 6. Registrar paso
            step = AgentStep(
                thought=thought,
                action=action,
                action_input=action_input,
                observation=str(observation),
                step_number=state.current_step
            )
            state.steps.append(step)
            
            # 7. Actualizar memoria
            self.memory.add(step)
            
            state.current_step += 1
        
        return state.final_answer or "No pude completar la tarea."

    def _build_context(self, state: AgentState) -> list[dict]:
        system_prompt = self._load_system_prompt()
        tools_schema = self.tools.get_schemas()
        history = self.memory.get_history()
        
        return [
            {"role": "system", "content": system_prompt.format(tools=tools_schema)},
            *history,
            {"role": "user", "content": state.task}
        ]
```

---

## Sistema de Herramientas (Tools / Function Calling)

Las herramientas deben definirse con schemas claros para que el LLM las entienda:

```python
# src/tools/base.py
from abc import ABC, abstractmethod
from typing import Any
from pydantic import BaseModel

class ToolInput(BaseModel):
    """Schema de entrada validado por Pydantic"""
    pass

class BaseTool(ABC):
    name: str
    description: str
    input_schema: type[ToolInput]
    
    @abstractmethod
    async def execute(self, **kwargs) -> str:
        """Ejecuta la herramienta y devuelve resultado como string"""
        ...
    
    def get_schema(self) -> dict:
        """Devuelve schema en formato OpenAI function calling"""
        return {
            "type": "function",
            "function": {
                "name": self.name,
                "description": self.description,
                "parameters": self.input_schema.model_json_schema()
            }
        }

# Ejemplo concreto
class WebSearchInput(ToolInput):
    query: str
    max_results: int = 5

class WebSearchTool(BaseTool):
    name = "web_search"
    description = "Busca en la web usando un motor de busqueda"
    input_schema = WebSearchInput
    
    async def execute(self, query: str, max_results: int = 5) -> str:
        # Implementacion con SerpAPI, Tavily, Brave Search, etc.
        results = await self.search_api.search(query, limit=max_results)
        return self._format_results(results)
```

---

## Sistema de Memoria

Tipos de memoria que un agente necesita:

```
Memoria de Corto Plazo (Short-term)
  └── Conversacion actual, ultimos N mensajes
      └── Implementacion: lista en memoria, Redis

Memoria de Trabajo (Working Memory)  
  └── Notas, resultados intermedios, variables
      └── Implementacion: diccionario en memoria

Memoria de Largo Plazo (Long-term)
  └── Conversaciones pasadas, aprendizajes, preferencias del usuario
      └── Implementacion: vector store + base de datos

Memoria de Entidades (Entity Memory)
  └── Informacion sobre entidades especificas (personas, proyectos)
      └── Implementacion: base de datos relacional + embeddings
```

---

## Seguridad y Limites (Safety)

Un agente autonomo es potencialmente peligroso. Debe tener:

```python
# src/safety/guardrails.py
class AgentGuardrails:
    def __init__(self):
        self.blocked_actions = [
            "delete_database",
            "drop_table",
            "sudo",
            "rm -rf",
            "format",
            "shutdown",
            "curl http://localhost",  # SSRF
        ]
        self.restricted_domains = [
            "localhost",
            "127.0.0.1",
            "internal.corp",
            "169.254.169.254",  # AWS metadata
        ]
        self.max_cost_per_run = 5.0  # USD
        self.max_tokens_per_run = 100_000
    
    def check_action(self, action: str, action_input: dict) -> bool:
        if action in self.blocked_actions:
            raise SafetyViolation(f"Accion bloqueada: {action}")
        # ... mas verificaciones ...
        return True
    
    def check_budget(self, total_cost: float) -> bool:
        if total_cost > self.max_cost_per_run:
            raise BudgetExceeded(f"Presupuesto excedido: ${total_cost}")
        return True
```

---

## Tipos de Agentes

### 1. Agente Simple (ReAct Pattern)

```
Pensar -> Actuar -> Observar -> Pensar -> Actuar -> ... -> Responder
```

### 2. Agente con Planificacion

```
Planificar -> [Ejecutar paso 1 -> Observar] -> [Ejecutar paso 2 -> ...] -> Verificar -> Responder
```

### 3. Agente Multi-agente (Swarm)

```
Orquestador -> delega a -> Agente Especialista 1
                        -> Agente Especialista 2  
                        -> Agente Especialista 3
```

### 4. Agente Reflexivo

```
Actuar -> Autoevaluar -> Si no es bueno -> Criticar -> Mejorar -> Reintentar
```

---

## Estructura para Multi-Agente

```
mi-sistema-multiagente/
├── src/
│   ├── orchestrator.py         # Orquestador central
│   ├── agents/
│   │   ├── base_agent.py
│   │   ├── researcher/         # Agente investigador
│   │   ├── coder/              # Agente programador
│   │   ├── reviewer/           # Agente revisor
│   │   ├── planner/            # Agente planificador
│   │   └── communicator/       # Agente comunicador
│   ├── communication/          # Comunicacion entre agentes
│   │   ├── message_bus.py
│   │   └── protocols.py
│   └── shared/                 # Recursos compartidos
│       ├── memory/
│       └── knowledge_base/
```

---

## Evaluacion de Agentes

Evaluar un agente es mas complejo que evaluar una funcion tradicional:

```python
# evals/runner.py
class AgentEvaluator:
    def __init__(self, agent, test_tasks: list[dict]):
        self.agent = agent
        self.test_tasks = test_tasks
    
    async def evaluate(self) -> dict:
        results = []
        for task in self.test_tasks:
            result = await self.agent.run(task["input"])
            
            score = self._score(task["expected_output"], result)
            results.append({
                "task_id": task["id"],
                "task": task["input"],
                "expected": task["expected_output"],
                "actual": result,
                "score": score,
                "steps_taken": len(self.agent.state.steps),
                "tools_used": [s.action for s in self.agent.state.steps],
                "total_cost": self.agent.cost_tracker.total_cost,
                "success": score >= task.get("threshold", 0.7)
            })
        
        return {
            "total_tasks": len(results),
            "successful": sum(1 for r in results if r["success"]),
            "avg_score": sum(r["score"] for r in results) / len(results),
            "details": results
        }
```

---

## Referencias Cruzadas

- [IA Generativa y LLMs](./ia-generativa.md) -- capa base de LLMs
- [LLM Ops](./llm-ops.md) -- operaciones y despliegue
- [MLOps](./ml-ops.md) -- practicas de MLOps aplicables
- [Guia de Vibe Coding](../docs/guia-vibe-coding.md) -- flujos con IA
- [Archivos IA Esenciales](../docs/archivos-ia-esenciales.md)
- [Ejemplo: Proyecto Agente IA](../ejemplos/agente-ia-proyecto.md)

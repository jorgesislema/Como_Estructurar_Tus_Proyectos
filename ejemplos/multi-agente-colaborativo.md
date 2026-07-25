# Ejemplo: Sistema Multi-Agente Colaborativo

Ejemplo de un sistema donde multiples agentes de IA especializados colaboran para resolver tareas complejas. Un orquestador descompone la tarea, asigna sub-tareas a agentes especialistas, y sintetiza los resultados.

Proyecto de ejemplo: equipo de desarrollo de software con IA donde un agente escribe codigo, otro lo revisa, y un tercero escribe los tests.

---

## Arbol de Carpetas Completo

```bash
devteam-ia/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── eval-agent.yml
├── .cursor/
│   └── rules
├── .gitignore
├── .env.example
├── README.md
├── RULES.md
├── CONTEXTO.md
├── pyproject.toml
├── Dockerfile
├── docker-compose.yml
├── Makefile
├── src/
│   ├── __init__.py
│   ├── main.py                       # CLI o API principal
│   ├── config.py
│   ├── orchestrator/                  # Orquestador central
│   │   ├── __init__.py
│   │   ├── task_decomposer.py        # Descompone tareas complejas en sub-tareas
│   │   ├── agent_dispatcher.py       # Asigna sub-tareas a agentes especialistas
│   │   ├── result_synthesizer.py     # Sintetiza resultados parciales
│   │   └── workflow_manager.py       # Gestiona dependencias entre sub-tareas
│   ├── agents/                        # Agentes especialistas
│   │   ├── __init__.py
│   │   ├── base_agent.py             # Clase base para todos los agentes
│   │   ├── architect/                # Agente arquitecto
│   │   │   ├── __init__.py
│   │   │   ├── agent.py
│   │   │   └── prompts/
│   │   │       └── architect_system.txt
│   │   ├── developer/                # Agente desarrollador
│   │   │   ├── __init__.py
│   │   │   ├── agent.py
│   │   │   └── prompts/
│   │   │       └── developer_system.txt
│   │   ├── reviewer/                 # Agente revisor de codigo
│   │   │   ├── __init__.py
│   │   │   ├── agent.py
│   │   │   └── prompts/
│   │   │       └── reviewer_system.txt
│   │   └── tester/                   # Agente de testing
│   │       ├── __init__.py
│   │       ├── agent.py
│   │       └── prompts/
│   │           └── tester_system.txt
│   ├── communication/                 # Comunicacion entre agentes
│   │   ├── __init__.py
│   │   ├── message_bus.py            # Bus de mensajes central (Redis pub/sub o in-memory)
│   │   ├── message.py                # Estructura de mensajes entre agentes
│   │   ├── protocols.py              # Protocolos de comunicacion
│   │   └── handoffs.py              # Transferencia de tareas entre agentes
│   ├── shared/                        # Recursos compartidos
│   │   ├── __init__.py
│   │   ├── blackboard.py             # Pizarra compartida (memoria del equipo)
│   │   ├── code_repository.py        # Repositorio de codigo (archivos, git)
│   │   ├── task_queue.py             # Cola de tareas pendientes
│   │   └── knowledge_base.py         # Base de conocimiento compartida
│   ├── llm/                           # Capa LLM (compartida por todos los agentes)
│   │   ├── __init__.py
│   │   └── client.py
│   └── observability/
│       ├── __init__.py
│       ├── agent_tracer.py           # Trazas de actividad de cada agente
│       ├── team_metrics.py           # Metricas de equipo (velocidad, calidad)
│       └── dashboard.py              # Dashboard de actividad del equipo
├── tests/
│   ├── conftest.py
│   ├── unit/
│   │   ├── test_task_decomposer.py
│   │   ├── test_agent_dispatcher.py
│   │   └── test_message_bus.py
│   └── integration/
│       ├── test_agent_communication.py
│       ├── test_full_workflow.py      # Test e2e: tarea -> codigo + tests + review
│       └── test_handoff.py
├── evals/
│   ├── tasks/
│   │   ├── simple_feature.json        # Feature simple
│   │   ├── complex_refactor.json      # Refactor complejo
│   │   └── bug_fix.json              # Correccion de bug
│   ├── metrics.py
│   └── runner.py
├── sandbox/                           # Sandbox Docker para ejecutar codigo
│   ├── Dockerfile.sandbox
│   └── executor.py
├── scripts/
│   ├── run_devteam.py                 # Ejecutar el equipo en una tarea
│   └── run_evals.py
└── docs/
    ├── agents.md                      # Descripcion de cada agente
    ├── communication_protocol.md      # Protocolo de comunicacion
    └── workflows.md                   # Flujos de trabajo del equipo
```

---

## Arquitectura del Equipo

```
[Usuario] -> "Crea un endpoint de usuarios con FastAPI"
    |
[Orquestador - Task Decomposer]
    ├── Sub-tarea 1: Disenar el modelo de datos y API schema
    ├── Sub-tarea 2: Implementar el endpoint con validacion
    ├── Sub-tarea 3: Escribir tests unitarios y de integracion
    └── Sub-tarea 4: Revisar codigo final
    |
[Agent Dispatcher]
    ├── Sub-tarea 1 -> Agente Arquitecto
    ├── Sub-tarea 2 -> Agente Desarrollador
    ├── Sub-tarea 3 -> Agente Tester
    └── Sub-tarea 4 -> Agente Revisor
    |
[Todos los agentes comparten Blackboard]
    ├── Arquitecto escribe: schema propuesto en la pizarra
    ├── Desarrollador lee: schema de la pizarra, escribe: implementacion
    ├── Tester lee: implementacion, escribe: tests y cobertura
    └── Revisor lee: implementacion + tests, escribe: feedback
    |
[Result Synthesizer]
    └── Compila todos los resultados en un PR o respuesta final
```

---

## Componentes Clave

### 1. Orquestador (`src/orchestrator/`)

El orquestador no es un agente mas: es el director del equipo.

```python
# Estructura conceptual del orquestador
class Orchestrator:
    def __init__(self, agents: dict[str, BaseAgent], blackboard: Blackboard):
        self.agents = agents
        self.blackboard = blackboard
        self.decomposer = TaskDecomposer()
        self.dispatcher = AgentDispatcher(agents)
        self.synthesizer = ResultSynthesizer()
    
    async def execute(self, task: str) -> TeamResult:
        # 1. Descomponer tarea compleja en sub-tareas
        sub_tasks = await self.decomposer.decompose(task)
        
        # 2. Identificar dependencias entre sub-tareas
        workflow = self._build_workflow(sub_tasks)
        
        # 3. Ejecutar sub-tareas respetando dependencias
        results = {}
        for stage in workflow.stages:
            # Las tareas en el mismo stage pueden ejecutarse en paralelo
            stage_results = await asyncio.gather(*[
                self.dispatcher.dispatch(task, context=results)
                for task in stage.tasks
            ])
        
        # 4. Sintetizar resultado final
        return await self.synthesizer.synthesize(results, task)
```

### 2. Agentes Especializados

Cada agente tiene su propio system prompt optimizado para su rol:

**Agente Arquitecto:** disena schemas, modelos de datos, estructura de archivos, patrones a usar. No escribe implementacion.

**Agente Desarrollador:** implementa codigo siguiendo el diseno del arquitecto. Escribe codigo funcional, con tipos, manejo de errores.

**Agente Tester:** escribe tests basados en la implementacion. Cubre casos felices, casos borde, errores esperados.

**Agente Revisor:** revisa el codigo contra RULES.md y estandares del proyecto. Senala bugs, mejoras, violaciones de estandar.

### 3. Pizarra Compartida (`src/shared/blackboard.py`)

La pizarra es el espacio de memoria compartida donde los agentes leen y escriben:

```python
# Estructura conceptual de la pizarra
class Blackboard:
    def __init__(self):
        self.entries: dict[str, BlackboardEntry] = {}
        self.subscribers: dict[str, list[callable]] = {}
    
    async def write(self, agent_id: str, key: str, data: Any):
        """Un agente escribe conocimiento en la pizarra"""
        entry = BlackboardEntry(
            author=agent_id,
            key=key,
            data=data,
            timestamp=datetime.now()
        )
        self.entries[key] = entry
        # Notificar a suscriptores
        await self._notify(key, entry)
    
    async def read(self, key: str) -> BlackboardEntry | None:
        """Un agente lee de la pizarra"""
        return self.entries.get(key)
    
    async def subscribe(self, pattern: str, callback: callable):
        """Suscribirse a cambios en una clave o patron"""
        ...
```

Ejemplo de uso:
```
Arquitecto: write("design/api_schema", {...})
Desarrollador: read("design/api_schema") -> espera a que exista o recibe notificacion
Desarrollador: write("implementation/user_endpoint.py", codigo)
Tester: read("implementation/user_endpoint.py") -> escribe tests
Revisor: read("implementation/") y read("tests/") -> escribe feedback
```

### 4. Comunicacion entre Agentes (`src/communication/`)

**Mensajes:**
```python
@dataclass
class AgentMessage:
    id: str
    sender: str            # Agent ID
    receiver: str           # Agent ID (o "broadcast")
    type: str               # "task_assignment", "query", "response", "handoff", "broadcast"
    payload: Any
    in_reply_to: str | None # ID del mensaje al que responde
    timestamp: datetime

class MessageBus:
    async def send(self, message: AgentMessage): ...
    async def receive(self, agent_id: str) -> AsyncIterator[AgentMessage]: ...
    async def broadcast(self, sender: str, message_type: str, payload: Any): ...
```

**Handoffs (transferencia de tareas):**
Cuando un agente necesita pasar el control a otro:
```
Desarrollador: "Termine la implementacion, handoff a Tester"
Tester: "Recibido, empezando tests"
```

**Patrones de comunicacion:**
- Directa: agente A envia a agente B
- Broadcast: un agente notifica a todos
- Suscripcion: un agente se suscribe a eventos de otro
- Request-Response: un agente pregunta y espera respuesta

### 5. Metricas de Equipo (`src/observability/team_metrics.py`)

```
Metricas por agente:
├── Tareas completadas
├── Tiempo promedio por tarea
├── Tasa de revisiones aceptadas (desarrollador)
├── Bugs encontrados post-revision (revisor)
├── Cobertura de tests generada (tester)

Metricas de equipo:
├── Tiempo total para completar una tarea compleja
├── Tasa de re-trabajo (cuantas veces se rechaza codigo)
├── Cuellos de botella (que agente es el mas lento)
├── Calidad final (bugs encontrados en tests E2E)
```

---

## Flujo de una Tarea Completa

```
Tarea: "Agregar autenticacion JWT al proyecto FastAPI existente"

[Orquestador descompone]
Sub-tareas detectadas:
  1. Disenar modelo de usuario y schema de auth (Arquitecto)
  2. Implementar endpoint de login y registro (Desarrollador)
  3. Implementar middleware de autenticacion JWT (Desarrollador)
  4. Escribir tests de auth (Tester) -- depende de 2 y 3
  5. Revisar todo el codigo (Revisor) -- depende de 4

[Stage 1 - Paralelo]
  Agente Arquitecto:
    - Lee CONTEXTO.md del proyecto
    - Disena User model, LoginRequest, TokenResponse schemas
    - Escribe en Blackboard: "design/auth_schema"
  
[Stage 2 - Secuencial]
  Agente Desarrollador:
    - Lee "design/auth_schema" de la pizarra
    - Implementa auth.py, security.py, dependencies.py
    - Escribe en Blackboard: "implementation/auth/"
    - Notifica: "implementacion lista para tests"
  
[Stage 3 - Paralelo]
  Agente Tester:
    - Lee "implementation/auth/" de la pizarra
    - Escribe test_auth.py con casos: login exitoso, credenciales incorrectas, token expirado, ruta protegida sin token
    - Ejecuta tests en sandbox Docker
    - Reporta cobertura
    - Escribe en Blackboard: "tests/auth/"
  
  Agente Revisor (se activa al recibir notificacion):
    - Lee implementacion + tests
    - Verifica contra RULES.md
    - Encuentra: falta validacion de email unico en registro
    - Escribe feedback en Blackboard: "review/auth_feedback"
  
[Stage 4 - Correccion]
  Agente Desarrollador:
    - Lee feedback del revisor
    - Agrega validacion de email unico
    - Actualiza implementacion
    - Notifica al revisor
  
[Stage 5 - Verificacion final]
  Agente Revisor:
    - Revisa cambios
    - APRUEBA
    - Marca tarea como completada

[Result Synthesizer]
  - Compila: schema + implementacion + tests + resultado de revision
  - Genera resumen: "Auth JWT implementada. 3 endpoints. 12 tests. Cobertura 94%. Revisada y aprobada."
```

---

## Prompt Para Iniciar el Proyecto

```
Quiero construir un sistema multi-agente donde un equipo de agentes de IA colabore en tareas de desarrollo de software.

Agentes requeridos:
1. ARQUITECTO: disena schemas, modelos, estructura. Lee CONTEXTO.md. No escribe implementacion.
2. DESARROLLADOR: implementa codigo siguiendo diseno del arquitecto y RULES.md.
3. TESTER: escribe tests unitarios y de integracion basados en la implementacion.
4. REVISOR: revisa codigo contra estandares. Encuentra bugs, problemas de estilo, violaciones.

Componentes requeridos:
- ORQUESTADOR: descompone tareas complejas en sub-tareas, maneja dependencias, asigna agentes
- PIZARRA COMPARTIDA: memoria comun donde agentes leen/escriben artefactos
- BUS DE MENSAJES: comunicacion async entre agentes (directa, broadcast, request-response)
- HANDOFFS: transferencia formal de tareas entre agentes
- SANDBOX: entorno aislado para ejecutar codigo y tests
- OBSERVABILIDAD: trazas de cada agente, metricas de equipo, dashboard

Stack: Python 3.12, asyncio, Redis (pub/sub), Docker (sandbox), OpenAI/Anthropic API.

Estructura: src/orchestrator/, src/agents/{architect,developer,reviewer,tester}/, src/communication/, src/shared/, src/observability/

Cada agente tiene su propio system prompt optimizado para su rol. La pizarra usa un patron pub/sub. El bus de mensajes puede ser Redis o in-memory para desarrollo.
```

---

## Referencias Cruzadas

- [Agentes IA](../por-dominio/agentes-ia.md) -- patrones de agentes individuales
- [IA Generativa](../por-dominio/ia-generativa.md) -- capa base de LLMs
- [Ejemplo: Agente IA](./agente-ia-proyecto.md) -- agente individual con ReAct
- [Guia de Vibe Coding](../docs/guia-vibe-coding.md) -- niveles de desarrollo con IA

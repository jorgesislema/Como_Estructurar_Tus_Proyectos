# Ejemplo: Microservicio de IA Profesional (Streaming, Rate Limiting, Fallback, Cache)

Ejemplo de un microservicio de IA listo para produccion que combina todos los concerns criticos: streaming SSE, rate limiting por usuario, circuit breaker, multi-proveedor fallback, cache semantica, autenticacion y observabilidad.

---

## Arbol de Carpetas Completo

```bash
ai-gateway/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── cd.yml
│       └── load-test.yml
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
│   ├── main.py                       # FastAPI app factory
│   ├── config.py                     # Pydantic Settings
│   ├── api/
│   │   ├── __init__.py
│   │   ├── v1/
│   │   │   ├── __init__.py
│   │   │   ├── router.py
│   │   │   ├── chat.py              # Endpoint de chat (streaming y no-streaming)
│   │   │   ├── embeddings.py        # Endpoint de embeddings
│   │   │   └── health.py            # Health check + readiness
│   │   ├── deps.py                  # Dependencias FastAPI (auth, rate limit)
│   │   ├── middleware.py            # Middleware HTTP global
│   │   └── schemas.py               # Request/Response schemas
│   ├── core/
│   │   ├── __init__.py
│   │   ├── auth.py                  # API key validation, JWT
│   │   ├── rate_limiter.py          # Sliding window rate limiter (Redis)
│   │   ├── circuit_breaker.py       # Circuit breaker pattern
│   │   ├── exceptions.py            # Excepciones personalizadas
│   │   └── events.py                # Startup/shutdown events
│   ├── providers/                    # Multi-proveedor LLM
│   │   ├── __init__.py
│   │   ├── base.py                  # Interfaz abstracta
│   │   ├── openai_provider.py       # OpenAI (GPT-4o, GPT-4o-mini)
│   │   ├── anthropic_provider.py    # Anthropic (Claude)
│   │   ├── google_provider.py       # Google (Gemini)
│   │   ├── azure_provider.py        # Azure OpenAI
│   │   ├── deepseek_provider.py     # DeepSeek
│   │   ├── groq_provider.py         # Groq (rapido, barato)
│   │   ├── together_provider.py     # Together AI
│   │   └── factory.py               # Provider factory
│   ├── gateway/                      # API Gateway interno
│   │   ├── __init__.py
│   │   ├── router.py                # Enrutamiento inteligente
│   │   ├── load_balancer.py         # Balanceo entre providers
│   │   ├── fallback_chain.py        # Cadena de fallback
│   │   └── cost_tracker.py          # Tracking de costos en tiempo real
│   ├── streaming/                    # Streaming SSE
│   │   ├── __init__.py
│   │   ├── sse_handler.py           # Server-Sent Events handler
│   │   ├── chunk_processor.py       # Procesamiento de chunks
│   │   └── connection_manager.py    # Gestion de conexiones activas
│   ├── cache/                        # Cache de respuestas
│   │   ├── __init__.py
│   │   ├── exact_cache.py           # Cache exacta (Redis)
│   │   ├── semantic_cache.py        # Cache semantica (Redis + embeddings)
│   │   ├── cache_policy.py          # Politicas de cache (TTL, invalidation)
│   │   └── cache_metrics.py         # Metricas de cache (hit rate, etc.)
│   ├── monitoring/                   # Observabilidad
│   │   ├── __init__.py
│   │   ├── metrics.py               # Prometheus metrics
│   │   ├── tracing.py               # OpenTelemetry tracing
│   │   ├── logging_middleware.py     # Logging estructurado de requests
│   │   └── health_checks.py         # Health checks
│   ├── guardrails/                   # Seguridad de contenido
│   │   ├── __init__.py
│   │   ├── input_guard.py           # Filtro de inputs
│   │   ├── output_guard.py          # Filtro de outputs
│   │   └── pii_detector.py          # Deteccion de PII
│   └── prompts/                      # Prompts del sistema
│       ├── __init__.py
│       └── templates/
│           └── default_assistant.txt
├── tests/
│   ├── conftest.py
│   ├── unit/
│   │   ├── test_rate_limiter.py
│   │   ├── test_circuit_breaker.py
│   │   ├── test_fallback_chain.py
│   │   └── test_semantic_cache.py
│   ├── integration/
│   │   ├── test_chat_endpoint.py
│   │   ├── test_streaming.py
│   │   └── test_e2e.py
│   └── load/
│       └── locustfile.py             # Pruebas de carga con Locust
├── deployment/
│   ├── docker/
│   │   └── Dockerfile.prod
│   ├── kubernetes/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   ├── hpa.yaml                 # Horizontal Pod Autoscaler
│   │   └── configmap.yaml
│   └── terraform/
│       └── main.tf
├── monitoring/
│   ├── grafana/
│   │   └── dashboards/
│   │       ├── overview.json
│   │       ├── costs.json
│   │       └── llm_quality.json
│   └── prometheus/
│       └── rules.yml
├── docs/
│   ├── architecture.md
│   ├── api.md                        # Documentacion de API
│   ├── rate_limiting.md
│   └── failover_strategy.md
├── scripts/
│   ├── generate_api_key.py
│   ├── run_load_test.sh
│   └── cost_report.py
└── configs/
    ├── providers.yaml                # Config de providers y prioridades
    ├── rate_limits.yaml              # Limites por tier de usuario
    └── fallback_chains.yaml          # Cadenas de fallback
```

---

## Componentes Clave

### 1. Rate Limiter (`src/core/rate_limiter.py`)

Sliding window en Redis por usuario y por endpoint:

```yaml
# configs/rate_limits.yaml
rate_limits:
  free:
    chat:
      requests_per_minute: 10
      tokens_per_day: 10000
    embeddings:
      requests_per_minute: 5
  pro:
    chat:
      requests_per_minute: 100
      tokens_per_day: 1000000
    embeddings:
      requests_per_minute: 50
  enterprise:
    chat:
      requests_per_minute: 1000
      tokens_per_day: 10000000
```

Estrategia: sliding window log en Redis. Cada request registra un timestamp. Se cuentan los requests en la ventana de tiempo actual. Si excede el limite, se devuelve HTTP 429 con header `Retry-After`.

### 2. Circuit Breaker (`src/core/circuit_breaker.py`)

Estados del circuit breaker por proveedor:

```
[CERRADO] -> llamadas normales
    |
    | Si tasa de errores > umbral en ventana de tiempo
    v
[ABIERTO] -> rechaza todas las llamadas (fast fail)
    |
    | Despues de timeout (ej: 30s)
    v
[SEMI-ABIERTO] -> permite algunas llamadas de prueba
    |
    ├── Si tienen exito -> vuelve a CERRADO
    └── Si fallan -> vuelve a ABIERTO
```

Configuracion tipica por proveedor:
- failure_threshold: 5 errores en 60s
- success_threshold: 3 exitos consecutivos en semi-abierto
- timeout: 30s en estado abierto

### 3. Cadena de Fallback (`src/gateway/fallback_chain.py`)

Orden de intentos cuando un proveedor falla:

```yaml
# configs/fallback_chains.yaml
chat_completion:
  primary: [openai, gpt-4o]
  fallback_chain:
    - [anthropic, claude-sonnet-4-20250514]
    - [openai, gpt-4o-mini]
    - [groq, llama-3.1-70b]
    - [together, mixtral-8x7b]

embeddings:
  primary: [openai, text-embedding-3-small]
  fallback_chain:
    - [cohere, embed-multilingual-v3]
    - [huggingface, sentence-transformers-local]
```

### 4. Streaming SSE (`src/streaming/sse_handler.py`)

Streaming de respuestas token por token via Server-Sent Events:

```
Cliente: POST /api/v1/chat/stream
Body: {"messages": [...], "stream": true}

Servidor: (EventSource connection)
data: {"type": "token", "content": "La"}
data: {"type": "token", "content": " configuracion"}
data: {"type": "token", "content": " de"}
data: {"type": "token", "content": " rate"}
...
data: {"type": "done", "usage": {"tokens": 150, "cost": 0.003}}
data: {"type": "sources", "documents": ["doc_1", "doc_3"]}
```

Manejo de desconexion: si el cliente cierra la conexion, se cancela la generacion y se registra el uso parcial.

### 5. Cache Semantica (`src/cache/semantic_cache.py`)

Dos niveles de cache:

**Cache Exacta (Redis):**
Para prompts identicos (mismo system prompt + mismos mensajes). TTL: 1 hora.

**Cache Semantica (Redis + Embeddings):**
Para preguntas semanticamente similares. El query del usuario se convierte a embedding y se busca en Redis. Si similitud coseno > 0.95, se devuelve respuesta cacheada.

```
Flujo de cache:
1. Usuario hace pregunta
2. Verificar cache exacta (hash del prompt completo)
   ├── Hit -> devolver respuesta cacheada (latencia: ~5ms)
   └── Miss -> continuar
3. Verificar cache semantica (embedding similarity)
   ├── Hit -> devolver respuesta cacheada (latencia: ~20ms)
   └── Miss -> llamar al LLM
4. LLM genera respuesta
5. Almacenar en ambos caches
6. Devolver respuesta al usuario
```

### 6. Observabilidad Integrada (`src/monitoring/`)

Metricas Prometheus expuestas en `/metrics`:

```
# Latencia
llm_request_duration_seconds{provider, model, status}  # histogram
llm_time_to_first_token_seconds{provider, model}       # histogram

# Trafico
llm_requests_total{provider, model, status}             # counter
llm_tokens_total{provider, model, type}                 # counter (input/output)

# Costos
llm_cost_usd_total{provider, model}                     # counter

# Cache
llm_cache_hits_total{cache_type}                        # counter
llm_cache_misses_total{cache_type}                      # counter

# Rate Limiting
llm_rate_limited_requests_total{user_tier}              # counter

# Circuit Breaker
llm_circuit_breaker_state{provider}                     # gauge (0=closed, 1=open, 2=half-open)

# Errores
llm_errors_total{provider, error_type}                  # counter
```

---

## Flujo de una Request Completa

```
1. Cliente envia POST /api/v1/chat
   Headers: Authorization: Bearer sk-xxx

2. Middleware de autenticacion
   └── Valida API key contra Redis/DB
   └── Identifica tier del usuario (free/pro/enterprise)

3. Rate Limiter
   └── Verifica cuota del usuario
   └── Si excede -> 429 Too Many Requests

4. Guardrails de Input
   └── Detecta PII, jailbreak, contenido prohibido
   └── Si detecta -> 400 Bad Request

5. Cache (antes de llamar al LLM)
   ├── Cache exacta: hit? -> devolver inmediatamente
   └── Cache semantica: hit? -> devolver inmediatamente

6. Seleccion de Provider
   └── Segun modelo solicitado, disponibilidad, costo
   └── Circuit breaker: proveedor primario sano?

7. Llamada al LLM (con tracing)
   ├── Exito -> respuesta
   └── Error -> fallback chain (siguiente proveedor)
       ├── Exito -> respuesta
       └── Error -> 503 Service Unavailable

8. Guardrails de Output
   └── Filtra contenido inseguro en la respuesta

9. Actualizar Cache
   └── Almacenar en cache exacta + semantica

10. Registrar metricas y costos
    └── Prometheus metrics, logs, cost tracker

11. Devolver respuesta al cliente
    ├── No-streaming: JSON completo
    └── Streaming: SSE token por token
```

---

## Configuracion de Entorno

```bash
# .env.example

# API Keys de LLMs
OPENAI_API_KEY=sk-proj-...
ANTHROPIC_API_KEY=sk-ant-...
GEMINI_API_KEY=AIza...
AZURE_OPENAI_API_KEY=...
AZURE_OPENAI_ENDPOINT=https://...
DEEPSEEK_API_KEY=sk-...
GROQ_API_KEY=gsk_...
TOGETHER_API_KEY=...

# Redis (rate limiting + cache)
REDIS_URL=redis://localhost:6379

# Base de datos (API keys, usuarios)
DATABASE_URL=postgresql://user:pass@localhost:5432/ai_gateway

# Auth
SECRET_KEY=clave-secreta-para-firmar-tokens
ADMIN_API_KEY=sk-admin-...

# Observabilidad
SENTRY_DSN=https://...@sentry.io/...
PROMETHEUS_PORT=9090

# Rate Limiting (defaults)
DEFAULT_RATE_LIMIT_RPM=60
DEFAULT_RATE_LIMIT_TPD=100000

# Circuit Breaker
CIRCUIT_BREAKER_FAILURE_THRESHOLD=5
CIRCUIT_BREAKER_TIMEOUT_SECONDS=30
```

---

## Referencias Cruzadas

- [LLM Ops](../por-dominio/llm-ops.md) -- operaciones y gateway
- [IA Generativa](../por-dominio/ia-generativa.md) -- capa base de LLMs
- [Observabilidad](../docs/observabilidad.md) -- metricas, logs, alertas
- [Networking y Seguridad](../docs/networking-seguridad.md) -- rate limiting, auth

# Ejemplo: Despliegue Local de LLMs con Ollama

Guia practica para ejecutar modelos de lenguaje localmente usando Ollama. Cubre instalacion, seleccion de modelos, configuracion de GPU, estimacion de recursos, integracion con aplicaciones y comparacion de costos vs API cloud.

---

## Que es Ollama

Ollama es una herramienta para ejecutar modelos de lenguaje grandes (LLMs) localmente en tu computadora o servidor. Soporta modelos como Llama 3.1, Mistral, Gemma, Phi, DeepSeek, Qwen y muchos mas. No requiere conexion a internet para funcionar.

## Cuando Usar Ollama vs API Cloud

| Criterio | Ollama (Local) | API Cloud (OpenAI, etc.) |
|----------|---------------|--------------------------|
| **Costo** | Solo electricidad + hardware | Pago por token |
| **Privacidad** | Datos nunca salen de tu maquina | Datos se envian al proveedor |
| **Latencia** | Depende de tu GPU | Depende del proveedor y ubicacion |
| **Calidad** | Modelos open-source (buenos, no top) | Mejores modelos (GPT-4, Claude) |
| **Disponibilidad** | 24/7 sin depender de terceros | Depende del servicio cloud |
| **Configuracion** | Requiere setup de hardware | Solo API key |
| **Escala** | Limitada a tu hardware | Escala bajo demanda |

**Casos de uso ideales para Ollama:**
- Desarrollo y prototipado (sin costo de API)
- Procesamiento de datos sensibles (privacidad)
- Tareas simples que no requieren GPT-4 (clasificacion, extraccion, resumen)
- Agentes que hacen muchas llamadas (evitar costos acumulativos)
- Entornos sin internet o con conectividad limitada
- Fine-tuning y experimentacion con modelos open-source

---

## Estructura del Proyecto con Ollama

```bash
mi-proyecto-ollama/
├── .gitignore
├── .env.example
├── README.md
├── RULES.md
├── pyproject.toml
├── docker-compose.yml               # Ollama + Open WebUI + tu app
├── Modelfile                        # Definicion de modelo personalizado
├── src/
│   ├── __init__.py
│   ├── config.py
│   ├── llm/
│   │   ├── __init__.py
│   │   ├── base.py                  # Interfaz abstracta
│   │   ├── ollama_client.py         # Cliente Ollama (API HTTP local)
│   │   ├── openai_client.py         # Cliente OpenAI (cloud)
│   │   └── router.py                # Enrutador: local vs cloud segun tarea
│   ├── models/                      # Configuracion y scripts de modelos
│   │   ├── download.py              # Descargar modelos
│   │   ├── benchmark.py             # Benchmark de velocidad/calidad
│   │   └── model_configs.yaml       # Configs por modelo
│   └── app/
│       ├── main.py                  # Tu aplicacion
│       └── rag.py                   # RAG con embeddings locales (nomic-embed-text)
├── scripts/
│   ├── setup_ollama.sh              # Instalacion y descarga de modelos
│   ├── benchmark_models.py          # Comparar modelos en tu hardware
│   └── cost_comparison.py           # Costo local vs cloud
├── notebooks/
│   └── model_comparison.ipynb       # Pruebas de calidad de modelos
├── configs/
│   └── models.yaml                  # Que modelos usar para cada tarea
└── docs/
    ├── setup.md                     # Guia de instalacion
    └── model_selection.md           # Criterios de seleccion de modelos
```

---

## Instalacion y Setup

### Instalacion de Ollama

```bash
# Linux / WSL
curl -fsSL https://ollama.com/install.sh | sh

# macOS
# Descargar de https://ollama.com/download

# Windows (Preview)
# Descargar de https://ollama.com/download/windows

# Docker (recomendado para servidores)
docker run -d --gpus all -v ollama:/root/.ollama \
  -p 11434:11434 --name ollama ollama/ollama
```

### Docker Compose (Ollama + Open WebUI + PostgreSQL)

```yaml
# docker-compose.yml
services:
  ollama:
    image: ollama/ollama:latest
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    restart: unless-stopped

  open-webui:
    image: ghcr.io/open-webui/open-webui:main
    ports:
      - "3000:8080"
    environment:
      - OLLAMA_BASE_URL=http://ollama:11434
    volumes:
      - open_webui_data:/app/backend/data
    depends_on:
      - ollama
    restart: unless-stopped

  db:
    image: pgvector/pgvector:pg16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secure_password
      POSTGRES_DB: app_db
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    restart: unless-stopped

volumes:
  ollama_data:
  open_webui_data:
  pgdata:
```

### Descarga de Modelos

```bash
# Modelos esenciales para desarrollo
ollama pull llama3.1:8b           # ~4.7GB, buen balance calidad/velocidad
ollama pull llama3.1:70b          # ~40GB, alta calidad (necesita GPU grande)
ollama pull mistral:7b            # ~4.1GB, bueno para codigo
ollama pull gemma2:9b             # ~5.4GB, alternativa a Llama
ollama pull phi3:mini             # ~2.2GB, muy rapido, tareas simples
ollama pull deepseek-coder-v2     # Bueno para generacion de codigo
ollama pull nomic-embed-text      # ~274MB, embeddings locales gratuitos
ollama pull mxbai-embed-large     # ~669MB, embeddings de alta calidad

# Ver modelos instalados
ollama list

# Probar modelo
ollama run llama3.1:8b "Explica que es RAG en una frase"
```

---

## Seleccion de Modelos por Tarea y Hardware

### Que Modelo Elegir segun tu GPU

| GPU VRAM | Modelos viables (Q4_K_M quantization) |
|----------|--------------------------------------|
| 6 GB (GTX 1060, RTX 2060) | Gemma 2B, Phi-3 mini, Llama 3.2 3B |
| 8 GB (RTX 2070, RTX 3070) | Llama 3.1 8B, Mistral 7B, Gemma 2 9B |
| 12 GB (RTX 3080, RTX 4070) | Llama 3.1 8B, Qwen 2.5 14B |
| 16 GB (RTX 4080) | Llama 3.1 8B, Mixtral 8x7B (~13B activo) |
| 24 GB (RTX 3090, RTX 4090) | Llama 3.1 70B (Q3), Mixtral 8x22B |
| 48 GB (A6000, 2x RTX 3090) | Llama 3.1 70B (Q4), Command R+ |
| 80 GB (A100) | Llama 3.1 70B (Q8), modelos grandes |

### Que Modelo Elegir segun la Tarea

| Tarea | Modelo recomendado | Alternativa cloud |
|-------|-------------------|-------------------|
| Chat general | Llama 3.1 8B | GPT-4o-mini |
| Generacion de codigo | DeepSeek Coder V2 | GPT-4o / Claude |
| Razonamiento complejo | Llama 3.1 70B | GPT-4o / Claude Opus |
| Clasificacion / extraccion | Phi-3 mini | GPT-4o-mini |
| Resumen de texto | Mistral 7B | GPT-4o-mini |
| Embeddings (gratis) | nomic-embed-text | text-embedding-3-small |
| RAG economico | Llama 3.1 8B + nomic-embed-text | GPT-4o-mini + OpenAI embeddings |
| Tareas simples/agentes | Phi-3 mini o Gemma 2B | GPT-4o-mini |

---

## Comparacion de Costos: Local vs Cloud

Ejemplo: Procesar 1000 documentos para RAG (embeddings + Q&A).

| Componente | Ollama Local | OpenAI API |
|-----------|-------------|------------|
| Embeddings (1000 docs, ~1M tokens) | $0 (nomic-embed-text) | $0.02 (text-embedding-3-small) |
| Q&A (100 preguntas/dia, 30 dias) | $0 | $15 (GPT-4o-mini) |
| Hardware (GPU 24/7) | ~$50/mes electricidad + GPU amortizada | $0 |
| Mantenimiento | Tu tiempo | $0 |
| **Total mensual** | **~$50 fijo** | **~$15 variable** |

Conclusion: para uso bajo/moderado, la API es mas barata. Para uso intensivo (1000+ llamadas/dia), Ollama puede ser mas economico. La decision real suele ser por privacidad o independencia, no por costo.

---

## Configuracion de Modelo Personalizado (Modelfile)

```dockerfile
# Modelfile - Modelo personalizado para tu dominio

FROM llama3.1:8b

# Temperatura baja para respuestas mas deterministicas
PARAMETER temperature 0.3

# Contexto maximo (en tokens)
PARAMETER num_ctx 4096

# System prompt personalizado
SYSTEM """
Eres un asistente especializado en documentacion tecnica de Python.
Respondes preguntas sobre FastAPI, Pydantic, SQLAlchemy y pytest.
Si no sabes la respuesta, dices claramente que no la sabes.
Provees ejemplos de codigo cuando es relevante.
"""

# Ejemplos few-shot (opcional)
MESSAGE user "Como validar un email en Pydantic?"
MESSAGE assistant "Usando EmailStr: from pydantic import EmailStr; class User(BaseModel): email: EmailStr"
```

```bash
# Construir el modelo personalizado
ollama create mi-asistente-tecnico -f Modelfile

# Usarlo
ollama run mi-asistente-tecnico "Como configuro CORS en FastAPI?"
```

---

## Integracion con Aplicaciones Python

```python
# src/llm/ollama_client.py - Cliente Python para Ollama
from openai import OpenAI  # Ollama expone API compatible con OpenAI

class OllamaClient:
    def __init__(self, base_url: str = "http://localhost:11434/v1", model: str = "llama3.1:8b"):
        self.client = OpenAI(base_url=base_url, api_key="ollama")  # api_key es requerido pero ignorado
        self.model = model
    
    async def generate(self, messages: list[dict], **kwargs) -> str:
        response = self.client.chat.completions.create(
            model=self.model,
            messages=messages,
            temperature=kwargs.get("temperature", 0.3),
            max_tokens=kwargs.get("max_tokens", 1024),
            stream=False
        )
        return response.choices[0].message.content
    
    async def generate_stream(self, messages: list[dict], **kwargs):
        stream = self.client.chat.completions.create(
            model=self.model,
            messages=messages,
            temperature=kwargs.get("temperature", 0.3),
            stream=True
        )
        for chunk in stream:
            if chunk.choices[0].delta.content:
                yield chunk.choices[0].delta.content
    
    async def embed(self, text: str) -> list[float]:
        response = self.client.embeddings.create(
            model="nomic-embed-text",
            input=text
        )
        return response.data[0].embedding
```

---

## Enrutador Local vs Cloud

```python
# src/llm/router.py - Decide si usar modelo local o cloud
class LLMRouter:
    def __init__(self, ollama_client, openai_client, config):
        self.ollama = ollama_client
        self.openai = openai_client
        self.config = config
    
    async def generate(self, messages: list[dict], task_type: str, **kwargs):
        model_config = self.config.get(task_type, {})
        
        if model_config.get("prefer_local", False):
            return await self.ollama.generate(messages, **kwargs)
        
        if model_config.get("require_high_quality", False):
            return await self.openai.generate(messages, **kwargs)
        
        # Default: intentar local, fallback a cloud
        try:
            return await self.ollama.generate(messages, **kwargs)
        except Exception:
            return await self.openai.generate(messages, **kwargs)
```

```yaml
# configs/models.yaml
task_routing:
  classification:
    prefer_local: true
    model: phi3:mini
  
  code_generation:
    require_high_quality: true
    local_model: deepseek-coder-v2
    cloud_model: gpt-4o
  
  chat_general:
    prefer_local: true
    model: llama3.1:8b
  
  complex_reasoning:
    require_high_quality: true
    cloud_model: claude-sonnet-4-20250514
  
  embeddings:
    prefer_local: true
    model: nomic-embed-text
```

---

## Referencias Cruzadas

- [Decision de Infraestructura](../docs/decision-infraestructura.md) -- VPS con GPU para Ollama
- [IA Generativa](../por-dominio/ia-generativa.md) -- capa de abstraccion de LLMs
- [Costos y FinOps](../docs/costos-finops.md) -- comparacion de costos
- [RAG End-to-End](./rag-end-to-end.md) -- RAG con embeddings locales

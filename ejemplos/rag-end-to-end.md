# Ejemplo: RAG End-to-End (Retrieval-Augmented Generation)

Ejemplo completo de un sistema RAG desde cero: ingestion de documentos, chunking, embeddings, vector store, recuperacion hibrida, re-ranking, generacion y evaluacion. Este es el patron de IA mas usado en produccion en 2025.

Proyecto de ejemplo: sistema de preguntas y respuestas sobre documentacion tecnica.

---

## Arbol de Carpetas Completo

```bash
rag-docs/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── eval-rag.yml             # Evaluacion programada de calidad RAG
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
│   ├── main.py                       # API FastAPI o CLI
│   ├── config.py                     # Pydantic Settings
│   ├── ingestion/                    # Ingestion de documentos
│   │   ├── __init__.py
│   │   ├── loader.py                 # Carga documentos (PDF, Markdown, HTML, TXT)
│   │   ├── chunker.py                # Division en chunks (fixed, recursive, semantic)
│   │   ├── cleaner.py                # Limpieza de texto (artefactos, headers, footers)
│   │   ├── metadata.py               # Extraccion de metadatos (titulo, fuente, fecha)
│   │   └── pipeline.py               # Pipeline completo de ingestion
│   ├── embeddings/                   # Generacion de embeddings
│   │   ├── __init__.py
│   │   ├── base.py                   # Interfaz abstracta
│   │   ├── openai_embeddings.py      # OpenAI (text-embedding-3-small/large)
│   │   ├── huggingface_embeddings.py # Modelos locales (sentence-transformers)
│   │   ├── cohere_embeddings.py      # Cohere (opcional)
│   │   └── batch.py                  # Procesamiento por lotes para corpora grandes
│   ├── retrieval/                    # Recuperacion de documentos
│   │   ├── __init__.py
│   │   ├── vector_store.py           # Almacen vectorial (pgvector, Chroma, Qdrant)
│   │   ├── sparse_retriever.py       # BM25 / SPLADE (busqueda por keywords)
│   │   ├── hybrid_retriever.py       # Combinacion dense + sparse + re-ranking
│   │   ├── re_ranker.py              # Re-ranking con cross-encoder
│   │   └── query_processor.py        # Expansion de query, filtros, metadata
│   ├── generation/                   # Generacion con LLM
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── openai_generator.py
│   │   ├── anthropic_generator.py
│   │   └── context_builder.py        # Construye el contexto optimo para el LLM
│   ├── evaluation/                   # Evaluacion RAG (RAGAS-style)
│   │   ├── __init__.py
│   │   ├── metrics/
│   │   │   ├── retrieval.py          # Hit rate, MRR, NDCG, recall@k
│   │   │   ├── generation.py         # Faithfulness, answer relevancy, correctness
│   │   │   └── end_to_end.py         # Latencia, costo, satisfaccion
│   │   ├── test_sets/
│   │   │   ├── sintetico.json        # Preguntas sinteticas con ground truth
│   │   │   └── real.json             # Preguntas reales de usuarios
│   │   └── runner.py                 # Ejecutor de evaluacion
│   ├── api/                          # API REST
│   │   ├── __init__.py
│   │   ├── router.py
│   │   ├── schemas.py                # Pydantic schemas para Q&A
│   │   └── streaming.py              # Streaming SSE de respuestas
│   └── prompts/
│       ├── manager.py
│       └── templates/
│           ├── rag_system.txt         # System prompt del asistente RAG
│           └── context_window.txt     # Template del contexto inyectado
├── tests/
│   ├── conftest.py
│   ├── unit/
│   │   ├── test_chunker.py
│   │   ├── test_hybrid_retriever.py
│   │   └── test_context_builder.py
│   └── integration/
│       ├── test_ingestion_pipeline.py
│       └── test_rag_e2e.py
├── evals/
│   ├── questions.json                # Preguntas de evaluacion con respuestas esperadas
│   └── ground_truth/
│       └── chunks.json               # Chunks relevantes para cada pregunta
├── data/
│   ├── documents/                    # Documentos fuente (versionados con DVC)
│   └── README.md
├── notebooks/
│   ├── 01_chunking_experiments.ipynb # Experimentos con estrategias de chunking
│   ├── 02_retrieval_tuning.ipynb     # Optimizacion de parametros de retrieval
│   └── 03_evaluation_analysis.ipynb  # Analisis de resultados de evaluacion
├── scripts/
│   ├── ingest_docs.py                # Script de ingestion batch
│   ├── run_retrieval_eval.py         # Evaluacion de retrieval
│   ├── run_generation_eval.py        # Evaluacion de generacion
│   └── benchmark_latency.py          # Benchmark de latencia
├── configs/
│   ├── chunking/
│   │   ├── default.yaml              # chunk_size=512, overlap=50
│   │   ├── small_chunks.yaml         # chunk_size=256, overlap=25
│   │   └── large_chunks.yaml         # chunk_size=1024, overlap=100
│   └── retrieval/
│       ├── hybrid.yaml               # Dense + BM25
│       └── dense_only.yaml           # Solo dense
└── docs/
    ├── chunking_strategy.md           # Documentacion de la estrategia de chunking
    ├── retrieval_pipeline.md          # Documentacion del pipeline de retrieval
    └── evaluation_results.md          # Resultados de evaluacion
```

---

## Componentes Clave Explicados

### 1. Estrategias de Chunking (`src/ingestion/chunker.py`)

La calidad de RAG depende mas del chunking que del vector store. Estrategias:

**Chunking por tamano fijo:**
Divide el texto en chunks de N caracteres con overlap. Simple pero rompe parrafos y oraciones.

**Chunking recursivo:**
Divide por parrafo, luego por oracion, luego por caracter. Mantiene la estructura semantica.

**Chunking semantico:**
Usa embeddings para detectar cambios de tema y dividir donde cambia el significado. Mas preciso pero mas costoso.

**Chunking por documento (para PDFs):**
Considera la estructura del PDF: secciones, subsecciones, tablas, figuras.

Configuracion tipica a experimentar:
```yaml
chunking:
  strategy: recursive
  chunk_size: 512         # tokens o caracteres
  chunk_overlap: 50       # solapamiento entre chunks
  separators: ["\n\n", "\n", ". ", " ", ""]
  min_chunk_size: 100     # descartar chunks muy pequenos
  metadata_fields:
    - source              # archivo de origen
    - page                # numero de pagina
    - section             # seccion del documento
```

### 2. Recuperacion Hibrida (`src/retrieval/hybrid_retriever.py`)

Combinar busqueda densa (embeddings) con busqueda dispersa (keywords):

```
Query del usuario: "Como configurar el rate limiting en FastAPI?"

Busqueda Densa (embeddings):            Busqueda Dispersa (BM25):
├── Doc A: "Rate limiting..." (0.92)   ├── Doc B: "FastAPI config..." (score BM25)
├── Doc C: "API security..." (0.85)    ├── Doc A: "Rate limiting..." (score BM25)
└── Doc E: "Throttling..." (0.78)      └── Doc D: "FastAPI rate limit..." (score BM25)

Fusion (RRF - Reciprocal Rank Fusion):
└── Resultado combinado ponderado

Re-ranking (Cross-encoder):
├── Cada candidato se evalua con un modelo cross-encoder mas preciso
└── Top-K final se pasa al LLM
```

### 3. Construccion de Contexto (`src/generation/context_builder.py`)

El arte de RAG es como presentas los documentos recuperados al LLM:

```
Sistema: Eres un asistente que responde preguntas basandote UNICAMENTE en 
los documentos proporcionados. Si la respuesta no esta en los documentos, 
di "No tengo suficiente informacion para responder".

Documentos de referencia:
[Doc 1 - fuente: fastapi-docs.md, seccion: Rate Limiting]
SlowAPI es una libreria para rate limiting en FastAPI...

[Doc 2 - fuente: fastapi-docs.md, seccion: Middleware]
El middleware en FastAPI se configura con app.add_middleware()...

[Doc 3 - fuente: fastapi-docs.md, seccion: Dependencies]
Las dependencias en FastAPI permiten inyectar...

Pregunta: Como configurar rate limiting en FastAPI?

Respuesta:
```

### 4. Evaluacion RAG (`src/evaluation/`)

Metricas clave del pipeline RAG:

**Metricas de Retrieval:**
- Hit Rate: % de preguntas donde al menos 1 chunk relevante esta en el top-K
- MRR (Mean Reciprocal Rank): posicion promedio del primer chunk relevante
- NDCG@k: calidad del ranking considerando relevancia gradual
- Recall@k: % de chunks relevantes recuperados en el top-K

**Metricas de Generacion:**
- Faithfulness (fidelidad): % de claims en la respuesta que estan respaldados por los documentos
- Answer Relevancy: que tan relevante es la respuesta a la pregunta
- Context Precision: % de chunks en el contexto que son realmente relevantes
- Context Recall: % de chunks relevantes que fueron recuperados

```
Flujo de evaluacion RAGAS:

1. Dataset: 100 preguntas con ground truth (chunks relevantes + respuesta ideal)
2. Para cada pregunta:
   a. Recuperar chunks con el pipeline de retrieval
   b. Medir metricas de retrieval (MRR, recall@k, NDCG)
   c. Generar respuesta con LLM
   d. LLM-as-judge evalua: faithfulness, relevancy, correctness
3. Reporte agregado con promedios y distribucion
```

---

## Flujo del Pipeline RAG

```
[Documentos] (PDF, MD, HTML, TXT)
    |
[Loader] -> Extrae texto y metadatos
    |
[Cleaner] -> Limpia artefactos, normaliza
    |
[Chunker] -> Divide en chunks con overlap
    |
[Embeddings] -> Genera vectores para cada chunk
    |
[Vector Store] -> Indexa vectores + metadatos + texto
    |
    |   >>> INDEXACION COMPLETA <<<
    |
[Usuario hace pregunta]
    |
[Query Processor] -> Expande/reescribe la query
    |
[Dense Retriever] -> Busca por similitud semantica (top-20)
    |
[Sparse Retriever] -> Busca por keywords BM25 (top-20)
    |
[Hybrid Fusion] -> Combina resultados (RRF) -> top-10
    |
[Re-ranker] -> Cross-encoder evalua relevancia -> top-3
    |
[Context Builder] -> Construye prompt con documentos + pregunta
    |
[LLM Generator] -> Genera respuesta con fuentes citadas
    |
[Respuesta al usuario] + [fuentes consultadas]
```

---

## Decisiones de Arquitectura RAG

### Chunk Size: Como Elegir

| Tamano de chunk | Pros | Contras | Usar cuando |
|-----------------|------|---------|-------------|
| Pequeno (128-256 tokens) | Alta precision, bueno para facts | Pierde contexto, respuestas fragmentadas | FAQ, datos estructurados |
| Medio (512 tokens) | Buen balance precision/contexto | -- | Uso general (recomendado) |
| Grande (1024-2048 tokens) | Rico en contexto, respuestas completas | Menos precision, mas tokens | Resumenes, analisis |

Regla practica: empieza con 512 tokens, 50 de overlap. Mide. Ajusta.

### Vector Store: Como Elegir

| Vector Store | Cuando usarla |
|-------------|---------------|
| pgvector (PostgreSQL) | Ya tienes PostgreSQL. < 1M de vectores. |
| ChromaDB | Prototipo rapido, desarrollo local. |
| Qdrant | Produccion, alto rendimiento, filtrado avanzado. |
| Pinecone | Cero operaciones, escala automatica. |
| Weaviate | Necesitas busqueda hibrida nativa (dense + BM25). |

### Embeddings: Como Elegir

| Modelo | Dimensiones | Costo | Uso |
|--------|------------|-------|-----|
| OpenAI text-embedding-3-small | 512/1536 | $0.02/1M tokens | Uso general, mejor relacion calidad/precio |
| OpenAI text-embedding-3-large | 256/1024/3072 | $0.13/1M tokens | Maxima calidad |
| Cohere embed-multilingual-v3 | 1024 | $0.10/1M tokens | Multilingue |
| sentence-transformers (local) | 384/768 | Gratis | Desarrollo, low-cost |
| Jina embeddings v3 | 1024 | Gratis/Pago | Documentos largos (hasta 8192 tokens) |

---

## Prompt Para Iniciar el Proyecto con IA

```
Quiero construir un sistema RAG completo para preguntas y respuestas sobre documentacion tecnica.

Componentes requeridos:

1. INGESTION:
   - Document loader: PDF (PyPDF2), Markdown, HTML, TXT
   - Chunker recursivo: configurable chunk_size, overlap, separadores
   - Cleaner: eliminar headers/footers, normalizar whitespace
   - Metadata extraction: source file, page number, section

2. EMBEDDINGS:
   - Abstraccion multi-proveedor (OpenAI, HuggingFace local, Cohere)
   - Batch processing para corpora grandes con progress bar
   - Cost estimation antes de generar embeddings

3. RETRIEVAL:
   - Dense retrieval (embeddings) via pgvector o ChromaDB
   - Sparse retrieval (BM25) con rank-bm25
   - Hybrid fusion con Reciprocal Rank Fusion (RRF)
   - Re-ranker con cross-encoder (sentence-transformers)
   - Query processor: expansion y filtros por metadata

4. GENERATION:
   - Context builder: arma el prompt optimo con docs + instrucciones
   - LLM abstraction: OpenAI + Anthropic
   - Citation: la respuesta debe citar fuentes [Doc 1, Doc 2]
   - Fallback: "No tengo suficiente informacion" si los docs no cubren la pregunta

5. EVALUACION:
   - Retrieval metrics: hit_rate, MRR, NDCG@k, recall@k
   - Generation metrics: faithfulness, answer_relevancy, correctness (LLM-as-judge)
   - Test sets: preguntas sinteticas + ground truth
   - CI/CD para evaluacion periodica

6. CONFIGS Y EXPERIMENTOS:
   - YAML configs para chunking (default, small, large)
   - YAML configs para retrieval (hybrid, dense_only)
   - Notebooks para experimentos de chunking y retrieval tuning

Stack: Python 3.12, FastAPI, pgvector o ChromaDB, OpenAI, sentence-transformers, rank-bm25, Pydantic v2, pytest.

Sigue RULES.md, CONTEXTO.md y las convenciones del proyecto.
```

---

## Referencias Cruzadas

- [IA Generativa](../por-dominio/ia-generativa.md) -- componentes base de LLMs
- [LLM Ops](../por-dominio/llm-ops.md) -- operaciones y cache semantica
- [Agentes IA](../por-dominio/agentes-ia.md) -- agentes que usan RAG como herramienta
- [Bases de Datos](../docs/bases-datos-almacenamiento.md) -- vector stores

# Bases de Datos y Almacenamiento: SQL, NoSQL, Vector Stores, Cache, Object Storage

Todo proyecto necesita guardar datos. Y la decision de donde y como guardarlos tiene mas impacto en la arquitectura que la eleccion del framework. Esto no se piensa en el editor de codigo: se piensa antes de escribir la primera linea.

---

## 1. Bases de Datos Relacionales (SQL)

**Que son:** Bases de datos que almacenan datos en tablas con esquemas rigidos y relaciones entre ellas. Usan SQL como lenguaje de consulta.

**Cuando usar SQL:**
- Tus datos tienen estructura predecible y relaciones claras
- Necesitas integridad de datos garantizada (transacciones ACID)
- Haces consultas complejas con joins, agregaciones, subconsultas
- Necesitas que los datos sean consistentes siempre (banca, e-commerce, finanzas)
- Es la opcion por defecto. Si no sabes cual elegir, elige PostgreSQL.

**Bases de datos SQL populares:**

| Base de Datos | Ideal para | Tipo | Hosting Gestionado |
|---------------|-----------|------|-------------------|
| **PostgreSQL** | Todo. La opcion por defecto. | Open source | RDS, Supabase, Neon, Railway |
| **SQLite** | Prototipos, apps moviles, embedded | Open source, archivo | Turso (distribuido) |
| **MySQL/MariaDB** | Web tradicional, WordPress | Open source | RDS, PlanetScale |
| **PlanetScale** | MySQL serverless, branching de DB | Gestionado | PlanetScale |

### PostgreSQL: el estandar de facto

Razones para elegir PostgreSQL en 2024-2025:
- Soporta JSON/JSONB (puede actuar como NoSQL cuando necesitas flexibilidad)
- Extensiones potentes: PostGIS (geoespacial), pgvector (vectores para IA)
- Full-text search integrado
- CTEs, window functions, transacciones avanzadas
- Row-level security
- La mayoria de ORMs y frameworks tienen mejor soporte para PostgreSQL

### SQLite: infravalorado

Razones para considerar SQLite:
- Cero configuracion (es un archivo, no un servidor)
- Ideal para prototipos y MVPs
- Sorprendentemente rapido para lecturas (hasta 1M requests/dia en un solo archivo)
- La mayoria de los ORMs lo soportan (SQLAlchemy, Prisma, Drizzle)
- Turso (gestionado) permite SQLite distribuido globalmente

Cuando NO usar SQLite: alta concurrencia de escritura (muchos usuarios escribiendo simultaneamente).

---

## 2. Bases de Datos NoSQL

### 2.1 Documentales (MongoDB, Firestore)

**Que son:** Almacenan documentos JSON-like sin esquema fijo. Flexibles pero sin joins ni transacciones complejas.

**Cuando usar:**
- Los datos no tienen estructura fija (cada documento puede ser diferente)
- Necesitas iterar rapido sin migraciones de esquema
- Los datos se acceden como documentos completos, no necesitas joins
- Tienes datos jerarquicos o anidados naturalmente

**Cuando NO usar:**
- Necesitas integridad referencial entre colecciones
- Haces consultas complejas con joins
- Necesitas transacciones ACID multi-documento (MongoDB las soporta pero son limitadas)

### 2.2 Clave-Valor (Redis, DynamoDB)

**Que son:** Guardan pares clave-valor. Extremadamente rapidos para lecturas/escrituras simples.

**Cuando usar:**
- Cache (sesiones, resultados frecuentes, rate limiting)
- Datos de sesion de usuario
- Contadores y estadisticas en tiempo real
- Colas de mensajes ligeras
- Leaderboards y rankings

### 2.3 Grafos (Neo4j, Amazon Neptune)

**Que son:** Optimizadas para datos altamente conectados (redes sociales, recomendaciones, deteccion de fraude).

**Cuando usar:**
- Recomendaciones ("usuarios como tu tambien compraron...")
- Grafos de conocimiento
- Deteccion de fraude (patrones de transacciones conectadas)
- Redes sociales y conexiones

### 2.4 Time-Series (InfluxDB, TimescaleDB)

**Que son:** Optimizadas para datos con timestamp (metricas, logs, IoT, datos financieros).

---

## 3. Vector Stores (Bases de Datos Vectoriales)

**Que son:** Bases de datos optimizadas para busqueda por similitud semantica usando embeddings (representaciones vectoriales de texto, imagenes, etc.).

**Criticas para proyectos de IA con RAG (Retrieval-Augmented Generation).**

**Cuando necesitas una vector store:**
- Busqueda semantica (encontrar documentos similares por significado, no por palabras clave)
- RAG: aumentar prompts de LLM con informacion relevante de tu base de conocimiento
- Memoria de largo plazo para agentes de IA
- Recomendaciones basadas en similitud
- Busqueda de imagenes similares
- Deteccion de duplicados o anomalias

**Vector stores populares:**

| Solucion | Tipo | Ideal para | Precio |
|----------|------|-----------|--------|
| **pgvector** | Extension PostgreSQL | Ya usas PostgreSQL y no quieres otra DB | Gratis (extension) |
| **ChromaDB** | Open source, embedded | Prototipos, proyectos locales, pocos docs | Gratis |
| **Qdrant** | Open source, cloud | Produccion, alta performance, filtrado avanzado | Gratis / Cloud desde $25 |
| **Pinecone** | Cloud gestionado | Produccion, cero administracion, escala | Desde $70/mes |
| **Weaviate** | Open source, cloud | Produccion, hibrido (vector + keyword search) | Gratis / Cloud desde $25 |
| **Milvus** | Open source | Gran escala (billones de vectores) | Gratis / Zilliz Cloud |
| **LanceDB** | Open source, embedded | Serverless, rapido, basado en Lance format | Gratis |
| **Supabase** | PostgreSQL + pgvector | Fullstack (auth, DB, vectors en un solo lugar) | Gratis / Pro $25 |

### Regla practica para elegir vector store

1. **Prototipo / < 1000 documentos:** ChromaDB o pgvector (0 configuracion)
2. **Proyecto pequeno-mediano:** pgvector (si ya tienes PostgreSQL) o Qdrant Cloud
3. **Produccion seria:** Pinecone (el mas caro pero cero operaciones)
4. **Fullstack simple:** Supabase (auth + DB + vectors + realtime en uno)

### Lo que NO te dicen sobre vector stores

- Los embeddings cuestan dinero (OpenAI embeddings: $0.00002 por 1K tokens)
- Indexar 100,000 documentos puede costar $10-50 solo en embeddings
- La calidad de la busqueda depende mas de como divides los documentos (chunking) que de la vector store
- pgvector es suficiente para el 90% de los casos de uso
- Re-rankear resultados (pasar los top-K por un LLM para evaluar relevancia) mejora drasticamente la calidad, pero cuesta tokens extra

---

## 4. Cache

**Que es:** Almacenamiento temporal ultra-rapido para datos que se acceden frecuentemente.

**Cuando usar cache:**
- Resultados de consultas costosas a la base de datos
- Respuestas de LLMs que se repiten (cache semantica)
- Sesiones de usuario
- Rate limiting
- Datos de configuracion que cambian poco
- Contadores en tiempo real

### Tipos de Cache

**Cache de Base de Datos:**
```python
# Sin cache: cada request pega a la DB
user = await db.query(User).filter(User.id == user_id).first()

# Con cache: solo va a DB si no esta en cache
user = await cache.get(f"user:{user_id}")
if not user:
    user = await db.query(User).filter(User.id == user_id).first()
    await cache.set(f"user:{user_id}", user, expire=300)  # 5 minutos
```

**Cache Semantica (para LLMs):**
```python
# Sin cache: cada pregunta similar llama al LLM
response = await llm.generate("Cual es la capital de Francia?")

# Con cache semantica: si alguien pregunto algo similar, se devuelve la respuesta cacheada
cached = await semantic_cache.search("Cual es la capital de Francia?")
if cached.similarity > 0.95:
    return cached.response
else:
    response = await llm.generate("Cual es la capital de Francia?")
    await semantic_cache.store(query, response)
```

### Herramientas de Cache

| Herramienta | Tipo | Ideal para |
|-------------|------|-----------|
| **Redis** | En memoria | Cache general, sesiones, colas, pub/sub |
| **Memcached** | En memoria | Cache simple clave-valor |
| **Cloudflare CDN** | Edge | Assets estaticos, paginas completas |
| **Vercel ISR** | Edge | Paginas estaticas regeneradas periodicamente |

---

## 5. Almacenamiento de Objetos (Object Storage)

**Que es:** Almacenamiento para archivos (imagenes, videos, documentos, backups). No es una base de datos.

**Cuando usar:**
- Imagenes de perfil de usuario (avatars)
- Archivos subidos por usuarios (PDFs, documentos)
- Backups de base de datos
- Artefactos de CI/CD
- Datasets para ML
- Archivos estaticos de la aplicacion

**Regla de oro:** NUNCA guardes archivos subidos por usuarios en el sistema de archivos del servidor. Si escalas a 2 servidores, el servidor B no tiene los archivos del servidor A. Usa object storage.

**Proveedores de Object Storage:**

| Proveedor | Precio (GB/mes) | Egress (descarga) | Ideal para |
|-----------|----------------|-------------------|-----------|
| **Cloudflare R2** | $0.015 | Gratis | Mejor relacion calidad/precio |
| **AWS S3** | $0.023 | $0.09/GB | El estandar de la industria |
| **Backblaze B2** | $0.006 | $0.01/GB | Almacenamiento barato, backups |
| **DigitalOcean Spaces** | $0.02 | $0.01/GB | Simple, integrado con DO |
| **Vercel Blob** | $0.20 | $0.15/GB | Si ya usas Vercel |
| **Supabase Storage** | $0.021 | Gratis (hasta cierto limite) | Si ya usas Supabase |

---

## 6. Arbol de Decision Completo

```
Que tipo de datos necesitas guardar?

1. Datos estructurados con relaciones (usuarios, pedidos, productos)
   -> PostgreSQL (opcion por defecto)
   -> SQLite (si es prototipo o app pequeña)

2. Datos que cambian de forma frecuentemente, sin esquema fijo
   -> MongoDB (si necesitas flexibilidad y escala)
   -> JSONB en PostgreSQL (si quieres flexibilidad sin otra DB)

3. Datos para acelerar consultas frecuentes
   -> Redis (cache en memoria)
   -> Si ya estas en Vercel/Cloudflare, su CDN cache

4. Embeddings para busqueda semantica / RAG
   -> pgvector (si ya usas PostgreSQL, cero infraestructura extra)
   -> ChromaDB (prototipo rapido)
   -> Pinecone/Qdrant (produccion seria)

5. Archivos de usuario (imagenes, PDFs, etc.)
   -> Cloudflare R2 (mejor precio, sin costo de descarga)
   -> S3 (si ya estas en AWS)

6. Datos de sesiones o estado temporal
   -> Redis

7. Mensajes entre servicios (asincronia)
   -> Redis Streams (ligero, si ya usas Redis)
   -> RabbitMQ (colas tradicionales)
   -> SQS/Google Pub/Sub (gestionado)
```

---

## 7. Lo Que el Vibe Coder No Sabe de Bases de Datos

1. **Migraciones**: cambiar el esquema de la base de datos sin perder datos. Herramientas: Alembic (Python), Prisma (Node.js), Drizzle (Node.js). Si no usas migraciones, tu unica opcion es borrar la DB y empezar de cero.

2. **Indices**: sin indices, una busqueda en una tabla de 1M de registros tarda segundos. Con indices, milisegundos. Pero los indices ocupan espacio y ralentizan escrituras. Hay que crear solo los que necesitas.

3. **Conexiones**: cada instancia de tu app abre conexiones a la base de datos. Si tienes 10 instancias y cada una abre 20 conexiones, son 200 conexiones. PostgreSQL tipicamente soporta 100-200 por defecto. Necesitas un connection pooler (PgBouncer, supavisor de Supabase).

4. **N+1 Queries**: el error mas comun con ORMs. Cargas 100 usuarios, luego para cada usuario cargas sus pedidos: 1 + 100 = 101 queries en lugar de 2. Usa eager loading.

5. **Backups**: si no tienes backups automaticos, un `DROP TABLE` accidental es catastrofico. Configura backups diarios desde el dia 1.

6. **Cold starts en serverless**: las bases de datos serverless (PlanetScale, Neon, Turso) tambien tienen cold starts. La primera conexion puede tardar 1-2 segundos.

---

## Referencias Cruzadas

- [Decision de Infraestructura](./decision-infraestructura.md) -- Donde ejecutar cada tipo de DB
- [Arquitecturas de Despliegue](./arquitecturas-despliegue.md) -- Como se integran las DBs en la arquitectura
- [Entornos y Stages](./entornos-y-stages.md) -- DBs por entorno (dev, staging, prod)
- [Costos y FinOps](./costos-finops.md) -- Cuanto cuesta cada opcion

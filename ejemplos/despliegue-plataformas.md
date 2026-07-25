# Ejemplos de Despliegue por Plataforma: VPS, PaaS, Edge y Cloud

Esta guia complementa los docs de infraestructura con estructuras de referencia para diferentes escenarios de despliegue, incluyendo los archivos y configuraciones necesarios en cada caso.

---

## 1. Despliegue en VPS (Hetzner + Docker + Caddy)

**Perfil:** Tienes control total, presupuesto bajo, quieres aprender.

**Estructura del proyecto para despliegue en VPS:**

```
mi-proyecto/
├── .github/
│   └── workflows/
│       └── deploy.yml              # CI/CD: test + build + rsync al VPS
├── docker/
│   ├── Dockerfile                  # Imagen de la app
│   └── Dockerfile.worker           # Worker separado (si aplica)
├── docker-compose.yml              # Desarrollo local
├── docker-compose.prod.yml         # Produccion en VPS
├── Caddyfile                       # Reverse proxy + HTTPS automatico
├── scripts/
│   ├── deploy.sh                   # Script de deploy
│   └── backup-db.sh                # Backup de base de datos
├── .env.example
├── .gitignore
├── src/
├── tests/
└── README.md
```

**Archivos clave para el VPS:**

**Caddyfile (reverse proxy + HTTPS automatico):**
```
tudominio.com {
    reverse_proxy app:8000
}

api.tudominio.com {
    reverse_proxy app:8000
}
```

**docker-compose.prod.yml (estructura, no codigo):**
```yaml
# Estructura conceptual del docker-compose de produccion:
# - Servicio app: tu aplicacion
# - Servicio db: PostgreSQL (sin exponer puerto al exterior)
# - Servicio redis: cache
# - Servicio caddy: reverse proxy con HTTPS automatico
# - Red interna entre servicios
# - Volumenes persistentes para DB y uploads
# - Healthchecks en cada servicio
```

**Lo que NO debes hacer en el VPS:**
- Exponer PostgreSQL a internet (solo red interna de Docker)
- Usar HTTP en produccion (Caddy lo resuelve solo)
- No tener backups automaticos de la base de datos
- Almacenar uploads en el sistema de archivos del contenedor (usa volumen)

---

## 2. Despliegue en PaaS (Railway / Render / Fly.io)

**Perfil:** No quieres administrar servidores. La plataforma lo gestiona todo.

**Estructura del proyecto para PaaS:**

```
mi-proyecto/
├── .github/
│   └── workflows/
│       └── ci.yml                  # Solo CI (el deploy lo hace la plataforma)
├── Dockerfile                      # Railway/Render lo usan para build
├── fly.toml                        # Solo si usas Fly.io
├── render.yaml                     # Solo si usas Render Blueprint
├── nixpacks.toml                   # Alternativa a Dockerfile (Railway)
├── .env.example
├── .gitignore
├── src/
├── tests/
├── pyproject.toml
└── README.md
```

**Caracteristicas del despliegue en PaaS:**
- La plataforma detecta el lenguaje/framework automaticamente
- Build y deploy automatico al pushear a main
- Escala manual o automatico
- Variables de entorno en el dashboard de la plataforma
- Base de datos como servicio adjunto (o externo)
- HTTPS automatico
- Previsualizacion de PRs en algunas plataformas

**Estrategia para multiples servicios en PaaS:**
Si necesitas API + Worker + Frontend:
- Railway: un proyecto con 3 servicios (api, worker, frontend)
- Render: 3 servicios separados (Web Service, Background Worker, Static Site)
- Fly.io: 3 apps separadas o entrypoints en fly.toml

---

## 3. Despliegue en Serverless (AWS Lambda + API Gateway)

**Perfil:** Trafico impredecible, presupuesto minimo cuando no hay uso.

**Estructura del proyecto para serverless:**

```
mi-proyecto/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── deploy-staging.yml
│       └── deploy-prod.yml
├── infrastructure/                 # IaC
│   └── terraform/
│       ├── main.tf                 # Lambda, API Gateway, DynamoDB
│       ├── variables.tf
│       └── outputs.tf
├── lambdas/
│   ├── api/                        # Lambda principal (FastAPI adaptado)
│   │   ├── handler.py
│   │   └── requirements.txt
│   ├── worker/                     # Lambda para procesamiento
│   │   └── handler.py
│   └── shared/                     # Codigo compartido entre lambdas
│       └── utils.py
├── layers/                         # Lambda Layers (dependencias compartidas)
│   └── requirements.txt
├── .env.example
├── .gitignore
├── src/                            # Logica de negocio (fuera de lambdas/)
├── tests/
└── README.md
```

**Consideraciones serverless:**
- Cold start: la primera llamada tras inactividad tarda mas
- Tiempo maximo: 15 min (900s) en Lambda
- Tamanio maximo: 250MB descomprimido (usa layers para dependencias)
- Concurrencia maxima por defecto: 1000 (solicitable a 10s de miles)
- Variables de entorno: maximo 4KB total
- /tmp: 512MB a 10GB de almacenamiento efimero (no persiste entre invocaciones)

---

## 4. Despliegue Edge (Cloudflare Workers / Vercel Edge)

**Perfil:** Necesitas minima latencia global, API ligera o middleware.

**Estructura del proyecto para edge:**

```
mi-proyecto/
├── .github/
│   └── workflows/
│       └── ci.yml
├── wrangler.toml                   # Config de Cloudflare Workers
├── src/
│   ├── worker.ts                   # Worker principal
│   ├── middlewares/                # Auth, rate limiting, logging
│   └── routes/                     # Rutas de la API
├── kv/                             # Cloudflare KV (si se usa)
├── durable-objects/                # Cloudflare Durable Objects
├── .env.example
├── .gitignore
├── package.json
├── tsconfig.json
└── README.md
```

**Limitaciones del edge (importante saberlo antes de elegirlo):**
- No tienes acceso al sistema de archivos (solo KV, R2, D1)
- No todas las librerias de Node.js funcionan (sin natives)
- Tiempo de CPU limitado (10-50ms para Workers gratis, 30s para paid)
- Memoria limitada (128MB)
- Las bases de datos tradicionales (PostgreSQL) requieren conexion HTTP, no TCP directo

---

## 5. Despliegue de Proyecto con IA en Produccion

**Perfil especifico:** Aplicacion que usa LLMs, embeddings, RAG.

**Consideraciones unicas de IA en produccion:**

**Estructura de deployment:**
```
├── api/                            # API principal (FastAPI, Next.js)
│   └── se despliega en Railway/Fly.io/ECS
├── worker-ia/                      # Procesamiento LLM asincrono (colas)
│   └── se despliega en Railway/Fly.io/ECS (escala con cola)
├── vector-store/                   # Base de datos vectorial
│   └── pgvector en Supabase, o Pinecone, o ChromaDB
├── cache/                          # Redis para cache semantica
│   └── Upstash o Redis en Fly.io
└── monitoring/                     # LangFuse o Helicone para tracing de LLM
```

**Decisiones clave para produccion con IA:**

| Componente | Opcion simple | Opcion escalable |
|-----------|---------------|-----------------|
| Inferencia LLM | API directa (OpenAI/Anthropic) | API Gateway propio con fallback multi-proveedor |
| Embeddings | OpenAI embeddings | OpenAI + modelo local para ahorro |
| Vector store | pgvector en PostgreSQL | Pinecone/Qdrant dedicado |
| Cache LLM | Redis | Redis + cache semantica con embeddings |
| Cola de trabajos | Redis/BullMQ | RabbitMQ/Kafka |
| Monitoreo LLM | LangFuse (gratis) | Datadog + custom metrics |

---

## 6. Checklist de Despliegue (Antes de Ir a Produccion)

```
Pre-deploy:
[ ] Variables de entorno configuradas en la plataforma (no en archivos)
[ ] API keys de produccion son diferentes a las de desarrollo
[ ] Base de datos tiene backups automaticos configurados
[ ] Migraciones de BD ejecutadas en staging y validadas
[ ] DEBUG=false en produccion
[ ] Rate limiting configurado
[ ] CORS restringido a origenes de produccion
[ ] .env en .gitignore (verificado)
[ ] Dependencias sin vulnerabilidades conocidas (pip-audit / npm audit)

Deploy:
[ ] Deploy a staging primero
[ ] Smoke tests post-deploy en staging
[ ] Si staging OK, deploy a produccion
[ ] Smoke tests en produccion inmediatamente despues del deploy

Post-deploy:
[ ] Health check de la API responde 200
[ ] Funcionalidad critica funciona (login, feature principal)
[ ] Logs muestran actividad normal (sin errores nuevos)
[ ] Metricas en rango normal (latencia, errores, CPU)
[ ] Costos dentro del presupuesto diario estimado

Rollback (si algo falla):
[ ] Revertir commit en GitHub/GitLab
[ ] Pipeline de CD ejecuta deploy de la version anterior
[ ] Verificar que todo vuelve a funcionar
[ ] Investigar que fallo ANTES de reintentar el deploy
```

---

## Referencias Cruzadas

- [Decision de Infraestructura](../docs/decision-infraestructura.md) -- Que opcion elegir
- [Arquitecturas de Despliegue](../docs/arquitecturas-despliegue.md) -- Patrones
- [CI/CD y GitOps](../docs/cicd-gitops.md) -- Automatizar el deploy
- [Networking y Seguridad](../docs/networking-seguridad.md) -- DNS, HTTPS, firewalls
- [Costos y FinOps](../docs/costos-finops.md) -- Cuanto cuesta cada opcion

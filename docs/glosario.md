# Glosario de Terminos para Ingenieria de Software e IA

Glosario completo de todos los terminos tecnicos usados en este repositorio y en la practica profesional de ingenieria de software e inteligencia artificial.

---

## A

**ABAC (Attribute-Based Access Control):** Control de acceso basado en atributos del usuario, recurso, accion y contexto. Mas flexible que RBAC. Ver `docs/control-acceso.md`.

**ACID (Atomicity, Consistency, Isolation, Durability):** Propiedades que garantizan que las transacciones de base de datos se procesen de forma confiable. Fundamental en bases de datos relacionales como PostgreSQL.

**ADN de un proyecto:** Metafora para referirse a los archivos y configuraciones fundamentales que definen como se construye, prueba y despliega un proyecto. Incluye `.gitignore`, `pyproject.toml`, `Dockerfile`, workflows CI/CD, etc.

**ADR (Architecture Decision Record):** Documento breve que registra una decision arquitectonica, el contexto, las alternativas consideradas y las consecuencias. Se almacena en `docs/adr/`.

**Aider:** Herramienta de pair programming con IA que funciona desde la terminal. Se configura con `.aider.conf.yml`. Ver `docs/archivos-ia-esenciales.md`.

**Alembic:** Herramienta de migraciones para SQLAlchemy (Python). Gestiona cambios de esquema de base de datos de forma versionada. Carpeta tipica: `alembic/`.

**API Gateway:** Servicio que actua como punto unico de entrada para todas las APIs. Maneja autenticacion, rate limiting, enrutamiento y agregacion de respuestas. Ejemplos: Kong, AWS API Gateway, Traefik.

**ArgoCD:** Herramienta de GitOps para Kubernetes que sincroniza el estado deseado (definido en Git) con el estado real del cluster. Ver `docs/cicd-gitops.md`.

---

## B

**Blue/Green Deployment:** Estrategia de despliegue donde se mantienen dos entornos identicos (blue = actual, green = nuevo). El trafico se cambia instantaneamente al nuevo entorno tras validacion. Permite rollback inmediato.

**Branch (rama):** Linea de desarrollo independiente en Git. Permite trabajar en funcionalidades sin afectar la rama principal (`main` o `master`).

---

## C

**Caddy:** Servidor web y reverse proxy que automaticamente obtiene y renueva certificados HTTPS via Let's Encrypt. Alternativa moderna y simple a Nginx. Ver `docs/networking-seguridad.md`.

**Cache Semantica:** Sistema de cache que almacena respuestas de LLMs basandose en la similitud semantica de las preguntas (usando embeddings), no en la igualdad exacta de texto. Permite reutilizar respuestas para preguntas similares.

**Canary Deployment:** Estrategia de despliegue donde la nueva version se libera gradualmente a un porcentaje pequeno de usuarios (5%, 10%, 50%, 100%) mientras se monitorean metricas.

**CDN (Content Delivery Network):** Red de servidores distribuidos globalmente que almacenan copias de contenido estatico cerca de los usuarios para reducir latencia.

**CI/CD (Continuous Integration / Continuous Deployment):** Practicas para automatizar la integracion de cambios de codigo y su despliegue a produccion. Ver `docs/cicd-gitops.md`.

**Claude Code:** Herramienta de agente de IA de Anthropic que trabaja directamente en el repositorio desde la terminal. Lee `CLAUDE.md` para instrucciones del proyecto.

**Clean Architecture:** Patron arquitectonico propuesto por Robert C. Martin que organiza el codigo en capas concentricas con dependencias hacia adentro: entidades -> casos de uso -> adaptadores -> frameworks.

**Cold Start:** Latencia adicional que ocurre cuando una funcion serverless o contenedor se ejecuta por primera vez despues de un periodo de inactividad. El runtime necesita inicializarse. Tipico: 100ms a 2 segundos.

**Conventional Commits:** Especificacion para mensajes de commit que siguen el formato `tipo(scope): descripcion`. Tipos: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`. Ver `docs/convenciones-nombres.md`.

**CORS (Cross-Origin Resource Sharing):** Mecanismo de seguridad que controla que dominios pueden acceder a los recursos de tu API desde un navegador.

**Cursor:** IDE basado en VS Code con capacidades de IA integradas. Lee `.cursor/rules` o `.cursorrules` para personalizar su comportamiento.

---

## D

**Dependabot:** Herramienta de GitHub que automaticamente crea PRs para actualizar dependencias con vulnerabilidades conocidas o nuevas versiones. Se configura en `.github/dependabot.yml`.

**Docker:** Plataforma para crear, distribuir y ejecutar aplicaciones en contenedores aislados. Usa `Dockerfile` para definir la imagen y `docker-compose.yml` para orquestar multiples servicios.

**DNS (Domain Name System):** Sistema que traduce nombres de dominio (ej: `google.com`) a direcciones IP (ej: `142.250.80.46`). Ver `docs/networking-seguridad.md`.

**DRY (Don't Repeat Yourself):** Principio que establece que cada pieza de conocimiento debe tener una representacion unica dentro del sistema. Evita duplicacion de logica.

**DVC (Data Version Control):** Herramienta para versionar datos, modelos y pipelines de machine learning. Complementa Git para archivos grandes. Usa archivos `.dvc` y `dvc.yaml`.

---

## E

**Egress:** Transferencia de datos que sale de la nube hacia internet. La mayoria de proveedores cloud cobran por GB de egress. Cloudflare R2 es la excepcion notable (egress gratuito).

**Embedding:** Representacion vectorial de texto, imagenes u otros datos. Los embeddings capturan significado semantico y permiten busqueda por similitud. Usados en RAG y busqueda semantica.

**Entorno Efimero:** Entorno de despliegue temporal que se crea para cada Pull Request y se destruye al mergear. Permite probar cambios en un ambiente aislado sin afectar staging o produccion.

**ESLint:** Herramienta de analisis de codigo estatico para JavaScript/TypeScript. Encuentra problemas de estilo y errores potenciales.

---

## F

**Feature Flag:** Tecnica que permite activar o desactivar funcionalidades en produccion sin necesidad de desplegar codigo nuevo. Separa el deploy (tecnico) del release (negocio).

**FinOps (Financial Operations):** Practica de gestion financiera de la nube que combina finanzas, operaciones y tecnologia para optimizar costos. Ver `docs/costos-finops.md`.

**Fly.io:** Plataforma PaaS que convierte aplicaciones Docker en instancias corriendo cerca de los usuarios globalmente. Comparable a Railway o Render.

**Function Calling:** Capacidad de los LLMs de devolver una llamada a funcion estructurada (en JSON) en lugar de texto libre. Permite que agentes de IA usen herramientas externas.

---

## G

**GitHub Actions:** Plataforma de CI/CD integrada en GitHub. Los workflows se definen en `.github/workflows/*.yml` y se disparan por eventos como push, PR, schedule, etc.

**GitHub Copilot:** Asistente de IA para codigo integrado en editores. Lee `.github/copilot-instructions.md` para personalizar sugerencias.

**GitFlow:** Estrategia de branching con ramas principales (`main`, `develop`) y ramas de soporte (`feature`, `release`, `hotfix`). Mas estructurado que GitHub Flow.

**GitOps:** Practica donde Git es la unica fuente de verdad de la infraestructura. Herramientas como ArgoCD o Flux sincronizan automaticamente el estado deseado con el cluster.

**gRPC:** Framework RPC de alto rendimiento de Google que usa Protocol Buffers. Alternativa moderna a REST para comunicacion entre servicios.

---

## H

**Helm:** Gestor de paquetes para Kubernetes. Los charts de Helm definen, instalan y actualizan aplicaciones en Kubernetes.

**Hexagonal Architecture (Ports and Adapters):** Patron arquitectonico que aisla la logica de negocio del mundo exterior mediante puertos (interfaces) y adaptadores (implementaciones concretas).

**Hot Reload:** Funcionalidad de desarrollo que recarga la aplicacion automaticamente al detectar cambios en el codigo fuente, sin necesidad de reiniciar manualmente.

**Hugging Face:** Plataforma para modelos de machine learning, datasets y espacios de demostracion. Principal repositorio de modelos open-source de IA.

---

## I

**IaC (Infrastructure as Code):** Practica de definir infraestructura (servidores, redes, bases de datos) mediante archivos de configuracion versionables en lugar de configuracion manual. Herramientas: Terraform, Pulumi, CloudFormation.

**IDE (Integrated Development Environment):** Editor de codigo con funcionalidades integradas como debugger, terminal, control de versiones. Ejemplos: VS Code, Cursor, IntelliJ.

---

## J

**Jinja2:** Motor de plantillas para Python. Usado frecuentemente para gestion de prompts de LLMs donde se necesita interpolacion de variables.

**JSON Web Token (JWT):** Estandar para transmitir informacion de autenticacion como un objeto JSON firmado digitalmente. Usado para autenticacion stateless en APIs.

---

## K

**KISS (Keep It Simple, Stupid):** Principio que establece que la simplicidad debe ser un objetivo clave en el diseno. Evitar complejidad innecesaria.

**Kubernetes (K8s):** Plataforma de orquestacion de contenedores que automatiza el despliegue, escalado y gestion de aplicaciones en contenedores.

**KV Store (Key-Value Store):** Base de datos que almacena pares clave-valor. Extremadamente rapida para lecturas/escrituras simples. Ejemplo: Redis, DynamoDB, Cloudflare KV.

---

## L

**Lambda:** Servicio de computacion serverless de AWS. Ejecuta codigo en respuesta a eventos sin aprovisionar servidores.

**LangChain / LangGraph:** Frameworks para construir aplicaciones con LLMs. LangChain para cadenas de procesamiento, LangGraph para agentes con estado.

**Let's Encrypt:** Autoridad certificadora gratuita que emite certificados SSL/TLS. Usada por Caddy, Certbot y la mayoria de PaaS.

**Living Documentation:** Documentacion que se genera o valida automaticamente a partir del codigo fuente, pruebas o configuraciones. Ver `docs/documentacion-viva.md`.

**LLM (Large Language Model):** Modelo de lenguaje de gran escala entrenado con vastas cantidades de texto. Ejemplos: GPT-4, Claude, Gemini, Llama, Mistral.

**LLM-as-Judge:** Tecnica de evaluacion donde un LLM evalua la calidad de las respuestas de otro LLM segun criterios predefinidos. Usado en evaluacion de outputs de IA.

**Load Balancer (Balanceador de Carga):** Componente que distribuye el trafico entrante entre multiples instancias de una aplicacion para mejorar disponibilidad y escalabilidad.

---

## M

**MCP (Model Context Protocol):** Protocolo abierto de Anthropic (2024) que estandariza como los LLMs se conectan a fuentes de datos y herramientas externas. Similar a un "USB-C para IA".

**Microservicios:** Arquitectura donde la aplicacion se divide en servicios independientes, cada uno con su propia base de datos, que se comunican via API.

**Migracion (de base de datos):** Cambio controlado y versionado del esquema de una base de datos. Herramientas: Alembic (Python), Prisma (Node.js), Flyway (Java).

**MkDocs:** Generador de documentacion estatica basado en Markdown. Popular para documentacion de proyectos Python. Se configura con `mkdocs.yml`.

**MLflow:** Plataforma open-source para gestionar el ciclo de vida de machine learning: seguimiento de experimentos, versionado de modelos y despliegue.

**MLOps (Machine Learning Operations):** Conjunto de practicas para desplegar, gestionar y monitorear modelos de ML en produccion de forma fiable. Ver `por-dominio/ml-ops.md`.

**Modal:** Plataforma serverless para Python con soporte de GPU. Ideal para inferencia y fine-tuning de modelos de IA. Pago por segundo de uso.

**Monolito Modular:** Aplicacion desplegada como una unidad pero con modulos internos claramente separados por dominio. Recomendado como punto de partida antes de migrar a microservicios.

**Monorepo:** Estrategia donde el codigo de multiples proyectos, librerias o servicios coexiste en un unico repositorio. Herramientas: Turborepo, Nx, Bazel.

**Mypy:** Type checker estatico para Python. Verifica anotaciones de tipo sin ejecutar el codigo.

---

## N

**N+1 Query Problem:** Problema de rendimiento donde se ejecutan N consultas adicionales para cargar datos relacionados. Se resuelve con eager loading (joins, prefetch).

**NAT Gateway:** Servicio de red en la nube que permite que recursos en una subred privada accedan a internet. Tiene costo fijo mensual + costo por trafico.

**Nginx:** Servidor web y reverse proxy de alto rendimiento. Tradicionalmente usado para servir aplicaciones web y como proxy inverso.

**NoSQL:** Bases de datos no relacionales. Tipos: documentales (MongoDB), clave-valor (Redis, DynamoDB), grafos (Neo4j), columnares (Cassandra).

---

## O

**Object Storage:** Almacenamiento para archivos no estructurados (imagenes, videos, backups). Ejemplos: AWS S3, Cloudflare R2, Backblaze B2.

**Observabilidad:** Capacidad de entender el estado interno de un sistema a partir de sus salidas externas. Tres pilares: logs, metrics, traces. Ver `docs/observabilidad.md`.

**Ollama:** Herramienta para ejecutar modelos de lenguaje localmente (Llama, Mistral, Gemma, etc.) sin conexion a internet. Ideal para desarrollo y prototipado.

**OpenAPI (Swagger):** Especificacion para describir APIs REST. Permite generar documentacion interactiva y codigo de cliente/servidor automaticamente.

**OpenCode:** Herramienta CLI de agente de IA que trabaja directamente en repositorios. Lee `AGENTS.md` para instrucciones del proyecto.

**OpenTelemetry:** Estandar open-source para generar, recolectar y exportar datos de telemetria (traces, metrics, logs). Soporta multiples backends.

**ORM (Object-Relational Mapping):** Tecnica que mapea objetos de programacion a tablas de base de datos relacional. Ejemplos: SQLAlchemy, Prisma, Hibernate.

---

## P

**PaaS (Platform as a Service):** Plataforma que abstrae la infraestructura y permite desplegar aplicaciones sin administrar servidores. Ejemplos: Railway, Render, Fly.io, Heroku.

**pgvector:** Extension de PostgreSQL para busqueda vectorial por similitud. Permite usar PostgreSQL como vector store para RAG sin infraestructura adicional.

**Pinecone:** Base de datos vectorial gestionada en la nube. Optimizada para busqueda por similitud a gran escala.

**Poetry:** Gestor de dependencias y empaquetado para Python. Define dependencias en `pyproject.toml`.

**Polyrepo / Multi-repo:** Estrategia donde cada proyecto, servicio o libreria tiene su propio repositorio Git independiente.

**Pre-commit Hooks:** Scripts que se ejecutan automaticamente antes de cada commit para verificar estilo, tipos, o ejecutar pruebas rapidas. Se configuran en `.pre-commit-config.yaml`.

**Prompt Injection:** Ataque de seguridad donde un usuario malicioso inyecta instrucciones en el prompt para hacer que el LLM ignore sus instrucciones originales o realice acciones no deseadas.

**Pydantic:** Libreria de validacion de datos para Python usando type hints. Base de FastAPI. Pydantic v2 es la version actual (2024+).

---

## R

**RAG (Retrieval-Augmented Generation):** Tecnica que combina busqueda de informacion (retrieval) con generacion de texto (LLM). El modelo aumenta sus respuestas con documentos relevantes recuperados de una base de conocimiento.

**Railway:** Plataforma PaaS que permite desplegar aplicaciones desde Git con deteccion automatica de lenguaje, variables de entorno y base de datos incluida.

**Rate Limiting:** Tecnica para limitar el numero de requests que un cliente puede hacer en un periodo de tiempo. Previene abusos y controla costos.

**RBAC (Role-Based Access Control):** Control de acceso basado en roles. Los permisos se asignan a roles y los roles a usuarios.

**ReAct (Reasoning + Acting):** Patron de agente de IA que alterna entre razonamiento (pensar que hacer) y accion (ejecutar herramienta). Ver `por-dominio/agentes-ia.md`.

**Redis:** Base de datos en memoria usada como cache, broker de mensajes y almacen de sesiones. Extremadamente rapida.

**Render:** Plataforma PaaS para desplegar aplicaciones web, APIs, workers y sitios estaticos. Similar a Railway.

**Repository Pattern:** Patron de diseno que abstrae el acceso a datos detras de una interfaz, permitiendo cambiar la implementacion (SQL, NoSQL, mock) sin afectar la logica de negocio.

**Reverse Proxy:** Servidor que recibe requests de internet y los redirige al servidor de aplicacion interno. Maneja SSL, balanceo de carga, compresion. Ejemplos: Nginx, Caddy, Traefik.

**Rollback:** Proceso de revertir un despliegue a una version anterior cuando la nueva version causa problemas.

**Ruff:** Linter y formateador de codigo extremadamente rapido para Python, escrito en Rust. Reemplaza a flake8, isort, black y otros en una sola herramienta.

---

## S

**Saga Pattern:** Patron para manejar transacciones distribuidas en microservicios mediante una secuencia de pasos locales con compensacion si algo falla.

**SemVer (Semantic Versioning):** Estandar de versionado `MAJOR.MINOR.PATCH`. MAJOR: cambios incompatibles. MINOR: funcionalidad nueva compatible. PATCH: correcciones. Ver `docs/versionado-semver.md`.

**Sentry:** Plataforma de monitoreo de errores que agrupa, rastrea y alerta sobre excepciones en aplicaciones. Ofrece free tier generoso.

**Serverless:** Modelo de computacion donde el proveedor cloud gestiona los servidores y tu solo escribes funciones. Pagas por ejecucion, no por servidor inactivo.

**Service Mesh:** Capa de infraestructura que gestiona la comunicacion entre microservicios (descubrimiento, balanceo, cifrado, observabilidad). Ejemplos: Istio, Linkerd.

**Sharding / Particionado:** Tecnica para dividir datos entre multiples bases de datos para escalar horizontalmente. Comun en bases de datos grandes.

**Sphinx:** Generador de documentacion para Python. Usa reStructuredText. El estandar para documentacion de paquetes Python publicados en PyPI.

**SQLAlchemy:** ORM y toolkit SQL para Python. Soporta operaciones sincronas y asincronas. Version 2.0+ usa estilo declarativo moderno.

**SSH (Secure Shell):** Protocolo para acceder de forma segura a servidores remotos. Usa cifrado para proteger la conexion.

**SSL/TLS:** Protocolos criptograficos que proporcionan comunicacion segura en internet. El certificado SSL (hoy TLS) es lo que activa HTTPS y el candado verde en el navegador.

**Supabase:** Alternativa open-source a Firebase. Provee PostgreSQL gestionado, autenticacion, almacenamiento, funciones edge y soporte para pgvector.

---

## T

**Tailscale:** VPN basada en WireGuard que crea una red privada entre tus dispositivos y servidores. Gratis para uso personal (hasta 100 dispositivos).

**Terraform:** Herramienta de Infraestructura como Codigo (IaC) para aprovisionar y gestionar recursos en multiples proveedores cloud. Usa lenguaje HCL.

**Trunk-Based Development:** Estrategia de branching donde los desarrolladores trabajan en ramas de corta duracion (maximo 1-2 dias) y mergean frecuentemente a `main`.

**Turborepo:** Herramienta para gestionar monorepos JavaScript/TypeScript con cache inteligente de builds y tests.

---

## U

**uv:** Gestor de paquetes y entornos Python extremadamente rapido, escrito en Rust. Reemplaza a pip, pip-tools, virtualenv y Poetry en un solo binario. Desarrollado por Astral (los mismos que Ruff).

---

## V

**Vector Store:** Base de datos optimizada para almacenar y buscar embeddings vectoriales. Usada en RAG para encontrar documentos relevantes por similitud semantica.

**Vercel:** Plataforma para desplegar aplicaciones frontend y funciones serverless. Especialmente optimizada para Next.js.

**Vibe Coding:** Paradigma de desarrollo donde el programador usa asistentes de IA como herramienta principal para escribir codigo, delegando la sintaxis a la IA. Ver `docs/guia-vibe-coding.md`.

**Virtualenv / venv:** Herramienta para crear entornos Python aislados con sus propias dependencias. El directorio `.venv/` nunca se commitea.

**VPS (Virtual Private Server):** Maquina virtual en un datacenter que tu administras completamente. Alternativa economica al cloud gestionado. Ejemplos: Hetzner, DigitalOcean, Linode.

---

## W

**WAF (Web Application Firewall):** Firewall que analiza trafico HTTP y bloquea ataques comunes como inyeccion SQL, XSS y fuerza bruta. Cloudflare WAF es el mas popular.

**Webhook:** Callback HTTP que un servicio externo llama cuando ocurre un evento (ej: Stripe notifica un pago, GitHub notifica un push).

**WebSocket:** Protocolo de comunicacion bidireccional entre cliente y servidor sobre una unica conexion TCP. Ideal para aplicaciones en tiempo real (chat, notificaciones).

**WireGuard:** Protocolo de VPN moderno, simple y rapido. Base de Tailscale.

---

## Y

**YAGNI (You Ain't Gonna Need It):** Principio que establece que no debes implementar funcionalidades que no necesitas ahora, basandote en especulacion sobre el futuro.

**YAML:** Formato de serializacion de datos legible por humanos. Usado en CI/CD (GitHub Actions), configuracion (docker-compose.yml) y IaC (Helm, Ansible).

---

## Referencias Cruzadas

- [Catalogo de Archivos y Carpetas](./catalogo-archivos.md) -- todos los archivos posibles en un proyecto
- [Tipos de Licencias](./tipos-licencias.md) -- guia de licencias de software
- [Principios Arquitectonicos](./principios-arquitectonicos.md) -- fundamentos teoricos
- [Guia de Vibe Coding](./guia-vibe-coding.md) -- niveles de desarrollo con IA

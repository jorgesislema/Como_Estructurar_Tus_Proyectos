# Decision de Infraestructura: Donde Ejecutar Tu Proyecto

La decision mas importante que no se toma en el editor de codigo: donde va a vivir tu aplicacion. Esta guia cubre todos los escenarios posibles, desde el prototipo local hasta la nube empresarial, con criterios de decision claros para cada nivel de experiencia.

---

## El Problema del "Solo Funciona en Mi Maquina"

El tipico flujo de vibe coding termina con una aplicacion que funciona en `localhost:3000` o `localhost:8000`. El programador la muestra, todos aplauden, y luego nadie puede usarla porque no esta desplegada en ningun lado. El despliegue no es un "despues", es parte del diseno desde el dia cero.

Preguntas que debes hacerte antes de escribir una linea de codigo:

1. Cuantos usuarios va a tener? (1, 100, 10,000, 1,000,000)
2. Necesita estar disponible 24/7 o puede tener downtime?
3. Cual es el presupuesto mensual maximo?
4. Donde estan los usuarios? (un pais, varios continentes)
5. La aplicacion maneja datos sensibles? (regulaciones GDPR, HIPAA, etc.)
6. Necesita GPU para modelos de IA?
7. Cual es la latencia maxima aceptable?
8. El equipo sabe administrar servidores o necesita algo gestionado?

---

## Opciones de Infraestructura (De Menos a Mas Complejo)

### 1. Solo Local (localhost)

**Que es:** La aplicacion solo corre en tu computadora. Nadie mas puede acceder.

**Cuando usarlo:**
- Prototipo personal o prueba de concepto
- Herramienta de linea de comandos (CLI) que solo usas tu
- Estas aprendiendo y experimentando
- Scripts de automatizacion personal

**Limitaciones:**
- Nadie mas puede usarlo
- Si apagas la computadora, deja de funcionar
- No hay respaldo automatico
- No escala a mas de un usuario

**Ejemplo de proyecto:** Un script que te resume PDFs locales usando un LLM local con Ollama.

---

### 2. Túnel Local (ngrok, Cloudflare Tunnel, LocalCan)

**Que es:** Expone tu `localhost` a internet mediante un tunel seguro. Obtienes una URL publica temporal.

**Cuando usarlo:**
- Mostrar un prototipo a un cliente o colega sin desplegar
- Probar webhooks de servicios externos (Stripe, GitHub, etc.)
- Desarrollo de features que requieren URL publica (OAuth callbacks)
- Demo rapida de un agente de IA o chatbot

**Pros:**
- No necesitas comprar dominio ni servidor
- Se configura en 2 minutos
- Ideal para iterar rapido con feedback

**Contras:**
- URL temporal (cambia al reiniciar en version gratuita)
- Depende de que tu maquina este encendida
- Latencia adicional
- No apto para produccion real

**Herramientas:**
- ngrok: `ngrok http 8000`
- Cloudflare Tunnel: `cloudflared tunnel --url http://localhost:8000`
- LocalCan (alternativa a ngrok)
- Serveo (gratuito, sin instalacion) `ssh -R 80:localhost:8000 serveo.net`

---

### 3. VPS (Servidor Privado Virtual)

**Que es:** Una maquina virtual en un datacenter que tu administras. Tienes control total del sistema operativo.

**Cuando usarlo:**
- Proyecto personal que necesita estar online 24/7
- Presupuesto limitado pero necesitas control total
- Stack no soportado por plataformas serverless (WebSockets, GPU, procesos largos)
- Quieres aprender administracion de servidores
- Necesitas correr modelos de IA que requieren GPU economica

**Pros:**
- Control total del sistema operativo y software
- Mas barato que cloud gestionado para cargas constantes
- Sin limites de tiempo de ejecucion
- Puedes instalar lo que quieras (Ollama, bases de datos, etc.)
- IP fija

**Contras:**
- Tu eres responsable de seguridad, actualizaciones, backups
- Escalar requiere migrar a una maquina mas grande (vertical)
- Si el servidor falla, tu lo arreglas
- Necesitas conocimientos de Linux y administracion de sistemas

**Proveedores y precios (aproximados, 2024-2025):**

| Proveedor | Plan Basico | RAM | vCPU | Precio/mes | Ideal para |
|-----------|-------------|-----|------|------------|------------|
| Hetzner | CX22 | 4 GB | 2 | 4 EUR | Mejor relacion calidad/precio en Europa |
| DigitalOcean | Basic Droplet | 1 GB | 1 | $6 USD | Facil de usar, buena documentacion |
| Linode | Nanode | 1 GB | 1 | $5 USD | Similar a DigitalOcean |
| Vultr | Regular | 1 GB | 1 | $6 USD | Buena cobertura global |
| OVHcloud | VPS Starter | 2 GB | 1 | 4 EUR | Opcion economica en Europa |
| Contabo | Cloud VPS S | 8 GB | 4 | 6 EUR | Mucha RAM por poco precio |
| AWS Lightsail | Basic | 512 MB | 1 | $3.50 USD | Entrada a AWS sin complejidad |

**Para IA/ML en VPS:**
- Lambda Labs: GPU cloud desde $0.50/hora
- RunPod: GPU cloud con precios spot
- Vast.ai: marketplace de GPUs (las mas baratas)

---

### 4. PaaS (Platform as a Service)

**Que es:** Subes tu codigo y la plataforma lo ejecuta. No administras servidores. Ideal para el 80% de los proyectos.

**Cuando usarlo:**
- No quieres administrar servidores
- Necesitas deploy rapido y simple
- El trafico es variable (escala automaticamente)
- Presupuesto medio
- Quieres enfocarte en el codigo, no en la infraestructura

**Comparativa de PaaS populares:**

| Plataforma | Ideal para | Precio inicial | Escala | Soporte IA/ML |
|------------|-----------|----------------|--------|---------------|
| **Railway** | Proyectos fullstack, APIs | $5/mes | Auto | Limitado |
| **Fly.io** | Apps con Docker, edge | Gratis (3 apps) | Auto, global | Bien (GPU bajo demanda) |
| **Render** | APIs, web apps, cron jobs | Gratis | Manual/Auto | Limitado |
| **Heroku** | Legacy, equipos grandes | $5/mes | Manual | No |
| **Koyeb** | Apps globales, buena free tier | Gratis | Auto | Limitado |
| **Zeabur** | Frontend, Next.js, Django | $5/mes | Auto | No |

**Para proyectos con IA:**
- **Modal**: Python serverless con GPU. Ideal para inferencia de LLMs, fine-tuning, procesamiento con GPU. Paga por segundo de GPU usada.
- **Replicate**: API para correr modelos de ML (Stable Diffusion, Llama, etc.). Solo pagas por inferencia.
- **Hugging Face Spaces**: Despliega demos de modelos ML con Gradio/Streamlit. Free tier generoso.
- **Banana.dev**: Serverless GPU para inferencia de modelos.

---

### 5. Serverless (FaaS - Function as a Service)

**Que es:** Escribes funciones que se ejecutan bajo demanda. Solo pagas por el tiempo de ejecucion, no por servidor inactivo.

**Cuando usarlo:**
- APIs con trafico impredecible o esporadico
- Tareas que se ejecutan por eventos (upload de archivo, webhook, cron)
- Prototipos y MVPs con presupuesto minimo
- Microservicios individuales, no aplicaciones completas

**Pros:**
- Cuesta $0 cuando no hay trafico
- Escala automaticamente de 0 a miles de requests/segundo
- Cero administracion de servidores
- Ideal para startups y MVPs

**Contras:**
- Tiempo maximo de ejecucion (15 min en AWS, 60 min en GCP)
- Cold start: latencia en primera ejecucion (100ms-2s)
- No apto para WebSockets (sin workarounds)
- No apto para procesos largos de ML/entrenamiento
- Vendor lock-in: cada proveedor tiene su propia API

**Proveedores serverless:**
- AWS Lambda (+ API Gateway para HTTP)
- Google Cloud Functions / Cloud Run
- Azure Functions
- Cloudflare Workers (edge, ultra-rapido, ideal para APIs ligeras)
- Vercel Functions (para frontend + API)
- Netlify Functions

---

### 6. Cloud Completo (AWS, GCP, Azure)

**Que es:** Infraestructura como servicio. Acceso a cientos de servicios gestionados.

**Cuando usarlo:**
- Proyecto empresarial o startup con inversion
- Necesitas servicios avanzados (Kubernetes, GPUs, data warehouses)
- Requisitos de compliance (SOC2, HIPAA, GDPR)
- Escala masiva o global
- Equipo de DevOps/SRE disponible

**Los tres grandes comparados:**

| Servicio | AWS | GCP | Azure |
|----------|-----|-----|-------|
| Compute | EC2, Lambda, ECS/EKS | Compute Engine, Cloud Run, GKE | VMs, Functions, AKS |
| Base de datos | RDS, DynamoDB, Aurora | Cloud SQL, Firestore, Spanner | SQL Database, Cosmos DB |
| IA/ML | SageMaker, Bedrock | Vertex AI, Gemini API | Azure AI, OpenAI Service |
| Serverless | Lambda | Cloud Run/Functions | Functions |
| Kubernetes | EKS | GKE (mejor) | AKS |
| Free tier | 12 meses | $300/90 dias + siempre gratis | $200/30 dias + siempre gratis |

---

### 7. Edge Computing

**Que es:** Tu codigo se ejecuta en servidores distribuidos globalmente, cerca del usuario.

**Cuando usarlo:**
- Necesitas latencia minima global (< 50ms)
- API ligera o middleware que procesa requests cerca del usuario
- Aplicacion que sirve contenido estatico con logica simple
- Redirecciones, A/B testing, autenticacion en el borde

**Proveedores:**
- Cloudflare Workers
- Vercel Edge Functions
- Deno Deploy
- Netlify Edge Functions
- AWS Lambda@Edge / CloudFront Functions

---

## Arbol de Decision: Que Infraestructura Elegir

```
Pregunta 1: Cuantos usuarios tendra?
  ├── Solo yo / < 10 personas
  │   └── Pregunta 2: Necesita estar 24/7?
  │       ├── No -> Localhost o Tunel (ngrok)
  │       └── Si -> VPS barato (Hetzner $4/mes) o PaaS free tier
  │
  ├── 10 - 1000 usuarios
  │   └── Pregunta 3: Cual es el presupuesto?
  │       ├── < $20/mes -> VPS (Hetzner, DigitalOcean)
  │       ├── $20-$100/mes -> PaaS (Railway, Render, Fly.io)
  │       └── > $100/mes -> Cloud gestionado (AWS/GCP/Azure)
  │
  └── > 1000 o crecimiento rapido
      └── Pregunta 4: Tienes equipo de DevOps?
          ├── No -> PaaS con auto-scaling (Railway, Fly.io)
          └── Si -> Cloud con Kubernetes o serverless

Pregunta adicional: El proyecto usa IA/ML con GPU?
  ├── Prototipo/Desarrollo -> Local (Ollama) o Google Colab
  ├── Produccion esporadica -> Modal, Replicate, RunPod (pago por uso)
  ├── Produccion constante baja -> VPS con GPU (Lambda Labs, Vast.ai)
  └── Produccion empresarial -> AWS SageMaker, GCP Vertex AI, Azure AI
```

---

## Que NO Considera el Vibe Coder (y Deberia)

Cuando estas codeando en VSCode con IA, estas son las cosas invisibles que determinan si tu proyecto vive o muere:

1. **Dominio y DNS**: como comprar un dominio, configurar DNS, apuntar a tu servidor. Sin dominio, tu app es una IP o URL fea que nadie recuerda.

2. **HTTPS y certificados SSL**: Let's Encrypt es gratis, pero hay que configurarlo. Sin HTTPS, los navegadores marcan tu sitio como "no seguro" y muchas APIs (como OpenAI) rechazan requests desde origenes no seguros.

3. **Backups**: si tu base de datos desaparece, perdiste todo. Un VPS de $4 no hace backups automaticos.

4. **Monitoreo basico**: al menos saber si tu app esta viva. Un health check simple que te avise por Telegram/Discord si algo falla.

5. **Limites de la plataforma**: cada plataforma tiene limites (tiempo de ejecucion, tamano de request, memoria, almacenamiento). Si no los conoces, tu app falla en produccion de formas misteriosas.

6. **Variables de entorno en produccion**: en local usas `.env`. En produccion necesitas configurarlas en la plataforma. Si usas VPS, necesitas un sistema para gestionarlas.

7. **Cold starts en serverless**: tu funcion serverless puede tardar 2 segundos en arrancar si no recibe trafico hace rato. El usuario ve un spinner. Eso no pasa en `localhost`.

8. **Rate limits de APIs externas**: OpenAI, Anthropic, Google tienen limites de requests por minuto. En local nunca los alcanzas. En produccion con 100 usuarios, si.

---

## Recomendacion por Perfil

### Vibe Coder Principiante (Nivel 0-1)
- **Desarrollo**: localhost
- **Mostrar a otros**: Tunel (ngrok) 
- **Produccion simple**: Railway ($5/mes) o Render (gratis para empezar)
- **No intentes**: VPS, Kubernetes, AWS desde cero

### Vibe Coder Intermedio (Nivel 2-3)
- **Desarrollo**: localhost + Docker
- **Produccion**: PaaS (Fly.io, Railway) o VPS (Hetzner + Coolify)
- **IA/ML**: Modal o Replicate para inferencia

### Ingeniero de IA Avanzado (Nivel 4)
- **Desarrollo**: localhost + Docker Compose + entornos efimeros
- **Staging**: Cloud gestionado (GCP Cloud Run, AWS ECS)
- **Produccion**: Kubernetes (GKE, EKS) o serverless para componentes especificos
- **IA/ML**: SageMaker/Vertex AI para fine-tuning, Modal/Replicate para inferencia

---

## Referencias Cruzadas

- [Arquitecturas de Despliegue](./arquitecturas-despliegue.md) -- Monolito, microservicios, serverless, edge
- [Entornos y Stages](./entornos-y-stages.md) -- Dev, staging, produccion, efimeros
- [Bases de Datos y Almacenamiento](./bases-datos-almacenamiento.md) -- SQL, NoSQL, vector stores, cache
- [Networking y Seguridad](./networking-seguridad.md) -- DNS, CDN, HTTPS, WAF
- [CI/CD y GitOps](./cicd-gitops.md) -- Pipelines y despliegues progresivos
- [Observabilidad](./observabilidad.md) -- Logs, metrics, traces, alertas
- [Costos y FinOps](./costos-finops.md) -- Estimacion y optimizacion de costos

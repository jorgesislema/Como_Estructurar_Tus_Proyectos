# Arquitecturas de Despliegue: Monolito, Microservicios, Serverless, Edge y Patrones Hibridos

Esta guia cubre los patrones de despliegue mas comunes, con criterios de decision, pros/contras, y recomendaciones por nivel de experiencia y tipo de proyecto.

---

## 1. Monolito

**Definicion:** Una sola aplicacion que contiene toda la logica de negocio, UI, acceso a datos y configuracion en un unico despliegue.

**Estructura tipica:**
```
[Usuario] --> [Load Balancer] --> [App Server 1]
                               --> [App Server 2]
                               --> [App Server N]
                                       |
                                  [Base de Datos]
```

### Cuando usar monolito

- Proyecto nuevo o MVP (no sabes aun como dividirlo)
- Equipo pequeno (1-5 desarrolladores)
- Dominio simple o bien conocido
- No necesitas escalar componentes independientemente
- Tiempo de mercado es la prioridad

### Ventajas
- Desarrollo mas rapido (un solo repo, un solo deploy)
- Debugging mas simple (todo en un proceso)
- Testing mas simple (no hay llamadas de red entre componentes)
- Operaciones mas simples (un solo artefacto)
- Latencia minima entre modulos (llamadas en memoria, no HTTP)

### Desventajas
- Escalar requiere escalar toda la aplicacion (no solo la parte con mas carga)
- Acoplamiento tiende a crecer con el tiempo
- Un bug en un modulo puede tirar toda la aplicacion
- Deploys mas lentos a medida que crece
- Dificil que multiples equipos trabajen simultaneamente

### El "Monolito Modular"

No todo monolito es una bola de barro. Un monolito bien disenado tiene modulos claramente separados por dominio, con interfaces explicitas entre ellos. Es la arquitectura recomendada para empezar.

```
src/
├── usuarios/       # Modulo de usuarios (independiente)
├── productos/      # Modulo de productos (independiente)
├── pedidos/        # Modulo de pedidos
├── pagos/          # Modulo de pagos
└── shared/         # Codigo compartido (cuidado con el acoplamiento)
```

### Recomendacion

Empieza siempre con un monolito modular. Solo dividelo en servicios separados cuando tengas una razon demostrable (no especulativa).

---

## 2. Microservicios

**Definicion:** Multiples servicios independientes, cada uno con su propia base de datos, que se comunican via API (HTTP, gRPC, mensajeria).

**Estructura tipica:**
```
                               [API Gateway]
                              /      |       \
                    [Servicio A] [Servicio B] [Servicio C]
                         |           |           |
                    [DB A]      [DB B]      [DB C]
                         
                    [Message Queue / Event Bus]
```

### Cuando usar microservicios

- Equipos grandes (20+ desarrolladores) organizados por dominio
- Necesitas escalar componentes independientemente
- Diferentes partes tienen requerimientos tecnologicos distintos
- Necesitas deploy independiente por equipo
- La aplicacion es tan grande que el monolito es inmanejable

### NO usar microservicios cuando

- El equipo es pequeno (< 10 personas)
- El dominio es simple
- No tienes automatizacion de CI/CD solida
- No tienes experiencia con sistemas distribuidos
- Es tu primer proyecto (empieza con monolito)

### Patrones comunes en microservicios

**API Gateway:**
Punto unico de entrada que enruta requests al servicio correcto, maneja autenticacion, rate limiting, y agregacion de respuestas.

**Service Discovery:**
Los servicios necesitan encontrarse entre si. Opciones: DNS (Kubernetes), Consul, etcd, o el propio API Gateway.

**Comunicacion entre servicios:**
- Sincrona: HTTP/REST, gRPC (respuesta inmediata, pero acopla servicios)
- Asincrona: RabbitMQ, Kafka, SQS/SNS, Pub/Sub (desacopla, pero anade complejidad)

**Saga Pattern (transacciones distribuidas):**
En un monolito, una transaccion de BD es atomica. En microservicios, una operacion que afecta varios servicios necesita Saga: secuencia de pasos con compensacion si algo falla.

### Desventajas reales de microservicios

- Complejidad operativa masiva (red, latencia, fallos parciales)
- Debugging mucho mas dificil (tracing distribuido obligatorio)
- Consistencia de datos compleja (cada servicio tiene su DB)
- Testing end-to-end casi imposible
- Costo de infraestructura mayor
- Se necesita un equipo con experiencia en sistemas distribuidos

---

## 3. Arquitectura Orientada a Eventos (Event-Driven)

**Definicion:** Los servicios se comunican mediante eventos asincronos. Un servicio publica un evento ("PedidoCreado") y otros servicios reaccionan.

```
[Servicio Pedidos] --(publica)--> [Message Queue]
                                       |
                    [Servicio Inventario] --(suscribe)-->
                    [Servicio Facturacion] --(suscribe)-->
                    [Servicio Notificaciones] --(suscribe)-->
```

### Cuando usar
- Flujos de negocio naturalmente asincronos
- Necesitas desacoplar servicios completamente
- Procesamiento de datos en tiempo real
- Integracion con multiples sistemas

### Herramientas
- RabbitMQ (colas tradicionales, facil)
- Apache Kafka (event streaming, alta escala)
- AWS SQS/SNS (gestionado, simple)
- Google Pub/Sub (gestionado, simple)
- Redis Streams (ligero, si ya usas Redis)

---

## 4. Serverless

**Definicion:** Funciones individuales que se ejecutan bajo demanda. No hay servidores que administrar.

**Estructura tipica:**
```
[API Gateway] --> [Funcion Auth]
              --> [Funcion GetUsers] --> [DynamoDB]
              --> [Funcion CreateOrder] --> [SQS] --> [Funcion ProcessOrder]
              --> [Funcion UploadFile] --> [S3] --> [Funcion ProcessFile]
```

### Cuando usar serverless
- APIs con trafico variable o esporadico
- Procesamiento de eventos (uploads, webhooks, cron)
- Microservicios muy pequenos y enfocados
- Startup/MVP con presupuesto limitado
- Componentes que no se usan constantemente

### Cuando NO usar serverless
- Aplicaciones con trafico constante predecible (VPS o contenedores son mas baratos)
- Procesos de larga duracion (> 15 minutos)
- Aplicaciones con estado (WebSockets, sesiones largas)
- Aplicaciones con cold start inaceptable (latencia < 100ms garantizada)
- Si no quieres vendor lock-in

### Cold Start: el enemigo silencioso

Cuando una funcion serverless no se ha ejecutado en minutos, la plataforma la "apaga". La siguiente llamada tiene que iniciar el runtime, cargar dependencias, y conectar a la DB. Eso puede tomar de 100ms a 2 segundos.

Soluciones:
- Lambda SnapStart (AWS, para Java)
- Provisioned Concurrency (pagas por mantener funciones "calientes")
- Cloudflare Workers (aislamiento V8, cold start < 5ms)
- Usar un runtime ligero (Python/Node.js tienen cold start mas rapido que Java/.NET)

---

## 5. Edge Computing

**Definicion:** El codigo se ejecuta en servidores distribuidos globalmente, en el borde de la red, cerca del usuario final.

```
[Usuario en Tokyo] --> [Edge Tokyo]
[Usuario en Londres] --> [Edge Londres]
[Usuario en Sao Paulo] --> [Edge Sao Paulo]
```

### Cuando usar edge
- APIs que necesitan latencia minima global
- Procesamiento de requests en el borde antes de llegar al origen
- A/B testing, feature flags, redirecciones geograficas
- Autenticacion en el borde (verificar JWT sin ir al servidor principal)
- Respuesta a ataques DDoS (filtrar en el borde)

### Limitaciones del edge
- Tiempo de ejecucion muy limitado (10-50ms en workers, 30s en funciones edge)
- APIs limitadas (no tienes acceso al sistema de archivos, no todas las librerias de Node.js)
- Memoria limitada (128 MB tipico)
- No apto para procesamiento pesado o acceso a bases de datos relacionales

---

## 6. Patrones Hibridos (lo que realmente se usa)

En la practica, la mayoria de los proyectos usan combinaciones:

### Patron 1: Monolito + Serverless para tareas especificas
```
[Monolito (API principal)] --> [SQS] --> [Funcion Serverless: Procesar Imagen]
                            --> [S3] --> [Funcion Serverless: Generar Thumbnail]
```
La logica de negocio central en monolito. Tareas intensivas o esporadicas en serverless.

### Patron 2: API Gateway + Microservicios + Serverless
```
[API Gateway] --> [Servicio Usuarios (contenedor)]
              --> [Servicio Pedidos (contenedor)]
              --> [Funcion Serverless: Enviar Email]
              --> [Funcion Serverless: Generar Reporte]
```
Servicios principales siempre corriendo. Funciones auxiliares serverless.

### Patron 3: Frontend Edge + Backend Serverless + DB Gestionada
```
[Vercel/Cloudflare (frontend estatico + edge functions)]
        |
[API Gateway + Lambda/Cloud Run]
        |
[RDS/PlanetScale/Supabase (base de datos)]
```
Stack completo sin servidores que administrar. Ideal para equipos sin DevOps.

### Patron 4: VPS Unico (el clasico)
```
[VPS Unico (Hetzner $4/mes)]
  ├── Nginx (reverse proxy + HTTPS)
  ├── Aplicacion (Django, FastAPI, Next.js)
  ├── PostgreSQL (local)
  └── Redis (local)
```
Todo en una maquina. Simple, barato, suficiente para el 90% de proyectos personales y MVPs. Usa Docker Compose para organizar los servicios dentro del VPS.

---

## Mapa de Decision por Tipo de Proyecto

### API REST sencilla (CRUD)
- Pocos usuarios: VPS (Hetzner) o PaaS (Railway)
- Muchos usuarios: Serverless (Lambda + API Gateway) o Cloud Run

### Aplicacion Web con IA (LLMs, chatbots)
- Prototipo: PaaS (Railway/Fly.io) + API de OpenAI
- Produccion pequena: VPS con GPU (RunPod) + API Gateway
- Produccion grande: Kubernetes + microservicios + colas para tareas de IA

### Pipeline de Datos / ETL
- Batch: Funciones serverless disparadas por cron o eventos
- Streaming: Kafka + microservicios

### Agente de IA autonomo
- Ejecucion bajo demanda: Modal o RunPod (pago por uso de GPU)
- Ejecucion 24/7: VPS con GPU (Lambda Labs desde $0.50/hr)

### Frontend estatico + API
- Vercel/Netlify (frontend) + Railway/Fly.io (backend)
- Cloudflare Pages (frontend) + Cloudflare Workers (API)

---

## Lo Que Debes Saber Antes de Elegir

1. **No hay arquitectura perfecta**. Todas tienen trade-offs. La arquitectura correcta es la que resuelve tu problema hoy y puede evolucionar manana.

2. **Empieza simple**. Monolito modular. Si funciona, no lo toques. Si duele, extrae servicios.

3. **El costo de operar microservicios es alto**. No lo subestimes. Necesitas CI/CD, observabilidad, service mesh, manejo de fallos, consistencia eventual.

4. **Serverless es barato... hasta que no lo es**. Si tienes trafico constante, un VPS de $4/mes es mas barato que Lambda. Haz las cuentas.

5. **La latencia de red existe**. En localhost, una llamada entre modulos toma microsegundos. En produccion, una llamada HTTP entre servicios toma milisegundos. Si encadenas 5 servicios, el usuario espera.

6. **El edge es un complemento, no un reemplazo**. No puedes correr toda tu app en el edge. Es para tareas especificas de baja latencia.

---

## Referencias Cruzadas

- [Decision de Infraestructura](./decision-infraestructura.md) -- VPS, Cloud, Serverless, PaaS
- [Entornos y Stages](./entornos-y-stages.md) -- Dev, staging, produccion
- [CI/CD y GitOps](./cicd-gitops.md) -- Automatizacion de despliegues
- [Costos y FinOps](./costos-finops.md) -- Cuanto cuesta cada arquitectura

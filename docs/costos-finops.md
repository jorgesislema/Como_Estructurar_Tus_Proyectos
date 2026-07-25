# Costos y FinOps: Cuanto Cuesta Realmente tu Proyecto

Uno de los choques mas duros al pasar de "funciona en localhost" a "lo despliego en la nube" es la factura. En local, el costo es $0. En la nube, cada recurso cuesta. Y los costos de IA (LLMs, GPUs, embeddings) pueden dispararse rapidamente si no los controlas.

Esta guia cubre estimacion de costos, optimizacion continua y practicas de FinOps (Financial Operations) para no llevarse sorpresas.

---

## 1. Los Costos Ocultos de "Gratis en Local"

En tu maquina, esto es gratis:
- CPU y RAM de tu computadora
- Almacenamiento (tu disco duro)
- Llamadas a la API de OpenAI (pagas con tu tarjeta, pero es tu bolsillo)
- Base de datos (SQLite en un archivo)

En produccion, todo esto cuesta. Y hay costos que ni siquiera sabias que existian:

### Costos visibles (los que esperas)
- Servidor(es) donde corre la app
- Base de datos gestionada
- Almacenamiento (S3, R2, etc.)

### Costos semi-visibles (los que olvidas)
- Transferencia de datos (egress): cada GB que sale de la nube a internet
- IPs estaticas (en AWS, una IP elastica sin usar cuesta)
- Snapshots y backups
- Certificados SSL (si no usas Let's Encrypt)

### Costos invisibles (los que descubres con la factura)
- NAT Gateway en AWS (~$32/mes por zona + trafico)
- Balanceador de carga (~$20/mes)
- Logs en CloudWatch (se llena y no te avisa)
- Metricas en CloudWatch (cada metrica custom cuesta)
- Trafico entre zonas de disponibilidad
- Cold storage retrieval (recuperar datos archivados)

---

## 2. Estimacion de Costos por Tipo de Proyecto

Los precios son aproximados (2024-2025) y asumen trafico moderado.

### Proyecto Personal (0-100 usuarios/dia)

| Componente | Opcion | Costo/mes |
|-----------|--------|-----------|
| Hosting | Railway / Render | $0-$5 |
| Base de datos | SQLite en el server / Neon free | $0 |
| Cache | No necesario | $0 |
| Dominio | Namecheap / Cloudflare | $1/mes |
| CDN | Cloudflare free | $0 |
| Monitoreo | Sentry free | $0 |
| **Total** | | **$1-$6/mes** |

### API con IA (100-1000 usuarios/dia)

| Componente | Opcion | Costo/mes |
|-----------|--------|-----------|
| Hosting | Railway Pro / Fly.io | $5-$25 |
| Base de datos | Neon / Supabase free | $0-$10 |
| Cache | Redis (Upstash free) | $0-$5 |
| LLM API | OpenAI (moderado) | $20-$200 |
| Dominio | Cloudflare | $1/mes |
| Monitoreo | Sentry + Grafana free | $0 |
| **Total** | | **$26-$241/mes** |

El LLM es el costo variable. 1000 requests/dia a GPT-4o pueden costar $50-200/mes. 1000 requests/dia a GPT-4o-mini pueden costar $5-20/mes. La diferencia es 10x.

### Startup / SaaS (1000-10000 usuarios/dia)

| Componente | Opcion | Costo/mes |
|-----------|--------|-----------|
| Hosting | Railway Pro / Fly.io / AWS ECS | $50-$200 |
| Base de datos | RDS / Supabase Pro | $25-$100 |
| Cache | Redis gestionado | $10-$50 |
| LLM API | OpenAI + cache semantica | $200-$2000 |
| Almacenamiento | R2 / S3 | $5-$50 |
| CDN | Cloudflare Pro | $20 |
| Monitoreo | Sentry + Grafana | $0-$50 |
| CI/CD | GitHub Actions | $0-$20 |
| **Total** | | **$310-$2490/mes** |

### Enterprise / Gran Escala (100K+ usuarios/dia)

| Componente | Costo/mes |
|-----------|-----------|
| Kubernetes (EKS/GKE/AKS) | $200-$1000 |
| Base de datos (varias instancias) | $200-$2000 |
| Cache distribuido | $100-$500 |
| LLMs (multiples proveedores) | $2000-$20000+ |
| CDN (alto trafico) | $100-$1000 |
| Monitoreo y observabilidad | $200-$1000 |
| Seguridad (WAF, auditoria) | $100-$500 |
| Soporte y operaciones | Personal o consultoria |
| **Total** | **$2900-$26000+/mes** |

---

## 3. Costos de IA/LLMs Especificamente

Los LLMs tienen el mayor potencial de descontrol de costos. Una sola llamada mal disenada puede multiplicar tu factura.

### Precios de referencia (2024-2025)

| Modelo | Input (por 1M tokens) | Output (por 1M tokens) |
|--------|----------------------|------------------------|
| GPT-4o | $2.50 | $10.00 |
| GPT-4o-mini | $0.15 | $0.60 |
| GPT-4-turbo | $10.00 | $30.00 |
| Claude Sonnet 4 | $3.00 | $15.00 |
| Claude Opus 4 | $15.00 | $75.00 |
| Claude Haiku | $0.25 | $1.25 |
| Gemini 2.0 Flash | $0.10 | $0.40 |
| Gemini 2.0 Pro | $1.25 | $5.00 |
| DeepSeek V3 | $0.27 | $1.10 |
| Llama 3.1 70B (Groq) | $0.59 | $0.79 |
| Mixtral 8x7B (Together) | $0.60 | $0.60 |

### Cuanto cuesta realmente una conversacion

Ejemplo: chatbot con 10 mensajes de usuario (~200 tokens cada uno), 10 respuestas del asistente (~300 tokens cada una), con un system prompt de 500 tokens:

```
System prompt (cada request): 500 tokens
Historial acumulado: variable, crece con la conversacion
Total prompt por request 10: ~500 + (10 mensajes * 200) = 2500 tokens input
Total respuesta: 300 tokens output

Con GPT-4o:
Input: 2500/1M * $2.50 = $0.00625
Output: 300/1M * $10.00 = $0.003
Total por respuesta: ~$0.01

Con GPT-4o-mini:
Input: 2500/1M * $0.15 = $0.000375
Output: 300/1M * $0.60 = $0.00018
Total por respuesta: ~$0.0005
```

Una conversacion de 10 intercambios: $0.10 con GPT-4o, $0.005 con GPT-4o-mini. Si tienes 1000 conversaciones/dia: $100 vs $5. La diferencia es 20x.

### Estrategias de Reduccion de Costos en LLMs

1. **Cache semantica**: si dos usuarios preguntan cosas similares, responde de cache (Redis + embeddings). Puede reducir costos 30-60%.

2. **Model routing**: usa modelos pequenos/baratos para tareas simples (clasificar, extraer, resumir) y modelos grandes solo para tareas complejas.

3. **Prompt mas corto**: cada token en el system prompt cuenta. Se conciso. Menos ejemplos few-shot.

4. **Limpiar historial**: no mandes toda la conversacion historica en cada request. Resume conversaciones largas.

5. **Limites por usuario**: define un maximo de tokens o llamadas por usuario/dia.

6. **Usar modelos locales para tareas simples**: Ollama con Llama 3.1 8B puede hacer clasificacion, extraccion, y tareas basicas sin costo de API.

---

## 4. FinOps: Practicas de Gestion de Costos

### Principios FinOps

1. **Visibilidad**: saber exactamente cuanto gastas, en que, y quien lo genera
2. **Responsabilidad**: cada equipo/feature tiene un presupuesto
3. **Optimizacion continua**: revisar costos regularmente, no solo cuando llega la factura

### Herramientas de Gestion de Costos

| Herramienta | Para | Precio |
|-------------|------|--------|
| AWS Cost Explorer | Solo AWS | Gratis |
| GCP Cost Management | Solo GCP | Gratis |
| Azure Cost Management | Solo Azure | Gratis |
| Vantage | Multi-cloud | Desde $0 (hasta $1000/mes de gasto) |
| CloudZero | Ingenieria, no solo finanzas | Bajo demanda |
| InfraCost | Estimar costos en PR (antes de deploy) | Gratis (open source) |
| Cast AI | Optimizacion automatica de K8s | % de ahorro |
| Dopplere | Gestion de costos LLM | Bajo demanda |

### Practicas Diarias de FinOps

1. **Presupuestos y alertas**: configura alertas en tu proveedor cloud cuando el gasto proyectado supere tu presupuesto

2. **Tags/Labels**: etiqueta todos los recursos por: proyecto, entorno, equipo, feature. Sin tags no sabes que cosa genera cada costo.

3. **Revisar factura mensual**: dedica 30 minutos al mes a revisar la factura. Busca: recursos huerfanos, IPs sin usar, volumenes sin adjuntar, instancias sobredimensionadas.

4. **Limpiar recursos no usados**: entornos de PR que no se eliminaron, snapshots viejos, buckets S3 sin uso, IPs elasticas sin asociar.

5. **Comprometer uso (Reserved/Savings Plans)**: si sabes que vas a usar un recurso por 1-3 años, comprometerte reduce el costo 30-60%. Pero no compres reserved instances el primer mes, espera a conocer tu uso real.

6. **Spot instances**: para cargas no criticas (workers, CI, procesamiento batch), usa instancias spot/preemptibles que cuestan 50-90% menos.

---

## 5. Errores Famosos que Cuestan Dinero

### Error 1: Dejar un servicio caro corriendo

Clasico: lanzas una instancia GPU ($3/hora) para fine-tuning, terminas, pero olvidas apagarla. Una semana despues: $504 de factura.

Solucion: programa apagado automatico. Usa instancias spot o plataformas como Modal/Replicate que cobran solo por ejecucion.

### Error 2: Bucle infinito en LLM

Tu agente de IA entra en un bucle: llama al LLM, el LLM llama una herramienta, la herramienta falla, el LLM reintenta, infinitamente. Cada iteracion cuesta tokens.

Solucion: `max_steps` obligatorio en todos los agentes y bucles. Alerta de costo por ejecucion.

### Error 3: Logs infinitos

Activas DEBUG en produccion "para ver que pasa". Cada request genera 500 lineas de log. CloudWatch cobra $0.50/GB ingesta + $0.03/GB almacenamiento. Miles de requests/dia = cientos de dolares en logs.

Solucion: usa el nivel de log apropiado por entorno. En produccion: WARNING o INFO como maximo.

### Error 4: Egress descontrolado

Tus usuarios descargan archivos grandes. Cada GB que sale de AWS a internet cuesta $0.09 (primeros 10TB). 1000 usuarios descargando 100MB cada uno = 100GB = $9. Pero si tienes 100,000 usuarios...

Solucion: usa CDN con egress gratuito (Cloudflare R2) o dentro del free tier.

### Error 5: NAT Gateway en AWS

Creaste un VPC con NAT Gateway para que tus funciones Lambda accedan a internet. El NAT Gateway cuesta $32/mes + $0.045/GB. Una cuenta de AWS nueva muchas veces recibe esta sorpresa.

Solucion: para Lambdas que solo llaman APIs externas, no necesitas VPC. Si necesitas VPC, considera alternativas mas baratas o un solo NAT Gateway compartido.

---

## 6. Recomendacion por Presupuesto

### Presupuesto: $0/mes (proyecto hobby)

- Frontend: Vercel/Netlify (gratis)
- Backend: Fly.io (3 apps gratis) o Cloudflare Workers (100K req/dia gratis)
- Base de datos: Turso (gratis 9GB) o Neon (gratis 10GB)
- Cache: Upstash Redis (gratis 10K comandos/dia)
- LLM: modelo local con Ollama o GPT-4o-mini (es tan barato que $10 duran meses)
- Dominio: no compres dominio, usa URL de la plataforma
- Monitoreo: Sentry free + Grafana Cloud free

### Presupuesto: $20/mes (proyecto personal serio)

- VPS: Hetzner CX22 (4GB, 2 vCPU, 40GB, $5/mes)
- Dominio: Cloudflare ($10/ano ~ $0.83/mes)
- Base de datos: PostgreSQL en el mismo VPS
- Cache: Redis en el mismo VPS
- LLM: OpenAI API (presupuesto: $10/mes para GPT-4o-mini)
- Almacenamiento: Cloudflare R2 ($0.015/GB)
- Monitoreo: Sentry free + Grafana free en el VPS

### Presupuesto: $100/mes (startup / SaaS temprano)

- Hosting: Railway Pro ($20/mes) o Fly.io
- Base de datos: Supabase Pro ($25/mes)
- Cache: Upstash Redis ($10/mes)
- LLM: OpenAI + Anthropic con cache ($30-50/mes)
- Dominio + CDN: Cloudflare ($20/mes Pro)
- Almacenamiento: R2 o S3
- Monitoreo: Sentry Team ($26/mes)
- CI/CD: GitHub Actions ($0 para publicos)

---

## Referencias Cruzadas

- [Decision de Infraestructura](./decision-infraestructura.md) -- Comparativa de costos entre opciones
- [Bases de Datos](./bases-datos-almacenamiento.md) -- Costos de almacenamiento
- [Observabilidad](./observabilidad.md) -- Herramientas de monitoreo y sus costos
- [Arquitecturas de Despliegue](./arquitecturas-despliegue.md) -- Impacto de la arquitectura en costos

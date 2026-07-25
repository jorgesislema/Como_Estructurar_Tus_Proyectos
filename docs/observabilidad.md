# Observabilidad: Logs, Metrics, Traces y Alertas

Tu aplicacion esta en produccion. Como sabes si esta funcionando? Como sabes si esta funcionando *bien*? Como detectas un problema ANTES de que un usuario te lo reporte? Esto es observabilidad. Y es lo mas olvidado por quien solo programa en VSCode.

---

## Los Tres Pilares de la Observabilidad

### 1. Logs (Registros)

**Que son:** Mensajes que tu aplicacion emite durante su ejecucion. "Usuario 123 inicio sesion", "Error al conectar a la base de datos", "Pago procesado: $50".

**Buenas practicas de logging:**

- Usa niveles: DEBUG, INFO, WARNING, ERROR, CRITICAL
- Usa logging estructurado (JSON) en lugar de texto plano
- Incluye contexto: request ID, user ID, timestamp
- NUNCA loguees secretos, contraseñas, tokens, o datos personales (PII)
- En produccion, log level minimo: WARNING o INFO

**Logging estructurado vs texto plano:**

Mal (texto plano):
```
User 123 logged in from 1.2.3.4
```

Bien (JSON estructurado):
```json
{
  "timestamp": "2025-01-15T10:30:00Z",
  "level": "INFO",
  "event": "user_login",
  "user_id": 123,
  "ip": "1.2.3.4",
  "request_id": "abc-def-123"
}
```

El logging estructurado permite buscar, filtrar y analizar logs eficientemente.

### Herramientas de Logging

| Herramienta | Precio | Ideal para |
|-------------|--------|-----------|
| Mezmo (antes LogDNA) | Gratis (hasta 500MB/mes) | Logs centralizados simple |
| Papertrail | $7/mes | Logs de servidores VPS |
| Axiom | Gratis (hasta 500MB) | Logs + dashboards |
| Grafana Loki | Gratis (self-hosted) | Si ya usas Grafana |
| Datadog | Caro | Empresas |
| CloudWatch (AWS) | Pago por uso | Si estas en AWS |
| Console del VPS | Gratis | Malo: no persiste, dificil buscar |

---

### 2. Metrics (Metricas)

**Que son:** Valores numericos medidos a lo largo del tiempo. "Requests por segundo: 150", "Latencia promedio: 45ms", "Uso de CPU: 67%".

**Metricas esenciales (las 4 de oro - Google SRE):**

| Metrica | Que mide | Ejemplo | Alerta si |
|---------|---------|---------|-----------|
| Latencia | Tiempo de respuesta | p99 < 200ms | > 1 segundo |
| Trafico | Requests por segundo | 500 req/s | Sube 3x de repente (ataque?) |
| Errores | Tasa de requests fallidos | < 1% | > 5% |
| Saturacion | Que tan "lleno" esta el sistema | CPU 40% | > 80% |

**Metricas adicionales para proyectos con IA:**

| Metrica | Que mide | Alerta si |
|---------|---------|-----------|
| Costo por llamada LLM | USD por request | > presupuesto diario |
| Tokens por request | Eficiencia de prompts | Promedio sube > 20% |
| Latencia del LLM | Tiempo de respuesta del modelo | p99 > 10 segundos |
| Cache hit rate | % de respuestas servidas de cache | < 50% |
| Tasa de guardrails activados | Contenido bloqueado | > 1% (posible jailbreak) |
| Puntuacion de evaluacion | Calidad de outputs | Degrada > 5% de baseline |

### Herramientas de Metrics

| Herramienta | Precio | Ideal para |
|-------------|--------|-----------|
| Prometheus + Grafana | Gratis (self-hosted) | Estandar open source |
| Grafana Cloud | Gratis (10K series) | Prometheus gestionado |
| Datadog | Desde $15/host/mes | Empresas, all-in-one |
| New Relic | Gratis (100GB/mes) | APM completo |
| VictoriaMetrics | Gratis (self-hosted) | Alternativa ligera a Prometheus |

---

### 3. Traces (Trazas Distribuidas)

**Que son:** El recorrido completo de un request a traves de todos los servicios que toca. "El request de login paso por: API Gateway (10ms) -> Auth Service (50ms) -> PostgreSQL (5ms) -> Redis (2ms) -> Respuesta".

Sin traces, cuando algo va lento no sabes que componente es el culpable.

**Cuando necesitas traces:**
- Tienes mas de 2 servicios (microservicios, API + worker + DB)
- Hay operaciones lentas que no sabes donde se atascan
- Usas colas de mensajes y necesitas seguir el flujo completo

**Herramientas de Tracing**

| Herramienta | Precio | Ideal para |
|-------------|--------|-----------|
| Jaeger | Gratis (self-hosted) | Open source, compatible con OpenTelemetry |
| Grafana Tempo | Gratis (self-hosted) | Si ya usas Grafana |
| Honeycomb | Desde $100/mes | Analisis avanzado de traces |
| Datadog APM | Caro | Empresas, all-in-one |
| Sentry | Gratis (errores) / $26/mes | Errores + performance |

---

## 4. Alertas: Que Hacer Cuando Algo Falla

### Niveles de Alerta

**P1 - Critico:** El servicio esta caido. Usuarios no pueden acceder.
- Notificar por telefono/llamada + mensaje + email
- Tiempo de respuesta esperado: < 15 minutos

**P2 - Alto:** Funcionalidad principal degradada.
- Notificar por mensaje + email
- Tiempo de respuesta: < 1 hora

**P3 - Medio:** Funcionalidad no critica afectada.
- Notificar por email o ticket
- Tiempo de respuesta: < 24 horas

**P4 - Bajo:** issue cosmético, mejora futura.
- Se crea un issue en el backlog

### Canales de Alerta

- Email (basico, para P3/P4)
- Slack/Discord/Teams (equipo)
- Telegram/WhatsApp (personal, rapido)
- PagerDuty/Opsgenie (profesional, escalado, guardias)
- Llamada telefonica (solo P1 critico)

### Buenas Practicas de Alertas

1. **Alerta por sintoma, no por causa**: alerta si "usuarios no pueden hacer login", no si "CPU > 80%". La CPU alta es la causa, el login fallido es el sintoma que afecta al usuario.

2. **Evita fatiga de alertas**: si recibes 50 alertas al dia que nunca requieren accion, eventualmente las ignoras todas. Solo alerta lo que requiere accion humana inmediata.

3. **Toda alerta debe tener un runbook**: documenta paso a paso que hacer cuando se dispara. En medio de un outage a las 3 AM no es momento de pensar.

4. **Alerta por umbrales, no por valores absolutos**: "error rate > 5% durante 5 minutos", no "hubo 1 error". Un error aislado no es una alerta.

5. **Prueba tus alertas**: simula un fallo y verifica que la alerta se dispara y llega por el canal correcto.

---

## 5. Monitoreo de Errores (Error Tracking)

No es lo mismo logs que error tracking. Los logs son todos los mensajes. El error tracking agrupa errores similares, muestra el stack trace, y te dice cuantos usuarios estan afectados.

**Sentry (recomendado):**
- Gratis para proyectos pequenos (5000 eventos/mes)
- Agrupa errores automaticamente
- Muestra el codigo que fallo y las variables en ese momento
- Funciona con Python, JavaScript, Go, Rust, etc.
- Integracion con GitHub/GitLab: crea issues automaticamente

---

## 6. Monitoreo Especifico para LLMs

Los proyectos con IA necesitan monitoreo adicional:

### Que monitorear en LLMs

| Metrica | Herramienta |
|---------|------------|
| Costo por request | Custom + Grafana |
| Latencia del modelo | OpenTelemetry |
| Tasa de errores de API | Sentry |
| Calidad de outputs | LLM-as-judge periodico |
| Token usage | Custom metrics |
| Cache hit rate | Redis/Vector store metrics |
| Guardrail activations | Custom metrics |
| User feedback (thumbs up/down) | Base de datos + analytics |

### Herramientas especializadas en monitoreo de LLMs

| Herramienta | Enfoque | Precio |
|-------------|---------|--------|
| LangSmith | Trazas y evaluacion de cadenas LLM | Desde $39/mes |
| LangFuse | Open source, tracing de LLMs | Gratis (self-hosted) |
| Helicone | Monitoreo de APIs LLM (proxy) | Gratis (100K req/mes) |
| Weights & Biases Prompts | Evaluacion y monitoreo de prompts | Desde $50/mes |
| Parea | Evaluacion y debugging de LLMs | Desde $99/mes |
| Galileo | Evaluacion de calidad de LLM | Bajo demanda |

---

## 7. Dashboard Minimo Recomendado

Un dashboard basico que todo proyecto en produccion deberia tener:

```
Panel 1: Requests por minuto (linea)
Panel 2: Latencia p50, p95, p99 (linea)
Panel 3: Tasa de errores % (linea con umbral)
Panel 4: Uso de CPU y memoria (linea)
Panel 5: Conexiones a base de datos (numero)
Panel 6: Costo de LLM ultimas 24h (barra, si aplica)
```

Si solo pudieras tener un panel: latencia + errores. Con esos dos sabes si la app esta viva y saludable.

---

## 8. Lo Que el Vibe Coder Ignora de Observabilidad

1. **Sin logs, estas ciego**: si un usuario reporta un error y no tienes logs, no puedes diagnosticarlo. Tienes que intentar reproducirlo a ciegas.

2. **Los logs en el servidor desaparecen**: si tu VPS se reinicia o el proceso de la app muere, los logs en memoria/console se pierden. Necesitas logs persistentes (archivo, servicio externo).

3. **Los logs cuestan dinero en serverless**: CloudWatch cobra por GB de logs ingeridos y almacenados. Loggea lo necesario, no todo.

4. **No toda metrica necesita alerta**: mide todo lo que puedas, pero solo alerta lo que requiere accion.

5. **La observabilidad es cultura, no herramienta**: puedes instalar Grafana, pero si el equipo no lo mira, no sirve. Designa a alguien responsable de revisar dashboards regularmente.

---

## Referencias Cruzadas

- [Decision de Infraestructura](./decision-infraestructura.md) -- Donde ejecutar herramientas de monitoreo
- [Entornos y Stages](./entornos-y-stages.md) -- Monitoreo por entorno
- [CI/CD y GitOps](./cicd-gitops.md) -- Integrar monitoreo en el pipeline
- [Costos y FinOps](./costos-finops.md) -- Monitorear costos

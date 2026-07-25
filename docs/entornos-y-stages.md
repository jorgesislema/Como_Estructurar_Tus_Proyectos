# Entornos y Stages: Dev, Staging, Produccion y Entornos Efimeros

Un error clasico del desarrollo con IA: pruebas todo en tu maquina, funciona, lo subes "a produccion", y explota. La razon: no hay entornos intermedios. Esta guia cubre como configurar entornos de desarrollo, pruebas y produccion para minimizar sorpresas.

---

## Por Que Necesitas Multiples Entornos

Imagina este escenario:
1. Desarrollas una feature en local. Funciona.
2. La despliegas directamente a produccion.
3. La nueva feature requiere una migracion de base de datos.
4. La migracion falla porque en produccion hay datos que no esperabas.
5. La aplicacion esta caida. Los usuarios se quejan.

Con un entorno de staging intermedio, ese fallo habria ocurrido en un ambiente sin usuarios reales, y lo habrias arreglado antes de tocar produccion.

---

## Los Entornos Estandar

### 1. Desarrollo (Development / Dev)

**Proposito:** Donde escribes y pruebas codigo localmente.

**Caracteristicas:**
- Corre en tu maquina (localhost)
- Base de datos local (SQLite, o PostgreSQL en Docker)
- Variables de entorno de desarrollo (.env con DEBUG=true)
- Sin SSL/HTTPS
- Hot reload activado (cambios se reflejan instantaneamente)
- Datos falsos o de prueba (seed data)
- Puedes romper cosas sin consecuencias

**Configuracion tipica:**
```
APP_ENV=development
DEBUG=true
LOG_LEVEL=DEBUG
DATABASE_URL=sqlite:///./dev.db
REDIS_URL=redis://localhost:6379
OPENAI_API_KEY=sk-dev-...  # Usar una API key de desarrollo
CORS_ORIGINS=*
```

**Herramientas utiles para dev:**
- Docker Compose: levanta todos los servicios (DB, Redis, etc.) con un comando
- Mailpit/Mailhog: captura emails en desarrollo sin enviarlos realmente
- Stripe CLI: testea webhooks de pago localmente
- ngrok: expone tu localhost para probar webhooks de servicios externos

---

### 2. Staging (Pre-produccion)

**Proposito:** Entorno identico a produccion, pero sin usuarios reales. Es donde validas que todo funciona antes del deploy final.

**Caracteristicas:**
- Infraestructura identica a produccion (mismo proveedor, mismas versiones)
- Base de datos con datos similares a produccion (anonimizados o generados)
- HTTPS habilitado
- Variables de entorno de staging (DEBUG=false, pero puede tener flags especiales)
- Monitoreo activo
- Acceso restringido (VPN o autenticacion basica)
- Aqui ejecutas migraciones de BD, pruebas de carga, y smoke tests

**Lo que se prueba en staging:**
- Migraciones de base de datos
- Integraciones con servicios externos
- Performance con datos realistas
- Despliegues (para validar que el script de deploy funciona)
- Rollbacks (practicar como volver atras)
- Nuevas variables de entorno o configuraciones

**Staging NO es opcional si:**
- Tienes usuarios pagando
- Tu aplicacion maneja datos sensibles
- Haces cambios frecuentes en infraestructura
- El equipo es de 3+ personas

---

### 3. Produccion (Production / Prod)

**Proposito:** El entorno real. Donde estan tus usuarios.

**Caracteristicas:**
- Infraestructura optimizada para carga real
- Base de datos con datos reales
- HTTPS obligatorio
- DEBUG=false (siempre, sin excepcion)
- Monitoreo completo (logs, metrics, traces, alertas)
- Backups automaticos
- Rate limiting activo
- CDN activo
- Variables de entorno de produccion (claves de API con limites de produccion)

**Configuracion tipica:**
```
APP_ENV=production
DEBUG=false
LOG_LEVEL=WARNING
DATABASE_URL=postgresql://user:pass@prod-db.internal:5432/app
REDIS_URL=redis://prod-redis.internal:6379
OPENAI_API_KEY=sk-prod-...  # Cuota y limites de produccion
CORS_ORIGINS=https://tudominio.com
SENTRY_DSN=https://...@sentry.io/...
```

**Reglas de oro de produccion:**
- Nadie hace deploy manual a produccion. Solo CI/CD.
- Nadie accede directamente a la base de datos de produccion sin un proceso.
- Todo cambio en produccion tiene un runbook de rollback.
- Las alertas de produccion son prioridad maxima.
- Las API keys de produccion son diferentes a las de desarrollo.

---

### 4. Entornos Efimeros (Preview / PR Environments)

**Proposito:** Entorno temporal que se crea para cada Pull Request y se destruye al mergear.

**Caracteristicas:**
- Se crea automaticamente al abrir un PR
- Contiene solo los cambios de esa rama
- URL unica (ej: `pr-123.tudominio.dev`)
- Base de datos temporal (seeded con datos de prueba)
- Se destruye al cerrar o mergear el PR
- Ideal para que revisores prueben cambios sin bajarse el codigo

**Cuando usarlos:**
- Equipo de 3+ personas
- Necesitas que disenadores/PMs revisen cambios visuales
- Los cambios son complejos y dificiles de revisar solo con codigo

**Plataformas que lo soportan nativamente:**
- Vercel (Preview Deployments)
- Railway (PR Environments)
- Render (Preview Environments)
- Heroku (Review Apps)
- Netlify (Deploy Previews)
- Fly.io (con scripts personalizados)

**Si usas VPS:** puedes implementar entornos efimeros con Docker y Traefik/Caddy, pero requiere mas trabajo de automatizacion.

---

## Configuracion por Entorno: Patrones

### Patron 1: Archivos .env por Entorno

```
.env.development
.env.staging
.env.production
.env.example           # Committeado, muestra la estructura
```

La aplicacion carga el archivo segun `APP_ENV`. Mas simple, pero menos seguro (los archivos de staging/prod contienen secretos).

### Patron 2: Variables de Entorno del Sistema (Recomendado para produccion)

En desarrollo usas `.env`. En staging y produccion, las variables se inyectan desde la plataforma:

- Railway: Variables panel
- Render: Environment variables
- Vercel: Environment variables
- AWS: Parameter Store / Secrets Manager
- GCP: Secret Manager
- VPS: systemd environment o Docker secrets

### Patron 3: Configuracion Centralizada

Un archivo de configuracion por entorno (YAML, TOML) y un sistema que selecciona cual cargar:

```python
# config.py
import os
import yaml

env = os.getenv("APP_ENV", "development")

with open(f"config/{env}.yaml") as f:
    config = yaml.safe_load(f)
```

---

## Pipeline de Promocion de Entornos

El flujo tipico de como el codigo avanza entre entornos:

```
[Developer escribe codigo]
        |
[PR abierto] --> [Entorno efimero (PR #123)] --> [CI: lint, test, type-check]
        |
[Merge a main]
        |
[Build automatico] --> [Deploy a Staging]
        |
[Smoke tests en Staging] --> [Pruebas manuales o E2E]
        |
[Aprobacion manual] --> [Deploy a Produccion]
        |
[Monitoreo post-deploy] --> [Rollback si algo falla]
```

---

## Errores Comunes con Entornos

### Error 1: Staging no es igual a Produccion

"En staging funciona" pero en produccion explota. Causa: diferencias sutiles entre entornos.

Como evitarlo:
- Usa exactamente las mismas versiones de software en staging y prod
- Usa el mismo tipo de base de datos (no SQLite en dev y PostgreSQL en prod)
- Usa Infrastructure as Code (Terraform, Pulumi) para garantizar que staging y prod son identicos

### Error 2: DEBUG=true en Produccion

Muestra stack traces completos a los usuarios, expone informacion interna, y consume mas recursos.

Regla: `DEBUG=false` en produccion. Sin excepciones. Bloquealo en CI si es necesario.

### Error 3: Usar la misma API Key para dev y prod

Si tu key de desarrollo alcanza el rate limit, tambien afectas produccion. O peor: si expones la key de produccion en desarrollo, la comprometes.

Usa keys separadas para cada entorno.

### Error 4: No tener datos realistas en staging

Si staging tiene 10 registros y produccion tiene 10 millones, las queries que van rapido en staging pueden ser lentisimas en prod.

Poblar staging con datos anonimizados de produccion, o generados con volumenes similares.

### Error 5: Acceso a produccion sin restricciones

Cualquier desarrollador puede hacer SSH al servidor de produccion. Cualquier error humano causa outage.

Acceso a produccion: solo CI/CD y SREs. Todo cambio por PR y pipeline.

---

## Entornos para Proyectos de IA

Los proyectos con LLMs tienen necesidades adicionales:

### Entorno de Evaluacion
Ademas de dev/staging/prod, necesitas un entorno donde ejecutar evaluaciones de calidad de outputs del LLM.

- Usa un subset de datos de produccion anonimizados
- Ejecuta evaluaciones automaticas (LLM-as-judge, metricas)
- Programa evaluaciones periodicas (semanales)
- Alerta si la calidad degrada mas de un umbral

### Entorno de Fine-tuning
Si fine-tuneas modelos, necesitas un entorno con GPU:
- Datos de entrenamiento versionados
- Jobs de fine-tuning que se ejecutan bajo demanda o programados
- Evaluacion automatica del modelo resultante
- A/B testing entre modelo base y modelo fine-tuned antes de promover a prod

---

## Checklist de Entornos

```
[ ] Desarrollo local funciona con Docker Compose
[ ] .env.example documenta todas las variables
[ ] Las variables de entorno se inyectan, no estan en archivos (staging/prod)
[ ] Staging es identico a produccion en infraestructura
[ ] Las migraciones de BD se prueban en staging antes de prod
[ ] Hay smoke tests automaticos post-deploy
[ ] DEBUG=false en staging y produccion
[ ] API keys separadas por entorno
[ ] Monitoreo y alertas activos en staging y produccion
[ ] Backups automaticos en produccion
[ ] Procedimiento de rollback documentado y probado
[ ] Entornos efimeros para PRs (si equipo >= 3 personas)
```

---

## Referencias Cruzadas

- [Decision de Infraestructura](./decision-infraestructura.md) -- Donde ejecutar
- [Arquitecturas de Despliegue](./arquitecturas-despliegue.md) -- Monolito, microservicios, serverless
- [CI/CD y GitOps](./cicd-gitops.md) -- Automatizacion del pipeline
- [Observabilidad](./observabilidad.md) -- Monitoreo y alertas

# CI/CD y GitOps: Automatizacion de Despliegues

CI/CD (Continuous Integration / Continuous Deployment) es lo que convierte tu codigo en un servicio funcionando en produccion sin intervencion manual. GitOps lleva esto un paso mas alla: Git es la unica fuente de verdad, y herramientas automatizadas sincronizan el estado deseado (definido en Git) con el estado real (tu infraestructura).

---

## 1. CI (Integracion Continua): Que Pasa al Hacer Push

Cada vez que haces push o abres un PR, un pipeline automatico ejecuta verificaciones. Si algo falla, el PR no se mergea.

### Pipeline minimo recomendado

```yaml
# .github/workflows/ci.yml (version conceptual)
# Cada push y PR ejecuta:

1. Checkout del codigo
2. Instalar dependencias
3. Linting (Ruff, ESLint, etc.)
4. Type checking (mypy, TypeScript)
5. Tests unitarios
6. Tests de integracion (con base de datos real)
7. Cobertura de codigo (minimo configurable)
8. Analisis de seguridad (dependencias vulnerables)
```

### Lo que SIEMPRE debe estar en CI

| Paso | Herramienta Python | Herramienta Node.js | Proposito |
|------|-------------------|--------------------|-----------|
| Lint | Ruff | ESLint | Estilo de codigo consistente |
| Formato | Ruff format | Prettier | Formato automatico |
| Tipos | mypy | tsc --noEmit | Detectar errores de tipo |
| Tests | pytest | Vitest/Jest | Verificar que el codigo funciona |
| Cobertura | pytest-cov | c8/istanbul | Que porcentaje del codigo esta testeado |
| Seguridad | pip-audit | npm audit | Dependencias con vulnerabilidades |
| Build | (empaquetado) | npm run build | Verificar que la app compila |

### Lo que NO debe estar en CI (pero mucha gente lo pone)

- Deploy a produccion automatico desde CI (usar CD separado)
- Pruebas que dependen de secretos de produccion
- Scans de seguridad que tardan horas (hacerlos programados, no en cada PR)
- Pruebas E2E largas (ejecutarlas en un pipeline separado o programado)

---

## 2. CD (Entrega/Despliegue Continuo)

### Diferencia entre Continuous Delivery y Continuous Deployment

**Continuous Delivery:** Cada merge a main produce un artefacto listo para desplegar. El despliegue a produccion requiere aprobacion manual.

**Continuous Deployment:** Cada merge a main se despliega automaticamente a produccion. Sin intervencion humana.

Recomendacion: empieza con Continuous Delivery. Cuando tengas confianza en tus tests y monitoreo, pasa a Continuous Deployment.

### Estrategias de Despliegue

**Big Bang (todo de golpe):**
El mas simple. Apagas la version vieja, enciendes la nueva. Hay downtime.

Usar solo si: el downtime es aceptable (app personal, proyecto interno).

**Rolling Update:**
Reemplazas instancias una por una. Sin downtime. La version vieja y nueva coexisten brevemente.

Usar cuando: tienes multiples instancias y los cambios de DB son compatibles hacia atras.

**Blue/Green:**
Mantienes dos entornos identicos (blue = actual, green = nuevo). Despliegas en green, validas, y cambias el trafico. Si algo falla, vuelves a blue instantaneamente.

Usar cuando: necesitas rollback instantaneo, cambios de DB complejos.

**Canary:**
Despliegas la nueva version para un porcentaje pequeno de usuarios (5%). Monitoreas. Si funciona, aumentas gradualmente al 100%.

Usar cuando: tienes muchos usuarios y necesitas validar en produccion real con riesgo controlado.

**Feature Flags:**
Despliegas el codigo "apagado" y lo activas por configuracion. Permite separar deploy (tecnico) de release (negocio).

Usar cuando: necesitas lanzar features a subconjuntos de usuarios, hacer A/B testing, o que marketing controle cuando se activa algo.

---

## 3. GitOps

### Que es

Git es la unica fuente de verdad de tu infraestructura. No haces `kubectl apply` manual, no tocas servidores por SSH. Defines el estado deseado en archivos YAML en Git, y un operador (ArgoCD, Flux) sincroniza automaticamente.

### Principios de GitOps

1. **Declarativo**: describes el estado deseado, no los pasos para llegar a el
2. **Versionado**: todo cambio en infraestructura es un commit en Git
3. **Automatizado**: el operador aplica cambios automaticamente
4. **Auditable**: cada cambio tiene autor, timestamp, y mensaje de commit
5. **Reversible**: rollback = revertir un commit

### Herramientas GitOps

| Herramienta | Plataforma | Complejidad |
|-------------|-----------|-------------|
| ArgoCD | Kubernetes | Media |
| Flux | Kubernetes | Media |
| Terraform Cloud | Multi-cloud | Media |
| Pulumi | Multi-cloud | Media |
| Ansible (pull mode) | VPS, servidores | Baja |

### Ejemplo conceptual de GitOps

```
Repositorio Git:
├── apps/
│   ├── api/
│   │   ├── deployment.yaml   # "Quiero 3 replicas de la API"
│   │   └── service.yaml
│   └── worker/
│       └── deployment.yaml
├── infrastructure/
│   ├── database.yaml         # "Quiero PostgreSQL 16"
│   └── redis.yaml            # "Quiero Redis 7"
└── environments/
    ├── staging/
    │   └── values.yaml        # Replicas: 1, CPU: 0.5
    └── production/
        └── values.yaml        # Replicas: 3, CPU: 2

[ArgoCD/Flux monitorea el repo]
    |
[Compara estado deseado (Git) con estado real (Cluster)]
    |
[Si hay diferencia, aplica cambios para sincronizar]
```

### GitOps NO es para todos

GitOps es potente pero complejo. Solo lo necesitas si:

- Usas Kubernetes
- Tienes multiples entornos y servicios
- El equipo es de 5+ personas
- Necesitas auditoria de cambios en infraestructura

Para el 80% de proyectos, CI/CD con GitHub Actions + deploy automatico es suficiente.

---

## 4. Infraestructura como Codigo (IaC)

En lugar de crear recursos cloud haciendo click en la consola, los defines en codigo.

### Herramientas IaC

| Herramienta | Enfoque | Curva de aprendizaje |
|-------------|---------|---------------------|
| Terraform | Multi-cloud, declarativo | Media |
| Pulumi | Multi-cloud, lenguajes de prog | Media |
| AWS CDK | Solo AWS, lenguajes de prog | Baja (si conoces AWS) |
| Ansible | Configuracion de servidores | Baja |

### Ejemplo conceptual de Terraform

No se trata de escribir codigo aqui, sino de entender que defines recursos en archivos `.tf` versionados en Git. Cuando ejecutas `terraform apply`, la herramienta compara lo que tienes definido con lo que existe en la nube y solo crea/modifica/destruye lo necesario.

---

## 5. Pipeline de CI/CD por Nivel

### Nivel 1: Proyecto Personal (Vibe Coder)

```
Push a main
    |
[GitHub Actions: test]
    |
[Push a Railway/Render (automatico)]
```

No necesitas mas. Simplicidad maxima.

### Nivel 2: Proyecto Profesional Pequeno

```
PR abierto
    |
[GitHub Actions: lint + type-check + unit tests]
    |
[Merge a main]
    |
[GitHub Actions: tests + build Docker image]
    |
[Push image a registry (GHCR, DockerHub)]
    |
[Deploy a staging (automatico)]
    |
[Smoke tests en staging]
    |
[Aprobacion manual]
    |
[Deploy a produccion]
```

### Nivel 3: Startup / Equipo Mediano

```
PR abierto
    |
[GitHub Actions: lint + type-check + unit tests + integration tests]
[Entorno efimero para el PR]
    |
[Merge a main]
    |
[CI: build + test + push image]
[CD: deploy a staging (automatico)]
    |
[Smoke tests + E2E tests en staging]
[Aprobacion manual en GitHub]
    |
[Despliegue Canary a produccion (10% -> 50% -> 100%)]
    |
[Monitoreo post-deploy (alertas si aumentan errores)]
[Rollback automatico si error rate > umbral]
```

### Nivel 4: Empresa / Gran Escala

Incluye todo lo del Nivel 3 mas:
- GitOps con ArgoCD/Flux para Kubernetes
- Feature flags para deployments desacoplados de releases
- A/B testing automatico
- Compliance checks (SOC2, HIPAA) en el pipeline
- Multiple stages de validacion (dev -> staging -> canary -> prod-us -> prod-eu)
- Rollback con un click o revert de commit

---

## 6. Herramientas de CI/CD por Plataforma

| Plataforma | CI/CD | Complejidad | Precio |
|-----------|-------|-------------|--------|
| GitHub | GitHub Actions (recomendado) | Baja | Gratis (2000 min/mes) |
| GitLab | GitLab CI | Media | Gratis (400 min/mes) |
| Vercel | Incluido | Muy baja | Gratis |
| Railway | Incluido | Muy baja | Desde $5/mes |
| Render | Incluido | Muy baja | Gratis |
| Fly.io | `fly deploy` | Baja | Gratis |
| VPS | GitHub Actions + rsync/SSH | Media | Gratis (acciones) |
| Kubernetes | ArgoCD + GitHub Actions | Alta | Gratis (ArgoCD) |

---

## 7. Lo Que El Vibe Coder No Sabe de CI/CD

1. **Los secretos en CI/CD son diferentes**: no uses el mismo `.env` de desarrollo en CI. Configura los secretos en GitHub Secrets / GitLab Variables. Se inyectan en el pipeline, no estan en el codigo.

2. **La base de datos en CI**: tus tests de integracion necesitan una base de datos. Opciones: SQLite en memoria (rapido pero no identico a prod), PostgreSQL en Docker (mas lento pero identico a prod), o servicios gestionados de CI (GitHub Actions service containers).

3. **El cache acelera todo**: sin cache, cada pipeline instala dependencias desde cero (2-5 minutos). Con cache, < 30 segundos. Configurar cache de dependencias es lo primero que debes hacer.

4. **No hagas deploy los viernes**: los despliegues a produccion un viernes a las 6 PM son mala idea. Si algo falla, nadie esta disponible para arreglarlo hasta el lunes.

5. **Siempre ten un plan de rollback**: antes de desplegar, sabe exactamente como volver atras. Idealmente, un revert de commit + deploy automatico.

6. **Los smoke tests post-deploy son obligatorios**: despues de cada deploy, ejecuta una prueba basica (health check, login, funcionalidad critica). Si falla, rollback automatico.

---

## Referencias Cruzadas

- [Decision de Infraestructura](./decision-infraestructura.md) -- Donde ejecutar
- [Arquitecturas de Despliegue](./arquitecturas-despliegue.md) -- Que patron usar
- [Entornos y Stages](./entornos-y-stages.md) -- Pipeline entre entornos
- [Observabilidad](./observabilidad.md) -- Monitorear despliegues

# CONTEXTO.md -- Plantilla de Contexto del Proyecto para IA

Copiar este archivo a la raiz del proyecto y completar con la informacion especifica.

---

```markdown
# Contexto del Proyecto: [NOMBRE_DEL_PROYECTO]

## Descripcion General
[2-3 parrafos explicando que hace el proyecto, para quien es, y por que existe.

Ejemplo: "API REST que permite a usuarios gestionar sus finanzas personales usando IA para analizar patrones de gasto y generar recomendaciones personalizadas."]

## Stack Tecnologico
- **Lenguaje:** [Python 3.12 | TypeScript 5.4 | Rust | Go]
- **Framework principal:** [FastAPI | Next.js 14 | React 18 | Django | Express]
- **Base de datos:** [PostgreSQL 16 | MongoDB 7 | SQLite | MySQL 8]
- **ORM / DB Client:** [SQLAlchemy 2.0 | Prisma | Drizzle | TypeORM]
- **Cache:** [Redis 7 | Memcached | No usa]
- **Testing:** [Pytest | Vitest | Jest | Playwright]
- **CI/CD:** [GitHub Actions | GitLab CI | CircleCI]
- **Despliegue:** [Docker + K8s | Railway | Vercel | Fly.io | AWS ECS]
- **Gestion de paquetes:** [uv | Poetry | npm | pnpm | yarn | Cargo]
- **IA / LLM:** [OpenAI API | Anthropic API | Azure OpenAI | Ollama local | No usa]

## Arquitectura

Este proyecto sigue una arquitectura [Clean Architecture | Hexagonal | MVC | Serverless].

Las capas principales son:
1. **[Capa 1]** (`src/[carpeta]/`): [que hace esta capa]
2. **[Capa 2]** (`src/[carpeta]/`): [que hace esta capa]
3. **[Capa 3]** (`src/[carpeta]/`): [que hace esta capa]

[Diagrama o descripcion del flujo de datos]

## Estructura de Carpetas
```
mi-proyecto/
├── src/
│   ├── api/            # [descripcion]
│   ├── core/           # [descripcion]
│   ├── domain/         # [descripcion]
│   ├── infrastructure/ # [descripcion]
│   └── services/       # [descripcion]
├── tests/
├── docs/
├── .github/workflows/
├── RULES.md
├── CONTEXTO.md
├── AGENTS.md
├── pyproject.toml
└── README.md
```

## Principios de Diseno
- [SOLID | DRY | KISS | YAGNI]
- [Composicion sobre herencia | Herencia donde tenga sentido]
- [Inmutabilidad | Mutabilidad controlada]
- [Funciones puras donde sea posible | Orientado a objetos]

## Flujo de Trabajo
1. Crear rama desde `main`: `git checkout -b feat/nombre-feature`
2. Desarrollar siguiendo RULES.md
3. Escribir tests
4. Ejecutar tests localmente: `[pytest | npm test | cargo test]`
5. Crear PR contra `main`
6. CI ejecuta: [lint -> type-check -> tests -> coverage]
7. [Review de codigo manual | Review manual + agente IA]
8. Merge a `main`, [despliegue automatico | despliegue manual]

## Dependencias Clave
[Lista de las dependencias mas importantes y para que se usan. Ejemplos:]
- **[Dependencia 1]**: [proposito]
- **[Dependencia 2]**: [proposito]
- **[Dependencia 3]**: [proposito]

## Servicios Externos
- **Base de datos:** [proveedor y plan]
- **Cache:** [proveedor y plan]
- **Almacenamiento:** [proveedor y plan]
- **Email:** [proveedor]
- **Monitoreo:** [Sentry | Datadog | Grafana | No usa]
- **IA/LLM:** [proveedor, modelos usados]

## Variables de Entorno Requeridas
Ver `.env.example` para la lista completa. Las principales:
- `[VARIABLE_1]`: [descripcion]
- `[VARIABLE_2]`: [descripcion]
- `[VARIABLE_3]`: [descripcion]

## Notas Importantes
- Este proyecto usa [async/await extensivamente | sincrono]
- La base de datos usa [UUIDs | IDs autoincrementales | ULIDs] como PK
- Los errores se manejan con [excepciones personalizadas | Result types | Either]
- El versionado de API se hace via [URL prefix | header]
- [Otra nota especifica del proyecto]
```

---

## Instrucciones de Uso

1. Copiar a `CONTEXTO.md` en la raiz del proyecto
2. Completar TODOS los campos entre corchetes
3. Eliminar secciones que no apliquen al proyecto
4. La IA usa este archivo para entender el proyecto sin leer todo el codigo
5. Mantener actualizado cuando cambie el stack, arquitectura o dependencias

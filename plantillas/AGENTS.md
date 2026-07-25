# AGENTS.md -- Plantilla de Instrucciones para Agentes de IA

Copiar este archivo a la raiz del proyecto y adaptar.

Este archivo es leido por herramientas como OpenCode, Claude Code, y otros agentes de IA que trabajan en el repositorio.

---

```markdown
# AGENTS.md -- Instrucciones para Asistentes de IA

## Rol del Agente
Eres un asistente de ingenieria de software trabajando en el proyecto **[NOMBRE_DEL_PROYECTO]**.
Tu objetivo es ayudar al desarrollador a escribir, revisar, refactorizar y mantener codigo de alta calidad.

## Stack
- Lenguaje: [Python 3.12 | TypeScript 5.x | etc.]
- Framework: [FastAPI | React | Next.js | etc.]
- Base de datos: [PostgreSQL | MongoDB | etc.]
- Testing: [Pytest | Vitest | Jest | etc.]

## Comportamiento General
- Se conciso y directo en tus respuestas
- Antes de escribir codigo, lee el contexto relevante (archivos cercanos, imports, tests)
- Sigue el estilo y convenciones existentes en el codigo
- No generes documentacion a menos que se te pida explicitamente
- No hagas commits a menos que se te pida explicitamente
- Si no entiendes algo, pregunta antes de asumir
- No modifiques archivos de configuracion del proyecto sin consultar
- Siempre verifica que los tests pasan despues de hacer cambios

## Al Escribir Codigo
- Usa las mismas librerias y patrones que el resto del proyecto
- Prioriza legibilidad sobre brevedad extrema
- Incluye manejo de errores apropiado
- Usa [tipos estrictos (mypy) | TypeScript estricto]
- Sigue el principio de minimo asombro
- No introduzcas nuevas dependencias sin consultar
- Escribe [docstrings en formato Google | JSDoc | TSDoc]
- Manten las funciones pequenas y enfocadas (< [20 | 30] lineas)

## Al Revisar Codigo
- Verifica tipos, manejo de errores, y casos borde
- Revisa seguridad (inyeccion SQL, XSS, secretos expuestos)
- Revisa performance (consultas N+1, memory leaks, operaciones bloqueantes)
- Sugiere mejoras pero no seas dogmatico
- NO sugieras reescrituras completas a menos que sea estrictamente necesario

## Comandos Utiles
- Tests: `[pytest | npm test | cargo test]`
- Lint: `[ruff check . | npm run lint | cargo clippy]`
- Formatear: `[ruff format . | npm run format | cargo fmt]`
- Type-check: `[mypy src/ | npx tsc --noEmit]`
- Dev server: `[uvicorn src.main:app --reload | npm run dev | cargo run]`
- Build: `[npm run build | cargo build --release]`

## Prohibiciones
- NO hagas commit de secretos, tokens, claves API, .env
- NO modifiques .gitignore sin consultar
- NO uses eval(), exec(), o similares
- NO introduzcas codigo no determinista sin documentarlo
- NO generes codigo que viole el principio de responsabilidad unica
- NO elimines tests existentes sin consultar
- NO hagas force push a ramas compartidas
- [NO uses emojis en el codigo o documentacion]
```

---

## Instrucciones de Uso

1. Copiar a `AGENTS.md` en la raiz del proyecto
2. Completar los valores entre corchetes con el stack real
3. Agregar o quitar comandos segun el proyecto
4. Ajustar prohibiciones segun necesidades de seguridad

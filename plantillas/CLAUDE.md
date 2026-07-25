# CLAUDE.md -- Plantilla de Instrucciones para Claude Code

Copiar este archivo como `CLAUDE.md` en la raiz del proyecto.
Claude Code lo lee automaticamente al iniciar una sesion en el repositorio.

---

```markdown
# CLAUDE.md -- Instrucciones para Claude Code

## Comandos del Proyecto
- Ejecutar tests: `pytest` (o `npm test`, `cargo test`)
- Linting: `ruff check .` (o `npm run lint`, `cargo clippy`)
- Formateo: `ruff format .` (o `npm run format`, `cargo fmt`)
- Type check: `mypy src/` (o `npx tsc --noEmit`)
- Construir: `[comando de build]`
- Ejecutar en dev: `[comando de dev server]`
- Instalar dependencias: `[pip install -r requirements.txt | npm install | cargo build]`

## Arquitectura del Proyecto
- Lenguaje: [Python 3.12 | TypeScript 5.x | Rust | Go]
- Framework: [FastAPI | React | Next.js | etc.]
- Base de datos: [PostgreSQL | SQLite | MongoDB | etc.]
- Estructura: [Clean Architecture | MVC | Vertical Slices]

## Arbol de Carpetas Principal
```
src/
├── api/            # [descripcion]
├── core/           # [descripcion]
├── domain/         # [descripcion]
├── infrastructure/ # [descripcion]
└── services/       # [descripcion]
tests/
├── unit/
└── integration/
```

## Notas para Claude
- Leer `RULES.md` y `CONTEXTO.md` antes de trabajar en el proyecto
- No modificar archivos de configuracion sin preguntar
- Mantener el estilo de codigo consistente con lo existente
- Siempre verificar que los tests pasan despues de cambios
- No hacer commits sin autorizacion explicita
- Usar `[tipos estrictos | TypeScript strict mode]`
- [async/await para operaciones asincronas]

## Convenciones del Proyecto
- Nombres: [snake_case | camelCase]
- Formateo: [Ruff | Prettier | Black]
- Docstrings: [Google | NumPy | JSDoc]
- Tests: [Pytest | Vitest | Jest]
- Commits: [Conventional Commits]
```

---

## Instrucciones de Uso

1. Copiar a `CLAUDE.md` en la raiz del proyecto
2. Completar los valores entre corchetes con el stack real
3. Agregar comandos especificos del proyecto
4. Mantener actualizado cuando cambie el stack o arquitectura

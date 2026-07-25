# RULES.md -- Plantilla de Reglas de Desarrollo

Copiar este archivo a la raiz del proyecto y adaptar los valores entre corchetes.

---

```markdown
# Reglas del Proyecto [NOMBRE_DEL_PROYECTO]

## Lenguaje y Estilo
- Lenguaje: [Python 3.12 | TypeScript 5.x | etc.]
- Formateo: [Ruff | Prettier | Black | ESLint]
- Longitud maxima de linea: [88 | 100 | 120] caracteres
- Nombres de variables: [snake_case | camelCase]
- Nombres de clases: [PascalCase]
- Constantes: [UPPER_SNAKE_CASE | UPPER_CASE]

## Tipos y Validacion
- [mypy --strict | TypeScript strict: true]
- [Prohibir `Any` | Prohibir `any`]
- Todas las funciones publicas con anotaciones de tipo
- [Pydantic v2 | Zod | Yup] para validacion de datos

## Arquitectura
- [Clean Architecture | Hexagonal | MVC | Vertical Slices]
- Capas: [presentacion -> aplicacion -> dominio -> infraestructura]
- [Inyeccion de dependencias | Service Locator]
- [Repository pattern | Active Record]
- Dependencias siempre apuntan hacia adentro (capas internas)

## Documentacion
- Docstrings en formato [Google | NumPy | JSDoc]
- Todo modulo publico debe tener docstring
- Toda funcion publica debe tener docstring con parametros y retorno
- Mantener [README.md | CONTEXTO.md] actualizado
- [ADRs para decisiones arquitectonicas | No requerido]

## Testing
- Framework: [Pytest | Vitest | Jest | Mocha]
- Cobertura minima: [70% | 80% | 90%]
- Toda nueva funcionalidad requiere tests
- Nombrar tests: test_[modulo]_[funcionalidad]_[escenario]
- [Fixtures en conftest.py | Test fixtures en archivo compartido]
- Mocking de servicios externos

## Git y Commits
- Conventional Commits: feat:, fix:, docs:, test:, refactor:, chore:, ci:
- [GitHub Flow | GitFlow | Trunk-Based Development]
- No hacer commit si los tests no pasan
- No hacer commit de secretos, claves API, .env, .venv/
- No hacer commit de archivos generados (build/, dist/, __pycache__/)

## Seguridad
- Claves API y secretos en variables de entorno (NUNCA en codigo fuente)
- Usar [.env + python-dotenv | .env + dotenv | Doppler | Infisical]
- [Dependabot | Renovate] para actualizaciones de seguridad
- [pip-audit | npm audit | cargo audit] en CI
- No usar eval(), exec(), Function()

## Performance
- Evitar consultas N+1 en base de datos
- [Paginacion | Cursor-based pagination] para listados grandes
- [Redis | Memcached] para cache
- Cerrar conexiones y archivos explicitamente

## Prohibiciones
- NO usar [Any | any | unknown sin validacion]
- NO usar [print() | console.log] para debugging (usa logging)
- NO hardcodear valores de configuracion
- NO dependencias circulares
- NO imports relativos (usar imports absolutos)
- NO modificar pyproject.toml / package.json sin consultar
- NO introducir nuevas dependencias sin consultar
- NO eliminar tests existentes

## Herramientas de IA (si aplica)
- [Cursor rules en .cursor/rules]
- [Copilot instructions en .github/copilot-instructions.md]
- [AGENTS.md con instrucciones para agentes]
- Al pedirle a la IA: incluir contexto del proyecto
```

---

## Instrucciones de Uso

1. Copiar esta plantilla a `RULES.md` en la raiz del proyecto
2. Eliminar las opciones que no aplican (dejando la que usas)
3. Completar los valores entre corchetes
4. Adaptar secciones segun el stack del proyecto
5. Committear y mantener actualizado

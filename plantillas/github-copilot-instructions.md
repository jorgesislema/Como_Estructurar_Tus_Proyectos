# .github/copilot-instructions.md -- Plantilla de Instrucciones para GitHub Copilot

Copiar este archivo a `.github/copilot-instructions.md` en el repositorio.
GitHub Copilot lo lee automaticamente para personalizar sus sugerencias.

---

```markdown
# Instrucciones para GitHub Copilot

## Lenguaje
- [Python 3.12 | TypeScript 5.x | JavaScript | Go | Rust]
- [Framework: FastAPI | React | Next.js | etc.]

## Estilo de Codigo
- [Tipos estrictos | Tipos siempre que sea posible]
- [snake_case | camelCase | PascalCase] segun convencion del proyecto
- Mantener funciones [pequenas (max 20 lineas) | enfocadas en una responsabilidad]
- Usar nombres descriptivos (evitar abreviaturas)
- [async/await para operaciones asincronas | sincrono]

## Testing
- [Pytest | Vitest | Jest]
- Nombrar: `test_[funcionalidad]_[escenario]`
- [AAA pattern: Arrange, Act, Assert]
- Cada test independiente (sin dependencia entre tests)

## Patrones del Proyecto
- [Repository pattern para acceso a datos]
- [Dependency Injection via FastAPI Depends | Constructor injection]
- [Custom exceptions para errores de dominio]
- [Pydantic | Zod] para validacion de datos
- [Middleware | Guards | Decorators] para cross-cutting concerns

## Imports
- [Orden: stdlib -> terceros -> locales]
- [Evitar import *]
- [Usar imports absolutos desde src/ | Usar path aliases (@/components)]

## No Sugerir
- [Any | any] como tipo
- [console.log | print] para debugging
- [any] como catch de errores (usar tipo especifico)
- Codigo sin manejo de errores
- Variables de una sola letra (excepto i, j en loops)
- [Hardcodeo de URLs, keys, o configuracion]
```

---

## Notas

- Este archivo debe estar en `.github/copilot-instructions.md`
- GitHub Copilot lo lee automaticamente
- Las instrucciones deben ser concisas y directas
- Actualizar cuando cambien las convenciones del proyecto

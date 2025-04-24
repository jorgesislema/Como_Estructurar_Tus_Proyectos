# Estructura de Repositorios para Herramientas de Desarrollo Python (Linters, Formatters, etc.)

¡Saludos, forjadores de herramientas Python! 🛠️ Si estás aquí, probablemente estés construyendo (o soñando con construir) la próxima herramienta indispensable que hará la vida de los desarrolladores Python un poco más fácil, rápida o simplemente... más ordenada. Ya sea un linter ultra-rápido, un formatter con opiniones firmes, un runner de tests ingenioso o cualquier otra utilidad que automatice o mejore el flujo de trabajo, ¡estás en el lugar correcto!

Construir una herramienta de desarrollo (DevTool) tiene sus propios desafíos. No solo debe funcionar, sino que debe ser **confiable, fácil de instalar, configurar y contribuir**. Una buena estructura de repositorio es fundamental para lograrlo. Piénsalo: estás construyendo el taller, no los muebles; el taller debe estar impecablemente organizado para ser eficiente.

Vamos a ver cómo estructurar tu proyecto para que sea un placer trabajar en él, tanto para ti como para tus futuros usuarios y contribuidores. Nos inspiraremos en gigantes como Black, pytest, Ruff, Mypy y otros.

## Consideraciones Clave para las DevTools Python 🤔

Antes de sumergirnos en las carpetas, pensemos en las características especiales de estos proyectos:

* **Punto de Entrada:** ¿Cómo se usará tu herramienta? ¿Principalmente como un comando CLI (`mi_herramienta archivo.py`)? ¿Como un hook de pre-commit? ¿Como una librería importable en otros scripts Python? Esto influirá en cómo estructuras tu código y defines los `entry_points`.
* **Configuración del Usuario:** ¿Cómo personalizarán los usuarios el comportamiento? Las opciones son muchas: argumentos CLI, variables de entorno, un archivo dedicado (`.miherramientarc`), o la opción cada vez más estándar (¡y recomendada!): la sección `[tool.mi_herramienta]` dentro de `pyproject.toml`.
* **Extensibilidad (¿Plugins?):** ¿Permitirás que otros extiendan tu herramienta con plugins? Si es así, necesitas un mecanismo claro para el descubrimiento y registro de estos plugins (¡`importlib.metadata.entry_points` es tu amigo!).
* **Rendimiento:** ¡A menudo crítico! Nadie quiere un linter que tarde 30 segundos en ejecutarse en cada commit. Decisiones de diseño, algoritmos e incluso el lenguaje (hola, Rust/Ruff 😉) pueden influir aquí.
* **Testing Exhaustivo:** Tu herramienta se enfrentará a código Python de todo tipo (¡y a veces muy raro!). Necesitas probarla contra múltiples versiones de Python, diferentes sistemas operativos, estructuras de proyecto diversas y todo tipo de casos límite sintácticos y semánticos.
* **Packaging y Distribución:** Debe ser trivial para los usuarios hacer `pip install mi_herramienta`. Esto significa cuidar los metadatos, dependencias y la construcción de *wheels*.

## Estructura de Directorio Recomendada 📂✅

Basado en las mejores prácticas actuales (Abril 2025) y observando proyectos exitosos, aquí tienes una estructura sólida y recomendada:
``` bash
mi-herramienta-python/
├── .github/                    # CI/CD (GitHub Actions workflows)
│   └── workflows/
│       └── ci.yml
├── .gitignore                  # Archivos a ignorar por Git
├── .pre-commit-config.yaml     # Configuración de pre-commit (¡usa tu propia herramienta!)
├── CHANGELOG.md                # Historial de cambios para los usuarios
├── CONTRIBUTING.md             # Guía para contribuidores
├── LICENSE                     # Licencia del proyecto (ej: MIT, Apache 2.0)
├── README.md                   # Instrucciones de uso, instalación, etc.
├── docs/                       # Documentación (a menudo Sphinx)
│   ├── source/                 # Archivos fuente de la documentación (.rst o .md)
│   │   ├── conf.py             # Configuración de Sphinx
│   │   └── index.rst           # ... y otros archivos de contenido ...
│   └── Makefile                # Helper para construir la documentación
├── examples/                   # (Opcional) Ejemplos de uso o configuraciones
│   └── ...
├── pyproject.toml              # ¡El corazón! Metadatos, dependencias, build, config.
├── scripts/                    # Scripts útiles (release, benchmarks, etc.)
│   └── ...
├── src/                        # Código fuente del paquete Python (¡usa el layout src!)
│   └── mi_herramienta/         # Nombre del paquete instalable
│       ├── init.py
│       ├── _version.py         # Gestión de la versión (a menudo auto-generado)
│       ├── cli.py              # Lógica del punto de entrada CLI (ej: usando Typer, Click, argparse)
│       ├── config.py           # Carga y validación de la configuración
│       ├── core/               # Lógica principal de la herramienta (análisis, formato, etc.)
│       │   └── ...
│       ├── plugin.py           # (Si aplica) Descubrimiento/manejo de plugins
│       └── utils.py            # Funciones de utilidad internas
└── tests/                      # Pruebas (¡muchas!)
├── conftest.py             # Fixtures y hooks globales de pytest
├── fixtures/               # Datos/archivos de prueba (ej: sample_project/)
│   └── ...
├── integration/            # Pruebas de integración entre módulos
│   └── ...
├── functional/             # Pruebas funcionales/e2e (ejecutando el CLI)
│   └── ...
└── unit/                   # Pruebas unitarias de módulos aislados
└── ...
```
**Puntos Clave de esta Estructura:**

* **Layout `src/`:** Separa limpiamente tu código fuente (`src/mi_herramienta`) de los archivos de configuración, tests y otros metadatos en la raíz. Evita problemas comunes de importación y asegura que estás testeando lo que se instalará.
* **`pyproject.toml` Central:** Es el estándar moderno (PEP 517, PEP 518, PEP 621) para definir metadatos, dependencias, el sistema de build, y cada vez más, la configuración de otras herramientas.
* **Carpeta `tests/` Robusta:** Bien organizada, con diferentes tipos de tests y datos de prueba claros (`fixtures`).
* **Documentación (`docs/`):** Fundamental para que los usuarios entiendan cómo usar y configurar tu herramienta. [Sphinx](https://www.sphinx-doc.org/) es el estándar de facto en el ecosistema Python.
* **Automatización (CI/CD, pre-commit):** Esencial para mantener la calidad y la consistencia.

## Packaging y Dependencias: El Rol Estelar de `pyproject.toml` 🎭

Este archivo es tu manifiesto. Aquí defines todo lo necesario para construir, distribuir y (a menudo) configurar tu herramienta.

``` bash
# pyproject.toml (Ejemplo Parcial)

[build-system]
requires = ["hatchling"] # O "setuptools>=61", "wheel", "poetry-core", etc.
build-backend = "hatchling.build" # O "setuptools.build_meta", "poetry.core.masonry.api"

[project]
name = "mi-herramienta-genial"
version = "0.1.0" # O dinámico: dynamic = ["version"]
description = "Una herramienta genial que hace X y Y para código Python."
readme = "README.md"
requires-python = ">=3.8"
license = { file = "LICENSE" }
authors = [
    { name = "Tu Nombre", email = "tu@email.com" },
]
keywords = ["linter", "formatter", "python", "development", "tool"]
classifiers = [ # ¡Importantes para PyPI!
    "Development Status :: 4 - Beta",
    "Environment :: Console",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.8",
    "Programming Language :: Python :: 3.9",
    "Programming Language :: Python :: 3.10",
    "Programming Language :: Python :: 3.11",
    "Programming Language :: Python :: 3.12",
    "Topic :: Software Development :: Libraries :: Python Modules",
    "Topic :: Software Development :: Quality Assurance",
]
dependencies = [
    "click >= 8.0", # Ejemplo de dependencia para CLI
    "tomli >= 2.0; python_version < '3.11'", # Dependencia condicional
]

[project.urls] # Links útiles
Homepage = "[https://github.com/tu_usuario/mi-herramienta-python](https://github.com/tu_usuario/mi-herramienta-python)"
Documentation = "[https://mi-herramienta-python.readthedocs.io](https://mi-herramienta-python.readthedocs.io)"
Repository = "[https://github.com/tu_usuario/mi-herramienta-python](https://github.com/tu_usuario/mi-herramienta-python)"
Changelog = "[https://github.com/tu_usuario/mi-herramienta-python/blob/main/CHANGELOG.md](https://github.com/tu_usuario/mi-herramienta-python/blob/main/CHANGELOG.md)"

[project.scripts] # Define el comando CLI
mi-herramienta = "mi_herramienta.cli:main"

[project.optional-dependencies] # Para extras, como plugins
dev = [
    "pytest",
    "pytest-cov",
    "coverage[toml]",
    "ruff",
    "mypy",
    "pre-commit",
    "tox",
]
plugin-feature-x = ["libreria-extra"]

[tool.ruff] # Configuración de Ruff (ejemplo)
line-length = 88
select = ["E", "F", "W", "I", "UP"]

[tool.pytest.ini_options] # Configuración de Pytest
minversion = "7.0"
addopts = "-ra -q --cov=mi_herramienta --cov-report=term-missing"
testpaths = ["tests"]
```

# ... y configuración para otras herramientas (mypy, coverage, etc.)
  
## Claves del pyproject.toml:

    Build Backend: Elige uno moderno (Hatch, Poetry, Flit, o Setuptools moderno). Hatch es muy popular y potente actualmente.
    Metadatos Completos: No escatimes en classifiers, keywords, urls. Ayudan a la descubribilidad en PyPI.
    Dependencias Claras: Separa las dependencias de ejecución de las de desarrollo (optional-dependencies). Usa marcadores de entorno si es necesario.
    Punto de Entrada ([project.scripts]): Esencial para que pip install cree el ejecutable en el PATH del usuario.
    ¡Fija tus Dependencias! Usa un gestor como Poetry, PDM, o pip-tools (pip-compile) para generar un archivo de bloqueo (poetry.lock, pdm.lock, requirements.txt). Esto asegura que tú, tus colaboradores y tu CI usen exactamente las mismas versiones de las dependencias, garantizando builds reproducibles. ¡No confíes solo en los rangos de pyproject.toml para tus entornos de desarrollo y CI!

## Testing Estratégico: ¡Prueba Como Si Tu Reputación Dependiera de Ello! (Porque Depende) 🧪

    Framework: Pytest es el rey indiscutible. Sus fixtures, plugins y sintaxis clara lo hacen ideal.
    Cobertura: Usa coverage.py (integrado con pytest vía pytest-cov) para medir qué porcentaje de tu código se ejecuta durante las pruebas. ¡Apunta alto! Y configura [tool.coverage.run] en pyproject.toml.
    Matrix Testing: Tu CI (GitHub Actions, GitLab CI) DEBE ejecutar los tests en todas las versiones de Python y sistemas operativos (Linux, macOS, Windows) que declaras soportar. tox es excelente para gestionar estos entornos localmente, y los servicios de CI facilitan la matriz remota.
    Tipos de Tests:
        Unitarios (tests/unit/): Prueban piezas aisladas de lógica (funciones, clases) con entradas controladas.
        Integración (tests/integration/): Prueban la interacción entre varios componentes internos de tu herramienta.
        Funcionales/E2E (tests/functional/): Ejecutan tu herramienta como un usuario final (a menudo el CLI) contra archivos o proyectos de ejemplo (tests/fixtures/) y verifican la salida, los efectos secundarios (archivos modificados) o el código de salida.
    Snapshot Testing: Para herramientas que modifican código (formatters) o generan salidas complejas, librerías como pytest-snapshot pueden ser útiles para detectar regresiones inesperadas en la salida.

Consejos de Oro 💡 (Directo del Horno de la Experiencia)

    ¡Come tu Propia Comida! (Dogfooding): La mejor manera de asegurar la calidad de tu DevTool es usarla intensivamente en su propio codebase. Configura pre-commit para ejecutar tu linter/formatter antes de cada commit. Si tu propio linter grita cada vez que intentas commitear... tienes un problema (o una excelente oportunidad para mejorar tu herramienta). 😉
    pyproject.toml es tu Torre de Control: Centraliza la configuración de Black, Ruff, Isort, Mypy, Pytest, Coverage.py, etc., aquí. Menos archivos de configuración sueltos, más consistencia.
    Versionado Semántico (SemVer): Adopta SemVer (MAJOR.MINOR.PATCH). Es crucial para que los usuarios entiendan qué esperar de cada release y gestionen sus dependencias.
    CI Implacable: Tests, linting, type checking (mypy), coverage. ¡En cada PR! Automatiza la publicación a PyPI en tags/releases de Git para evitar errores manuales.
    Documentación Nivel Dios: No basta con que funcione. Explica claramente cómo instalar, CÓMO CONFIGURAR (¡todas las opciones!), los códigos de error (si aplica), y cómo la gente puede contribuir. Sphinx + Read the Docs es una combinación probada y gratuita para OSS.
    Gestión de la Configuración Amigable: A medida que añades opciones, piensa en la experiencia del usuario. ¿Son los defaults sensatos? ¿Los mensajes de error de configuración son claros y útiles? ¿Hay algún comando para validar la configuración?

## ¡Cuidado! Errores Comunes y Cómo Evitarlos ⚠️

    ❌ Flat Layout (Código Mezclado en Raíz): Poner mi_herramienta/ directamente en la raíz. Causa confusión, problemas de importación (PYTHONPATH hell) y dificulta saber qué se instala realmente.
        Solución: ¡Usa la src/ layout! Es el estándar moderno y resuelve estos problemas.
    ❌ Dependencias Flotantes (No Lockfile): Confiar solo en los rangos de pyproject.toml para el desarrollo y CI. Un día pip install -e .[dev] funciona, al siguiente una sub-dependencia se actualiza y todo explota.
        Solución: ¡Usa un lockfile! poetry install, pdm install, o pip install -r requirements-dev.txt (generado con pip-compile). Commitea el lockfile.
    ❌ Ignorar Windows/macOS (El Pecado del Pingüino): Desarrollar y testear solo en Linux. ¡Sorpresa! Los paths, el sistema de archivos, y a veces hasta pequeñas diferencias en CPython pueden morderte.
        Solución: CI con una matriz de OS (ubuntu-latest, macos-latest, windows-latest). Usa pathlib para manejar paths de forma agnóstica.
    ❌ Testing Anémico: Solo unos pocos tests unitarios "felices". No pruebas cómo maneja tu herramienta errores de sintaxis, archivos vacíos, configuraciones raras, o la interacción real a través del CLI.
        Solución: Diversifica tus tests. Piensa en los casos límite. Usa archivos de prueba realistas (tests/fixtures/). Prueba la interfaz CLI explícitamente.
    ❌ Packaging de Pacotilla: Olvidar LICENSE en el wheel, classifiers incorrectos, no incluir archivos de datos necesarios, romper la compatibilidad sin cambiar la versión MAJOR.
        Solución: Lee la Guía de Packaging de Python. Usa twine check dist/* antes de subir a PyPI. Sigue SemVer religiosamente.
    ❌ Reinventar el Parsing (¡No Uses Regex para HTML/XML/Python!): Intentar analizar código Python complejo con expresiones regulares o lógica manual frágil.
        Solución: ¡Usa las herramientas adecuadas! El módulo ast de Python es tu punto de partida. Para análisis más complejos o refactorizaciones seguras, mira LibCST. Para otros formatos (TOML, YAML, JSON), usa librerías robustas y estándar.

### Conclusión: Forjando Calidad desde la Estructura

Crear una herramienta de desarrollo Python exitosa es un desafío gratificante. Requiere no solo ingenio en la lógica central, sino también disciplina en la estructura del repositorio, rigor en las pruebas, y cuidado en el empaquetado y la documentación.

Adoptando una estructura clara como la descrita aquí, aprovechando las herramientas modernas del ecosistema Python (como pyproject.toml, pytest, pre-commit, tox, Sphinx) y prestando atención a los detalles, estarás sentando las bases para un proyecto mantenible, robusto y amigable para los contribuidores.
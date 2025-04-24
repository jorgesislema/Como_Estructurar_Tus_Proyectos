# Convenciones de Nombres en Repositorios

La forma en que nombramos los elementos dentro de un repositorio —desde las ramas y commits hasta los archivos y directorios— tiene un impacto profundo en la legibilidad, mantenibilidad y colaboración del proyecto. Establecer y seguir convenciones de nombres claras y consistentes es una de las prácticas fundamentales más efectivas para mejorar la calidad de cualquier base de código o proyecto.

Este documento describe las convenciones de nombres recomendadas dentro del ecosistema de `repo-universal-structure`. El objetivo principal es fomentar la **claridad**, la **consistencia** y facilitar la **automatización**.

## Principios Generales

Antes de entrar en detalles específicos, recordemos algunos principios generales:

1.  **Claridad ante Todo:** Los nombres deben ser descriptivos y reflejar el propósito del elemento que nombran. Evita abreviaturas excesivamente crípticas o nombres ambiguos.
2.  **Consistencia Férrea:** Una vez elegida una convención, aplícala de manera uniforme en todo el proyecto. La inconsistencia genera confusión.
3.  **Prefiere el Inglés:** En la mayoría de los contextos de desarrollo de software, el inglés es el idioma estándar para el código, los comentarios y los nombres de archivos/directorios. Esto facilita la colaboración internacional y el uso de herramientas estándar.
4.  **Evita Caracteres Especiales y Espacios:** Utiliza únicamente caracteres alfanuméricos, guiones (`-`) o guiones bajos (`_`) según la convención. Evita espacios, acentos, `ñ` u otros caracteres no estándar que pueden causar problemas entre diferentes sistemas operativos o herramientas.
5.  **Sensibilidad a Mayúsculas/Minúsculas:** Ten en cuenta que sistemas como Git pueden ser configurados para ser sensibles o insensibles a mayúsculas/minúsculas, y los sistemas de archivos (Windows vs. Linux/macOS) tienen comportamientos diferentes. Usar consistentemente minúsculas para directorios y la mayoría de los archivos (excepto donde la convención del lenguaje indique lo contrario) ayuda a prevenir problemas.

## Convenciones Específicas

### 1. Ramas (Branches)

Utilizar un esquema de nombres consistente para las ramas mejora enormemente la organización del flujo de trabajo de Git. Recomendamos un formato basado en prefijos que indiquen el propósito de la rama:

* **Rama Principal:**
    * `main` (Preferida actualmente sobre `master`)
* **Desarrollo de Funcionalidades:**
    * `feature/<descripcion-breve>` (ej. `feature/user-authentication`, `feature/add-shopping-cart`)
* **Corrección de Errores (Bugfixes):**
    * `fix/<descripcion-o-issue-id>` (ej. `fix/login-error`, `fix/issue-123-alignment`)
    * `bugfix/<descripcion-o-issue-id>` (Alternativa común)
* **Versiones de Lanzamiento (Releases):**
    * `release/<version>` (ej. `release/v1.0.0`, `release/v2.1.0-beta`) - Usada para preparar un lanzamiento.
* **Correcciones Urgentes en Producción (Hotfixes):**
    * `hotfix/<version-o-descripcion>` (ej. `hotfix/v1.0.1`, `hotfix/security-patch-login`)

**Rationale:** Los prefijos agrupan ramas por propósito, facilitando la visualización (`git branch --list 'feature/*'`) y la comprensión del estado del desarrollo.

### 2. Mensajes de Commit

Recomendamos encarecidamente adoptar el estándar **[Conventional Commits](https://www.conventionalcommits.org/)**. Proporciona una estructura simple pero poderosa para los mensajes de commit que facilita la automatización (como la generación de `CHANGELOG`s) y mejora la legibilidad del historial.

**Formato Básico:**

**Tipos Comunes:**

* `feat`: Introduce una nueva funcionalidad para el usuario.
* `fix`: Corrige un error (bug) en el código.
* `build`: Cambios que afectan al sistema de construcción o dependencias externas (ej. `npm install`, `pom.xml`).
* `chore`: Otros cambios que no modifican el código fuente o las pruebas (ej. actualización de tareas Grunt, configuración de linters).
* `ci`: Cambios en los archivos y scripts de configuración de Integración Continua (ej. GitHub Actions, GitLab CI).
* `docs`: Cambios únicamente en la documentación.
* `style`: Cambios que no afectan el significado del código (espacios, formato, puntos y comas faltantes, etc.).
* `refactor`: Cambios en el código que no corrigen un error ni añaden una funcionalidad.
* `perf`: Cambios en el código que mejoran el rendimiento.
* `test`: Añadir pruebas faltantes o corregir pruebas existentes.

### 3. Directorios

Para nombres de directorios, la convención más común y recomendada es **`kebab-case`** (minúsculas, palabras separadas por guiones).

* **Ejemplos:** `src/`, `docs/`, `user-authentication/`, `data-processing-scripts/`, `.github/`

**Rationale:**
* Es fácil de leer.
* Evita problemas de sensibilidad a mayúsculas/minúsculas entre sistemas operativos.
* Es amigable con las URLs (si los directorios se exponen de alguna manera).
* El guion es generalmente preferido sobre el guion bajo para separar palabras en nombres de directorios y archivos que no son código fuente directo.

### 4. Archivos

La convención para nombres de archivo depende del tipo de archivo:

* **Archivos de Configuración, Documentación, Scripts, etc.:** Se recomienda **`kebab-case`**, igual que los directorios.
    * **Ejemplos:** `README.md`, `CONTRIBUTING.md`, `LICENSE`, `docker-compose.yml`, `package.json`, `requirements.txt`, `build-script.sh`
* **Archivos de Código Fuente:** **La convención del lenguaje o framework específico tiene prioridad.** Esto es crucial para la consistencia dentro del ecosistema tecnológico.
    * **Python:** `snake_case` (ej. `my_module.py`, `data_processing_utils.py`)
    * **Java/C#:** `PascalCase` para clases (ej. `UserAuthenticationService.java`, `Order.cs`)
    * **JavaScript/TypeScript (React/Vue/Angular):** A menudo `PascalCase` (ej. `UserProfile.jsx`) o `kebab-case` (ej. `user-profile.component.ts`) dependiendo del framework y la configuración del linter.
    * **JavaScript (Node.js modules):** A menudo `kebab-case` o `camelCase` (ej. `server.js`, `authController.js`).
    * **CSS/SCSS Modules:** A menudo `kebab-case` (ej. `main-styles.scss`).

**Rationale:** Adherirse a las convenciones del ecosistema tecnológico facilita la integración con herramientas (linters, IDEs) y la comprensión por parte de desarrolladores familiarizados con ese lenguaje o framework. Para archivos genéricos, `kebab-case` mantiene la consistencia con los directorios.

### 5. Tags (Etiquetas para Versiones)

Para etiquetar versiones, recomendamos seguir **[Semantic Versioning (SemVer)](https://semver.org/)**.

* **Formato:** `vMAJOR.MINOR.PATCH`
    * `MAJOR`: Cambios incompatibles con versiones anteriores (breaking changes).
    * `MINOR`: Añadir funcionalidad compatible con versiones anteriores.
    * `PATCH`: Correcciones de errores compatibles con versiones anteriores.
* **Prefijo `v`:** Es común y recomendado añadir un prefijo `v` (ej. `v1.0.0`, `v2.3.1`) para distinguir claramente las etiquetas de versión de otras posibles etiquetas o números.
* **Pre-releases:** `v1.0.0-alpha.1`, `v2.0.0-beta.3`
* **Build Metadata:** `v1.0.0+build.123` (menos común para tags públicos)

**Rationale:** SemVer comunica claramente el impacto de los cambios entre versiones, esencial para la gestión de dependencias y la planificación de actualizaciones.

## Herramientas para la Consistencia

Existen herramientas que pueden ayudarte a mantener estas convenciones:

* **Linters y Formatters:** (ESLint, Prettier, Black, Flake8, Checkstyle) para el formato del código y, a veces, nombres de archivos.
* **Hooks de Git:** (Husky, pre-commit) para ejecutar scripts antes de hacer commit o push.
* **Commitizen / commitlint:** Para ayudar a formatear mensajes de commit según Conventional Commits.

## Conclusión

Las convenciones de nombres pueden parecer un detalle menor, pero su aplicación consistente es una marca de profesionalismo y una inversión invaluable en la salud a largo plazo de tu proyecto. Al adoptar un conjunto claro de convenciones (ya sean estas recomendaciones u otras que definas para tu equipo), reducirás la fricción, mejorarás la comunicación y harás que tu repositorio sea un lugar mucho más agradable y eficiente para trabajar.
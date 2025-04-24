# Estructura y Buenas Prácticas para Proyectos Fullstack

Los proyectos Fullstack abarcan tanto el desarrollo de la interfaz de usuario (Frontend) como la lógica del servidor, bases de datos y APIs (Backend) dentro del mismo contexto de aplicación. Ejemplos comunes incluyen aplicaciones web tradicionales, aplicaciones de página única (SPAs) con su propia API backend, o proyectos donde un equipo maneja ambas partes.

Gestionar un proyecto fullstack presenta desafíos únicos: mantener una separación clara de conceptos entre el cliente y el servidor (a menudo con diferentes lenguajes o stacks tecnológicos), coordinar cambios que afectan a ambas partes, decidir cómo y qué código compartir (si aplica), y establecer procesos de build, testing y despliegue eficientes para todo el conjunto.

Esta guía se enfoca en estructurar un proyecto fullstack dentro de un **único repositorio (monorepo)**, una estrategia común para mantener la cohesión, aunque enfatizando la necesidad de límites claros entre Frontend y Backend.

## Principios Clave para Fullstack

Adaptando nuestros principios generales al contexto fullstack, destacamos:

1.  **Límites Claros (Clear Boundaries):** La estructura del repositorio DEBE separar inequívocamente el código y las preocupaciones del Frontend y del Backend. Esto es crucial para la mantenibilidad y el desarrollo independiente.
2.  **Contrato API como Interfaz:** La API definida por el Backend es el contrato fundamental que conecta ambas partes. Debe estar bien definida, documentada (OpenAPI/Swagger) y versionada. Cambios en la API requieren coordinación.
3.  **Compartir Código con Precaución:** Si se comparte código (ej. tipos TypeScript, lógica de validación), debe hacerse de forma explícita y controlada (ej. en un directorio `shared` o `packages`), entendiendo las implicaciones de acoplamiento.
4.  **Fomentar Desarrollo Independiente:** Aunque estén en el mismo repo, la estructura debe permitir, en la medida de lo posible, que los desarrolladores frontend y backend trabajen en sus respectivas áreas sin bloquearse mutuamente.
5.  **Gestión de Configuración Diferenciada:** Reconocer y gestionar las distintas necesidades de configuración del Frontend (variables de entorno en tiempo de build) y del Backend (variables de entorno y secretos en tiempo de ejecución).
6.  **Automatización Integral:** Los procesos de CI/CD deben poder gestionar el build, las pruebas y el despliegue de ambas partes, idealmente de forma independiente si es posible.

## Estrategia de Repositorio: El Monorepo

Para proyectos fullstack donde se desea gestionar el código fuente de manera unificada, el enfoque **monorepo** (un único repositorio conteniendo múltiples proyectos o paquetes relacionados) es una opción popular y poderosa, especialmente en ecosistemas como JavaScript/TypeScript con herramientas de soporte.

**Alternativa:** La principal alternativa es usar repositorios separados (polyrepo) para frontend y backend. Sin embargo, esta guía se centra en la estructura *dentro* de un monorepo.

## Estructura de Directorios Recomendada (Monorepo)

Una estructura típica y efectiva para un monorepo fullstack es:

``` bash
mi-proyecto-fullstack/
├── .github/              # Workflows CI/CD (manejando FE y BE)
│   └── workflows/
│       ├── frontend-ci-cd.yml
│       └── backend-ci-cd.yml
├── .gitignore            # Ignorar archivos de FE y BE (node_modules/, dist/, venv/, etc.)
├── .husky/               # (Opcional) Git hooks
├── .prettierrc.js        # (Opcional) Formateador común en la raíz
├── .editorconfig         # (Opcional) Configuración común de editor
├── backend/              # Proyecto Backend completo
│   ├── src/
│   ├── tests/
│   ├── db/
│   ├── Dockerfile        # Dockerfile específico del Backend
│   ├── package.json      # O requirements.txt, pom.xml, etc.
│   └── README.md         # README específico del Backend
├── frontend/             # Proyecto Frontend completo
│   ├── public/
│   ├── src/
│   ├── tests/
│   ├── Dockerfile        # (Opcional) Dockerfile para servir/buildear el Frontend
│   ├── package.json      # package.json específico del Frontend
│   └── README.md         # README específico del Frontend
├── packages/             # (Opcional) Código compartido entre FE y BE
│   ├── types/            # Ej. Definiciones TypeScript compartidas
│   │   └── package.json
│   └── validation/       # Ej. Esquemas de validación compartidos (Joi, Zod)
│       └── package.json
├── docker-compose.yml    # Para levantar todo el stack localmente (FE, BE, DB)
├── LICENSE
├── package.json          # (Opcional, Raíz) Si se usan Workspaces/Lerna/Nx
└── README.md             # README principal del proyecto Fullstack
```

**Explicación de Componentes Clave:**

* **`frontend/`**: Contiene la aplicación frontend completa. Su estructura interna debe seguir las [buenas prácticas para Frontend](./frontend.md). Tiene sus propias dependencias (`package.json`), scripts de build y tests.
* **`backend/`**: Contiene la aplicación backend completa (API, lógica de negocio, acceso a datos). Su estructura interna debe seguir las [buenas prácticas para Backend](./backend.md). Tiene sus propias dependencias, Dockerfile, scripts y tests.
* **`packages/` (o `shared/`)**: (Opcional pero potente en monorepos) Directorio para paquetes de código destinados a ser utilizados tanto por el frontend como por el backend. Requiere herramientas para gestionar dependencias entre paquetes (npm/yarn workspaces, Lerna, Nx, Turborepo).
* **`README.md` (Raíz):** Documentación general del proyecto fullstack, explicando cómo interactúan las partes, y cómo configurar y ejecutar *todo* el stack localmente (a menudo usando `docker-compose.yml`).
* **`.gitignore` (Raíz):** Debe incluir patrones para ignorar artefactos de ambas partes (ej. `frontend/dist`, `backend/target`, `node_modules` en ambos, `*.pyc`, `.env` en ambos).
* **`.github/workflows/` (Raíz):** Los pipelines de CI/CD necesitan ser más sofisticados. Pueden tener workflows separados para FE y BE, o uno que maneje ambos, usando filtros de ruta (`paths:`) para ejecutar jobs solo cuando cambian archivos relevantes en `frontend/` o `backend/`.
* **`docker-compose.yml` (Raíz):** Muy útil para desarrollo local, permitiendo levantar la base de datos, el backend y el servidor de desarrollo del frontend con un solo comando (`docker-compose up`).

## El Contrato API: El Nexo Crucial

La comunicación entre `frontend/` y `backend/` se realiza a través de la API expuesta por el backend. Es vital:

1.  **Definir la API:** Usar OpenAPI/Swagger.
2.  **Documentar la API:** Guardar la especificación (ej. en `backend/api-docs/` o `docs/api/`).
3.  **Versionar la API:** Gestionar cambios en la API de forma controlada.

## Compartir Código (Cuándo y Cómo)

El directorio `packages/` (o `shared/`) permite compartir código, pero debe usarse con criterio:

* **Casos de Uso Comunes:**
    * **Tipos/Interfaces (TypeScript):** Definir estructuras de datos compartidas.
    * **Esquemas de Validación:** Compartir reglas de validación (ej. con Zod, Joi, Yup) para usarlas en el frontend (formularios) y backend (validación de API).
    * **Constantes:** Valores constantes usados en ambos lados.
    * **Utilidades Muy Simples:** Funciones puras muy básicas (con precaución).
* **Qué Evitar Compartir:** Evita compartir lógica de negocio compleja, acceso a datos o componentes UI directamente. Mantén esos aspectos dentro de sus respectivos dominios (`frontend` o `backend`).
* **Herramientas:** Para gestionar eficazmente los paquetes compartidos, especialmente en JavaScript/TypeScript, se suelen usar herramientas como **npm/yarn/pnpm Workspaces**, **Lerna**, **Nx**, o **Turborepo**. Estas ayudan a manejar las dependencias locales, ejecutar scripts y optimizar builds.

## Build, CI/CD y Despliegue Fullstack

* **Build Separado:** Generalmente, el frontend y el backend tienen procesos de build independientes definidos en sus respectivos `package.json` (o `pom.xml`, etc.).
* **CI/CD Inteligente:** El pipeline debe:
    * Detectar qué parte ha cambiado (frontend, backend, shared).
    * Ejecutar tests específicos para las partes afectadas.
    * Construir los artefactos necesarios (bundle JS/CSS para FE, imagen Docker/JAR para BE).
    * Desplegar las partes de forma independiente o coordinada según la estrategia.
* **Estrategias de Despliegue:** Comunes:
    * Frontend: A un CDN (Netlify, Vercel, S3/CloudFront, Azure Static Web Apps).
    * Backend: A un servidor de aplicaciones, plataforma serverless (Lambda, Cloud Functions), contenedores (Kubernetes, ECS, Cloud Run).

## Testing en Fullstack

* **Unit/Integration:** Residen dentro de `frontend/tests/` y `backend/tests/`, probando cada parte de forma aislada. Las pruebas de integración del backend pueden necesitar una base de datos de prueba.
* **End-to-End (E2E):** Prueban el sistema completo simulando la interacción del usuario desde el frontend hasta el backend y la base de datos. Suelen ubicarse en un directorio raíz (`tests/e2e/`) o dentro del frontend, y usan herramientas como Cypress o Playwright.

## Gestión de Configuración Fullstack

* **Frontend:** Principalmente variables de entorno en tiempo de *build* (ej. `VITE_API_URL` o `REACT_APP_API_ENDPOINT`) inyectadas por el bundler. Se configuran a través de archivos `.env` (ej. `.env.development`, `.env.production`) que deben estar en el `.gitignore`.
* **Backend:** Variables de entorno y secretos en tiempo de *ejecución* (conexiones a BD, claves de API secretas). Se gestionan mediante variables de entorno del servidor, gestores de secretos o archivos de configuración leídos al arrancar.

## Conclusión

Estructurar un proyecto fullstack en un monorepo ofrece ventajas en términos de visibilidad y potencial para compartir código, pero requiere disciplina para mantener límites claros entre el frontend y el backend. La clave es tratar cada parte como un proyecto semi-independiente dentro del repositorio mayor, conectados por un contrato API bien definido. Con las herramientas y la estructura adecuadas, se puede lograr un flujo de desarrollo eficiente, coordinado y mantenible para aplicaciones fullstack complejas.
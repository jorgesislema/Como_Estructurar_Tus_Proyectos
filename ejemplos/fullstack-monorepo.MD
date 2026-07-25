
# Ejemplo: Monorepo Fullstack Moderno (Turborepo, PNPM, React, Node.js)

¡Bienvenido/a a la plantilla de ejemplo de un monorepo fullstack! Este repositorio demuestra cómo estructurar y gestionar un proyecto que contiene múltiples aplicaciones (como un frontend web y una API backend) y librerías compartidas, todo dentro de un único repositorio Git.

## ¿Qué es un Monorepo y Por Qué Usarlo? 🤔

Un **monorepo** es una estrategia de control de versiones donde el código de muchos proyectos relacionados vive en el mismo repositorio. Contrasta con el enfoque *polyrepo* (un repositorio por proyecto).

**Ventajas:**

* **Código Compartido Simplificado:** Fácil de crear y consumir librerías/tipos compartidos (`packages/shared-types`) entre el frontend y el backend.
* **Cambios Atómicos:** Un solo commit/PR puede abarcar cambios coordinados en el frontend, backend y librerías compartidas.
* **Refactorización más Sencilla:** Mover código entre paquetes o renombrar interfaces compartidas es más directo.
* **Consistencia de Tooling:** Más fácil aplicar linters, formateadores y configuraciones de TypeScript de forma uniforme en todo el proyecto.
* **Gestión de Dependencias Centralizada (Parcial):** Aunque cada paquete tiene sus dependencias, herramientas como PNPM optimizan el almacenamiento y enlazado.

**Desafíos (¡Abordados aquí!):**

* **Tooling Complejo:** Se necesitan herramientas específicas para gestionar dependencias y ejecutar tareas eficientemente. (Usamos **PNPM Workspaces** y **Turborepo**).
* **Tiempos de Build/Test Largos:** Sin optimización, construir/testear todo en cada cambio es lento. (**Turborepo** soluciona esto con cacheo inteligente).
* **Control de Acceso:** Puede ser más complejo si diferentes equipos necesitan permisos distintos (más relevante a gran escala).

Este ejemplo utiliza **PNPM Workspaces** para manejar las dependencias entre paquetes locales de forma eficiente y **Turborepo** para orquestar tareas (build, test, lint) y aplicar un cacheo muy efectivo, acelerando significativamente los flujos de trabajo.

## Objetivo 🎯

* Mostrar una **estructura de monorepo funcional y escalable** usando herramientas modernas.
* Demostrar cómo gestionar **múltiples paquetes** (frontend, backend, compartido) en un solo lugar.
* Ilustrar el uso de **Turborepo** para optimizar la ejecución de tareas y el cacheo.
* Servir como un **punto de partida práctico** para tus propios proyectos fullstack en monorepo.

## Tecnologías Clave 🛠️

* **Gestor de Monorepo:** [Turborepo](https://turbo.build/repo)
* **Gestor de Paquetes / Workspaces:** [PNPM](https://pnpm.io/)
* **Frontend (`packages/web`):** [React](https://react.dev/), [Vite](https://vitejs.dev/), [TypeScript](https://www.typescriptlang.org/)
* **Backend (`packages/api`):** [Node.js](https://nodejs.org/), [Express](https://expressjs.com/) (o Fastify), [TypeScript](https://www.typescriptlang.org/)
* **Paquete Compartido (`packages/shared-types`):** [TypeScript](https://www.typescriptlang.org/)
* **Calidad de Código:** ESLint, Prettier (configurados en la raíz y/o por paquete).

## Estructura del Directorio 🗺️

* **Raíz:** Contiene la configuración global del monorepo y herramientas.
    * `package.json`: Define los `workspaces` (usando PNPM), los scripts globales que usan Turbo (ej: `pnpm dev`), y las dependencias de desarrollo raíz (Turborepo, TypeScript, linters). **Importante:** `private: true` para evitar publicar la raíz en npm.
    * `pnpm-workspace.yaml`: Especifica dónde buscar los paquetes dentro del monorepo (usualmente `packages/*`).
    * `turbo.json`: ¡El cerebro de Turborepo! Define los `pipelines` (cómo ejecutar tareas como `build`, `dev`, `lint`, `test`), sus dependencias (ej: `build` depende de los `build` de sus dependencias internas y de `^build` que significa las dependencias del paquete), y qué artefactos/logs cachear.
    * `tsconfig.base.json`: Configuración base de TypeScript que los paquetes pueden extender.
* **`packages/`**: Directorio que contiene cada aplicación o librería individual. Cada subdirectorio es un "paquete" autocontenido con su propio `package.json`, código fuente y configuración específica.
    * **`api/`**: Aplicación backend Node.js/Express. Tiene sus propias dependencias y scripts. Puede depender de `shared-types`.
    * **`web/`**: Aplicación frontend React/Vite. Tiene sus propias dependencias y scripts. Puede depender de `shared-types` y consumir la `api`.
    * **`shared-types/`**: Ejemplo de librería compartida. En este caso, solo contiene definiciones de tipos TypeScript. Otros paquetes (`api`, `web`) pueden declararla como dependencia en sus `package.json`.

## Cómo Usar esta Plantilla 🚀

1.  **Prerrequisitos:**
    * Node.js (verifica la versión recomendada si hay un archivo `.node-version` o `.nvmrc`).
    * PNPM instalado globalmente: `npm install -g pnpm`
2.  **Clonar:** `git clone <URL_DEL_REPO_UNIVERSAL> && cd ejemplos/fullstack-monorepo`
3.  **Instalar Dependencias:** Este comando instalará las dependencias para **todos** los paquetes definidos en los workspaces y los enlazará localmente.
    ```bash
    pnpm install
    ```
4.  **Variables de Entorno:** Configura los archivos `.env` necesarios dentro de cada paquete que lo requiera (ej: `packages/api/.env`, `packages/web/.env`) basándote en sus respectivos `.env.example`.
5.  **Ejecutar Servidores de Desarrollo:** Turborepo orquestará el inicio de los servidores de `api` y `web` (y cualquier otro paquete con script `dev`).
    ```bash
    # Ejecuta el script 'dev' definido en el package.json raíz,
    # que a su vez usa 'turbo run dev'
    pnpm dev
    ```
    Turborepo ejecutará los scripts `dev` definidos en los `package.json` de `api` y `web` en paralelo.
6.  **Construir Todos los Paquetes:** Turborepo construirá los paquetes en el orden correcto (respetando dependencias) y usará el cache si es posible.
    ```bash
    pnpm build
    ```
7.  **Ejecutar Linters/Tests:**
    ```bash
    pnpm lint
    pnpm test
    ```
    Turborepo ejecutará los scripts correspondientes en todos los paquetes que los definan.
8.  **Ejecutar Comandos en un Paquete Específico:** Usa el flag `--filter` de PNPM (o Turbo).
    ```bash
    # Ejecutar solo el dev server de la API
    pnpm --filter api dev

    # Ejecutar solo los tests del frontend
    pnpm --filter web test

    # Construir solo la librería compartida y los paquetes que dependen de ella
    pnpm --filter shared-types... build
    ```

## Conceptos Clave del Monorepo Aquí Implementado 🧩

* **Workspaces (PNPM):** PNPM lee `pnpm-workspace.yaml` y el campo `workspaces` en `package.json` para saber dónde están los paquetes. `pnpm install` instala todo y crea symlinks en `node_modules` para que los paquetes puedan importarse entre sí como dependencias normales (ej: `import { User } from 'shared-types';`).
* **Task Orchestration (Turborepo):** Turborepo lee `turbo.json` para entender las dependencias entre tareas (ej: el `build` de `web` depende del `build` de `shared-types`). Ejecuta tareas en paralelo cuando es posible y en el orden correcto.
* **Caching (Turborepo):** Turborepo cachea la salida (`dist/`, `.turbo/`) y los logs de las tareas. Si ejecutas `pnpm build` una segunda vez sin cambios en el código fuente de un paquete y sus dependencias, Turborepo restaurará la salida desde el cache instantáneamente (verás `FULL TURBO`). ¡Esto ahorra muchísimo tiempo!
* **Shared Packages:** El paquete `shared-types` es un ejemplo. Se construye (compila TS a JS + `.d.ts`) y otros paquetes (`api`, `web`) lo declaran como dependencia en su `package.json` (usando `pnpm add shared-types --filter web`). PNPM se encarga del enlace local.

## Siguientes Pasos y Adaptación 👣

* **Añade una Base de Datos:** Integra una base de datos en el backend (`api/`) y usa Docker Compose para gestionarla localmente.
* **Implementa Autenticación Completa:** Añade lógica de autenticación en `api` y `web`.
* **Crea más Paquetes:**
    * Una librería de componentes UI compartida (`packages/ui-library`).
    * Una aplicación móvil (React Native, Flutter).
    * Herramientas de scripting (`packages/scripts`).
* **Refina el Pipeline de Turborepo:** Ajusta `turbo.json` para optimizar dependencias entre tareas y estrategias de cacheo.
* **Despliegue:** Configura despliegues independientes para `api` y `web` usando la CI/CD, aprovechando el cache de Turborepo (Remote Caching). Plataformas como Vercel y Netlify tienen integración nativa con Turborepo.

## Contribuciones a este Ejemplo ✨

Las sugerencias para mejorar *esta plantilla especifica de monorepo* son bienvenidas. Sigue la guia general (`plantillas/CONTRIBUTING.md`) indicando que tu aporte es para `ejemplos/fullstack-monorepo`.

¡Esperamos que esta plantilla te dé un impulso inicial fantástico para tus proyectos fullstack en monorepo! ¡Disfruta de la potencia y la organización! 🚀
```bash
ejemplos/fullstack-monorepo/
├── .gitattributes              # (Opcional) Para normalizar finales de línea
├── .gitignore                  # Ignorar node_modules, dist, .turbo, .env, etc.
├── .prettierignore             # (Opcional) Ignorar archivos para Prettier
├── .prettierrc.json            # (Opcional) Configuración de Prettier en la raíz
├── LICENSE
├── README.md                   # Este archivo: explicación del monorepo
├── package.json                # Raíz: Define workspaces, scripts globales, devDeps (turbo, typescript, etc.)
├── pnpm-workspace.yaml         # Define la ubicación de los paquetes/workspaces
├── tsconfig.base.json          # Configuración base de TypeScript compartida
└── turbo.json                  # Configuración de Turborepo (pipelines, cacheo)
└── packages/                   # Directorio que contiene todos los paquetes/apps
    ├── api/                    # Paquete del Backend (Node.js/Express/TypeScript)
    │   ├── .env.example
    │   ├── package.json        # Dependencias (express), scripts (dev, build, start)
    │   ├── src/
    │   │   ├── index.ts        # Punto de entrada del servidor
    │   │   ├── routes/
    │   │   └── services/
    │   └── tsconfig.json       # Extiende tsconfig.base.json
    ├── shared-types/           # Paquete compartido (ej: tipos TypeScript)
    │   ├── package.json        # Mínimo, define 'main' y 'types'
    │   ├── src/
    │   │   ├── index.ts        # Exporta todo
    │   │   └── user.ts
    │   └── tsconfig.json       # Extiende tsconfig.base.json, config de build .d.ts
    └── web/                    # Paquete del Frontend (React/Vite/TypeScript)
        ├── .env.example
        ├── index.html
        ├── package.json        # Dependencias (react), scripts (dev, build, preview)
        ├── public/
        ├── src/                # Similar al ejemplo frontend-react standalone
        │   ├── main.tsx
        │   ├── App.tsx
        │   ├── components/
        │   ├── hooks/
        │   ├── pages/
        │   └── services/       # Importa tipos desde 'shared-types'
        ├── tsconfig.json       # Extiende tsconfig.base.json
        └── vite.config.ts
```

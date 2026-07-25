# Ejemplo: Frontend React Moderno con Vite y TypeScript

¡Hola y bienvenido/a a esta plantilla de ejemplo para un frontend React! Este proyecto está diseñado para ser un punto de partida robusto y moderno para construir interfaces de usuario interactivas y escalables.

Utiliza **Vite** como herramienta de build y servidor de desarrollo por su increíble velocidad, junto con **React** (v18+) y **TypeScript** para un desarrollo tipado y más seguro. Se enfoca en una estructura clara, herramientas de calidad de código (ESLint, Prettier) y un setup de testing listo para usar.

## Objetivo 🎯

Este ejemplo busca:

* Proporcionar una **estructura de carpetas organizada y escalable** para aplicaciones React.
* Mostrar una configuración moderna con **Vite, TypeScript, ESLint y Prettier**.
* Incluir ejemplos básicos de **componentes, routing, manejo de estado (simple), llamadas a API y testing**.
* Servir como una **base sólida** que puedas clonar y personalizar para tus proyectos.

**Disclaimer:** Como plantilla, se enfoca en la estructura y el setup inicial. Para producción, considera aspectos adicionales como optimización de rendimiento avanzada, accesibilidad (a11y), seguridad, estrategias de manejo de errores más complejas, etc.

## Tecnologías Clave 🛠️

* **Framework:** [React](https://react.dev/) (v18+)
* **Build Tool:** [Vite](https://vitejs.dev/)
* **Lenguaje:** [TypeScript](https://www.typescriptlang.org/)
* **Routing:** [React Router DOM](https://reactrouter.com/) (v6+)
* **Linting:** [ESLint](https://eslint.org/)
* **Formateo:** [Prettier](https://prettier.io/)
* **Testing:** [Vitest](https://vitest.dev/) + [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/)
* **Gestión de Estado (Ejemplo Base):** React Context API (fácilmente reemplazable por Zustand, Redux Toolkit, Jotai, etc.)
* **Llamadas API (Ejemplo Base):** `Workspace` API (fácilmente reemplazable por `axios`, `react-query`, `swr`, etc.)
* **Estilos (Ejemplo Base):** CSS plano o CSS Modules (fácilmente adaptable a Tailwind CSS, Styled Components, Emotion, etc.)

## Estructura del Directorio 🗺️

Este proyecto utiliza una estructura común en aplicaciones React modernas:

* **`public/`**: Contiene activos estáticos que no son procesados por Vite y se sirven directamente desde la raíz (ej: `favicon.ico`, `robots.txt`).
* **`src/`**: ¡El corazón de tu aplicación! Todo el código fuente reside aquí.
    * **`main.tsx`**: El punto de entrada que renderiza el componente raíz (`App.tsx`) en el DOM.
    * **`App.tsx`**: Componente principal donde usualmente se configura el enrutador y los layouts globales.
    * **`assets/`**: Imágenes, fuentes u otros activos que *son importados* por tus componentes.
    * **`components/`**: La joya de la corona. Componentes de UI reutilizables. A menudo se subdividen:
        * `common/`: Componentes muy genéricos (Button, Input, Card, Modal).
        * `layout/`: Componentes para la estructura de la página (Header, Footer, Sidebar).
        * *(Opcional)* Carpetas por feature: Componentes específicos de una funcionalidad.
    * **`config/`**: Constantes y configuración de la aplicación (ej: URL base de la API).
    * **`contexts/`**: Si usas Context API, aquí van tus proveedores de contexto.
    * **`features/` / `modules/`**: *(Alternativa Estructural)* En lugar de agrupar por tipo (`components`, `hooks`, `services`), puedes agrupar por funcionalidad. Cada carpeta de feature contendría sus propios componentes, hooks, servicios, etc. ¡Muy útil para proyectos grandes!
    * **`hooks/`**: Tus propios Custom Hooks de React para encapsular lógica reutilizable con estado o efectos.
    * **`layouts/`**: Componentes que definen la estructura visual de diferentes tipos de páginas (ej: un layout para páginas públicas, otro para el dashboard de admin).
    * **`pages/` / `views/`**: Componentes que representan una página o vista completa de la aplicación, usualmente asociados a una ruta.
    * **`routes/`**: Definición y configuración de las rutas de la aplicación usando `react-router-dom`.
    * **`services/` / `api/`**: Funciones o clases para interactuar con APIs backend.
    * **`store/` / `state/`**: Si usas una librería de estado global más compleja (Redux Toolkit, Zustand, Jotai), aquí iría su configuración y lógica (slices, stores, atoms).
    * **`styles/`**: *(Opcional)* Para estilos globales, temas, variables CSS, si no usas CSS-in-JS o Tailwind. `index.css` es el punto de entrada global por defecto de Vite.
    * **`types/`**: Definiciones globales de TypeScript (interfaces, types) que se usan en múltiples partes de la aplicación.
    * **`utils/`**: Funciones de utilidad puras y genéricas (formateo de fechas, validaciones simples, etc.).
* **`tests/` o Colocados:** Los archivos de test (`*.test.tsx`). Vitest permite colocarlos junto a los archivos que prueban o en una carpeta `tests/` separada.
* **Archivos de Configuración Raíz:** `package.json`, `vite.config.ts`, `tsconfig.json`, `.eslintrc.cjs`, `.prettierrc.json` controlan el comportamiento del proyecto, las dependencias y las herramientas.

## Cómo Usar esta Plantilla 🚀

1.  **Clonar:** `git clone <URL_DEL_REPO_UNIVERSAL> && cd ejemplos/frontend-react`
2.  **Instalar Dependencias:** Elige tu gestor de paquetes preferido (asegúrate de tener Node.js instalado, verifica la versión en `.node-version` si existe).
    ```bash
    npm install
    # o
    yarn install
    # o
    pnpm install
    ```
3.  **Variables de Entorno:** Copia `.env.example` a `.env` y configura las variables necesarias (ej: `VITE_API_BASE_URL`).
    ```bash
    cp .env.example .env
    # Edita .env con tus valores
    ```
    *Recuerda: Vite solo expone variables prefijadas con `VITE_` al código frontend.*
4.  **Ejecutar Servidor de Desarrollo:**
    ```bash
    npm run dev
    # o
    yarn dev
    # o
    pnpm dev
    ```
    Abre tu navegador en la dirección indicada (usualmente `http://localhost:5173`). ¡Disfruta del Hot Module Replacement (HMR)!
5.  **Construir para Producción:**
    ```bash
    npm run build
    # o yarn build / pnpm build
    ```
    Esto generará los archivos estáticos optimizados en la carpeta `dist/`.
6.  **Previsualizar el Build:**
    ```bash
    npm run preview
    # o yarn preview / pnpm preview
    ```
    Levanta un servidor local simple para servir los archivos de `dist/`.
7.  **Linting y Formateo:**
    ```bash
    npm run lint       # Revisa problemas de linting
    npm run format     # Revisa problemas de formato con Prettier (o formatea si está configurado)
    ```
8.  **Ejecutar Pruebas:**
    ```bash
    npm run test       # Ejecuta Vitest
    npm run test:ui    # (Si tienes un script para abrir la UI de Vitest)
    npm run test:coverage # (Si tienes un script para generar coverage)
    ```

## Patrones y Decisiones Clave 🤔

* **Estructura:** Este ejemplo usa una mezcla de agrupación por *tipo* (`components`, `hooks`, `pages`) pero menciona la agrupación por *feature* como una alternativa escalable.
* **Componentes:** Fomenta la creación de componentes pequeños y reutilizables (`common/`).
* **Estilos:** El ejemplo base puede usar CSS simple o CSS Modules por simplicidad. Puedes adaptarlo fácilmente a Tailwind, Styled Components, Emotion, etc., instalando las dependencias y configurando Vite/PostCSS si es necesario.
* **Estado:** Usa Context API para ejemplos básicos. Para aplicaciones más complejas, considera librerías dedicadas como Zustand (simple), Jotai (atómico) o Redux Toolkit (robusto, más boilerplate).
* **Testing:** Vitest se integra perfectamente con Vite. React Testing Library fomenta probar los componentes como lo haría un usuario.

## Siguientes Pasos y Adaptación 👣

* **Elige tu Estilo y Estado:** Adapta la estrategia de estilos y la librería de gestión de estado a tus preferencias y necesidades.
* **Implementa la Lógica de API:** Rellena `services/` con las llamadas a tu backend real. Considera librerías como `axios` o herramientas de fetching/cache como `TanStack Query (React Query)` o `SWR`.
* **Añade Autenticación:** Implementa los flujos de login/registro y protege rutas usando tu contexto de autenticación y `react-router-dom`.
* **Desarrolla tus Features:** ¡Crea tus propios componentes, páginas, hooks y servicios!

## Contribuciones a este Ejemplo ✨

Si tienes ideas para mejorar *esta plantilla especifica*, por favor, sigue la guia de contribucion general (`plantillas/CONTRIBUTING.md`) e indica que tu aporte es para `ejemplos/frontend-react`.

¡Esperamos que esta plantilla te dé un excelente punto de partida para tus aventuras con React! 🎉

``` bash
ejemplos/frontend-react/
├── .env.example                # Ejemplo de variables de entorno (prefijo VITE_)
├── .eslintignore               # (Opcional) Archivos a ignorar por ESLint
├── .eslintrc.cjs               # Configuración de ESLint (o .json, .js)
├── .gitignore                  # Archivos a ignorar por Git (estándar Node + build)
├── .node-version               # (Opcional) Versión recomendada de Node.js
├── .prettierignore             # (Opcional) Archivos a ignorar por Prettier
├── .prettierrc.json            # (Opcional) Configuración de Prettier (o .js)
├── index.html                  # Punto de entrada HTML (procesado por Vite)
├── package.json                # Dependencias, scripts (dev, build, test, lint), metadata
├── public/                     # Activos estáticos (copiados tal cual a la raíz del build)
│   ├── favicon.ico
│   └── robots.txt
├── README.md                   # Este archivo: explicación del ejemplo
├── src/                        # Código fuente de la aplicación React
│   ├── App.tsx                 # Componente raíz (a menudo configura rutas)
│   ├── assets/                 # Activos importados por componentes (imágenes, fuentes)
│   │   └── logo.svg
│   ├── components/             # Componentes de UI reutilizables
│   │   ├── common/             # Componentes genéricos (Button, Modal, Input)
│   │   │   └── Button.tsx
│   │   ├── layout/             # Componentes estructurales (Header, Footer, Sidebar)
│   │   │   └── Header.tsx
│   │   └── featureX/           # (Opcional) Componentes específicos de una feature
│   ├── config/                 # Configuración de la aplicación (endpoints API, constantes)
│   │   └── index.ts
│   ├── contexts/               # (Si usas Context API) Proveedores de contexto
│   │   └── AuthContext.tsx
│   ├── features/               # (Alternativa) Agrupación por feature (componentes, hooks, servicios)
│   │   └── authentication/
│   │       ├── components/
│   │       ├── hooks/
│   │       └── services/
│   ├── hooks/                  # Custom Hooks reutilizables
│   │   └── useFetch.ts
│   ├── index.css               # Estilos globales o punto de entrada de estilos
│   ├── layouts/                # (Opcional) Componentes de layout que envuelven páginas
│   │   └── MainLayout.tsx
│   ├── main.tsx                # Punto de entrada de la app (renderiza <App />)
│   ├── pages/                  # Componentes que representan páginas/vistas completas
│   │   ├── HomePage.tsx
│   │   └── LoginPage.tsx
│   ├── routes/                 # Configuración del enrutador (ej: react-router-dom)
│   │   └── index.tsx
│   ├── services/               # Funciones para interactuar con APIs externas
│   │   └── apiClient.ts
│   ├── store/                  # (Si usas Redux, Zustand, etc.) Lógica de estado global
│   │   └── userSlice.ts
│   ├── styles/                 # (Alternativa) Estilos más organizados (globales, temas)
│   │   └── theme.ts
│   ├── types/                  # Definiciones TypeScript (interfaces, types) globales
│   │   └── index.ts
│   └── utils/                  # Funciones de utilidad genéricas
│       └── formatters.ts
├── tests/                      # (Alternativa) Carpeta de tests separada
│   └── components/
│       └── Button.test.tsx
├── tsconfig.json               # Configuración principal de TypeScript
└── vite.config.ts              # Configuración de Vite (plugins, server, build)
```

### Nota: La ubicación de los tests (*.test.tsx) puede estar junto a los componentes (src/components/Button.test.tsx) o en una carpeta tests/ separada. Ambas son prácticas comunes.
# Flujos de Trabajo con Ramas de Git (Branching Workflows)

Si bien las [Buenas Prácticas de Git](./buenas-practicas-git.md) nos dicen *cómo* usar Git de manera efectiva a nivel individual (commits atómicos, ramas de funcionalidad, etc.), un **flujo de trabajo de ramas (branching workflow)** define la estrategia general que sigue un equipo para gestionar estas ramas, integrar cambios y realizar lanzamientos.

Adoptar un flujo de trabajo definido y consistente es vital para la colaboración ordenada, la gestión de lanzamientos y el mantenimiento de la estabilidad en diferentes entornos (desarrollo, pruebas, producción).

## ¿Por Qué un Flujo Definido?

* **Consistencia:** Todos en el equipo siguen las mismas reglas, reduciendo la confusión y los errores.
* **Predictibilidad:** Se sabe qué representa cada rama principal (ej. `main` es producción estable) y cómo fluyen los cambios entre ellas.
* **Gestión de Lanzamientos:** Facilita la preparación, prueba y ejecución de lanzamientos de software, ya sean continuos o versionados.
* **Paralelización:** Permite a los desarrolladores trabajar en diferentes funcionalidades o correcciones de forma aislada sin interferir entre sí.
* **Estabilidad:** Ayuda a proteger las ramas críticas (como `main`) de código inestable o no probado.

## Modelos Populares de Flujo de Ramas

Existen varios modelos populares, cada uno con sus propias ventajas y desventajas. Aquí describimos los más comunes:

### 1. GitHub Flow

* **Filosofía:** Simple, ligero y optimizado para la **Integración Continua y el Despliegue Continuo (CI/CD)**. La premisa central es que **`main` siempre debe estar en un estado desplegable**.
* **Ramas Principales:**
    * `main`: Refleja el código que está en producción o listo para desplegarse inmediatamente.
    * Ramas de funcionalidad/fix: Creadas a partir de `main` (ej. `feature/new-login`, `fix/typo-homepage`).
* **Flujo Típico:**
    1.  Todo en `main` es desplegable.
    2.  Para trabajar en algo nuevo, crea una rama descriptiva desde `main`.
    3.  Realiza commits en esa rama localmente y empújalos regularmente al repositorio remoto.
    4.  Cuando necesites feedback o ayuda, o la rama esté lista para fusionar, abre un **Pull Request (PR)**.
    5.  Después de la revisión y aprobación del PR (y de que pasen las pruebas de CI), **fusiona la rama en `main`**.
    6.  Una vez fusionado en `main`, **despliega inmediatamente** a producción.
* **Pros:**
    * Muy simple y fácil de entender.
    * Fomenta ciclos de desarrollo rápidos y despliegues frecuentes.
    * Excelente para equipos que practican CI/CD y despliegan varias veces al día.
    * Minimiza la cantidad de ramas activas simultáneamente.
* **Contras:**
    * Requiere una cultura sólida de pruebas automatizadas y CI/CD para asegurar que `main` sea *realmente* siempre desplegable.
    * No es ideal si necesitas gestionar múltiples versiones en producción o tener ciclos de lanzamiento programados y largos.
    * La gestión de hotfixes puede requerir disciplina (crear rama desde `main`, PR, merge, deploy).
* **Ideal Para:** Proyectos web, servicios que se despliegan continuamente, equipos que pueden asegurar la estabilidad de `main`.

### 2. GitFlow

* **Filosofía:** Un flujo más estructurado y robusto, diseñado originalmente por Vincent Driessen. Proporciona un marco explícito para gestionar funcionalidades, lanzamientos (releases) y mantenimiento (hotfixes), ideal para proyectos con ciclos de lanzamiento programados.
* **Ramas Principales:**
    * `main` (o `master`): Refleja el código de producción **estable y etiquetado con versiones**. Solo recibe merges desde ramas `release` y `hotfix`.
    * `develop`: Rama principal de desarrollo que contiene las últimas funcionalidades integradas y probadas, listas para el *próximo* lanzamiento. Es la rama base para nuevas funcionalidades.
    * Ramas `feature/*`: Creadas a partir de `develop`, usadas para desarrollar nuevas funcionalidades. Se fusionan de nuevo en `develop`.
    * Ramas `release/*`: Creadas a partir de `develop` cuando se decide preparar un nuevo lanzamiento. Se usan para pruebas finales, corrección de errores menores y preparación de metadatos (ej. bumping de versión). Se fusionan en `main` (para lanzar) y en `develop` (para incorporar las últimas correcciones).
    * Ramas `hotfix/*`: Creadas a partir de `main` para corregir errores críticos en producción. Se fusionan en `main` (para lanzar la corrección) y en `develop` (para asegurar que la corrección esté en el próximo lanzamiento).
* **Flujo Típico (Simplificado):**
    * Desarrollo de Funcionalidad: `develop` -> `feature/*` -> `develop` (vía PR).
    * Preparación de Release: `develop` -> `release/*` -> Pruebas/Fixes -> `main` (con tag) Y `develop`.
    * Corrección Urgente: `main` -> `hotfix/*` -> Pruebas/Fixes -> `main` (con tag) Y `develop`.
* **Pros:**
    * Muy estructurado y explícito, deja poco lugar a la ambigüedad.
    * Excelente separación entre el desarrollo en curso (`develop`), la preparación de lanzamientos (`release`) y el código de producción estable (`main`).
    * Maneja bien los lanzamientos versionados y el soporte a múltiples versiones (aunque el modelo original no lo detalla explícitamente, se puede adaptar).
* **Contras:**
    * Puede ser **complejo** de entender y gestionar debido a la cantidad de ramas y merges.
    * Puede ralentizar el ciclo de lanzamiento en comparación con GitHub Flow.
    * Considerado por algunos como "overkill" para proyectos más simples o aquellos con CI/CD agresivo.
    * El merge bidireccional de `release` y `hotfix` a `main` y `develop` puede ser propenso a errores si no se hace con cuidado.
* **Ideal Para:** Proyectos con ciclos de lanzamiento programados, necesidad de gestionar versiones explícitas, aplicaciones de escritorio, librerías versionadas, equipos que necesitan una estructura muy definida.

### 3. GitLab Flow

* **Filosofía:** Intenta ser un punto medio, más simple que GitFlow pero más estructurado que GitHub Flow, enfocándose en la integración con herramientas de CI/CD y diferentes estrategias de despliegue. Es más una familia de flujos adaptables.
* **Ramas Principales (Variantes Comunes):**
    * **Con Ramas de Entorno:**
        * `main`: Rama principal de desarrollo (similar a `develop` en GitFlow o `main` en GitHub Flow).
        * Ramas de funcionalidad (`feature/*`): Creadas desde `main`, fusionadas de vuelta a `main` vía MR.
        * Ramas de entorno: Ramas de larga duración que representan entornos desplegados (ej. `staging`, `pre-production`, `production`). Los merges a estas ramas disparan despliegues a esos entornos (ej. merge `main` -> `staging`, merge `staging` -> `production`).
    * **Con Ramas de Release:**
        * `main`: Rama principal de desarrollo.
        * Ramas de funcionalidad (`feature/*`): Creadas desde `main`, fusionadas de vuelta a `main` vía MR.
        * Ramas de release (`release-*`): Creadas desde `main` para lanzamientos específicos. El despliegue se hace desde estas ramas o desde tags creados en `main` derivados de ellas.
* **Flujo Típico (Variante con Ramas de Entorno):**
    1.  Crea rama `feature/*` desde `main`.
    2.  Desarrolla y abre un Merge Request (MR) hacia `main`.
    3.  Tras revisión/pruebas, fusiona en `main`.
    4.  Fusiona `main` en `staging` (o `pre-production`). Esto dispara despliegue a ese entorno.
    5.  Realiza pruebas en el entorno intermedio.
    6.  Si todo está bien, fusiona `staging` en `production`. Esto dispara despliegue a producción.
* **Pros:**
    * Más simple que GitFlow.
    * Flexible y adaptable a diferentes necesidades (CI/CD, releases versionados, múltiples entornos).
    * Se alinea bien con las capacidades de plataformas como GitLab (y similarmente adaptable a GitHub Actions, etc.).
* **Contras:**
    * Menos estandarizado que los otros dos; requiere que el equipo defina claramente la variante específica que se utilizará.
    * La gestión de múltiples ramas de entorno o release puede añadir cierta complejidad.
* **Ideal Para:** Equipos que necesitan más estructura que GitHub Flow pero encuentran GitFlow demasiado complejo, equipos que gestionan múltiples entornos, proyectos con CI/CD pero quizás no con despliegue inmediato a producción desde `main`.

## ¿Qué Flujo Elegir?

La elección depende de varios factores:

* **Cadencia de Lanzamiento:** ¿Despliegas varias veces al día (GitHub Flow)? ¿Tienes lanzamientos programados (GitFlow, GitLab Flow)?
* **Estrategia de Despliegue:** ¿Despliegue continuo directo desde `main`? ¿Necesitas entornos intermedios (staging)?
* **Complejidad del Proyecto/Producto:** ¿Necesitas soportar versiones antiguas en producción? ¿El proyecto es una librería que otros consumirán por versión?
* **Tamaño y Experiencia del Equipo:** Equipos más pequeños o nuevos pueden preferir la simplicidad de GitHub Flow. Equipos más grandes o con necesidades complejas pueden beneficiarse de la estructura de GitFlow o GitLab Flow.
* **Herramientas:** ¿Tu plataforma de CI/CD y hosting se alinea mejor con un flujo específico?

**Recomendación General:** Empieza simple (quizás con GitHub Flow o una variante simple de GitLab Flow) y añade complejidad solo si es necesario.

## Adaptación y Consistencia

Estos modelos son **guías**, no leyes inmutables. Muchos equipos adoptan variantes o combinaciones. Lo **crucial** es:

1.  **Definir claramente** el flujo de trabajo que seguirá el equipo.
2.  **Documentarlo** (ej. en el `README.md` o `CONTRIBUTING.md`).
3.  **Ser Consistente** en su aplicación.

## Conclusión

Elegir e implementar un flujo de trabajo de ramas adecuado es esencial para escalar las prácticas de desarrollo en equipo. Ya sea el simple GitHub Flow, el estructurado GitFlow o el flexible GitLab Flow (o una variante propia), el objetivo es lograr un proceso predecible, consistente y que facilite la entrega de software estable y de calidad. Discute las opciones con tu equipo, elige el que mejor se adapte a tu contexto y asegúrate de que todos lo entiendan y lo sigan.
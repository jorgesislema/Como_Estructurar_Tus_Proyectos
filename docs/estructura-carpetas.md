# Guía para la Estructura de Carpetas en Repositorios

La forma en que organizamos los archivos y directorios dentro de un repositorio es, en esencia, el mapa de nuestro proyecto. Una estructura de carpetas lógica y bien pensada es crucial para la navegabilidad, la mantenibilidad y la escalabilidad del código base. Actúa como una guía silenciosa para cualquier persona que interactúe con el proyecto, desde un nuevo colaborador hasta un desarrollador experimentado que regresa después de un tiempo.

Este documento explora las filosofías, patrones comunes y consideraciones clave al diseñar la estructura de directorios de tu repositorio, basándose siempre en los [Principios Fundamentales](./principios-basicos.md) que hemos establecido.

## ¿Por Qué es Crucial una Buena Estructura de Carpetas?

Invertir tiempo en definir una estructura de carpetas coherente ofrece beneficios tangibles:

* **Navegación Intuitiva:** Permite a los desarrolladores encontrar rápidamente el código o los recursos que necesitan, reduciendo la fricción y aumentando la productividad.
* **Separación de Conceptos (SoC):** Una buena estructura refleja y refuerza la arquitectura del software, agrupando lógicamente los componentes relacionados y separando responsabilidades.
* **Escalabilidad:** Facilita el crecimiento ordenado del proyecto. Añadir nuevas funcionalidades o módulos se vuelve más sencillo si existe un patrón claro a seguir.
* **Mejora del Onboarding:** Los nuevos miembros del equipo pueden comprender la organización del proyecto más rápidamente.
* **Descubrimiento:** Ayuda a entender qué hace el proyecto y cómo está organizado con solo mirar la disposición de los directorios raíz.
* **Facilita la Automatización:** Las herramientas de CI/CD, linters y scripts de construcción a menudo dependen de ubicaciones predecibles para los archivos fuente, pruebas, configuraciones, etc.

## Directorios Raíz Comunes

Si bien cada proyecto es único, ciertos directorios de nivel superior son ampliamente reconocidos y utilizados en la comunidad de desarrollo. Adoptarlos (cuando apliquen) mejora la comprensión inmediata del repositorio:

* 📁 `src/` (o `lib/` en algunos contextos como librerías): Contiene el código fuente principal de la aplicación o librería. La organización *interna* de `src/` es un tema clave que discutiremos a continuación.
* 📁 `tests/` (o `test/`, `spec/`): Contiene las pruebas automatizadas (unitarias, de integración, end-to-end). Su estructura interna a menudo refleja la de `src/`.
* 📁 `docs/`: Documentación del proyecto (guías de usuario, documentación de arquitectura, etc., más allá del `README.md` principal).
* 📁 `scripts/`: Scripts de utilidad para el proyecto (construcción, despliegue, migraciones, tareas de automatización).
* 📁 `config/` (o `cfg/`): Archivos de configuración específicos de la aplicación (a diferenciar de la configuración de herramientas).
* 📁 `data/`: Puede contener archivos de datos necesarios para el proyecto, como seeds de bases de datos, archivos de ejemplo, etc. (Evitar almacenar grandes volúmenes de datos aquí; usar soluciones externas).
* 📁 `api/`, `pkg/`, `cmd/`: Comunes en proyectos Go y a veces adoptados en otros lenguajes. `api/` para definiciones de API (protobufs, OpenAPI specs), `pkg/` para código reutilizable internamente, `cmd/` para los ejecutables principales.
* 📁 `build/`, `dist/`, `target/`, `out/`: Directorios para los artefactos generados por el proceso de construcción. Suelen incluirse en `.gitignore`.
* 📁 `.github/`, `.gitlab/`: Contienen configuraciones específicas de la plataforma de hosting de Git, como workflows de CI/CD (ej. `.github/workflows/`).
* 📄 `.gitignore`: Especifica los archivos y directorios que Git debe ignorar.
* 📄 `README.md`: El punto de entrada principal, explicando qué es el proyecto y cómo empezar.
* 📄 `LICENSE`: El archivo con la licencia de software del proyecto.
* 📄 `CONTRIBUTING.md`: Guía para colaboradores sobre cómo contribuir al proyecto.
* 📄 `CODE_OF_CONDUCT.md`: Código de conducta para la comunidad del proyecto.
* 📄 Archivos de gestión de dependencias (ej. `package.json`, `requirements.txt`, `pom.xml`, `go.mod`).

**Nota:** ¡No necesitas todos estos directorios en cada proyecto! Empieza con los esenciales y añade otros a medida que surja la necesidad.

## Estrategias para Organizar el Código Fuente (`src/` o `lib/`)

La organización dentro del directorio `src/` (o su equivalente) es donde residen las decisiones más significativas y específicas del proyecto. Aquí algunas estrategias comunes:

### 1. Agrupación por Capa Técnica (Layering)

* **Descripción:** Se organizan los archivos y subdirectorios basándose en la capa técnica a la que pertenecen dentro de la arquitectura (ej. Presentación, Aplicación, Dominio, Infraestructura en Domain-Driven Design; o Controllers, Services, Repositories en arquitecturas más tradicionales).
* **Ejemplo:**
    ```
    src/
    ├── presentation/ (o ui/, api/, controllers/)
    ├── application/ (o services/, use_cases/)
    ├── domain/ (o core/, model/)
    ├── infrastructure/ (o db/, clients/, persistence/)
    ```
* **Pros:** Refuerza la arquitectura en capas, promueve la separación de conceptos técnicos.
* **Contras:** Puede llevar a navegar entre muchas carpetas para entender o modificar una única funcionalidad completa. Puede volverse complejo si las capas no están bien definidas.

### 2. Agrupación por Funcionalidad/Módulo (Feature/Module)

* **Descripción:** Se organizan los archivos y subdirectorios basándose en la funcionalidad o dominio de negocio que implementan. Todo lo relacionado con una característica (UI, lógica, datos) se encuentra junto.
* **Ejemplo:**
    ```
    src/
    ├── users/
    │   ├── user_controller.py
    │   ├── user_service.py
    │   ├── user_repository.py
    │   └── user_model.py
    ├── orders/
    │   ├── order_controller.py
    │   ├── order_service.py
    │   ├── order_repository.py
    │   └── order_model.py
    └── shared/ (o common/)
        └── utils.py
    ```
* **Pros:** Alta cohesión (todo lo de una feature está junto), facilita el trabajo en paralelo en diferentes funcionalidades, buena escalabilidad para añadir/eliminar features.
* **Contras:** Requiere cuidado para evitar duplicación de código (usando `shared/` o `common/`), puede ser menos obvio cómo interactúan las diferentes funcionalidades a nivel técnico.

### 3. Agrupación por Tipo de Componente (Component Type)

* **Descripción:** Se organizan los archivos en directorios según el tipo de componente que representan, común en muchos frameworks MVC.
* **Ejemplo:**
    ```
    src/
    ├── controllers/
    ├── services/
    ├── models/
    ├── repositories/ (o daos/)
    ├── utils/
    ├── views/ (o templates/)
    ```
* **Pros:** Estructura familiar para quienes conocen el framework, simple para proyectos pequeños.
* **Contras:** Baja cohesión (archivos de una misma funcionalidad dispersos), puede llevar a directorios muy grandes y planos, dificulta ver los límites de una funcionalidad.

### Enfoques Híbridos y Consideraciones

A menudo, la mejor solución es un **enfoque híbrido**. Por ejemplo, se puede organizar por funcionalidad en el nivel superior de `src/`, y dentro de cada directorio de funcionalidad, quizás usar una mini-estructura por capas o por tipo.

La elección depende de:

* **La Arquitectura del Software:** DDD favorece la agrupación por Módulo/Dominio. MVC tradicional a menudo usa por Tipo.
* **El Tamaño y Complejidad:** Proyectos pequeños pueden empezar por Tipo o Capa simple. Proyectos grandes suelen beneficiarse de la agrupación por Funcionalidad.
* **El Tamaño del Equipo:** La agrupación por Funcionalidad puede facilitar la asignación de trabajo a equipos específicos.
* **Las Convenciones del Framework/Lenguaje:** Algunos frameworks imponen o sugieren fuertemente una estructura particular.

## La Regla de Oro: El Contexto Manda

**No existe una única estructura de carpetas "perfecta" para todos los proyectos.** La mejor estructura es aquella que mejor sirve a las necesidades específicas de tu proyecto, tu equipo y tu tecnología.

* Una **librería pequeña** tendrá una estructura muy diferente a una **aplicación web monolítica** o a un sistema de **microservicios** (donde cada microservicio tendría su propia estructura interna).
* Un **proyecto de Data Science** tendrá directorios para `notebooks/`, `data/`, `models/`, `reports/`, etc.

Consulta nuestros **[Ejemplos](./../ejemplos/)** y las futuras guías en **[Dominios Específicos](./../dominios/)** para ver cómo aplicar estos principios a diferentes tipos de proyectos.

## No Sobrediseñar (Start Simple)

Para proyectos pequeños o en etapas iniciales, no caigas en la trampa de crear una estructura excesivamente compleja. Empieza con una estructura simple y plana. A medida que el proyecto crezca y la complejidad aumente, **refactoriza la estructura** para mantener la claridad y la organización. Es más fácil añadir profundidad que quitarla.

## El Poder de los `README` Locales

No subestimes el valor de añadir un archivo `README.md` simple dentro de directorios cuyo propósito o contenido no sea inmediatamente obvio. Una breve descripción puede ahorrar mucho tiempo y confusión.
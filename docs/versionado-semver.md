# Versionado Semántico (SemVer): Comunicación Clara de Cambios

A medida que los proyectos de software evolucionan, dependen de otras piezas de software (librerías, frameworks, servicios) y, a su vez, pueden ser utilizados como dependencia por otros. Gestionar estas interconexiones puede volverse increíblemente complejo, un problema a menudo denominado "infierno de las dependencias" (dependency hell). ¿Cómo podemos saber si actualizar una librería romperá nuestro código? ¿Cómo comunicamos el impacto de los cambios en nuestra propia librería o API?

Aquí es donde entra en juego el **Versionado Semántico (SemVer)**. SemVer es una especificación simple pero poderosa que establece un conjunto de reglas para asignar números de versión a los lanzamientos de software, con el objetivo de transmitir significado sobre los cambios subyacentes y el impacto esperado en la compatibilidad.

Adoptar SemVer es fundamental para la madurez de un proyecto, facilitando la gestión de dependencias, la colaboración y la automatización.

## ¿Qué es el Versionado Semántico?

SemVer define un formato de versión de tres partes:

**`MAJOR.MINOR.PATCH`**

* **`MAJOR` (Mayor):** Se incrementa cuando realizas cambios **incompatibles** con la API pública anterior (breaking changes). Resetear `MINOR` y `PATCH` a 0.
* **`MINOR` (Menor):** Se incrementa cuando añades **funcionalidad** de manera **compatible** con la versión anterior. Resetear `PATCH` a 0.
* **`PATCH` (Parche):** Se incrementa cuando realizas **correcciones de errores** (bug fixes) de manera **compatible** con la versión anterior.

**Ejemplo:** `1.7.2` -> `MAJOR=1`, `MINOR=7`, `PATCH=2`.

## ¿Por Qué Utilizar SemVer?

Los beneficios de adoptar SemVer son significativos:

* **Comunicación Clara:** El número de versión transmite inmediatamente la naturaleza de los cambios desde la última versión. Los consumidores de tu software saben si pueden actualizar de forma segura (patch, minor) o si necesitan revisar y adaptar su código (major).
* **Gestión de Dependencias Predecible:** Herramientas de gestión de paquetes (como npm, pip, Maven, Cargo) pueden usar las reglas de SemVer para resolver dependencias de forma segura (ej. permitir actualizaciones de parche automáticamente `~1.7.2`, o de parche y menor `^1.7.2`).
* **Planificación de Actualizaciones:** Los equipos pueden planificar mejor cuándo y cómo actualizar las dependencias basándose en el riesgo percibido por el tipo de incremento de versión.
* **Automatización:** Facilita la automatización de procesos como la generación de `CHANGELOG`s (especialmente si se combina con [Conventional Commits](./convenciones-nombres.md#2-mensajes-de-commit)) y la gestión de lanzamientos.
* **Profesionalismo:** Indica madurez y un compromiso con la estabilidad y la comunicación clara hacia los usuarios de tu software.

## Las Reglas Clave de SemVer

La [especificación oficial de SemVer (semver.org)](https://semver.org/lang/es/) detalla todas las reglas, pero las más importantes son:

### Incremento de `MAJOR`

* **Cuándo:** Realizas un cambio que **rompe la compatibilidad** con la API pública documentada. Esto incluye eliminar funcionalidad, cambiar firmas de métodos/funciones públicas, modificar el comportamiento esperado de forma incompatible, etc.
* **Impacto:** Los usuarios **no pueden** actualizar a esta versión sin revisar y potencialmente modificar su propio código.
* **Ejemplo:** `1.7.2` -> `2.0.0`

### Incremento de `MINOR`

* **Cuándo:** Añades **nueva funcionalidad** a la API pública de forma **compatible** con versiones anteriores. También puede incluir mejoras significativas internas o marcado de funcionalidad existente como obsoleta (deprecated), siempre que no rompa la compatibilidad.
* **Impacto:** Los usuarios **deberían poder** actualizar a esta versión sin romper su código existente que utiliza la API pública.
* **Ejemplo:** `1.7.2` -> `1.8.0`

### Incremento de `PATCH`

* **Cuándo:** Realizas **correcciones de errores** internas o en la API pública que son **compatibles** con versiones anteriores. Solo deben ser correcciones, sin añadir nueva funcionalidad.
* **Impacto:** Los usuarios **deberían poder** actualizar a esta versión de forma segura para obtener las correcciones.
* **Ejemplo:** `1.7.2` -> `1.7.3`

## Versiones de Pre-lanzamiento (Pre-releases)

SemVer permite añadir identificadores adicionales para indicar versiones de pre-lanzamiento, útiles para pruebas y feedback antes de un lanzamiento estable. Se añaden después del número de parche, separados por un guion.

* **Formato:** `MAJOR.MINOR.PATCH-identificador.numero` (ej. `1.0.0-alpha.1`, `2.1.0-beta.3`, `3.0.0-rc.1`).
* **Precedencia:** Las versiones de pre-lanzamiento tienen *menor* precedencia que la versión estable normal asociada (ej. `1.0.0-rc.1` < `1.0.0`). La precedencia entre pre-lanzamientos se determina alfabética y numéricamente (ej. `alpha.1` < `alpha.2` < `beta.1`).
* **Uso:** Para lanzamientos alfa (pruebas internas/limitadas), beta (pruebas más amplias), release candidates (RC - candidatos a versión final).

## Metadatos de Construcción (Build Metadata)

Opcionalmente, se pueden añadir metadatos de construcción después de la versión normal o de pre-lanzamiento, separados por un signo más (`+`).

* **Formato:** `MAJOR.MINOR.PATCH[-pre-release]+metadata` (ej. `1.0.0+build.123`, `2.1.5-alpha+git.sha.c0ffee`).
* **Precedencia:** Los metadatos de construcción **se ignoran** al determinar la precedencia de versiones. Dos versiones que solo difieren en los metadatos se consideran iguales en términos de versionado (ej. `1.0.0+build.1` tiene la misma precedencia que `1.0.0+build.2`).
* **Uso:** Para incluir información adicional como el hash del commit de Git, la fecha de compilación, etc., útil para trazabilidad pero no para la lógica de dependencias.

## Desarrollo Inicial (Versiones 0.y.z)

Las versiones `0.y.z` tienen un significado especial: indican que el **proyecto está en desarrollo inicial y la API pública se considera inestable**.

* **Regla:** Durante la fase `0.y.z`, **cualquier cosa puede cambiar en cualquier momento**. La API pública no debe considerarse estable.
* **Incrementos:** En esta fase, `MINOR` (el `y` en `0.y.z`) se incrementa para cambios que *podrían* ser incompatibles (equivalente a `MAJOR` en >1.0.0), y `PATCH` (el `z`) se incrementa para nuevas funcionalidades y correcciones compatibles (equivalente a `MINOR` y `PATCH`).
* **Lanzamiento `1.0.0`:** Marca el primer lanzamiento con una **API pública estable**. A partir de aquí, se aplican las reglas normales de SemVer.

## SemVer en la Práctica con Git/GitHub

SemVer se integra perfectamente con el flujo de trabajo de Git y plataformas como GitHub:

### Tags de Git

La forma estándar de marcar un punto de lanzamiento en Git es mediante **tags anotados**. Se recomienda usar el número de versión SemVer como nombre del tag, prefijado comúnmente con `v`.

* **Crear un tag:**
    ```bash
    # Ejemplo para lanzar la versión 1.2.3
    git tag -a v1.2.3 -m "Release version 1.2.3: Fix critical login bug"
    ```
* **Empujar tags al remoto:**
    ```bash
    git push origin v1.2.3
    # O para empujar todos los tags locales que no estén en el remoto:
    git push origin --tags
    ```

### GitHub Releases

GitHub utiliza los tags de Git para crear **Releases**. Una Release es una forma enriquecida de presentar un tag, permitiendo:

* Añadir notas de lanzamiento detalladas (Markdown).
* Adjuntar artefactos binarios (ejecutables, paquetes compilados).
* Marcar una release como pre-lanzamiento si el tag sigue el formato SemVer de pre-release.

Crear releases basadas en tags SemVer es la forma estándar de distribuir software y comunicar cambios en GitHub.

## Mejores Prácticas y Automatización

* **Comprométete:** Una vez que adoptas SemVer a partir de `1.0.0`, respeta las reglas. No introduzcas breaking changes en versiones minor o patch.
* **Documenta los Cambios:** Mantén un archivo `CHANGELOG.md` (o usa las GitHub Releases) para documentar los cambios importantes en cada versión, especialmente los breaking changes.
* **Usa Conventional Commits:** Facilitan enormemente determinar el siguiente número de versión SemVer y generar automáticamente `CHANGELOG`s.
* **Automatiza:** Existen herramientas (como `standard-version`, `semantic-release`) que pueden analizar tu historial de commits (si usas Conventional Commits), determinar el siguiente número de versión, crear el tag, generar el changelog y crear la GitHub Release, todo automáticamente.

## Conclusión

El Versionado Semántico es mucho más que una simple forma de numerar versiones; es un **contrato de comunicación** con los usuarios de tu software. Al adoptar SemVer, proporcionas claridad, fomentas la confianza y habilitas un ecosistema de dependencias más robusto y predecible. Es una práctica esencial para cualquier proyecto de software que aspire a ser utilizado o mantenido más allá de su desarrollador inicial.

Para una referencia completa, consulta siempre la [especificación oficial de SemVer v2.0.0](https://semver.org/lang/es/).
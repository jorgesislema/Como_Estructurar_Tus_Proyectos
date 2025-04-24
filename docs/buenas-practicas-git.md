# Buenas Prácticas Esenciales para Trabajar con Git

Git es el sistema de control de versiones estándar de facto en el desarrollo de software moderno. Su flexibilidad y potencia son innegables, pero también pueden llevar al caos si no se utiliza con disciplina, especialmente en entornos colaborativos. Seguir un conjunto de buenas prácticas no solo mejora la eficiencia del equipo, sino que también contribuye a un historial de proyecto limpio, comprensible y mantenible, lo cual es invaluable para la depuración, la revisión y la comprensión de la evolución del código.

Esta guía resume las prácticas esenciales que recomendamos para maximizar los beneficios de Git y minimizar sus posibles dificultades.

## Prácticas Clave

### 1. Commits Atómicos y Mensajes Claros

* **Atomicidad:** Cada commit debe representar una **única unidad lógica de cambio**. Evita commits gigantes que mezclan correcciones de errores, nuevas funcionalidades y refactorizaciones. Un commit atómico es más fácil de entender, revisar y revertir si es necesario.
* **Mensajes Descriptivos:** Escribe mensajes de commit claros y significativos. Recomendamos encarecidamente seguir las [Convenciones de Nombres para Mensajes de Commit](./convenciones-nombres.md#2-mensajes-de-commit) (como Conventional Commits). Un buen mensaje explica *qué* cambió y *por qué*, no solo *cómo*.
    ```bash
    # Malo:
    git commit -m "cambios"

    # Bueno (siguiendo Conventional Commits):
    git commit -m "fix(auth): correct password validation logic for special chars" -m "The previous regex did not handle passwords containing '#' or '&'. Updated validation to allow these characters as per requirements."
    ```

### 2. Estrategia de Ramificación (Branching) Clara

* **Usa Ramas de Funcionalidad (Feature Branches):** Nunca trabajes directamente en `main` (o `master`). Para cada nueva funcionalidad, corrección de error o tarea significativa, crea una nueva rama descriptiva (ej. `feature/add-user-profile`, `fix/login-bug-123`). Consulta nuestras [Convenciones de Nombres para Ramas](./convenciones-nombres.md#1-ramas-branches).
* **Ramas de Corta Duración:** Intenta mantener las ramas lo más cortas posible en términos de tiempo y alcance. Integra los cambios de nuevo en `main` frecuentemente para evitar divergencias masivas y merges dolorosos.
* **`main` Estable:** La rama `main` debe reflejar siempre un estado estable, probado y potencialmente desplegable del proyecto.

### 3. Pull Requests (PRs) / Merge Requests (MRs) para Revisión

* **Siempre Revisa Antes de Fusionar:** Utiliza Pull Requests (en GitHub/Bitbucket) o Merge Requests (en GitLab) como mecanismo principal para integrar cambios en `main`. Esto permite:
    * **Revisión de Código:** Otros miembros del equipo pueden revisar los cambios, sugerir mejoras y detectar errores antes de que lleguen a la rama principal.
    * **Discusión:** Proporciona un foro para discutir la implementación.
    * **Integración Continua:** Dispara automáticamente pruebas y otros chequeos de CI.
* **PRs Enfocados:** Al igual que los commits, los PRs deben ser lo más pequeños y enfocados posible para facilitar la revisión. Un PR que cambia miles de líneas en docenas de archivos es muy difícil de revisar eficazmente.

### 4. Mantén `main` Siempre Desplegable

* **Protege `main`:** Configura reglas de protección de ramas en tu plataforma (GitHub, GitLab, etc.) para evitar pushes directos a `main` y requerir que los PRs pasen ciertas comprobaciones (como pruebas de CI y aprobación de revisores) antes de poder fusionarse.
* **Fusiona Solo Código Probado:** Asegúrate de que el código en una rama de funcionalidad esté bien probado (manual y automáticamente) antes de crear un PR para fusionarlo en `main`.

### 5. Sincroniza Frecuentemente tu Rama (`pull`/`rebase`)

* **Evita Divergencias Grandes:** Para minimizar conflictos de merge complejos, actualiza tu rama de funcionalidad frecuentemente con los últimos cambios de `main`.
* **`rebase` vs. `merge` para Actualizar:**
    * `git pull origin main` (o `git fetch origin` seguido de `git merge origin/main`): Trae los cambios de `main` y crea un commit de merge en tu rama de funcionalidad. Esto preserva el historial exacto pero puede "ensuciar" el historial de la rama con muchos merges.
    * `git pull --rebase origin main` (o `git fetch origin` seguido de `git rebase origin/main`): Trae los cambios de `main` y vuelve a aplicar tus commits *encima* de los cambios más recientes de `main`. Esto crea un historial lineal más limpio para tu rama, facilitando la revisión del PR. **Es generalmente la opción preferida para actualizar ramas de funcionalidad *antes* de compartirlas.**
    ```bash
    # Estando en tu rama 'feature/my-work'
    git fetch origin
    git rebase origin/main
    # Resuelve conflictos si los hay y continúa el rebase
    # git rebase --continue
    ```

### 6. Limpia tu Historial Local *Antes* de Compartir (`rebase -i`)

* **Rebase Interactivo:** Antes de crear un Pull Request (o empujar tu rama por primera vez), considera limpiar tu historial *local* usando `git rebase -i` (rebase interactivo). Esto te permite:
    * `squash`: Combinar varios commits pequeños en uno lógico.
    * `fixup`: Similar a squash, pero descarta el mensaje del commit que se está aplicando.
    * `reword`: Cambiar mensajes de commit.
    * `edit`: Modificar un commit específico.
    * `reorder`: Cambiar el orden de los commits.
* **¡PRECAUCIÓN!:** **NUNCA uses `git rebase` (interactivo o no) en ramas que ya has empujado y que otros pueden estar utilizando.** Modificar el historial compartido crea problemas graves para tus colaboradores. Usa `rebase -i` solo en tu historial local *antes* de empujar o antes de que otros basen su trabajo en tu rama.

### 7. Configura un `.gitignore` Robusto

* **Mantén el Repositorio Limpio:** El archivo `.gitignore` le dice a Git qué archivos o directorios debe ignorar. Es crucial para evitar que artefactos de construcción, archivos de dependencias (`node_modules/`, `venv/`), archivos de configuración de IDE, secretos y otros archivos no deseados terminen en el repositorio.
* **Usa Plantillas:** Empieza con plantillas de `.gitignore` generadas para tu sistema operativo, IDE y lenguajes/frameworks específicos. El sitio [gitignore.io](https://gitignore.io) es un excelente recurso.
* **Sé Específico:** Añade patrones específicos de tu proyecto según sea necesario.

### 8. ¡Nunca Commitees Secretos!

* **Riesgo de Seguridad Grave:** Nunca, bajo ninguna circunstancia, guardes información sensible como contraseñas, claves de API, tokens de acceso, certificados privados, etc., directamente en el repositorio, ¡ni siquiera en ramas privadas!
* **Alternativas Seguras:**
    * **Variables de Entorno:** Carga secretos desde variables de entorno en tu servidor o sistema de CI/CD.
    * **Gestores de Secretos:** Utiliza servicios dedicados (AWS Secrets Manager, Azure Key Vault, HashiCorp Vault).
    * **Archivos `.env`:** Guarda secretos en un archivo `.env` local y asegúrate de que `.env` esté incluido en tu `.gitignore`. Proporciona un archivo de ejemplo (`.env.example` o similar) en el repositorio con claves pero sin valores.

### 9. Comprende `merge` vs. `rebase` (para Integrar)

* **`git merge`:** Cuando fusionas una rama de funcionalidad en `main` (usualmente a través de un PR), `git merge` (con la opción `--no-ff` a menudo por defecto en plataformas como GitHub/GitLab para PRs) crea un *commit de merge*. Esto preserva el historial de la rama de funcionalidad y muestra claramente dónde se integró. Mantiene el historial exacto de lo que sucedió.
* **`git rebase` (para integrar):** Alternativamente, podrías hacer rebase de tu rama de funcionalidad sobre `main` y luego hacer un merge *fast-forward* en `main`. Esto crea un historial lineal en `main`, como si todos los commits se hubieran hecho directamente allí. Puede ser más limpio visualmente, pero oculta el contexto de la rama de funcionalidad y puede ser más difícil de deshacer.
* **Recomendación Común:** Usar `rebase` para mantener actualizada tu rama de funcionalidad localmente (ver punto 5) y usar `merge` (con commit de merge) al integrar PRs en `main` para preservar el contexto de la revisión y la integración.

### 10. Abraza la Resolución de Conflictos

* **Es Normal:** Los conflictos de merge ocurren, especialmente en equipos activos. No les temas.
* **Comunica:** Si tienes un conflicto complejo, habla con el compañero que modificó la misma parte del código.
* **Comprende:** Usa las herramientas de Git (`git status`, las marcas de conflicto `<<<<<<<`, `=======`, `>>>>>>>` en los archivos) y tu IDE para entender qué cambios están en conflicto.
* **Prueba:** Después de resolver un conflicto, **siempre** prueba el código cuidadosamente para asegurarte de que la resolución es correcta y no has introducido nuevos errores.

### 11. Usa Tags para los Lanzamientos

* **Marca Puntos Significativos:** Utiliza tags anotados de Git para marcar versiones de lanzamiento específicas, siguiendo idealmente el [Versionado Semántico (SemVer)](./versionado-semver.md).
    ```bash
    git tag -a v1.0.0 -m "Initial stable release"
    git push origin v1.0.0
    ```

## Conclusión

Dominar Git es un viaje continuo. Adoptar estas buenas prácticas no es una restricción, sino una inversión en la calidad, estabilidad y eficiencia de tu proceso de desarrollo. Fomentan una mejor colaboración, crean un historial valioso y, en última instancia, ayudan a construir mejor software. ¡Sigue aprendiendo, experimentando (en ramas seguras) y compartiendo tus conocimientos con tu equipo!
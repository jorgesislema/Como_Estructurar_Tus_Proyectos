### plantillas/ISSUE_TEMPLATE/config.yml (Configuración del Selector de Issues)
Este archivo (opcional pero recomendado) te permite desactivar issues en blanco y dirigir a los usuarios a recursos externos o a las plantillas específicas

``` yaml
# .github/ISSUE_TEMPLATE/config.yml
# Configura el selector de "Nuevo Issue" en GitHub

# Desactiva la opción de abrir un issue completamente en blanco.
# Fuerza a los usuarios a elegir una plantilla (¡recomendado!).
blank_issues_enabled: false

# Añade enlaces a recursos externos que los usuarios podrían necesitar
# antes de abrir un issue.
contact_links:
  - name: ❓ Preguntas Generales / Discusión de la Comunidad
    url: [URL_A_TU_FORO_DISCORD_SLACK_O_GITHUB_DISCUSSIONS] # Reemplaza con tu URL
    about: Por favor, haz preguntas generales o inicia discusiones aquí.
  - name: 📚 Documentación del Proyecto
    url: [URL_A_TU_DOCUMENTACION] # Reemplaza con tu URL
    about: Revisa la documentación completa aquí. ¡Quizás tu pregunta ya esté respondida!
  - name: 🔒 Reportar una Vulnerabilidad de Seguridad
    url: [URL_REPO]/blob/main/SECURITY.md # Enlace a tu SECURITY.md
    about: Por favor, revisa nuestra política de seguridad para reportar vulnerabilidades de forma privada.
```

### 2. plantillas/ISSUE_TEMPLATE/bug_report.md (Plantilla para Reportar Bugs)

Este archivo define la plantilla que los usuarios verán cuando elijan reportar un bug. Usa "front matter" YAML al inicio para configurar etiquetas, título por defecto, etc.

``` markdown
---
# .github/ISSUE_TEMPLATE/bug_report.md
name: 🐞 Reporte de Bug
description: Crea un reporte detallado para ayudarnos a encontrar y solucionar un error.
title: "[Bug]: Breve descripción del bug"
labels: ["bug", "triage"] # Etiquetas a aplicar automáticamente
assignees: '' # Puedes asignar a alguien por defecto si quieres, ej: 'tu_usuario'
---

**Describe el Bug**
Una descripción clara y concisa de cuál es el bug.

**Pasos para Reproducir el Bug**
Pasos para reproducir el comportamiento:
1. Ve a '...'
2. Haz clic en '....'
3. Desplázate hacia abajo hasta '....'
4. Observa el error '....'

**Comportamiento Esperado**
Una descripción clara y concisa de lo que esperabas que sucediera.

**Comportamiento Actual**
Una descripción clara y concisa de lo que sucede en realidad.

**Capturas de Pantalla (Opcional)**
Si aplica, añade capturas de pantalla para ayudar a explicar tu problema.
**Entorno (Por favor, completa esta información):**
 - OS: [ej: macOS Sonoma, Windows 11, Ubuntu 22.04]
 - Navegador (si aplica): [ej: Chrome 110, Firefox 109]
 - Versión de [Nombre del Proyecto]: [ej: 1.2.3, o commit SHA si es desde `main`]
 - Versión de [Dependencia Clave, ej: Node.js, Python]: [ej: Node v18.17.0]

**Contexto Adicional (Opcional)**
Añade cualquier otro contexto sobre el problema aquí. ¿Empezó a suceder después de una actualización específica? ¿Solo ocurre bajo ciertas condiciones? ¿Has probado alguna solución alternativa?

**Logs (Opcional)**
Si tienes logs relevantes de la consola del navegador o del servidor, pégalos aquí dentro de bloques de código.
```log
(Pega tus logs aquí)

```


**3. `plantillas/ISSUE_TEMPLATE/feature_request.md` (Plantilla para Sugerir Mejoras)**

Este archivo define la plantilla para solicitar nuevas características o mejoras.

```markdown
---
# .github/ISSUE_TEMPLATE/feature_request.md
name: ✨ Solicitud de Nueva Característica / Mejora
description: Sugiere una idea o mejora para este proyecto.
title: "[Feature]: Breve descripción de la característica"
labels: ["enhancement", "needs-discussion"] # Etiquetas a aplicar
assignees: ''
---

**¿Tu solicitud de característica está relacionada con un problema existente? Por favor, descríbelo.**
Una descripción clara y concisa de cuál es el problema o la necesidad. Ej: "Actualmente me frustra que [...] porque [...]"

**Describe la Solución que te Gustaría**
Una descripción clara y concisa de lo que quieres que suceda. ¿Cómo resolvería el problema mencionado anteriormente?

**Describe Alternativas que has Considerado (Opcional)**
Una descripción clara y concisa de cualquier solución o característica alternativa que hayas considerado. ¿Por qué prefieres la solución propuesta?

**Contexto Adicional (Opcional)**
Añade cualquier otro contexto, maquetas (mockups), o capturas de pantalla sobre la solicitud de característica aquí. ¿Hay ejemplos en otros proyectos?

**¿Por qué es importante esta característica? (Opcional)**
Ayúdanos a entender el valor o el impacto de esta propuesta. ¿A cuántos usuarios afectaría? ¿Desbloquearía nuevos casos de uso?
```
### Cómo Implementar:

    Crea la Carpeta: En la raíz de tu repositorio, crea la carpeta .github/ (si no existe) y dentro de ella, crea la carpeta ISSUE_TEMPLATE/.
    Crea los Archivos: Dentro de .github/ISSUE_TEMPLATE/, crea los archivos:
        config.yml
        bug_report.md
        feature_request.md
        (Puedes añadir más plantillas .md si lo necesitas)
    Copia el Contenido: Copia el contenido de las plantillas que te he proporcionado en los archivos correspondientes.
    Personaliza:
        config.yml: Reemplaza los [URL_...] con tus enlaces reales. Decide si quieres blank_issues_enabled: false.
        Plantillas .md: Ajusta los labels, assignees, y el texto/preguntas para que se adapten mejor a tu proyecto y flujo de trabajo. Puedes añadir más secciones si es necesario.
    Commitea y Empuja: Añade la carpeta .github/ a Git, commitea y empuja los cambios.

## Ahora, cuando alguien vaya a crear un nuevo issue en tu repositorio de GitHub, se le presentará un selector con las plantillas que has definido (y los enlaces del config.yml), en lugar de una caja de texto en blanco. ¡Esto mejora enormemente la calidad de los issues que recibes!

# Política de Seguridad de [Nombre del Proyecto]

## Introducción

La seguridad de [Nombre del Proyecto] es una prioridad para nosotros. Agradecemos enormemente los esfuerzos de la comunidad de seguridad y de los desarrolladores para identificar y reportar vulnerabilidades de manera responsable.

Este documento describe nuestra política y procedimiento para reportar posibles vulnerabilidades de seguridad encontradas en el proyecto. Por favor, léelo detenidamente antes de reportar.

**NO reportes vulnerabilidades de seguridad a través de los issues públicos de GitHub/GitLab.**

## Versiones Soportadas

Nos comprometemos a investigar y solucionar vulnerabilidades en las siguientes versiones de [Nombre del Proyecto]:

| Versión | Soportada          |
| ------- | ------------------ |
| `[2.x]` | ✅                 |
| `[1.5.x]` | ✅ (Solo Críticas) |
| `< 1.5`  | ❌                 |

## 🚨 Reportando una Vulnerabilidad

Si crees que has encontrado una vulnerabilidad de seguridad en [Nombre del Proyecto], te pedimos que nos lo comuniques de forma **privada** para darnos la oportunidad de solucionarlo antes de que sea divulgado públicamente.

**Por favor, NO crees un Issue público en GitHub/GitLab para reportar vulnerabilidades de seguridad.**

**Método Preferido para Reportar:**

* **Opción 1 (Recomendada si está disponible):** Usa la función de **Reporte Privado de Vulnerabilidades** de GitHub. Ve a la pestaña "Security" del repositorio y haz clic en "Report a vulnerability". Sigue las instrucciones allí.
    * ➡️ [Enlace Directo a la Pestaña de Seguridad de tu Repo: [URL_REPO]/security/advisories/new]

* **Opción 2:** Envía un correo electrónico detallado a nuestra dirección de seguridad dedicada:
    * 📧 **[EMAIL_DE_SEGURIDAD, ej: security@tuproyecto.com]**
    * Por favor, usa un asunto claro como "Reporte de Vulnerabilidad de Seguridad: [Nombre del Proyecto]".
    * *(Opcional: Añade una clave PGP si quieres fomentar la comunicación cifrada)* Puedes cifrar tu correo usando nuestra clave PGP: `[ENLACE_A_CLAVE_PGP o FINGERPRINT]`

* **Opción 3 (Para proyectos más pequeños/personales):** Contacta directamente a los maintainers principales por email:
    * Maintainer 1: `[EMAIL_MAINTAINER_1]`
    * Maintainer 2: `[EMAIL_MAINTAINER_2]`

**¿Qué Información Incluir?**

Para ayudarnos a investigar y solucionar el problema lo más rápido posible, por favor incluye la siguiente información (si es aplicable) en tu reporte:

* **Descripción:** Una descripción clara y concisa de la vulnerabilidad.
* **Versión(es) Afectada(s):** Qué versiones del proyecto están afectadas.
* **Pasos para Reproducir:** Instrucciones detalladas paso a paso para reproducir la vulnerabilidad. Incluye cualquier configuración o condición necesaria.
* **Impacto:** Cuál crees que es el impacto de la vulnerabilidad (ej: ejecución remota de código, divulgación de información, denegación de servicio).
* **Prueba de Concepto (PoC):** Código, scripts o instrucciones que demuestren la vulnerabilidad.
* **(Opcional) Mitigación Sugerida:** Si tienes ideas sobre cómo podría solucionarse.
* **(Opcional) Referencias:** Enlaces a documentación relevante, CVEs relacionados, etc.

## Nuestro Proceso de Respuesta

Una vez que recibamos tu reporte de vulnerabilidad, haremos lo siguiente:

1.  **Acuse de Recibo:** Te enviaremos una confirmación de recepción (normalmente dentro de [ej: 2 días hábiles]) para informarte que hemos recibido tu reporte.
2.  **Investigación:** Investigaremos la vulnerabilidad reportada para confirmar su validez y determinar su impacto y severidad. Te mantendremos informado sobre nuestro progreso si es posible.
3.  **Solución:** Si la vulnerabilidad es confirmada, trabajaremos en una solución. La complejidad determinará el tiempo necesario, pero intentaremos actuar con prontitud, especialmente para vulnerabilidades críticas.
4.  **Comunicación:** Mantendremos una comunicación razonable contigo durante el proceso. Te informaremos cuando la solución esté lista.
5.  **Divulgación:** Una vez que la solución esté disponible (ej: en una nueva versión o parche), coordinaremos contigo (si lo deseas) la divulgación pública de la vulnerabilidad. Normalmente crearemos un aviso de seguridad (Security Advisory) en GitHub/GitLab y podríamos solicitar un CVE si aplica.

Nuestro objetivo es manejar los reportes de seguridad de manera profesional, respetuosa y oportuna.

## Alcance (Fuera de Alcance - Opcional)

Los siguientes tipos de issues generalmente se consideran fuera del alcance de nuestra política de reporte de vulnerabilidades de seguridad (a menos que demuestren un impacto de seguridad explotable):

* Reportes de scanners automáticos sin una prueba de concepto demostrable.
* Ataques de Denegación de Servicio (DoS) teóricos o que requieran una capacidad de ataque extremadamente alta.
* Ingeniería social de los maintainers o miembros de la comunidad.
* Vulnerabilidades en dependencias de terceros (por favor, repórtalas a los maintainers de esas dependencias, aunque te agradecemos si nos avisas si afectan directamente a nuestro proyecto).
* Errores de configuración del servidor o de la infraestructura donde se despliega el proyecto (a menos que el proyecto facilite directamente esa mala configuración).
* Issues relacionados con SPF, DKIM, DMARC.
* Ausencia de "mejores prácticas" de seguridad que no lleven a una vulnerabilidad explotable demostrada.

## Agradecimientos (Opcional)

Agradecemos a todos los investigadores de seguridad que colaboran con nosotros para mantener [Nombre del Proyecto] seguro. Si lo deseas, estaremos encantados de reconocer tu contribución en nuestros avisos de seguridad o en una sección de agradecimientos una vez que la vulnerabilidad haya sido solucionada y divulgada. Por favor, indícanos si prefieres permanecer anónimo.

---

Gracias por ayudarnos a mantener la seguridad de [Nombre del Proyecto].

### Instrucciones Cruciales:

    Copia y Renombra: Copia este contenido a un archivo llamado SECURITY.md en la raíz de tu proyecto o en la carpeta .github/.
    Personaliza Contacto: ¡FUNDAMENTAL! Reemplaza los placeholders en la sección "Reportando una Vulnerabilidad" ([EMAIL_DE_SEGURIDAD], [ENLACE_A_GITHUB_SECURITY_ADVISORIES], [EMAIL_MAINTAINER_1], etc.) con los métodos de contacto reales y privados que tu equipo utilizará para recibir estos reportes. Elige la opción que mejor funcione para tu proyecto.
    Adapta Versiones Soportadas: Modifica la tabla para reflejar las versiones de tu software que realmente reciben parches de seguridad.
    Revisa Alcance y Agradecimientos: Ajusta o elimina las secciones opcionales ("Alcance", "Agradecimientos") según las políticas de tu proyecto.
    Considera GitHub Security Advisories: Si tu repositorio está en GitHub, familiarízate con su sistema de reporte privado y avisos de seguridad y considera usarlo como método principal.

Tener un SECURITY.md claro inspira confianza y facilita la gestión responsable de la seguridad.
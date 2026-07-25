# Tipos de Licencias de Software: Cual Elegir y Por Que

Elegir una licencia para tu proyecto es una decision legal que afecta quien puede usar, modificar y distribuir tu codigo. Esta guia cubre las licencias mas comunes, cuando usar cada una, y sus implicaciones practicas. No es asesoria legal, es orientacion tecnica.

---

## Conceptos Clave Antes de Elegir

### Permisos comunes en licencias

| Permiso | Significado |
|---------|-------------|
| Uso comercial | Se puede usar el software con fines de lucro |
| Distribucion | Se puede compartir el software con otros |
| Modificacion | Se puede cambiar el codigo fuente |
| Uso de patente | Proteccion explicita contra litigios de patentes |
| Uso privado | Se puede usar sin publicar cambios |

### Obligaciones comunes en licencias

| Obligacion | Significado |
|------------|-------------|
| Incluir copyright | Debes mantener el aviso de copyright original |
| Incluir licencia | Debes incluir una copia de la licencia con el software |
| Estado de cambios | Debes documentar los cambios realizados |
| Divulgar fuente | Debes publicar el codigo fuente de tus modificaciones |
| Misma licencia | Las obras derivadas deben usar la misma licencia (copyleft) |
| Sin responsabilidad | El autor no se hace responsable por danos |

---

## Licencias Permisivas (Pocas Restricciones)

Estas licencias permiten casi cualquier uso, incluyendo software propietario, con minima obligacion.

### MIT (Recomendada para la mayoria de proyectos)

**Tipo:** Permisiva

**Permite:** Todo (uso comercial, modificacion, distribucion, uso privado, uso de patente).

**Obliga a:** Incluir el aviso de copyright y el texto de la licencia en todas las copias.

**Ideal para:**
- Proyectos open source que quieres que tengan maxima adopcion
- Librerias y paquetes (npm, PyPI, crates.io)
- Proyectos personales que quieres compartir sin restricciones
- Startups que quieren que su codigo sea usado ampliamente

**Ejemplos de proyectos que usan MIT:** React, Angular, .NET Core, Rails, jQuery, Node.js, Express.js, Next.js, Laravel.

**La licencia MIT completa:**

```
MIT License

Copyright (c) [AÑO] [TITULAR DEL COPYRIGHT]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### Apache 2.0

**Tipo:** Permisiva con concesion de patentes

**Permite:** Todo lo de MIT, mas concesion explicita de derechos de patente.

**Obliga a:** Incluir copyright, licencia, aviso de cambios en archivos modificados, y texto de NOTICE si existe.

**Ideal para:**
- Proyectos empresariales donde las patentes son una preocupacion real
- Proyectos grandes con multiples contribuidores corporativos
- Cuando necesitas proteccion explicita contra litigios de patentes

**Diferencia clave con MIT:** La clausula de patentes. Si contribuyes codigo a un proyecto Apache 2.0, estas otorgando una licencia de patente a los usuarios. Si alguien inicia un litigio de patentes contra el proyecto, pierde su licencia de patente.

**Ejemplos:** Kubernetes, Android, Swift, Rust (lenguaje), Apache HTTP Server, TensorFlow, Supabase.

### BSD (Berkeley Software Distribution)

**Tipo:** Permisiva, similar a MIT

**Variantes:**
- **BSD 2-Clause:** Como MIT. Dos condiciones: copyright + disclaimer.
- **BSD 3-Clause:** Anade prohibicion de usar nombres de contribuidores para promocion.
- **BSD 4-Clause:** Incluye clausula de publicidad (obsoleta, no recomendada).

**Ejemplos:** Go (lenguaje), FreeBSD, Nginx, Django, NumPy.

### ISC

**Tipo:** Equivalente funcional a MIT pero con texto mas corto y simple.

**Ideal para:** Cuando quieres la licencia mas corta posible que sea legalmente equivalente a MIT.

**Ejemplos:** OpenBSD, node-semver, ini.

### Unlicense

**Tipo:** Dominio publico. Renuncia a todos los derechos.

**Permite:** Absolutamente todo, sin condiciones.

**Ideal para:** Codigo que quieres dedicar al dominio publico sin ninguna restriccion. Cuestionable en jurisdicciones que no permiten renunciar a derechos morales.

**No recomendada para:** La mayoria de proyectos. MIT o Apache 2.0 son opciones mas seguras y reconocidas.

---

## Licencias Copyleft (Obligan a Compartir)

Estas licencias requieren que las obras derivadas se distribuyan bajo la misma licencia.

### GPL (General Public License) v3

**Tipo:** Copyleft fuerte

**Permite:** Uso comercial, modificacion, distribucion, uso de patente.

**Obliga a:** Divulgar el codigo fuente de cualquier obra derivada bajo la misma GPL. Si usas codigo GPL en tu proyecto, tu proyecto DEBE ser GPL.

**Ideal para:**
- Proyectos que quieren garantizar que todas las mejoras permanezcan libres
- Cuando quieres prevenir que empresas tomen tu codigo y lo hagan propietario
- Proyectos con fuerte etica de software libre (FSF, GNU)

**No usar si:** Quieres que tu codigo sea usado en software propietario. Las empresas suelen evitar codigo GPL en productos comerciales.

**Ejemplos:** Linux kernel, Git, Bash, GIMP, Audacity, Ansible.

### AGPL (Affero GPL) v3

**Tipo:** Copyleft de red (GPL + clausula de red)

**Diferencia con GPL:** La GPL se activa al "distribuir" el software. La AGPL se activa al "usar" el software en un servidor de red. Si modificas codigo AGPL y lo usas en tu SaaS, debes publicar tus cambios.

**Ideal para:** SaaS y servicios web que quieren garantizar que las modificaciones al codigo sean publicas aunque el software nunca se "distribuya" (solo se acceda via web).

**Ejemplos:** MongoDB (hasta 2018, luego cambio a SSPL), Grafana, MinIO, Mastodon.

### LGPL (Lesser GPL) v3

**Tipo:** Copyleft debil

**Diferencia con GPL:** Permite que software propietario "enlace" (link) con librerias LGPL sin tener que liberar su codigo. Solo las modificaciones a la libreria LGPL misma deben ser publicas.

**Ideal para:** Librerias que quieren proteccion copyleft pero permitiendo uso en software propietario.

**Ejemplos:** Qt (framework), GTK+, FFmpeg, 7-Zip.

### MPL (Mozilla Public License) 2.0

**Tipo:** Copyleft a nivel de archivo

**Diferencia con GPL:** El copyleft aplica solo a los archivos originales con MPL, no a archivos nuevos anadidos. Permite combinar codigo MPL con codigo propietario en el mismo proyecto.

**Ideal para:** Proyectos que quieren proteccion intermedia entre MIT y GPL.

**Ejemplos:** Firefox, Thunderbird, Rust (compilador), Servo.

---

## Licencias de Codigo Abierto pero Restrictivas

### SSPL (Server Side Public License)

**Tipo:** Copyleft extremo, similar a AGPL pero mas fuerte.

**Obliga a:** Liberar no solo el codigo del software, sino TODO el software necesario para operarlo como servicio (gestion de usuarios, monitoreo, backup, etc.).

**Controversia:** No es considerada open source por la OSI. MongoDB la adopto para prevenir que proveedores cloud ofrezcan MongoDB como servicio sin contribuir.

**Ejemplos:** MongoDB, Elasticsearch (desde 2021).

### BUSL (Business Source License)

**Tipo:** Temporalmente restrictiva. El codigo se vuelve open source (usualmente MIT o Apache) despues de un periodo (tipicamente 2-4 anos).

**Permite:** Uso en produccion, pero NO ofrecer el software como servicio competidor durante el periodo restrictivo.

**Ideal para:** Startups que quieren proteger su ventaja competitiva temporal mientras eventualmente liberan el codigo.

**Ejemplos:** CockroachDB, Sentry, Materialize, TimescaleDB.

---

## Licencias para Documentacion y Contenido

### Creative Commons (CC)

Para documentacion, guias, libros y contenido no-codigo.

| Licencia | Uso comercial | Modificaciones | Misma licencia |
|----------|--------------|----------------|----------------|
| CC0 | Si | Si | No (dominio publico) |
| CC BY | Si | Si | No |
| CC BY-SA | Si | Si | Si (copyleft) |
| CC BY-NC | No | Si | No |
| CC BY-ND | Si | No | No |
| CC BY-NC-SA | No | Si | Si |

**Recomendaciones para documentacion:**
- Documentacion de proyecto open source: CC BY o CC BY-SA
- Documentacion que quieres maxima difusion: CC0 o dominio publico
- Guias y tutoriales: CC BY-SA (como Wikipedia)

---

## Arbol de Decision: Que Licencia Elegir

```
Pregunta 1: Quieres que tu codigo sea open source?
  ├── No -> Licencia propietaria (no necesitas elegir de esta guia)
  └── Si -> Sigue a Pregunta 2

Pregunta 2: Te preocupa que empresas usen tu codigo en productos propietarios?
  ├── No me importa -> MIT o Apache 2.0
  └── Si, quiero evitarlo -> Sigue a Pregunta 3

Pregunta 3: Es una libreria o un servicio/SaaS?
  ├── Libreria (se enlaza con otro software)
  │   └── LGPL o MPL 2.0
  └── Servicio/SaaS (se accede via web)
      └── GPL v3 (si se distribuye) o AGPL v3 (si se usa como servicio)

Pregunta adicional: Te preocupan las patentes?
  ├── Si -> Apache 2.0 (tiene clausula explicita de patentes)
  └── No -> MIT (mas simple y corta)
```

---

## Recomendacion por Tipo de Proyecto

| Tipo de proyecto | Licencia recomendada | Razon |
|-----------------|---------------------|-------|
| Libreria o paquete (npm, PyPI) | MIT | Maxima adopcion, minima friccion |
| Proyecto personal open source | MIT | Simple, universalmente reconocida |
| Proyecto empresarial open source | Apache 2.0 | Proteccion de patentes |
| Framework o herramienta | MIT o Apache 2.0 | Maximo alcance |
| SaaS / Aplicacion web | MIT o AGPL (si quieres copyleft) | Segun filosofia del proyecto |
| Documentacion y guias | CC BY-SA 4.0 | Permite compartir con atribucion |
| Startup que protege ventaja temporal | BUSL | Se vuelve MIT en 2-4 anos |
| Proyecto que quiere evitar abuso cloud | AGPL o SSPL | Previene SaaS no contributivo |
| "No me importa, solo quiero compartir" | MIT | La respuesta por defecto |

---

## Como Aplicar una Licencia a tu Proyecto

1. Crea un archivo `LICENSE` (o `LICENSE.txt`, `LICENSE.md`) en la raiz del proyecto.
2. Copia el texto completo de la licencia elegida (NO solo el nombre).
3. Reemplaza `[AÑO]` y `[TITULAR DEL COPYRIGHT]` con tus datos.
4. Menciona la licencia en tu `README.md`.

Ejemplo en README.md:
```markdown
## Licencia

Este proyecto esta licenciado bajo los terminos de la licencia MIT.
Ver el archivo [LICENSE](LICENSE) para mas detalles.
```

Alternativamente, en GitHub puedes usar la opcion "Add License" al crear el repositorio.

---

## Lo Que NO Cubre una Licencia Open Source

- **Marca registrada:** el nombre y logo de tu proyecto no estan protegidos por la licencia de codigo. Si quieres proteger tu marca, registrala aparte.
- **Garantia:** TODAS las licencias open source incluyen un disclaimer "AS IS" (tal cual). No ofrecen garantia. Si alguien usa tu software y pierde datos, no eres responsable.
- **Soporte:** la licencia no obliga a dar soporte tecnico. Puedes elegir dar soporte (pago o gratuito) aparte.
- **Contribuciones:** considera agregar un DCO (Developer Certificate of Origin) o CLA (Contributor License Agreement) si tu proyecto es grande.
- **Datos de usuarios:** una licencia de codigo no cubre privacidad de datos. Para eso necesitas terminos de servicio y politica de privacidad.

---

## Referencias Cruzadas

- [Plantilla LICENSE](../plantillas/LICENSE.md) -- guia y textos completos de licencias
- [Glosario](./glosario.md) -- definiciones de terminos legales y tecnicos
- [Plantilla README](../plantillas/README.md) -- donde incluir la mencion de licencia
- [Seguridad de Codigo](./seguridad-codigo.md) -- consideraciones de seguridad en el codigo

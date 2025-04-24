# Estructura de Repositorios para Herramientas de Línea de Comandos (CLI)

¡Saludos, artesanos de la terminal! Las herramientas de línea de comandos (CLI) son los caballos de batalla silenciosos del mundo digital. Desde simples scripts hasta aplicaciones increíblemente complejas (`git`, `docker`, `kubectl`, `aws-cli`), las CLIs nos permiten automatizar tareas, gestionar sistemas e interactuar con servicios de forma potente y eficiente.

Crear una *buena* CLI es más que simplemente escribir un script. Requiere pensar en la **experiencia del usuario (UX) en la terminal**: ¿son claros los comandos y argumentos? ¿Es útil la salida? ¿Se integra bien con otros scripts? Y, por supuesto, ¿es fácil de instalar y usar?

Una estructura de repositorio bien pensada es la base para construir una CLI robusta, mantenible y agradable de usar. Piensa en ella como el diseño de una navaja suiza fiable: cada herramienta (comando/función) debe ser fácil de encontrar, usar y debe cumplir su propósito sin fallar. 🔪

## Consideraciones Clave para las CLIs 🤔

Antes de empezar a crear carpetas, reflexionemos sobre los aspectos cruciales de una CLI:

* **Lenguaje y Runtime:** ¿Go, Rust, Python, Node.js, Bash, otro? Tu elección impacta drásticamente en el rendimiento (especialmente el tiempo de arranque), la gestión de dependencias, las opciones de build y, sobre todo, cómo la distribuirás.
* **Análisis de Argumentos (Parsing):** ¿Cómo manejarás los comandos, subcomandos, flags (`-f`, `--force`) y argumentos posicionales? Usar una librería robusta (`cobra` en Go, `clap` en Rust, `click`/`typer` en Python, `yargs`/`commander` en Node.js) es casi obligatorio para cualquier cosa mínimamente compleja.
* **Configuración:** ¿Cómo permitirás a los usuarios personalizar el comportamiento? Las capas típicas son: valores por defecto < archivos de configuración (¡respeta [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html)!) < variables de entorno < flags/argumentos CLI.
* **Salida (Output):** ¡Crítico para la usabilidad!
    * **Humano vs. Máquina:** ¿La salida es principalmente para lectura humana o para ser procesada por otros scripts (`grep`, `jq`)? Ofrecer formatos como JSON (`--output json`) es genial para scripting.
    * **Streams:** Usa `stdout` para los resultados "buenos" (lo que el usuario quiere capturar o redirigir) y `stderr` para todo lo demás (mensajes de error, logs, indicadores de progreso). ¡Esto es vital para que los pipes (`|`) funcionen!
    * **Estilo y Color:** El color puede mejorar la legibilidad, pero siempre ofrece una forma de desactivarlo (variable `NO_COLOR`, flag `--no-color`).
    * **Verbosidad:** Niveles como `-q` (quiet), normal, `-v` (verbose), `-vv` (debug).
* **Códigos de Salida (Exit Codes):** ¡Esenciales para scripting! Sigue la convención: `0` para éxito, un número diferente de cero para error. Si puedes, usa códigos distintos para diferentes tipos de error.
* **Testing:** Necesitas probar diferentes combinaciones de argumentos, manejo de errores, la salida exacta (stdout/stderr) y los códigos de salida.
* **Distribución:** ¿Cómo instalarán los usuarios tu CLI? ¿Binarios precompilados? ¿`pip install`? ¿`npm install -g`? ¿Paquetes del sistema (`.deb`, `.rpm`)? ¿Homebrew/Scoop? ¿Docker?
* **Rendimiento (Tiempo de Arranque):** Para CLIs que se ejecutan frecuentemente, un arranque lento (común en lenguajes interpretados como Python o Node.js sin optimizar) puede ser muy molesto. Go y Rust brillan aquí.

## Estructura de Directorio Recomendada (Genérica y Adaptable) 📂✅

Esta estructura intenta ser agnóstica del lenguaje en su núcleo, pero deberás adaptarla a las convenciones de tu ecosistema.

``` bash
¡Claro! El archivo cli-herramientas.md suena a que queremos una guía para estructurar proyectos que son herramientas de línea de comandos (CLI - Command Line Interface), un pilar fundamental del desarrollo y la administración de sistemas.

Aunque puede haber cierto solapamiento con la guía devtools-python.md (ya que muchas DevTools son CLIs), esta guía será más genérica, aplicable a CLIs escritas en cualquier lenguaje (Go, Rust, Python, Node.js, incluso Bash elaborado) y para cualquier propósito (gestión de sistemas, interacción con APIs, procesamiento de datos, etc.).

Asumiendo que pertenece a las guías por dominio, procederé a generar el contenido para:

docs/por-dominio/cli-herramientas.md

Si prefieres otra ubicación, ¡dímelo!

Aquí tienes el contenido, con el enfoque en las CLIs:
Markdown

# Estructura de Repositorios para Herramientas de Línea de Comandos (CLI)

¡Saludos, artesanos de la terminal! Las herramientas de línea de comandos (CLI) son los caballos de batalla silenciosos del mundo digital. Desde simples scripts hasta aplicaciones increíblemente complejas (`git`, `docker`, `kubectl`, `aws-cli`), las CLIs nos permiten automatizar tareas, gestionar sistemas e interactuar con servicios de forma potente y eficiente.

Crear una *buena* CLI es más que simplemente escribir un script. Requiere pensar en la **experiencia del usuario (UX) en la terminal**: ¿son claros los comandos y argumentos? ¿Es útil la salida? ¿Se integra bien con otros scripts? Y, por supuesto, ¿es fácil de instalar y usar?

Una estructura de repositorio bien pensada es la base para construir una CLI robusta, mantenible y agradable de usar. Piensa en ella como el diseño de una navaja suiza fiable: cada herramienta (comando/función) debe ser fácil de encontrar, usar y debe cumplir su propósito sin fallar. 🔪

## Consideraciones Clave para las CLIs 🤔

Antes de empezar a crear carpetas, reflexionemos sobre los aspectos cruciales de una CLI:

* **Lenguaje y Runtime:** ¿Go, Rust, Python, Node.js, Bash, otro? Tu elección impacta drásticamente en el rendimiento (especialmente el tiempo de arranque), la gestión de dependencias, las opciones de build y, sobre todo, cómo la distribuirás.
* **Análisis de Argumentos (Parsing):** ¿Cómo manejarás los comandos, subcomandos, flags (`-f`, `--force`) y argumentos posicionales? Usar una librería robusta (`cobra` en Go, `clap` en Rust, `click`/`typer` en Python, `yargs`/`commander` en Node.js) es casi obligatorio para cualquier cosa mínimamente compleja.
* **Configuración:** ¿Cómo permitirás a los usuarios personalizar el comportamiento? Las capas típicas son: valores por defecto < archivos de configuración (¡respeta [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html)!) < variables de entorno < flags/argumentos CLI.
* **Salida (Output):** ¡Crítico para la usabilidad!
    * **Humano vs. Máquina:** ¿La salida es principalmente para lectura humana o para ser procesada por otros scripts (`grep`, `jq`)? Ofrecer formatos como JSON (`--output json`) es genial para scripting.
    * **Streams:** Usa `stdout` para los resultados "buenos" (lo que el usuario quiere capturar o redirigir) y `stderr` para todo lo demás (mensajes de error, logs, indicadores de progreso). ¡Esto es vital para que los pipes (`|`) funcionen!
    * **Estilo y Color:** El color puede mejorar la legibilidad, pero siempre ofrece una forma de desactivarlo (variable `NO_COLOR`, flag `--no-color`).
    * **Verbosidad:** Niveles como `-q` (quiet), normal, `-v` (verbose), `-vv` (debug).
* **Códigos de Salida (Exit Codes):** ¡Esenciales para scripting! Sigue la convención: `0` para éxito, un número diferente de cero para error. Si puedes, usa códigos distintos para diferentes tipos de error.
* **Testing:** Necesitas probar diferentes combinaciones de argumentos, manejo de errores, la salida exacta (stdout/stderr) y los códigos de salida.
* **Distribución:** ¿Cómo instalarán los usuarios tu CLI? ¿Binarios precompilados? ¿`pip install`? ¿`npm install -g`? ¿Paquetes del sistema (`.deb`, `.rpm`)? ¿Homebrew/Scoop? ¿Docker?
* **Rendimiento (Tiempo de Arranque):** Para CLIs que se ejecutan frecuentemente, un arranque lento (común en lenguajes interpretados como Python o Node.js sin optimizar) puede ser muy molesto. Go y Rust brillan aquí.

## Estructura de Directorio Recomendada (Genérica y Adaptable) 📂✅

Esta estructura intenta ser agnóstica del lenguaje en su núcleo, pero deberás adaptarla a las convenciones de tu ecosistema.

mi-cli/
├── .github/                    # CI/CD (GitHub Actions workflows, etc.)
│   └── workflows/
│       └── release.yml         # Build binaries, create release, publish packages
│       └── test.yml            # Run tests on multiple OS/versions
├── cmd/                        # (Convención Go/Rust) Puntos de entrada principales
│   └── mi-cli/
│       └── main.go             # O main.rs
├── internal/                   # (Convención Go) Código privado de la aplicación
│   └── ...
├── pkg/                        # (Convención Go/Rust) Código público/reutilizable si aplica
│   └── ...
├── src/                        # (Convención Python/Node.js/Otros) Código fuente
│   ├── main.py             # (Python) Si es ejecutable como módulo
│   ├── cli.py                  # (Python) Lógica CLI principal
│   ├── commands/               # Lógica para subcomandos (ej: user_add.py)
│   ├── core/                   # Lógica de negocio/principal agnóstica de CLI
│   └── ...
├── lib/                        # (Alternativa a src/ o pkg/)
│   └── ...
├── bin/                        # (Alternativa a cmd/) Scripts o binarios compilados
│   └── mi-cli                  # Script principal (Bash) o binario compilado
├── config/                     # Archivos de configuración por defecto
│   └── default.yaml
├── docs/                       # Documentación (Guía de usuario, man pages)
│   └── usage.md
├── examples/                   # Ejemplos de uso, configs de ejemplo
│   └── example.sh
├── scripts/                    # Scripts auxiliares (build, release, lint)
│   └── build.sh
├── test/                       # (o tests/) Pruebas
│   ├── fixtures/               # Datos/archivos de prueba
│   ├── unit/                   # Pruebas unitarias
│   ├── integration/            # Pruebas de integración
│   └── e2e/                    # Pruebas End-to-End (ejecutando el CLI compilado)
│       └── test_basic_command.sh
├── Makefile                    # (Opcional, pero útil) Tareas comunes (build, test, lint)
├── .gitignore
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
├── README.md                   # ¡Instalación, uso básico, config, contribución!
└── ...                         # Archivos específicos del lenguaje/ecosistema
# (go.mod, Cargo.toml, pyproject.toml, package.json)

```


**Puntos Clave:**

* **Organización del Código Fuente:** Adapta (`cmd`/`internal`/`pkg` vs `src`/`lib`) a las convenciones de tu lenguaje.
* **Separación de Lógica:** Intenta separar la lógica *del CLI* (parsing de args, formato de salida) de la lógica *del negocio* (`core`). Facilita testing y reutilización.
* **Subcomandos:** Si tu CLI es compleja (`mi-cli user add`, `mi-cli system status`), organiza el código por comando (ej: carpeta `commands/` o `cmd/`).
* **Testing E2E:** Es crucial tener tests que ejecuten el CLI compilado/final y verifiquen su comportamiento real (salida, errores, exit code).
* **Automatización de Release:** La CI debe encargarse de construir los binarios/paquetes para diferentes plataformas y adjuntarlos a un Release de GitHub (o publicar en registros).

## Adaptaciones por Lenguaje (Mini-Guía) 🌐

* **Go:** Usa la estructura `cmd/`, `internal/`, `pkg/`. `go build` crea binarios estáticos fácilmente. Cross-compilación trivial con variables de entorno (`GOOS`, `GOARCH`). Excelente para CLIs rápidas y fáciles de distribuir. Librerías: `cobra`, `urfave/cli`.
* **Rust:** Estructura alrededor de `Cargo.toml`. `src/main.rs` para el binario principal, `src/lib.rs` si también es librería, `src/bin/` para múltiples binarios. `cargo build --release` para binarios optimizados. Cross-compilación requiere instalar toolchains. Muy rápido y seguro. Librerías: `clap`, `structopt`.
* **Python:** Usa `src/` layout y `pyproject.toml`. Librerías: `click` (muy popular), `typer` (moderno, con type hints), `argparse` (built-in). Define el script en `[project.scripts]`. Distribución vía PyPI (`pip install`) es fácil, pero requiere Python instalado. Para distribución autocontenida: `pipx`, `shiv`, `pex`, `pyinstaller` (aumentan tamaño).
* **Node.js:** `package.json` es central. Código en `src/` o `lib/`. Define el ejecutable en el campo `bin`. Librerías: `yargs`, `commander`, `oclif`. Distribución vía `npm`/`yarn` (`npm install -g`). Requiere Node.js. Puedes crear ejecutables autocontenidos con `pkg` o `nexe`.

## Argument Parsing y UX: La Cara Amable de tu CLI 😊

* **Usa una Buena Librería:** ¡No reinventes la rueda parseando `sys.argv`! Usa las librerías estándar/populares de tu lenguaje. Te darán validación, generación de `--help`, manejo de subcomandos, etc.
* **Ayuda Útil:** El mensaje `--help` debe ser claro, conciso y completo. Las librerías suelen generarlo, pero puedes personalizarlo.
* **Subcomandos para Complejidad:** Si tienes muchas operaciones, agrúpalas con subcomandos (ej: `docker image ls`, `docker container run`).
* **Convenciones POSIX:** Usa flags cortos (`-v`) y largos (`--verbose`). Sigue las convenciones de tu ecosistema.
* **Defaults Sensatos:** Configura valores por defecto razonables para que el usuario no tenga que especificar todo cada vez.
* **Errores Claros:** Si el usuario se equivoca (flag inválido, argumento faltante), da un mensaje de error claro y útil, idealmente sugiriendo la opción correcta o mostrando parte de la ayuda.

## Distribución y Packaging: ¡Que Llegue a los Usuarios! 🚀

Esta es una de las partes más críticas y variadas:

* **Lenguajes Compilados (Go/Rust):**
    * **GitHub Releases:** Proporciona binarios pre-compilados para Windows (x64, arm64), macOS (x64, arm64/Apple Silicon) y Linux (x64, arm64) como artefactos de tus releases.
    * **Paquetes de Sistema:** Considera crear `.deb`, `.rpm`, paquetes para Homebrew (macOS/Linux), Scoop/Chocolatey (Windows). Herramientas como `goreleaser` (Go) o `cargo-deb`/`cargo-rpm` (Rust) ayudan enormemente.
* **Lenguajes Interpretados (Python/Node.js):**
    * **Registros de Paquetes:** Publica en PyPI (`pip`) o npm registry (`npm`/`yarn`). Es la forma más común.
    * **Instaladores Dedicados:** Usa `pipx` (Python) para instalar CLIs en entornos aislados. `npm install -g` para Node.
    * **Ejecutables Autocontenidos:** Considera `pyinstaller`, `shiv`, `pex` (Python) o `pkg`, `nexe` (Node.js) para crear un "binario" que incluya el intérprete y las dependencias. Ventaja: el usuario no necesita instalar Python/Node. Desventaja: tamaño considerable.
* **Contenedores Docker:** Proporciona una imagen Docker con tu CLI preinstalada. Útil para entornos de CI/CD o usuarios que prefieren Docker.

## Consejos de Oro 💡 (Mandamientos del Buen CLI)

* **Sé Consistente con el Ecosistema:** Sigue las convenciones de instalación, configuración y comportamiento esperadas en el lenguaje/plataforma que elegiste.
* **Errores y Exit Codes Significativos:** ¡Vital para scripting! Usa `stderr` para mensajes no deseados en pipelines. Devuelve `0` solo en caso de éxito total. Documenta tus exit codes si son específicos. *Un CLI que no maneja bien los errores es como un GPS que solo dice "recalculando..." cuando te pierdes.* 🧭❌
* **Configuración Flexible y Estándar:** Defaults < Archivo (`~/.config/mi-cli/config.yaml` o similar) < Env Vars (`MI_CLI_API_KEY`) < Flags (`--api-key X`). Usa librerías que faciliten esto.
* **Testea el Binario Final (E2E):** Tus tests unitarios pueden pasar, pero ¿funciona el CLI real compilado/empaquetado? Ten tests que lo invoquen desde un shell y comprueben stdout, stderr y exit code.
* **Documenta los Comandos y Flags:** Genera man pages, o ten una sección clara en tu `README` o web con todos los comandos, subcomandos, flags y ejemplos. ¡La ayuda (`--help`) es tu amiga!
* **Piensa en la "Pipeabilidad" (Piping):** Si tu CLI genera datos, ¿pueden usarse fácilmente con `| grep`, `| jq`, `| awk`? Ofrece formatos de salida estructurados (JSON, TSV) y usa `stdout` correctamente.

## ¡Cuidado! Errores Comunes y Cómo Evitarlos ⚠️

* **❌ Parseo Manual de `sys.argv` (El Camino al Dolor):** Para CLIs no triviales, es frágil, propenso a errores y no escala. No obtienes `--help` gratis.
    * **Solución:** Adopta una librería de parsing de argumentos estándar para tu lenguaje desde el principio.
* **❌ Diarrea en `stdout`:** Mezclar resultados, logs, errores, todo en la salida estándar. Rompe el uso con pipes (`|`).
    * **Solución:** Disciplina: resultados (lo que el usuario quiere capturar) a `stdout`; todo lo demás (logs, errores, prompts, progreso) a `stderr`.
* **❌ Exit Codes Vag
os (Siempre 0 o Siempre 1):** Hace imposible saber si un script que usa tu CLI tuvo éxito o falló, o por qué falló.
    * **Solución:** Devuelve `0` solo en éxito. Usa diferentes códigos > 0 para diferentes clases de error si ayuda al scripting. Documenta los códigos principales.
* **❌ Configuración Rígida o No Estándar:** Forzar toda la configuración por flags, ignorar env vars, poner archivos de config en lugares raros.
    * **Solución:** Implementa capas de configuración. Respeta los directorios base XDG. Usa librerías de configuración si es complejo.
* **❌ Distribución "Hazlo Tú Mismo":** Instrucciones de instalación que parecen un conjuro arcano. Depender de que el usuario compile desde fuente sin ayuda.
    * **Solución:** ¡Facilita la vida al usuario! Binarios, paquetes (`pip`/`npm`), Homebrew/Scoop, Docker. Automatiza la creación de estos artefactos en tu CI.
* **❌ Arranque Glacial:** CLIs (especialmente en Python/Node) que tardan varios segundos en iniciarse para tareas rápidas. Frustrante.
    * **Solución:** Optimiza importaciones. Considera técnicas de lazy loading. Para rendimiento crítico, Go o Rust son opciones excelentes. Herramientas de bundling pueden ayudar (con sobrepeso).

## Conclusión: El Poder en Tus Dedos (y los de Tus Usuarios)

Una CLI bien hecha es una herramienta poderosa y elegante. Combina buena ingeniería (código modular, tests robustos) con un diseño centrado en el usuario de la terminal (argumentos claros, salida útil, errores informativos, instalación sencilla).

Al estructurar tu repositorio con estos principios en mente, adaptándolos a tu lenguaje y herramientas, estarás sentando las bases para una CLI que la gente no solo use, sino que realmente aprecie. ¡Ahora ve y empodera esas terminales! 💪
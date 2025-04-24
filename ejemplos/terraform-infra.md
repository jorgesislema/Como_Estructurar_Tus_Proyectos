# Ejemplo: Infraestructura como Código (IaC) con Terraform

¡Bienvenido/a a la plantilla de ejemplo para gestionar tu infraestructura usando Terraform! La Infraestructura como Código (IaC) te permite definir y aprovisionar tu infraestructura (servidores, bases de datos, redes, etc.) mediante código, lo que aporta **automatización, consistencia, control de versiones y colaboración** al proceso.

Este ejemplo se centra en estructurar un proyecto Terraform para manejar **múltiples entornos** (ej: desarrollo, staging, producción) de forma segura y escalable, utilizando **módulos reutilizables** y **estado remoto**.

## Objetivo 🎯

* Mostrar una **estructura organizada y escalable** para proyectos Terraform multi-entorno.
* Fomentar el uso de **módulos** para la reutilización y abstracción del código.
* Destacar la importancia del **estado remoto y el bloqueo de estado** para el trabajo en equipo.
* Proveer un **punto de partida práctico** para gestionar tu infraestructura real.

## Tecnologías y Conceptos Clave 🏗️

* **Terraform:** La herramienta principal para definir y aplicar la IaC.
* **Módulos Terraform:** Bloques de código reutilizables que encapsulan un conjunto de recursos relacionados (ej: una VPC, una instancia EC2).
* **Entornos Separados:** Estructura de directorios para aislar la configuración y el estado de cada entorno (dev, staging, prod).
* **Estado Remoto (Remote State):** Almacenar el archivo de estado de Terraform (`terraform.tfstate`) en un backend remoto y compartido (como AWS S3 con bloqueo en DynamoDB, Azure Storage, Google Cloud Storage, Terraform Cloud/Enterprise) en lugar de localmente. ¡Esencial para la colaboración y la seguridad!
* **Bloqueo de Estado (State Locking):** Mecanismo (usualmente provisto por el backend remoto) que previene que múltiples usuarios apliquen cambios concurrentemente al mismo estado, evitando corrupción.
* **Variables y Salidas:** Parameterizar la infraestructura y exponer información útil.
* **(Opcional) Herramientas de Calidad/Seguridad:** `terraform fmt` (incluido), [TFLint](https://github.com/terraform-linters/tflint) (linter), [tfsec](https://github.com/aquasecurity/tfsec) o [Checkov](https://github.com/bridgecrewio/checkov) (escáneres de seguridad).

## Estructura del Directorio 🗺️

* **`environments/`**: Contiene la configuración específica para cada entorno de despliegue.
    * **`<environment_name>/`** (ej: `dev`, `staging`, `prod`): Cada subdirectorio representa un entorno aislado. Contiene su propia configuración de `backend.tf` (apuntando a un *estado remoto diferente*), `providers.tf`, y un `main.tf` que orquesta la creación de infraestructura *llamando a los módulos reutilizables*. Los valores específicos del entorno se definen en `terraform.tfvars` (¡cuidado con los secretos!).
    * **`_shared/`**: (Opcional) Para definir variables o configuraciones comunes a todos los entornos, aunque es preferible pasar todo a través de variables a los módulos.
* **`modules/`**: Contiene módulos Terraform locales reutilizables. Cada módulo es una unidad autocontenida que define un conjunto lógico de recursos (ej: `network`, `compute`, `database`).
    * **`<module_name>/`**: Cada subdirectorio es un módulo con sus propios `main.tf`, `variables.tf`, y `outputs.tf`. Un buen módulo tiene un `README.md` explicando cómo usarlo.
* **Archivos Raíz:** Configuraciones globales de herramientas (linters, gitignore), este `README.md`.

## Archivos y Conceptos Clave Detallados ✨

* **`environments/<env>/backend.tf`:** ¡Fundamental! Configura dónde se almacenará el estado de *este entorno específico*. **Cada entorno DEBE tener su propio archivo de estado remoto aislado** para evitar que cambios en `dev` afecten a `prod`. El backend elegido (ej: S3) debe soportar **bloqueo de estado** (ej: usando DynamoDB con S3).
* **`modules/<module>/`:** La clave de la reutilización. Permiten abstraer la complejidad. Puedes usar módulos locales (dentro de esta carpeta) o módulos externos (del [Registro de Terraform](https://registry.terraform.io/), GitHub, etc.). Se llaman desde los `main.tf` de los entornos.
* **`environments/<env>/main.tf`:** No define recursos directamente (o muy pocos). Su función principal es **llamar a los módulos** definidos en `modules/` o externos, pasándoles las variables necesarias para ese entorno. Compone la infraestructura a partir de bloques reutilizables.
* **`variables.tf` / `terraform.tfvars`:** Definen las entradas para cada entorno o módulo. `terraform.tfvars` es la forma estándar de pasar valores, pero **NUNCA commitees secretos** (API keys, contraseñas) en él. Usa variables de entorno (`TF_VAR_nombre_variable`), un archivo `.tfvars` separado y gitignoreado, o herramientas de gestión de secretos (HashiCorp Vault, AWS Secrets Manager, etc.).
* **`providers.tf`:** Configura los proveedores de nube (AWS, Azure, GCP...) indicando región, versión, y potentially credenciales (aunque es mejor manejar credenciales fuera del código, ej: variables de entorno, perfiles IAM/CLI).

## Cómo Usar esta Plantilla 🚀

1.  **Prerrequisitos:**
    * [Terraform CLI](https://developer.hashicorp.com/terraform/downloads) instalado.
    * Cuenta en un proveedor de nube (ej: AWS, Azure, GCP).
    * Credenciales del proveedor configuradas de forma segura para que Terraform las use (ej: variables de entorno `AWS_ACCESS_KEY_ID`/`AWS_SECRET_ACCESS_KEY`, perfil `aws configure`, etc.).
2.  **Clonar:** `git clone <URL_DEL_REPO_UNIVERSAL> && cd ejemplos/terraform-infra`
3.  **Configurar el Backend Remoto:** **¡Paso Manual Inicial!** Antes de ejecutar `terraform init` por primera vez en un entorno, necesitas crear manualmente los recursos para el backend remoto (ej: el bucket S3 y la tabla DynamoDB especificados en `environments/<env>/backend.tf`). Asegúrate de que los nombres sean únicos por entorno.
4.  **Navegar al Entorno:** Elige un entorno para trabajar.
    ```bash
    cd environments/dev
    ```
5.  **Inicializar Terraform:** Descarga los providers y configura el backend remoto.
    ```bash
    terraform init
    ```
6.  **(Opcional) Planificar Cambios:** Revisa qué acciones ejecutará Terraform. Pasa variables mediante un archivo `.tfvars` (si no contiene secretos) o variables de entorno.
    ```bash
    # Opción 1: Usando un archivo .tfvars (¡asegúrate que está en .gitignore si tiene secretos!)
    terraform plan -var-file="terraform.tfvars"

    # Opción 2: Usando variables de entorno (para secretos)
    # export TF_VAR_db_password="mi_secreto"
    # terraform plan
    ```
7.  **Aplicar Cambios:** Crea o actualiza la infraestructura. Se te pedirá confirmación.
    ```bash
    # Opción 1: Con .tfvars
    terraform apply -var-file="terraform.tfvars"

    # Opción 2: Con env vars
    # terraform apply
    ```
8.  **Destruir Infraestructura:** Elimina los recursos gestionados por Terraform en este entorno. ¡Ten cuidado!
    ```bash
    # Opción 1: Con .tfvars
    terraform destroy -var-file="terraform.tfvars"

    # Opción 2: Con env vars
    # terraform destroy
    ```

## Flujo de Trabajo Típico 🔄

1.  Crea una nueva rama en Git (`git checkout -b feature/nueva-infra`).
2.  Realiza cambios en los módulos (`modules/`) o en la configuración de un entorno (`environments/<env>/`).
3.  Navega al directorio del entorno afectado (`cd environments/<env>`).
4.  Ejecuta `terraform fmt -recursive ../../` para formatear.
5.  Ejecuta `terraform validate` para chequear sintaxis.
6.  Ejecuta `terraform plan` para revisar los cambios.
7.  (En un entorno de CI/CD) El plan se revisa y aprueba mediante un Pull Request.
8.  (En un entorno de CI/CD o manualmente) Ejecuta `terraform apply` para aplicar los cambios al entorno correspondiente.

## Alternativas (Mención Breve) 🤔

* **Terraform Workspaces:** Permiten gestionar múltiples estados dentro de un mismo directorio de configuración y backend. Útil para variaciones menores, pero la separación por directorios suele ser más clara para entornos fundamentalmente distintos.
* **Terragrunt:** Una herramienta wrapper sobre Terraform que ayuda a mantener el código DRY (Don't Repeat Yourself) al gestionar múltiples módulos y entornos, reduciendo la duplicación en archivos `backend.tf` y `providers.tf`. Añade otra capa de abstracción.

## Siguientes Pasos y Adaptación 👣

* **Adapta los Módulos:** Crea o modifica los módulos en `modules/` según tu infraestructura real.
* **Configura tu Backend:** Cambia la configuración en `backend.tf` para usar tu bucket S3, Terraform Cloud, Azure Storage, etc.
* **Gestiona Secretos:** Implementa una estrategia segura para las variables sensibles (Vault, AWS Secrets Manager, Azure Key Vault, GCP Secret Manager, variables de entorno en CI/CD). **¡No commitees secretos en `.tfvars`!**
* **Integra CI/CD:** Configura GitHub Actions, GitLab CI, Jenkins, etc., para automatizar `plan` y `apply` en tus entornos.
* **Añade Pruebas y Validación:** Integra `tflint`, `tfsec`/`checkov` en tus pre-commit hooks o CI para mejorar la calidad y seguridad. Considera herramientas de testing como Terratest.

## Contribuciones a este Ejemplo ✨

Las mejoras a *esta plantilla específica de Terraform* son bienvenidas. Sigue la guía general (`comunidad/como-contribuir.md`) indicando que tu aporte es para `ejemplos/terraform-infra`.

¡Esperamos que esta estructura te sirva como una base sólida y escalable para gestionar tu infraestructura como código con Terraform! ☁️⚙️


```bash 

ejemplos/terraform-infra/
├── .gitignore                  # Ignorar .terraform/, *.tfstate, *.tfstate.*, crash.log, *.tfvars (si contienen secretos)
├── .tflint.hcl                 # (Opcional) Configuración para TFLint
├── .terraform-docs.yml         # (Opcional) Configuración para terraform-docs
├── README.md                   # Este archivo: explicación del ejemplo
├── environments/               # Directorio raíz para las configuraciones por entorno
│   ├── _shared/                # (Opcional) Archivos comunes o variables compartidas
│   │   └── shared_vars.tf
│   ├── dev/                    # Configuración específica para el entorno DEV
│   │   ├── backend.tf          # Configuración del backend de estado remoto para DEV
│   │   ├── main.tf             # Orquestación principal: Llama a los módulos para DEV
│   │   ├── outputs.tf          # Salidas del entorno DEV
│   │   ├── providers.tf        # Configuración de providers (ej: región AWS) para DEV
│   │   ├── terraform.tfvars    # **¡IGNORAR EN GIT SI CONTIENE SECRETOS!** Valores para DEV
│   │   └── variables.tf        # Variables de entrada para el entorno DEV
│   ├── staging/                # Configuración específica para el entorno STAGING
│   │   ├── backend.tf          # Backend de estado remoto para STAGING (¡DIFERENTE!)
│   │   ├── main.tf
│   │   ├── outputs.tf
│   │   ├── providers.tf
│   │   ├── terraform.tfvars    # **¡IGNORAR EN GIT SI CONTIENE SECRETOS!**
│   │   └── variables.tf
│   └── prod/                   # Configuración específica para el entorno PROD
│       ├── backend.tf          # Backend de estado remoto para PROD (¡DIFERENTE!)
│       ├── main.tf
│       ├── outputs.tf
│       ├── providers.tf
│       ├── terraform.tfvars    # **¡IGNORAR EN GIT SI CONTIENE SECRETOS!**
│       └── variables.tf
└── modules/                    # Módulos Terraform reutilizables locales
    ├── network/                # Ejemplo: Módulo para crear una VPC, subnets, etc.
    │   ├── main.tf             # Recursos del módulo (aws_vpc, aws_subnet...)
    │   ├── outputs.tf          # Salidas del módulo (vpc_id, subnet_ids...)
    │   ├── README.md           # Documentación del módulo
    │   └── variables.tf        # Variables de entrada del módulo (cidr_block, etc.)
    ├── compute/                # Ejemplo: Módulo para crear instancias EC2, ASG, etc.
    │   ├── main.tf
    │   ├── outputs.tf
    │   ├── README.md
    │   └── variables.tf
    └── database/               # Ejemplo: Módulo para crear una base de datos RDS
        ├── main.tf
        ├── outputs.tf
        ├── README.md
        └── variables.tf
```
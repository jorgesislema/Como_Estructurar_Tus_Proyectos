# Estudio de Caso: Estructura de Repositorio Inspirada en Google Dataflow

**Google Dataflow** es una plataforma de procesamiento de datos en flujo y por lotes basada en Apache Beam. Aunque el repositorio oficial de Dataflow en sí no siempre está disponible públicamente, existen proyectos *open-source* y ejemplos publicados por Google y la comunidad que siguen sus principios. A continuación, se presenta un esquema basado en buenas prácticas observadas en proyectos similares.

```bash
dataflow-proyecto/
├── .github/                    # Workflows de CI/CD
│   └── workflows/
├── .gitignore                  # Archivos a ignorar por Git
├── README.md                   # Descripción general del proyecto
├── LICENSE                     # Licencia del proyecto
├── setup.py                    # Instalación como paquete Python
├── requirements.txt            # Dependencias de ejecución
├── requirements-dev.txt        # Dependencias de desarrollo
├── Makefile                    # Tareas automatizadas (formato, test, etc.)
├── scripts/                    # Scripts CLI para pruebas, ejecución local, etc.
│   └── run_pipeline.py
├── config/                     # Archivos de configuración YAML/JSON
│   └── pipeline_config.yaml
├── pipelines/                  # Definición de pipelines Beam (por dominio)
│   ├── __init__.py
│   ├── streaming/
│   │   └── process_events.py
│   └── batch/
│       └── aggregate_metrics.py
├── src/                        # Código fuente auxiliar
│   ├── __init__.py
│   ├── io/                     # Lectura/escritura a GCS, BigQuery, etc.
│   ├── transforms/             # Transformaciones personalizadas de Beam
│   └── utils/                  # Funciones auxiliares
├── tests/                      # Pruebas unitarias e integración
│   ├── __init__.py
│   ├── test_batch_pipeline.py
│   └── test_streaming_pipeline.py
└── docs/                       # Documentación del proyecto
    └── arquitectura.md
```

---

## 📌 Notas clave sobre esta estructura

- **Separación por pipelines**: `batch` y `streaming` están separados para mayor claridad y mantenimiento.
- **Modularidad**: funciones de IO, transformaciones y utilidades organizadas bajo `src/`.
- **Configuración desacoplada**: el archivo `pipeline_config.yaml` centraliza parámetros y rutas.
- **Workflows de CI/CD**: el uso de GitHub Actions asegura pruebas automatizadas y calidad continua.

Esta estructura permite escalar y mantener pipelines complejos en entornos de producción siguiendo los estándares modernos de ingeniería de datos.


# Ejemplo: Proyecto Vibe Coding Basico (Nivel 1)

Este es un proyecto de ejemplo para el Nivel 1 de Vibe Coding: un programador principiante que usa IA (ChatGPT, Claude, Cursor) para generar la mayoria del codigo, pero ya entiende conceptos basicos de Git, entornos virtuales y estructura de proyectos.

Proyecto de ejemplo: un generador de resumenes de texto usando la API de OpenAI.

---

## Arbol de Carpetas Completo

```bash
resumidor-ia/
├── .gitignore
├── .env.example
├── README.md
├── RULES.md
├── requirements.txt
├── main.py                    # Script principal
├── src/
│   ├── __init__.py
│   ├── config.py              # Carga variables de entorno
│   ├── llm.py                 # Cliente de OpenAI
│   ├── resumidor.py           # Logica de resumen
│   └── utils.py               # Funciones auxiliares
├── tests/
│   ├── __init__.py
│   ├── test_llm.py
│   └── test_resumidor.py
└── prompts/                   # Prompts versionados
    └── resumen_system.txt
```

---

## Contenido de Cada Archivo

### .gitignore

```gitignore
.venv/
.env
__pycache__/
*.pyc
.pytest_cache/
.ruff_cache/
.mypy_cache/
.vscode/
.idea/
.DS_Store
dist/
build/
*.egg-info/
```

### .env.example

```bash
# OpenAI API Key - obtener de https://platform.openai.com/api-keys
OPENAI_API_KEY=sk-proj-...

# Modelo a utilizar
OPENAI_MODEL=gpt-4o-mini

# Temperatura (0.0 = deterministico, 1.0 = creativo)
TEMPERATURE=0.3

# Maximo de tokens en la respuesta
MAX_TOKENS=500
```

### README.md

```markdown
# Resumidor IA

Herramienta para generar resumenes de texto usando OpenAI.

## Requisitos

- Python 3.10+
- API Key de OpenAI

## Instalacion

```bash
# 1. Clonar
git clone <url>
cd resumidor-ia

# 2. Crear entorno virtual
python -m venv .venv

# 3. Activar entorno
# Windows: .venv\Scripts\activate
# Linux/Mac: source .venv/bin/activate

# 4. Instalar dependencias
pip install -r requirements.txt

# 5. Configurar .env
cp .env.example .env
# Editar .env con tu API key de OpenAI
```

## Uso

```bash
python main.py --archivo texto_largo.txt --max_palabras 100
```

## Estructura

```
resumidor-ia/
├── main.py            # Punto de entrada
├── src/
│   ├── config.py      # Configuracion desde .env
│   ├── llm.py         # Cliente OpenAI
│   ├── resumidor.py   # Logica de resumen
│   └── utils.py       # Utilidades
├── prompts/           # Prompts versionados
└── tests/             # Tests automatizados
```

## Tests

```bash
pytest
```
```

### RULES.md

```markdown
# Reglas del Proyecto Resumidor IA

## Lenguaje y Estilo
- Python 3.10+
- Tipos en todas las funciones (def funcion(x: str) -> int:)
- Docstrings en formato Google
- snake_case para variables, funciones y archivos
- PascalCase para clases

## Arquitectura
- Separacion de responsabilidades
- config.py: solo carga variables de entorno
- llm.py: solo interactua con la API de OpenAI
- resumidor.py: contiene la logica de resumen
- main.py: punto de entrada, parseo de argumentos

## Testing
- pytest
- Un archivo de test por modulo
- Usar monkeypatch para mockear llamadas a API

## Prohibido
- NO hagas commit de .env ni .venv/
- NO hardcodees la API key
- NO uses print() para debugging (usa logging)
- NO uses tipos Any (usa tipos concretos)

## Commits
- Usar Conventional Commits: feat:, fix:, docs:, test:, refactor:
```

### requirements.txt

```
openai>=1.0.0
python-dotenv>=1.0.0
pydantic>=2.0.0
pytest>=8.0.0
ruff>=0.5.0
```

### main.py

```python
"""Punto de entrada del resumidor IA."""
import argparse
import logging
from pathlib import Path

from src.config import Config
from src.resumidor import Resumidor

logging.basicConfig(level=logging.INFO, format="%(levelname)s: %(message)s")
logger = logging.getLogger(__name__)


def main() -> None:
    parser = argparse.ArgumentParser(description="Genera resumenes de texto con IA")
    parser.add_argument("--archivo", required=True, help="Archivo de texto a resumir")
    parser.add_argument("--max_palabras", type=int, default=100, help="Maximo de palabras en el resumen")
    parser.add_argument("--idioma", default="es", help="Idioma del resumen (es, en)")
    args = parser.parse_args()

    config = Config.from_env()
    resumidor = Resumidor(config)

    texto = Path(args.archivo).read_text(encoding="utf-8")
    resumen = resumidor.resumir(texto, max_palabras=args.max_palabras, idioma=args.idioma)

    print(f"\nResumen ({len(resumen.split())} palabras):\n")
    print(resumen)


if __name__ == "__main__":
    main()
```

### src/__init__.py

```python
"""Paquete principal del Resumidor IA."""
```

### src/config.py

```python
"""Configuracion de la aplicacion desde variables de entorno."""
import os
from dataclasses import dataclass
from dotenv import load_dotenv

load_dotenv()


@dataclass
class Config:
    openai_api_key: str
    model: str = "gpt-4o-mini"
    temperature: float = 0.3
    max_tokens: int = 500

    @classmethod
    def from_env(cls) -> "Config":
        api_key = os.getenv("OPENAI_API_KEY")
        if not api_key:
            raise ValueError("OPENAI_API_KEY no esta configurada en .env")
        return cls(
            openai_api_key=api_key,
            model=os.getenv("OPENAI_MODEL", "gpt-4o-mini"),
            temperature=float(os.getenv("TEMPERATURE", "0.3")),
            max_tokens=int(os.getenv("MAX_TOKENS", "500")),
        )
```

### src/llm.py

```python
"""Cliente para la API de OpenAI."""
import logging
from openai import AsyncOpenAI
from src.config import Config

logger = logging.getLogger(__name__)


class OpenAIClient:
    def __init__(self, config: Config) -> None:
        self.client = AsyncOpenAI(api_key=config.openai_api_key)
        self.model = config.model
        self.temperature = config.temperature
        self.max_tokens = config.max_tokens

    async def completar(self, system_prompt: str, user_prompt: str) -> str:
        try:
            response = await self.client.chat.completions.create(
                model=self.model,
                temperature=self.temperature,
                max_tokens=self.max_tokens,
                messages=[
                    {"role": "system", "content": system_prompt},
                    {"role": "user", "content": user_prompt},
                ],
            )
            return response.choices[0].message.content or ""
        except Exception as e:
            logger.error(f"Error en llamada a OpenAI: {e}")
            raise
```

### src/resumidor.py

```python
"""Logica de generacion de resumenes."""
import asyncio
from pathlib import Path
from src.config import Config
from src.llm import OpenAIClient

PROMPTS_DIR = Path(__file__).parent.parent / "prompts"


class Resumidor:
    def __init__(self, config: Config) -> None:
        self.llm = OpenAIClient(config)

    def resumir(self, texto: str, max_palabras: int = 100, idioma: str = "es") -> str:
        system_prompt = self._cargar_prompt("resumen_system.txt").format(
            max_palabras=max_palabras, idioma=idioma
        )
        return asyncio.run(self.llm.completar(system_prompt, texto))

    def _cargar_prompt(self, nombre: str) -> str:
        return (PROMPTS_DIR / nombre).read_text(encoding="utf-8")
```

### src/utils.py

```python
"""Funciones auxiliares."""
import re


def contar_palabras(texto: str) -> int:
    return len(re.findall(r"\b\w+\b", texto))


def validar_texto(texto: str) -> bool:
    return len(texto.strip()) > 10
```

### prompts/resumen_system.txt

```
Eres un asistente especializado en generar resumenes concisos y precisos.

Reglas:
1. El resumen debe tener un maximo de {max_palabras} palabras.
2. El resumen debe estar en idioma {idioma}.
3. Conserva la informacion mas importante del texto original.
4. No inventes informacion que no este en el texto.
5. Usa un tono neutral y profesional.
6. Estructura el resumen en un solo parrafo coherente.
```

### tests/__init__.py

```python
"""Tests para el Resumidor IA."""
```

### tests/test_resumidor.py

```python
"""Tests para el modulo resumidor."""
import pytest
from src.resumidor import Resumidor
from src.config import Config


@pytest.fixture
def config() -> Config:
    return Config(
        openai_api_key="test-key",
        model="gpt-4o-mini",
        temperature=0.0,
        max_tokens=100,
    )


def test_resumidor_se_inicializa(config: Config) -> None:
    resumidor = Resumidor(config)
    assert resumidor is not None
    assert resumidor.llm.model == "gpt-4o-mini"


def test_cargar_prompt_existe(config: Config) -> None:
    resumidor = Resumidor(config)
    prompt = resumidor._cargar_prompt("resumen_system.txt")
    assert "{max_palabras}" in prompt
    assert "{idioma}" in prompt


def test_cargar_prompt_formateado(config: Config) -> None:
    resumidor = Resumidor(config)
    prompt = resumidor._cargar_prompt("resumen_system.txt")
    formateado = prompt.format(max_palabras=50, idioma="en")
    assert "50" in formateado
    assert "en" in formateado
```

### tests/test_llm.py

```python
"""Tests para el modulo llm."""
import pytest
from src.llm import OpenAIClient
from src.config import Config


@pytest.fixture
def config() -> Config:
    return Config(openai_api_key="test-key")


def test_cliente_se_inicializa(config: Config) -> None:
    cliente = OpenAIClient(config)
    assert cliente.model == "gpt-4o-mini"
    assert cliente.temperature == 0.3


def test_config_sin_api_key_lanza_error() -> None:
    import os
    # Guardar valor original si existe
    original = os.environ.pop("OPENAI_API_KEY", None)
    try:
        with pytest.raises(ValueError, match="OPENAI_API_KEY"):
            Config.from_env()
    finally:
        if original:
            os.environ["OPENAI_API_KEY"] = original
```

---

## Como Usar Este Ejemplo con IA

Para iniciar este proyecto con Cursor/Claude/ChatGPT, copia y pega este prompt inicial:

```
Quiero crear un proyecto Python llamado "resumidor-ia" que use la API de OpenAI para generar resumenes de texto.

Estructura deseada:
- .gitignore con exclusiones estandar de Python
- .env.example con OPENAI_API_KEY, OPENAI_MODEL, TEMPERATURE, MAX_TOKENS
- README.md explicando instalacion y uso
- RULES.md con reglas de desarrollo
- requirements.txt con openai, python-dotenv, pydantic, pytest, ruff
- main.py como script CLI con argparse (--archivo, --max_palabras, --idioma)
- src/config.py con dataclass Config que lee de .env
- src/llm.py con clase OpenAIClient usando AsyncOpenAI
- src/resumidor.py con clase Resumidor que orquesta el flujo
- src/utils.py con contar_palabras() y validar_texto()
- prompts/resumen_system.txt con template de system prompt
- tests/test_resumidor.py y tests/test_llm.py con pytest

Reglas:
- Python 3.10+, tipos en todas las funciones
- snake_case para todo excepto clases PascalCase
- Docstrings en formato Google
- Usar python-dotenv para .env
- Usar asyncio para llamadas a OpenAI
- Manejar errores de API con try/except
- Conventional Commits para mensajes de git
```

---

## Siguiente Nivel

Cuando este proyecto crezca, evolucionar a la estructura del [Ejemplo Vibe Coding Avanzado](./vibe-coding-avanzado.md).

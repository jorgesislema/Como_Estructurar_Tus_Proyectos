# MCP (Model Context Protocol): El Estandar de Conexion para Herramientas de IA

MCP (Model Context Protocol) es un protocolo abierto desarrollado por Anthropic en 2024 que estandariza como los LLMs y agentes de IA se conectan a fuentes de datos y herramientas externas. Es el equivalente a "USB-C para IA": un conector universal entre modelos de lenguaje y el mundo exterior.

---

## Que es MCP y Por Que Importa

Antes de MCP, cada herramienta de IA implementaba su propia forma de conectarse a APIs, bases de datos o sistemas de archivos. Esto generaba integraciones fragiles, codigo duplicado y vendor lock-in.

**MCP resuelve esto con una arquitectura cliente-servidor estandarizada:**

```
[Host - Cursor, Claude Desktop, OpenCode]
    |
[MCP Client] --(protocolo estandar)--> [MCP Server A] --> [Base de Datos]
    |                                  [MCP Server B] --> [API Externa]
    |                                  [MCP Server C] --> [Sistema de Archivos]
    |
[LLM] usa los servidores MCP para acceder a herramientas y datos
```

---

## Arquitectura MCP

### Componentes

**MCP Host:** La aplicacion que ejecuta el LLM (Cursor, Claude Desktop, OpenCode, VS Code con extension MCP).

**MCP Client:** Conecta el Host con uno o mas MCP Servers. Mantiene conexiones 1:1 con cada servidor.

**MCP Server:** Expone herramientas (tools), recursos (resources) y plantillas de prompts (prompts) a traves del protocolo estandar. Cada servidor se especializa en una capacidad.

**Transporte:** La comunicacion entre cliente y servidor. Dos opciones:
- **stdio** (local): El servidor corre como proceso hijo del cliente. Comunicacion por stdin/stdout. Simple, ideal para herramientas locales.
- **HTTP + SSE** (remoto): El servidor corre como servicio HTTP. Comunicacion por HTTP POST + Server-Sent Events. Para servidores compartidos o en la nube.

### Capacidades que Expone un MCP Server

| Capacidad | Descripcion | Ejemplo |
|-----------|-------------|---------|
| **Tools** | Funciones que el LLM puede llamar | `search_database(query)`, `create_file(path, content)` |
| **Resources** | Datos que el LLM puede leer | `file://docs/readme.md`, `postgres://users/table` |
| **Prompts** | Plantillas de prompts reutilizables | `review_code_prompt(code, language)` |
| **Sampling** | El servidor puede pedirle al LLM que genere texto | Agentic workflows donde el servidor necesita razonamiento |

---

## Estructura de un Proyecto con MCP

### Proyecto que Expone MCP Servers

```bash
mi-proyecto-mcp/
├── .github/
│   └── workflows/
│       └── ci.yml
├── .gitignore
├── .env.example
├── README.md
├── RULES.md
├── CONTEXTO.md
├── pyproject.toml
├── src/
│   ├── __init__.py
│   ├── servers/                       # Cada servidor MCP es un modulo
│   │   ├── __init__.py
│   │   ├── database_server.py         # MCP Server para PostgreSQL
│   │   ├── file_system_server.py      # MCP Server para sistema de archivos
│   │   ├── api_server.py              # MCP Server para APIs externas
│   │   ├── git_server.py              # MCP Server para Git
│   │   ├── web_search_server.py       # MCP Server para busqueda web
│   │   └── rag_server.py              # MCP Server para RAG/busqueda documental
│   ├── tools/                         # Implementaciones de las herramientas
│   │   ├── __init__.py
│   │   ├── database_tools.py
│   │   ├── file_tools.py
│   │   └── api_tools.py
│   ├── resources/                     # Definiciones de recursos expuestos
│   │   ├── __init__.py
│   │   └── resource_definitions.py
│   ├── prompts/                       # Plantillas de prompts MCP
│   │   ├── __init__.py
│   │   └── prompt_templates.py
│   └── transport/                     # Capa de transporte
│       ├── __init__.py
│       ├── stdio_server.py            # Servidor via stdio
│       └── http_server.py             # Servidor via HTTP+SSE
├── tests/
│   ├── conftest.py
│   ├── test_database_server.py
│   ├── test_file_system_server.py
│   └── test_server_integration.py
├── configs/
│   └── mcp_servers.yaml              # Configuracion de servidores MCP
└── docs/
    └── mcp_servers.md                 # Documentacion de servidores disponibles
```

### Configuracion MCP en el Cliente

Los clientes MCP se configuran con un archivo JSON que define que servidores cargar:

```json
{
  "mcpServers": {
    "database": {
      "command": "python",
      "args": ["-m", "src.servers.database_server"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost:5432/mydb"
      }
    },
    "filesystem": {
      "command": "python",
      "args": ["-m", "src.servers.file_system_server"],
      "args": ["--allowed-dirs", "/home/user/projects"]
    },
    "web-search": {
      "command": "python",
      "args": ["-m", "src.servers.web_search_server"],
      "env": {
        "BRAVE_API_KEY": "${BRAVE_API_KEY}"
      }
    }
  }
}
```

---

## Ejemplos de Servidores MCP

### 1. MCP Server para Base de Datos

Este servidor expone la base de datos como un recurso que el LLM puede consultar:

**Tools expuestas:**
- `query_database(sql: str)` -> Ejecuta una query SQL de solo lectura
- `list_tables()` -> Lista todas las tablas disponibles
- `describe_table(table_name: str)` -> Describe el schema de una tabla

**Resources expuestos:**
- `database://schema` -> Schema completo de la base de datos
- `database://table/{name}` -> Muestra de datos de una tabla

**Ejemplo de uso desde un agente:**
```
Agente: Necesito encontrar los usuarios que no han hecho login en 30 dias.
Agente: [Llama a list_tables()] -> "users", "logins", "orders"
Agente: [Llama a describe_table("users")] -> columnas: id, email, last_login
Agente: [Llama a query_database("SELECT email FROM users WHERE last_login < now() - interval '30 days'")]
Agente: Encontre 15 usuarios inactivos. Sus emails son: ...
```

### 2. MCP Server para Sistema de Archivos

**Tools:**
- `read_file(path: str)` -> Lee un archivo del sistema
- `write_file(path: str, content: str)` -> Escribe un archivo
- `list_directory(path: str)` -> Lista archivos en un directorio
- `search_files(pattern: str)` -> Busca archivos por patron

**Resources:**
- `file://{path}` -> Contenido del archivo como recurso

### 3. MCP Server para Git

**Tools:**
- `git_status()` -> Estado actual del repositorio
- `git_diff(staged: bool)` -> Ver cambios
- `git_log(count: int)` -> Ultimos commits
- `git_branch_list()` -> Lista ramas

**Resources:**
- `git://diff/staged` -> Diff de cambios staged
- `git://log/{branch}` -> Log de una rama

### 4. MCP Server para RAG

**Tools:**
- `search_documents(query: str, top_k: int)` -> Busqueda semantica en la base documental
- `get_document(doc_id: str)` -> Obtener documento completo
- `list_document_sources()` -> Listar fuentes disponibles

---

## Beneficios de MCP

1. **Estandarizacion:** Un solo protocolo para conectar cualquier LLM con cualquier herramienta. No mas integraciones ad-hoc.

2. **Reutilizacion:** Un MCP server para PostgreSQL lo puede usar Cursor, Claude Desktop, OpenCode y cualquier otro cliente MCP.

3. **Seguridad:** El servidor define sus permisos. El cliente puede limitar que herramientas estan disponibles para el LLM. Los servidores locales corren en el mismo proceso, sin exponer puertos.

4. **Descubrimiento:** El cliente puede listar automaticamente que herramientas, recursos y prompts ofrece cada servidor.

5. **Ecosistema creciente:** Servidores MCP oficiales y comunitarios para: PostgreSQL, SQLite, GitHub, Slack, Google Drive, sistemas de archivos, APIs REST, Brave Search, Puppeteer, etc.

---

## MCP vs Otras Alternativas

| Protocolo | Alcance | Estado | Estandarizacion |
|-----------|---------|--------|-----------------|
| **MCP** | Universal (herramientas + recursos + prompts) | Activo, creciendo | Si, especificacion abierta |
| **OpenAI Function Calling** | Solo llamadas a funciones | Maduro | Propietario de OpenAI |
| **LangChain Tools** | Solo herramientas Python | Maduro | Framework, no protocolo |
| **A2A (Agent-to-Agent, Google)** | Comunicacion entre agentes | Propuesto | Especificacion abierta |

---

## Estructura de un Proyecto que USA MCP (Cliente)

Cuando tu proyecto es un consumidor de MCP, no un proveedor:

```bash
mi-app-con-mcp/
├── .cursor/
│   └── mcp.json                    # Configuracion MCP para Cursor
├── .github/
│   └── copilot-instructions.md
├── .gitignore
├── .env.example
├── mcp_config.json                 # Configuracion MCP general
├── src/
│   ├── agents/
│   │   └── agent.py                # Agente que usa herramientas via MCP
│   └── mcp/
│       ├── client.py               # Cliente MCP (conexion a servidores)
│       └── tool_discovery.py       # Descubrimiento de herramientas disponibles
└── README.md
```

---

## Referencias Cruzadas y Recursos

- **Especificacion oficial:** https://modelcontextprotocol.io
- **Repositorio GitHub:** https://github.com/modelcontextprotocol
- **Servidores MCP oficiales:** https://github.com/modelcontextprotocol/servers
- [Agentes IA](../por-dominio/agentes-ia.md) -- patrones de agentes que usan MCP
- [IA Generativa](../por-dominio/ia-generativa.md) -- integracion de herramientas
- [Glosario](./glosario.md) -- definicion de MCP y terminos relacionados

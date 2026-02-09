# Интеграция с MCP (Model Context Protocol)
# MCP Integration Guide

**Версия**: 2.0.0
**Дата создания**: 2026-02-09
**Категория**: Integration Guide
**Сложность**: Intermediate

---

## 📖 Содержание

1. [Что такое MCP](#что-такое-mcp)
2. [Архитектура интеграции](#архитектура-интеграции)
3. [Настройка MCP для Knowledge Base](#настройка-mcp-для-knowledge-base)
4. [Создание кастомных MCP серверов](#создание-кастомных-mcp-серверов)
5. [Примеры использования](#примеры-использования)
6. [Лучшие практики](#лучшие-практики)
7. [Troubleshooting](#troubleshooting)

---

## 🎯 Что такое MCP

**Model Context Protocol (MCP)** - это открытый протокол, который позволяет Claude Code подключаться к внешним источникам данных и инструментам.

### Основные возможности MCP

- 🗂️ **Доступ к файловой системе**: Чтение и запись файлов на локальном компьютере
- 🌐 **Web доступ**: Получение данных из интернета
- 🗃️ **База данных**: Подключение к различным БД
- 🔧 **Кастомные инструменты**: Создание собственных интеграций
- 💾 **Долговременная память**: Сохранение контекста между сессиями

### Почему это важно для Knowledge Base

MCP позволяет:
- Хранить знания локально на компьютере
- Динамически загружать инструкции по требованию
- Синхронизировать знания между проектами
- Создавать персонализированные агенты
- Расширять возможности без изменения кода Claude

---

## 🏗️ Архитектура интеграции

```
┌─────────────────────────────────────────────────────────────┐
│                     Claude Code                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │              Agent System                             │  │
│  │  - Task Agent                                        │  │
│  │  - Explore Agent                                     │  │
│  │  - Custom Agents                                     │  │
│  └──────────────────────────────────────────────────────┘  │
│                           │                                 │
│                           │ MCP Protocol                    │
│                           ▼                                 │
│  ┌──────────────────────────────────────────────────────┐  │
│  │              MCP Client                               │  │
│  │  - Manages connections                               │  │
│  │  - Routes requests                                   │  │
│  │  - Caches responses                                  │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                           │
                           │ JSON-RPC
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
┌───────────────┐  ┌──────────────┐  ┌──────────────┐
│  MCP Server   │  │  MCP Server  │  │  MCP Server  │
│  FileSystem   │  │  Knowledge   │  │  Custom      │
└───────────────┘  └──────────────┘  └──────────────┘
        │                  │                  │
        ▼                  ▼                  ▼
┌───────────────┐  ┌──────────────┐  ┌──────────────┐
│  Local Files  │  │  Knowledge   │  │  Your API    │
│  /home/user/  │  │  Base        │  │  Database    │
└───────────────┘  └──────────────┘  └──────────────┘
```

---

## ⚙️ Настройка MCP для Knowledge Base

### Шаг 1: Конфигурация Claude Code

Создайте или отредактируйте файл конфигурации MCP:

**Путь**: `~/.config/claude/mcp-config.json`

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/home/user/projects"
      ],
      "description": "Access to project files",
      "enabled": true
    },

    "knowledge-base": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/home/user/projects/.claude-knowledge"
      ],
      "description": "Claude Knowledge Base access",
      "enabled": true,
      "priority": "high"
    },

    "memory": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-memory"
      ],
      "description": "Persistent memory storage",
      "enabled": true
    }
  },

  "defaults": {
    "timeout": 30000,
    "retries": 3,
    "cache_ttl": 300
  }
}
```

### Шаг 2: Установка MCP серверов

```bash
# Установка официальных MCP серверов
npm install -g @modelcontextprotocol/server-filesystem
npm install -g @modelcontextprotocol/server-memory
npm install -g @modelcontextprotocol/server-web

# Проверка установки
npx @modelcontextprotocol/server-filesystem --version
```

### Шаг 3: Настройка прав доступа

```bash
# Создание директории для MCP
mkdir -p ~/.config/claude

# Права на конфигурацию
chmod 600 ~/.config/claude/mcp-config.json

# Права на knowledge base
chmod -R 755 ~/.claude-knowledge
```

### Шаг 4: Проверка подключения

Создайте тестовый скрипт:

```bash
#!/bin/bash
# test-mcp.sh

echo "Testing MCP connection..."

# Тест filesystem сервера
npx @modelcontextprotocol/server-filesystem ~/.claude-knowledge <<EOF
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/list"
}
EOF

echo "✅ MCP test completed"
```

---

## 🛠️ Создание кастомных MCP серверов

### Структура кастомного MCP сервера

```javascript
// knowledge-server.js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import fs from "fs/promises";
import path from "path";

const KNOWLEDGE_BASE = process.env.KNOWLEDGE_BASE_PATH || "./.claude-knowledge";

class KnowledgeServer {
  constructor() {
    this.server = new Server(
      {
        name: "claude-knowledge-server",
        version: "1.0.0",
      },
      {
        capabilities: {
          tools: {},
          resources: {},
        },
      }
    );

    this.setupHandlers();
  }

  setupHandlers() {
    // Регистрация инструментов
    this.server.setRequestHandler("tools/list", async () => ({
      tools: [
        {
          name: "search_instructions",
          description: "Search for instructions in knowledge base",
          inputSchema: {
            type: "object",
            properties: {
              query: {
                type: "string",
                description: "Search query",
              },
              category: {
                type: "string",
                description: "Category filter (optional)",
              },
            },
            required: ["query"],
          },
        },
        {
          name: "get_instruction",
          description: "Get specific instruction by path",
          inputSchema: {
            type: "object",
            properties: {
              path: {
                type: "string",
                description: "Path to instruction file",
              },
            },
            required: ["path"],
          },
        },
        {
          name: "list_agents",
          description: "List all available agents",
          inputSchema: {
            type: "object",
            properties: {},
          },
        },
        {
          name: "get_agent_config",
          description: "Get agent configuration",
          inputSchema: {
            type: "object",
            properties: {
              agent_name: {
                type: "string",
                description: "Name of the agent",
              },
            },
            required: ["agent_name"],
          },
        },
      ],
    }));

    // Обработчик вызова инструментов
    this.server.setRequestHandler("tools/call", async (request) => {
      const { name, arguments: args } = request.params;

      switch (name) {
        case "search_instructions":
          return await this.searchInstructions(args.query, args.category);

        case "get_instruction":
          return await this.getInstruction(args.path);

        case "list_agents":
          return await this.listAgents();

        case "get_agent_config":
          return await this.getAgentConfig(args.agent_name);

        default:
          throw new Error(`Unknown tool: ${name}`);
      }
    });

    // Регистрация ресурсов
    this.server.setRequestHandler("resources/list", async () => ({
      resources: [
        {
          uri: "knowledge://instructions",
          name: "Instructions Library",
          mimeType: "application/json",
        },
        {
          uri: "knowledge://agents",
          name: "Agents Registry",
          mimeType: "application/json",
        },
        {
          uri: "knowledge://skills",
          name: "Skills Collection",
          mimeType: "application/json",
        },
      ],
    }));
  }

  async searchInstructions(query, category = null) {
    const instructionsDir = path.join(KNOWLEDGE_BASE, "instructions");
    const results = [];

    async function searchDir(dir) {
      const entries = await fs.readdir(dir, { withFileTypes: true });

      for (const entry of entries) {
        const fullPath = path.join(dir, entry.name);

        if (entry.isDirectory()) {
          await searchDir(fullPath);
        } else if (entry.name.endsWith(".md")) {
          const content = await fs.readFile(fullPath, "utf-8");

          if (content.toLowerCase().includes(query.toLowerCase())) {
            // Извлечение метаданных
            const metadata = this.extractMetadata(content);

            if (!category || metadata.category === category) {
              results.push({
                path: fullPath.replace(KNOWLEDGE_BASE + "/", ""),
                title: metadata.title || entry.name,
                category: metadata.category,
                description: metadata.description,
                relevance: this.calculateRelevance(content, query),
              });
            }
          }
        }
      }
    }

    await searchDir(instructionsDir);

    // Сортировка по релевантности
    results.sort((a, b) => b.relevance - a.relevance);

    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(results, null, 2),
        },
      ],
    };
  }

  async getInstruction(instructionPath) {
    const fullPath = path.join(KNOWLEDGE_BASE, instructionPath);

    try {
      const content = await fs.readFile(fullPath, "utf-8");
      const metadata = this.extractMetadata(content);

      return {
        content: [
          {
            type: "text",
            text: content,
          },
        ],
        metadata,
      };
    } catch (error) {
      throw new Error(`Instruction not found: ${instructionPath}`);
    }
  }

  async listAgents() {
    const agentsDir = path.join(KNOWLEDGE_BASE, "agents/custom-agents");
    const agents = [];

    const entries = await fs.readdir(agentsDir, { withFileTypes: true });

    for (const entry of entries) {
      if (entry.name.endsWith(".yml") || entry.name.endsWith(".yaml")) {
        const fullPath = path.join(agentsDir, entry.name);
        const content = await fs.readFile(fullPath, "utf-8");

        // Простой парсинг YAML (в продакшене использовать библиотеку)
        const nameMatch = content.match(/name:\s*"?([^"\n]+)"?/);
        const versionMatch = content.match(/version:\s*"?([^"\n]+)"?/);
        const descMatch = content.match(/description:\s*"?([^"\n]+)"?/);

        agents.push({
          name: nameMatch ? nameMatch[1] : entry.name,
          version: versionMatch ? versionMatch[1] : "unknown",
          description: descMatch ? descMatch[1] : "",
          path: `agents/custom-agents/${entry.name}`,
        });
      }
    }

    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(agents, null, 2),
        },
      ],
    };
  }

  async getAgentConfig(agentName) {
    const agentPath = path.join(
      KNOWLEDGE_BASE,
      `agents/custom-agents/${agentName}.yml`
    );

    try {
      const config = await fs.readFile(agentPath, "utf-8");

      return {
        content: [
          {
            type: "text",
            text: config,
          },
        ],
      };
    } catch (error) {
      throw new Error(`Agent not found: ${agentName}`);
    }
  }

  extractMetadata(content) {
    // Извлечение YAML frontmatter
    const frontmatterMatch = content.match(/^---\n([\s\S]*?)\n---/);

    if (!frontmatterMatch) return {};

    const frontmatter = frontmatterMatch[1];
    const metadata = {};

    // Простой парсинг (в продакшене использовать yaml библиотеку)
    frontmatter.split("\n").forEach((line) => {
      const match = line.match(/^(\w+):\s*"?([^"\n]+)"?/);
      if (match) {
        metadata[match[1]] = match[2];
      }
    });

    return metadata;
  }

  calculateRelevance(content, query) {
    const lowerContent = content.toLowerCase();
    const lowerQuery = query.toLowerCase();

    // Простая оценка релевантности
    const titleMatch = content.slice(0, 200).toLowerCase().includes(lowerQuery)
      ? 10
      : 0;
    const occurrences = (lowerContent.match(new RegExp(lowerQuery, "g")) || [])
      .length;

    return titleMatch + occurrences;
  }

  async start() {
    const transport = new StdioServerTransport();
    await this.server.connect(transport);
    console.error("Knowledge MCP Server running on stdio");
  }
}

// Запуск сервера
const server = new KnowledgeServer();
server.start().catch(console.error);
```

### package.json для кастомного сервера

```json
{
  "name": "claude-knowledge-mcp-server",
  "version": "1.0.0",
  "type": "module",
  "description": "MCP server for Claude Knowledge Base",
  "main": "knowledge-server.js",
  "bin": {
    "claude-knowledge-server": "./knowledge-server.js"
  },
  "scripts": {
    "start": "node knowledge-server.js",
    "dev": "nodemon knowledge-server.js"
  },
  "dependencies": {
    "@modelcontextprotocol/sdk": "^0.5.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.0"
  },
  "keywords": ["mcp", "claude", "knowledge-base"],
  "author": "Your Name",
  "license": "MIT"
}
```

### Регистрация кастомного сервера

Обновите `~/.config/claude/mcp-config.json`:

```json
{
  "mcpServers": {
    "knowledge-custom": {
      "command": "node",
      "args": ["/path/to/knowledge-server.js"],
      "env": {
        "KNOWLEDGE_BASE_PATH": "/home/user/projects/.claude-knowledge"
      },
      "description": "Custom Knowledge Base server",
      "enabled": true
    }
  }
}
```

---

## 💡 Примеры использования

### Пример 1: Поиск инструкций через MCP

```javascript
// Использование в Claude Code
User: "Найди инструкции по Docker"

Claude: [Вызывает MCP tool search_instructions]
{
  "tool": "search_instructions",
  "arguments": {
    "query": "docker",
    "category": "deployment"
  }
}

MCP Response:
[
  {
    "path": "instructions/deployment/docker-setup.md",
    "title": "Настройка Docker для проекта",
    "category": "deployment",
    "relevance": 15
  }
]

Claude: "Нашел инструкцию по настройке Docker. Вот основные шаги..."
```

### Пример 2: Загрузка конфигурации агента

```javascript
User: "Запусти code reviewer агент"

Claude: [Вызывает MCP tool get_agent_config]
{
  "tool": "get_agent_config",
  "arguments": {
    "agent_name": "code-reviewer"
  }
}

MCP Response:
```yaml
agent:
  name: "code-reviewer"
  capabilities: [...]
  prompts: [...]
```

Claude: [Загружает конфигурацию и запускает агента]
```

### Пример 3: Интеграция с базой данных

```javascript
// mcp-database-server.js
const tools = [
  {
    name: "query_knowledge_metrics",
    description: "Query usage metrics from database",
    inputSchema: {
      type: "object",
      properties: {
        metric_type: {
          type: "string",
          enum: ["usage", "popularity", "success_rate"],
        },
        time_range: {
          type: "string",
          description: "Time range in days",
        },
      },
    },
  },
];

// Использование
User: "Покажи самые популярные инструкции за последнюю неделю"

Claude: [Вызывает query_knowledge_metrics через MCP]
Result: [Список популярных инструкций]
```

---

## ✅ Лучшие практики

### 1. Безопасность

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/allowed/path/only"
      ],
      "security": {
        "allowedPaths": ["/allowed/path/only"],
        "deniedPaths": ["/home/*/secrets", "~/.ssh"],
        "maxFileSize": "10MB"
      }
    }
  }
}
```

### 2. Производительность

- **Кеширование**: Кешируйте часто запрашиваемые данные
- **Пагинация**: Используйте пагинацию для больших результатов
- **Индексация**: Создайте индекс для быстрого поиска

```javascript
// Пример кеширования
const cache = new Map();
const CACHE_TTL = 5 * 60 * 1000; // 5 минут

async function getCached(key, fetcher) {
  const cached = cache.get(key);

  if (cached && Date.now() - cached.timestamp < CACHE_TTL) {
    return cached.data;
  }

  const data = await fetcher();
  cache.set(key, { data, timestamp: Date.now() });

  return data;
}
```

### 3. Error Handling

```javascript
async function safeToolCall(toolFunc) {
  try {
    return await toolFunc();
  } catch (error) {
    console.error(`Tool error: ${error.message}`);

    return {
      content: [
        {
          type: "text",
          text: JSON.stringify({
            error: error.message,
            fallback: "Try alternative approach",
          }),
        },
      ],
      isError: true,
    };
  }
}
```

### 4. Логирование

```javascript
const log = {
  info: (msg) => console.error(`[INFO] ${new Date().toISOString()} ${msg}`),
  error: (msg) => console.error(`[ERROR] ${new Date().toISOString()} ${msg}`),
  debug: (msg) =>
    process.env.DEBUG &&
    console.error(`[DEBUG] ${new Date().toISOString()} ${msg}`),
};
```

---

## 🔧 Troubleshooting

### Проблема 1: MCP сервер не запускается

**Симптомы**: Claude Code не видит MCP сервер

**Решение**:

```bash
# Проверка конфигурации
cat ~/.config/claude/mcp-config.json

# Проверка установки
which npx
npx @modelcontextprotocol/server-filesystem --version

# Проверка логов
tail -f ~/.config/claude/mcp.log

# Тест вручную
npx @modelcontextprotocol/server-filesystem /test/path
```

### Проблема 2: Permission denied

**Решение**:

```bash
# Проверка прав
ls -la ~/.claude-knowledge

# Исправление прав
chmod -R 755 ~/.claude-knowledge
chown -R $USER:$USER ~/.claude-knowledge
```

### Проблема 3: Медленный ответ MCP

**Решение**:

- Добавить кеширование
- Уменьшить timeout
- Оптимизировать поиск
- Использовать индексацию

```json
{
  "mcpServers": {
    "knowledge": {
      "timeout": 10000,
      "cache": {
        "enabled": true,
        "ttl": 300
      }
    }
  }
}
```

---

## 📚 Дополнительные ресурсы

- [MCP Specification](https://github.com/modelcontextprotocol/specification)
- [MCP SDK Documentation](https://github.com/modelcontextprotocol/sdk)
- [Official MCP Servers](https://github.com/modelcontextprotocol/servers)
- [Community MCP Servers](https://github.com/topics/mcp-server)

---

**Версия документа**: 2.0.0
**Последнее обновление**: 2026-02-09
**Статус**: Production Ready

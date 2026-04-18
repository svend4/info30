# Форматы хранения знаний | Knowledge Storage Formats

**Версия**: 1.0.0
**Дата**: 2026-02-09
**Категория**: Методология

---

## 📋 Содержание

1. [Обзор форматов](#обзор-форматов)
2. [Markdown (.md)](#markdown-md)
3. [YAML (.yml)](#yaml-yml)
4. [JSON (.json)](#json-json)
5. [Код и скрипты](#код-и-скрипты)
6. [Гибридные форматы](#гибридные-форматы)
7. [Рекомендации по выбору](#рекомендации-по-выбору)

---

## 🎯 Обзор форматов

### Поддерживаемые форматы

| Формат | Использование | Читаемость | Структурированность | Примеры |
|--------|---------------|------------|---------------------|---------|
| **Markdown** | Инструкции, документация | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | README, guides |
| **YAML** | Конфигурации, метаданные | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | agent configs |
| **JSON** | Данные, API схемы | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | metrics, schemas |
| **Code** | Примеры, шаблоны | ⭐⭐⭐⭐ | ⭐⭐⭐ | .js, .py, .ts |
| **Hybrid** | Комплексные задачи | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | MD+YAML frontmatter |

---

## 📝 Markdown (.md)

### Назначение
- Основной формат для человеко-читаемой документации
- Инструкции и руководства
- Описания агентов и скиллов
- Примеры с пояснениями

### Структура базового документа

```markdown
---
title: "Название документа"
category: "instructions/coding"
level: "intermediate"
tags: ["python", "api", "rest"]
version: "1.0.0"
author: "Name"
created: "2026-02-09"
updated: "2026-02-09"
---

# Название документа

**Краткое описание**: одной строкой

## Предварительные требования

- Python 3.9+
- pip installed
- Basic REST API knowledge

## Основное содержание

### Секция 1

Текст с **выделением** и *курсивом*.

```python
# Пример кода
def example():
    return "Hello"
```

### Секция 2

> **Важно**: Обратите внимание на...

- Пункт списка 1
- Пункт списка 2

## Результат

[Описание ожидаемого результата]

## Дополнительные ресурсы

- [Ссылка 1](https://example.com)
- [Ссылка 2](https://example.org)
```

### Расширенные возможности Markdown

#### 1. Таблицы

```markdown
| Заголовок 1 | Заголовок 2 | Заголовок 3 |
|-------------|-------------|-------------|
| Ячейка 1    | Ячейка 2    | Ячейка 3    |
| Данные 1    | Данные 2    | Данные 3    |
```

#### 2. Чеклисты

```markdown
## Проверочный список

- [x] Задача выполнена
- [ ] Задача в процессе
- [ ] Задача не начата
```

#### 3. Блоки кода с подсветкой

```markdown
```python
def fibonacci(n):
    """Вычисление числа Фибоначчи"""
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
\```  # (убрать обратный слеш)

```javascript
const fetchData = async (url) => {
  const response = await fetch(url);
  return await response.json();
};
\```  # (убрать обратный слеш)
```

#### 4. Цитаты и предупреждения

```markdown
> **Note**: Полезная информация

> **Warning**: Важное предупреждение

> **Tip**: Совет по оптимизации
```

#### 5. Сноски

```markdown
Это текст со сноской[^1].

[^1]: Это текст сноски.
```

### Лучшие практики для Markdown

✅ **DO**:
- Используйте заголовки иерархически (H1 → H2 → H3)
- Добавляйте YAML frontmatter для метаданных
- Разбивайте длинные документы на секции
- Используйте якоря для навигации
- Добавляйте примеры кода с комментариями
- Используйте списки для перечислений
- Выделяйте важные моменты

❌ **DON'T**:
- Не пропускайте уровни заголовков (H1 → H3)
- Не используйте HTML если есть Markdown аналог
- Не делайте документы длиннее 500 строк
- Не забывайте про пустые строки между блоками

---

## ⚙️ YAML (.yml)

### Назначение
- Конфигурационные файлы
- Метаданные агентов
- Настройки скиллов
- Параметры и опции

### Структура конфигурации агента

```yaml
# Конфигурация агента
---
agent:
  name: "code-analyzer"
  version: "1.2.0"
  description: "Анализирует качество кода и предлагает улучшения"
  author: "AI Team"
  created: "2026-01-15"
  updated: "2026-02-09"

metadata:
  category: "code-quality"
  language: "multi"
  difficulty: "intermediate"
  tags:
    - code-review
    - static-analysis
    - best-practices

capabilities:
  - analyze_code_quality
  - detect_code_smells
  - suggest_refactoring
  - check_best_practices
  - generate_reports

parameters:
  language:
    type: string
    required: true
    default: "python"
    allowed_values:
      - python
      - javascript
      - typescript
      - java
    description: "Язык программирования для анализа"

  strictness:
    type: string
    required: false
    default: "medium"
    allowed_values:
      - low
      - medium
      - high
    description: "Уровень строгости проверок"

  output_format:
    type: string
    required: false
    default: "markdown"
    allowed_values:
      - markdown
      - json
      - html
    description: "Формат выходного отчета"

dependencies:
  tools:
    - Read
    - Grep
    - Bash
  external:
    - name: "pylint"
      version: ">=2.0.0"
      optional: false
    - name: "eslint"
      version: ">=8.0.0"
      optional: true

prompts:
  system: |
    Вы - эксперт по анализу кода.
    Ваша задача - найти проблемы и предложить улучшения.
    Будьте конкретны и приводите примеры.

  task_template: |
    Проанализируй код в файле ${file_path}:
    - Язык: ${language}
    - Строгость: ${strictness}
    - Формат отчета: ${output_format}

    Проверь:
    1. Читаемость кода
    2. Соответствие best practices
    3. Потенциальные баги
    4. Производительность
    5. Безопасность

workflow:
  - step: "read_file"
    action: "Read file content"
    tools: [Read]

  - step: "analyze_syntax"
    action: "Check syntax and style"
    tools: [Bash]
    command: "${linter_command}"

  - step: "analyze_structure"
    action: "Analyze code structure"
    tools: [Read, Grep]

  - step: "generate_report"
    action: "Create analysis report"
    output_format: "${output_format}"

examples:
  - name: "Analyze Python file"
    input:
      file_path: "src/main.py"
      language: "python"
      strictness: "high"
    expected_output: |
      # Отчет об анализе кода
      - Найдено 3 проблемы
      - Предложено 5 улучшений

  - name: "Quick JavaScript check"
    input:
      file_path: "app.js"
      language: "javascript"
      strictness: "medium"
    expected_output: |
      ✅ Код соответствует стандартам

error_handling:
  file_not_found:
    message: "Файл не найден: ${file_path}"
    action: "abort"

  syntax_error:
    message: "Синтаксическая ошибка в файле"
    action: "report_and_continue"

  tool_not_available:
    message: "Инструмент ${tool_name} недоступен"
    action: "use_fallback"

performance:
  max_file_size_mb: 10
  timeout_seconds: 300
  cache_results: true
  cache_duration_hours: 24

integration:
  claude_code:
    command: "/analyze"
    alias: ["check", "review"]
    auto_trigger:
      on_save: false
      on_commit: true

  mcp:
    protocol_version: "1.0"
    capabilities:
      - file_read
      - tool_execution
```

### Структура конфигурации скилла

```yaml
---
skill:
  name: "smart-commit"
  type: "command"
  version: "1.0.0"
  description: "Создает осмысленные commit messages на основе изменений"

trigger:
  command: "/smart-commit"
  aliases:
    - "/sc"
    - "/commit-smart"
  auto_trigger: false

parameters:
  - name: "scope"
    type: "string"
    required: false
    description: "Область изменений (feat, fix, docs, etc.)"

  - name: "message"
    type: "string"
    required: false
    description: "Дополнительное описание"

execution:
  steps:
    - analyze_changes
    - generate_message
    - confirm_with_user
    - execute_commit

  tools_required:
    - Bash
    - Read

prompt: |
  Проанализируй изменения в git и создай commit message:
  1. Используй conventional commits format
  2. Первая строка - краткое описание (до 72 символов)
  3. Пустая строка
  4. Детальное описание изменений
  5. Добавь ссылку на Claude session

examples:
  - input: "/smart-commit"
    output: |
      feat: add user authentication module

      - Implement JWT token generation
      - Add login/logout endpoints
      - Create user session management

      https://claude.ai/code/session_xxx

validation:
  max_message_length: 500
  require_description: true
  check_conventional_format: true
```

### Лучшие практики для YAML

✅ **DO**:
- Используйте 2 пробела для отступов
- Добавляйте комментарии для сложных секций
- Группируйте связанные параметры
- Используйте якоря для переиспользования
- Валидируйте YAML перед сохранением
- Используйте кавычки для строк со специальными символами

❌ **DON'T**:
- Не смешивайте табы и пробелы
- Не используйте сложные вложенные структуры (>5 уровней)
- Не дублируйте данные (используйте якоря)
- Не забывайте про типы данных

### Якоря и псевдонимы в YAML

```yaml
# Определение якоря
default_tools: &common_tools
  - Read
  - Write
  - Bash

# Переиспользование
agent1:
  tools: *common_tools

agent2:
  tools: *common_tools

# Слияние с добавлением
agent3:
  tools:
    - *common_tools
    - Grep
    - Glob
```

---

## 📊 JSON (.json)

### Назначение
- Структурированные данные
- API схемы
- Метрики и статистика
- Конфигурации для парсинга

### Структура файла метрик

```json
{
  "metadata": {
    "version": "1.0.0",
    "generated_at": "2026-02-09T10:30:00Z",
    "generator": "claude-knowledge-metrics",
    "period": {
      "start": "2026-02-01",
      "end": "2026-02-09"
    }
  },

  "usage_statistics": {
    "instructions": {
      "total_views": 1247,
      "unique_files": 45,
      "most_popular": [
        {
          "file": "ci-cd-setup.md",
          "category": "deployment",
          "views": 156,
          "success_rate": 0.94,
          "avg_completion_time_minutes": 23,
          "last_used": "2026-02-09T09:15:00Z"
        },
        {
          "file": "rest-api-design.md",
          "category": "architecture",
          "views": 134,
          "success_rate": 0.91,
          "avg_completion_time_minutes": 45,
          "last_used": "2026-02-08T14:22:00Z"
        }
      ]
    },

    "agents": {
      "total_executions": 892,
      "unique_agents": 12,
      "success_rate": 0.87,
      "performance": [
        {
          "agent": "code-analyzer",
          "executions": 234,
          "success": 210,
          "failures": 24,
          "avg_duration_seconds": 45.3,
          "errors": [
            {
              "type": "timeout",
              "count": 15
            },
            {
              "type": "tool_not_found",
              "count": 9
            }
          ]
        }
      ]
    },

    "skills": {
      "total_invocations": 567,
      "unique_skills": 8,
      "top_skills": [
        {
          "name": "smart-commit",
          "invocations": 203,
          "success_rate": 0.98
        },
        {
          "name": "code-review",
          "invocations": 156,
          "success_rate": 0.92
        }
      ]
    }
  },

  "quality_metrics": {
    "documentation_coverage": 0.78,
    "example_availability": 0.85,
    "outdated_content_percentage": 0.12,
    "validation_pass_rate": 0.94
  },

  "user_feedback": {
    "total_ratings": 234,
    "average_rating": 4.3,
    "feedback_by_category": {
      "instructions": {
        "helpful": 145,
        "needs_improvement": 23,
        "outdated": 8
      },
      "agents": {
        "helpful": 98,
        "needs_improvement": 34,
        "buggy": 12
      }
    }
  },

  "recommendations": [
    {
      "type": "update_required",
      "target": "instructions/testing/unit-testing.md",
      "reason": "Not accessed in 90 days",
      "priority": "low"
    },
    {
      "type": "add_examples",
      "target": "agents/custom-agents/deployment-agent.yml",
      "reason": "Low success rate (0.65)",
      "priority": "high"
    }
  ]
}
```

### JSON Schema для валидации

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Agent Configuration",
  "type": "object",
  "required": ["name", "version", "capabilities"],
  "properties": {
    "name": {
      "type": "string",
      "pattern": "^[a-z][a-z0-9-]*$",
      "description": "Agent name in kebab-case"
    },
    "version": {
      "type": "string",
      "pattern": "^\\d+\\.\\d+\\.\\d+$",
      "description": "Semantic version"
    },
    "capabilities": {
      "type": "array",
      "items": {
        "type": "string"
      },
      "minItems": 1,
      "description": "List of agent capabilities"
    },
    "parameters": {
      "type": "object",
      "additionalProperties": {
        "type": "object",
        "required": ["type", "required"],
        "properties": {
          "type": {
            "enum": ["string", "number", "boolean", "array", "object"]
          },
          "required": {
            "type": "boolean"
          },
          "default": {},
          "description": {
            "type": "string"
          }
        }
      }
    }
  }
}
```

### Лучшие практики для JSON

✅ **DO**:
- Используйте 2 или 4 пробела для отступов (консистентно)
- Валидируйте JSON перед сохранением
- Используйте JSON Schema для описания структуры
- Добавляйте метаданные в корне
- Используйте понятные ключи
- Форматируйте для читаемости

❌ **DON'T**:
- Не используйте trailing commas
- Не дублируйте ключи
- Не храните очень большие массивы (>10000 элементов)
- Не смешивайте типы данных в массивах
- Не используйте комментарии (JSON не поддерживает)

---

## 💻 Код и скрипты

### Назначение
- Примеры кода
- Шаблоны проектов
- Утилиты и хелперы
- Скрипты автоматизации

### Структура файла с примером кода

```python
"""
Название модуля: User Authentication Service

Категория: backend/authentication
Сложность: intermediate
Теги: #auth #jwt #security

Описание:
Реализация сервиса аутентификации пользователей с использованием JWT токенов.

Требования:
- Python 3.9+
- PyJWT
- bcrypt

Использование:
    from auth_service import AuthService

    auth = AuthService(secret_key="your-secret")
    token = auth.login("user@example.com", "password")

Автор: AI Assistant
Дата создания: 2026-02-09
Лицензия: MIT
"""

from datetime import datetime, timedelta
from typing import Optional, Dict
import jwt
import bcrypt


class AuthService:
    """
    Сервис аутентификации пользователей.

    Attributes:
        secret_key (str): Секретный ключ для подписи JWT
        algorithm (str): Алгоритм шифрования (default: HS256)
        token_expiry_hours (int): Время жизни токена в часах
    """

    def __init__(
        self,
        secret_key: str,
        algorithm: str = "HS256",
        token_expiry_hours: int = 24
    ):
        """
        Инициализация сервиса аутентификации.

        Args:
            secret_key: Секретный ключ для JWT
            algorithm: Алгоритм подписи
            token_expiry_hours: Время жизни токена

        Raises:
            ValueError: Если secret_key пустой
        """
        if not secret_key:
            raise ValueError("Secret key cannot be empty")

        self.secret_key = secret_key
        self.algorithm = algorithm
        self.token_expiry_hours = token_expiry_hours

    def hash_password(self, password: str) -> str:
        """
        Хеширование пароля с использованием bcrypt.

        Args:
            password: Пароль в открытом виде

        Returns:
            Хешированный пароль

        Example:
            >>> auth = AuthService("secret")
            >>> hashed = auth.hash_password("my_password")
            >>> isinstance(hashed, str)
            True
        """
        salt = bcrypt.gensalt()
        return bcrypt.hashpw(password.encode('utf-8'), salt).decode('utf-8')

    def verify_password(self, password: str, hashed: str) -> bool:
        """
        Проверка пароля.

        Args:
            password: Пароль для проверки
            hashed: Хешированный пароль из БД

        Returns:
            True если пароль верный, иначе False
        """
        return bcrypt.checkpw(
            password.encode('utf-8'),
            hashed.encode('utf-8')
        )

    def create_token(self, user_id: str, email: str) -> str:
        """
        Создание JWT токена для пользователя.

        Args:
            user_id: ID пользователя
            email: Email пользователя

        Returns:
            JWT токен

        Example:
            >>> auth = AuthService("secret")
            >>> token = auth.create_token("123", "user@test.com")
            >>> isinstance(token, str)
            True
        """
        payload = {
            'user_id': user_id,
            'email': email,
            'exp': datetime.utcnow() + timedelta(
                hours=self.token_expiry_hours
            ),
            'iat': datetime.utcnow()
        }

        return jwt.encode(
            payload,
            self.secret_key,
            algorithm=self.algorithm
        )

    def verify_token(self, token: str) -> Optional[Dict]:
        """
        Проверка и декодирование JWT токена.

        Args:
            token: JWT токен для проверки

        Returns:
            Декодированные данные или None если токен невалидный

        Example:
            >>> auth = AuthService("secret")
            >>> token = auth.create_token("123", "test@test.com")
            >>> data = auth.verify_token(token)
            >>> data['user_id']
            '123'
        """
        try:
            payload = jwt.decode(
                token,
                self.secret_key,
                algorithms=[self.algorithm]
            )
            return payload
        except jwt.ExpiredSignatureError:
            print("Token has expired")
            return None
        except jwt.InvalidTokenError:
            print("Invalid token")
            return None


# ============================================
# ПРИМЕРЫ ИСПОЛЬЗОВАНИЯ
# ============================================

if __name__ == "__main__":
    # Пример 1: Создание сервиса и регистрация пользователя
    auth_service = AuthService(secret_key="super-secret-key-123")

    # Хеширование пароля при регистрации
    password = "my_secure_password_123"
    hashed_password = auth_service.hash_password(password)
    print(f"Hashed password: {hashed_password[:50]}...")

    # Пример 2: Вход пользователя
    is_valid = auth_service.verify_password(password, hashed_password)
    print(f"Password valid: {is_valid}")

    if is_valid:
        # Создание токена
        token = auth_service.create_token(
            user_id="user_123",
            email="user@example.com"
        )
        print(f"Generated token: {token[:50]}...")

        # Проверка токена
        decoded = auth_service.verify_token(token)
        print(f"Decoded token: {decoded}")

    # Пример 3: Обработка невалидного токена
    invalid_token = "invalid.token.here"
    result = auth_service.verify_token(invalid_token)
    print(f"Invalid token result: {result}")


# ============================================
# ТЕСТЫ
# ============================================

def test_auth_service():
    """Базовые тесты для AuthService"""
    auth = AuthService("test-secret")

    # Test 1: Password hashing
    password = "test123"
    hashed = auth.hash_password(password)
    assert auth.verify_password(password, hashed)
    assert not auth.verify_password("wrong", hashed)

    # Test 2: Token creation and verification
    token = auth.create_token("user1", "test@test.com")
    decoded = auth.verify_token(token)
    assert decoded is not None
    assert decoded['user_id'] == "user1"
    assert decoded['email'] == "test@test.com"

    print("✅ All tests passed!")


# Раскомментируйте для запуска тестов
# test_auth_service()
```

### Структура JavaScript/TypeScript примера

```typescript
/**
 * API Client для работы с REST API
 *
 * @category networking
 * @complexity intermediate
 * @tags api, http, rest, typescript
 *
 * @description
 * Универсальный клиент для работы с REST API с поддержкой:
 * - Автоматической retry логики
 * - Interceptors для request/response
 * - Типизации
 * - Error handling
 *
 * @requires axios ^1.6.0
 *
 * @example
 * ```typescript
 * const client = new APIClient('https://api.example.com');
 * const data = await client.get('/users');
 * ```
 *
 * @author AI Assistant
 * @created 2026-02-09
 * @license MIT
 */

import axios, {
  AxiosInstance,
  AxiosRequestConfig,
  AxiosResponse,
  AxiosError
} from 'axios';

/**
 * Конфигурация для API Client
 */
interface APIClientConfig {
  /** Базовый URL API */
  baseURL: string;
  /** Таймаут запроса в миллисекундах */
  timeout?: number;
  /** Максимальное количество повторов */
  retries?: number;
  /** Задержка между повторами в мс */
  retryDelay?: number;
  /** Дополнительные заголовки */
  headers?: Record<string, string>;
}

/**
 * Типы для Response
 */
interface APIResponse<T = any> {
  data: T;
  status: number;
  message?: string;
}

/**
 * Класс для работы с API
 */
export class APIClient {
  private client: AxiosInstance;
  private config: Required<APIClientConfig>;

  /**
   * Создание экземпляра API Client
   */
  constructor(config: APIClientConfig) {
    this.config = {
      baseURL: config.baseURL,
      timeout: config.timeout || 30000,
      retries: config.retries || 3,
      retryDelay: config.retryDelay || 1000,
      headers: config.headers || {}
    };

    this.client = axios.create({
      baseURL: this.config.baseURL,
      timeout: this.config.timeout,
      headers: {
        'Content-Type': 'application/json',
        ...this.config.headers
      }
    });

    this.setupInterceptors();
  }

  /**
   * Настройка interceptors
   */
  private setupInterceptors(): void {
    // Request interceptor
    this.client.interceptors.request.use(
      (config) => {
        console.log(`→ ${config.method?.toUpperCase()} ${config.url}`);
        return config;
      },
      (error) => {
        console.error('Request error:', error);
        return Promise.reject(error);
      }
    );

    // Response interceptor
    this.client.interceptors.response.use(
      (response) => {
        console.log(`← ${response.status} ${response.config.url}`);
        return response;
      },
      async (error: AxiosError) => {
        return this.handleError(error);
      }
    );
  }

  /**
   * Обработка ошибок с retry логикой
   */
  private async handleError(error: AxiosError): Promise<any> {
    const config = error.config as AxiosRequestConfig & { retryCount?: number };

    if (!config) {
      return Promise.reject(error);
    }

    config.retryCount = config.retryCount || 0;

    if (config.retryCount < this.config.retries) {
      config.retryCount++;
      console.log(`Retry ${config.retryCount}/${this.config.retries}`);

      await new Promise(resolve =>
        setTimeout(resolve, this.config.retryDelay)
      );

      return this.client(config);
    }

    return Promise.reject(error);
  }

  /**
   * GET запрос
   */
  async get<T = any>(
    url: string,
    config?: AxiosRequestConfig
  ): Promise<APIResponse<T>> {
    const response = await this.client.get<T>(url, config);
    return this.formatResponse(response);
  }

  /**
   * POST запрос
   */
  async post<T = any>(
    url: string,
    data?: any,
    config?: AxiosRequestConfig
  ): Promise<APIResponse<T>> {
    const response = await this.client.post<T>(url, data, config);
    return this.formatResponse(response);
  }

  /**
   * PUT запрос
   */
  async put<T = any>(
    url: string,
    data?: any,
    config?: AxiosRequestConfig
  ): Promise<APIResponse<T>> {
    const response = await this.client.put<T>(url, data, config);
    return this.formatResponse(response);
  }

  /**
   * DELETE запрос
   */
  async delete<T = any>(
    url: string,
    config?: AxiosRequestConfig
  ): Promise<APIResponse<T>> {
    const response = await this.client.delete<T>(url, config);
    return this.formatResponse(response);
  }

  /**
   * Форматирование ответа
   */
  private formatResponse<T>(response: AxiosResponse<T>): APIResponse<T> {
    return {
      data: response.data,
      status: response.status,
      message: response.statusText
    };
  }

  /**
   * Установка токена авторизации
   */
  setAuthToken(token: string): void {
    this.client.defaults.headers.common['Authorization'] = `Bearer ${token}`;
  }

  /**
   * Удаление токена авторизации
   */
  clearAuthToken(): void {
    delete this.client.defaults.headers.common['Authorization'];
  }
}

// ============================================
// ПРИМЕРЫ ИСПОЛЬЗОВАНИЯ
// ============================================

/**
 * Пример 1: Базовое использование
 */
async function example1() {
  const api = new APIClient({
    baseURL: 'https://jsonplaceholder.typicode.com'
  });

  try {
    // GET запрос
    const users = await api.get('/users');
    console.log('Users:', users.data);

    // POST запрос
    const newUser = await api.post('/users', {
      name: 'John Doe',
      email: 'john@example.com'
    });
    console.log('Created user:', newUser.data);
  } catch (error) {
    console.error('Error:', error);
  }
}

/**
 * Пример 2: С авторизацией
 */
async function example2() {
  const api = new APIClient({
    baseURL: 'https://api.example.com',
    timeout: 10000,
    retries: 5
  });

  // Установка токена
  api.setAuthToken('your-jwt-token-here');

  try {
    const profile = await api.get('/user/profile');
    console.log('Profile:', profile.data);
  } catch (error) {
    console.error('Auth error:', error);
  }
}

/**
 * Пример 3: Типизированные запросы
 */
interface User {
  id: number;
  name: string;
  email: string;
}

async function example3() {
  const api = new APIClient({
    baseURL: 'https://api.example.com'
  });

  const response = await api.get<User[]>('/users');
  const users: User[] = response.data;

  users.forEach(user => {
    console.log(`${user.name} <${user.email}>`);
  });
}

// Раскомментируйте для запуска примеров
// example1();
// example2();
// example3();

export default APIClient;
```

### Лучшие практики для кода

✅ **DO**:
- Добавляйте подробные docstrings/JSDoc
- Включайте примеры использования
- Пишите комментарии для сложной логики
- Добавляйте type hints/types
- Включайте тесты в файл
- Обрабатывайте ошибки
- Следуйте стилю языка (PEP 8, ESLint)

❌ **DON'T**:
- Не оставляйте закомментированный код
- Не используйте магические числа
- Не пишите функции длиннее 50 строк
- Не игнорируйте ошибки
- Не забывайте про edge cases

---

## 🔀 Гибридные форматы

### Markdown + YAML Frontmatter

Комбинация читаемости Markdown и структурированности YAML:

```markdown
---
type: "instruction"
category: "coding/python"
title: "Создание REST API с FastAPI"
difficulty: "intermediate"
duration_minutes: 60
prerequisites:
  - Python 3.9+
  - pip
  - Basic REST knowledge
tags:
  - python
  - fastapi
  - rest-api
  - async
version: "1.2.0"
author: "AI Assistant"
created: "2026-01-15"
updated: "2026-02-09"
related_files:
  - "examples/fastapi-example.py"
  - "templates/api-template.py"
tools_required:
  - Read
  - Write
  - Bash
success_criteria:
  - API responds on localhost:8000
  - All endpoints return valid JSON
  - Tests pass with 100% coverage
---

# Создание REST API с FastAPI

## Обзор

В этой инструкции мы создадим полноценный REST API с использованием FastAPI...

[Остальное содержимое в Markdown]
```

### Markdown + JSON Metadata

```markdown
<!-- metadata
{
  "id": "inst-001",
  "type": "instruction",
  "tracking": {
    "created": "2026-02-09",
    "views": 0,
    "success_rate": null
  }
}
-->

# Инструкция...
```

---

## 🎯 Рекомендации по выбору формата

### Выбирайте Markdown если:
- Нужна человеко-читаемая документация
- Содержимое в основном текстовое
- Нужны примеры с подсветкой кода
- Документ будет редактироваться вручную

### Выбирайте YAML если:
- Нужна конфигурация
- Требуется строгая структура
- Файл парсится программой
- Нужны комментарии в конфиге

### Выбирайте JSON если:
- Данные для API или программной обработки
- Нужна валидация по схеме
- Требуется максимальная совместимость
- Файл генерируется автоматически

### Выбирайте исходный код если:
- Примеры для копирования
- Шаблоны проектов
- Утилиты и инструменты
- Тестовые кейсы

### Выбирайте гибридный формат если:
- Нужны и метаданные и описание
- Требуется автоматическая обработка + читаемость
- Сложная структура документа

---

## 📋 Чеклист создания документа

- [ ] Выбран правильный формат
- [ ] Добавлены метаданные (version, date, author)
- [ ] Структура соответствует стандарту
- [ ] Добавлены примеры использования
- [ ] Код проверен и работает
- [ ] Добавлены комментарии где нужно
- [ ] Документ отформатирован
- [ ] Проверена орфография
- [ ] Добавлены теги для поиска
- [ ] Файл сохранен в правильную категорию

---

**Создано**: 2026-02-09
**Версия**: 1.0.0
**Статус**: Active

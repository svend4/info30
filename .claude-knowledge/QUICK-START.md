# 🚀 Quick Start Guide
# Быстрый старт с Claude Knowledge System

**Время на изучение**: 15 минут
**Уровень**: Beginner

---

## 📌 Что это?

**Claude Knowledge System** - это RAG-подобная система хранения и организации дополнительных знаний, инструкций и возможностей для Claude Code.

**Зачем это нужно?**
- 📚 Хранение инструкций и best practices
- 🤖 Создание кастомных агентов
- 🎯 Расширение функциональности через скиллы
- 💾 Персистентная база знаний
- 🔄 Переиспользование решений

---

## ⚡ Быстрый старт (5 минут)

### Шаг 1: Изучите структуру (1 мин)

```bash
cd .claude-knowledge

# Основные директории:
tree -L 2
```

```
.claude-knowledge/
├── README.md                    # Главная документация
├── QUICK-START.md              # Этот файл
├── instructions/               # Инструкции
│   ├── coding/
│   ├── deployment/
│   └── testing/
├── agents/                     # Агенты
│   └── custom-agents/
├── skills/                     # Скиллы
│   └── custom-skills/
├── templates/                  # Шаблоны
├── examples/                   # Примеры
├── integrations/              # Интеграции
└── documentation/             # Доки
```

### Шаг 2: Прочитайте пример (2 мин)

```bash
# Пример инструкции
cat instructions/deployment/docker-setup.md

# Пример агента
cat agents/custom-agents/code-reviewer.yml

# Пример скилла
cat skills/custom-skills/smart-refactor.md
```

### Шаг 3: Используйте в работе (2 мин)

**Вариант A: Прямое использование**

```
User: "Покажи инструкцию по настройке Docker"

Claude: [Читает .claude-knowledge/instructions/deployment/docker-setup.md]
```

**Вариант B: Через memory**

Добавьте в `~/.claude/projects/[project]/memory/MEMORY.md`:

```markdown
## Knowledge Base Links
- Docker setup: .claude-knowledge/instructions/deployment/docker-setup.md
- Code review: .claude-knowledge/agents/custom-agents/code-reviewer.yml
```

**Вариант C: MCP интеграция**

```json
// ~/.config/claude/mcp-config.json
{
  "mcpServers": {
    "knowledge-base": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem",
               "/path/to/.claude-knowledge"]
    }
  }
}
```

---

## 📖 Основные сценарии использования

### Сценарий 1: Создание новой инструкции

```bash
# 1. Выберите категорию
cd .claude-knowledge/instructions/coding/

# 2. Создайте файл
touch rest-api-best-practices.md

# 3. Используйте шаблон
cat > rest-api-best-practices.md << 'EOF'
---
type: "instruction"
category: "coding"
title: "REST API Best Practices"
difficulty: "intermediate"
tags: ["api", "rest", "backend"]
---

# REST API Best Practices

## Описание
...

## Пошаговая инструкция
...

EOF

# 4. Claude теперь может это использовать!
```

### Сценарий 2: Создание кастомного агента

```bash
# 1. Перейдите в директорию агентов
cd .claude-knowledge/agents/custom-agents/

# 2. Создайте YAML конфигурацию
cat > my-agent.yml << 'EOF'
---
agent:
  name: "my-custom-agent"
  version: "1.0.0"
  description: "Описание агента"

capabilities:
  - analyze_code
  - generate_tests

parameters:
  file_path:
    type: string
    required: true

prompts:
  system: |
    Ты - эксперт по [задача].
    Твоя цель - [цель].

  task_template: |
    Выполни задачу:
    1. Шаг 1
    2. Шаг 2

workflow:
  - step: "analyze"
    tools: [Read, Grep]
  - step: "generate"
    tools: [Write]
EOF
```

### Сценарий 3: Создание кастомного скилла

```markdown
# File: skills/custom-skills/my-skill.md

# Skill: My Custom Skill

**Type**: command
**Trigger**: /my-skill

## Описание
Что делает этот скилл

## Параметры
- param1: описание

## Промпт
```
Выполни задачу ${param1}:
1. Шаг 1
2. Шаг 2
\```

## Примеры
```bash
/my-skill value1
```
```

---

## 🎯 Практические примеры

### Пример 1: Использование инструкций

```
User: "Мне нужно настроить Docker для моего Node.js проекта"

Claude: "Я найду инструкцию по настройке Docker..."
[Читает .claude-knowledge/instructions/deployment/docker-setup.md]
[Следует шагам из инструкции]

Result:
✅ Создан Dockerfile
✅ Создан docker-compose.yml
✅ Настроен .dockerignore
✅ Приложение запущено в контейнере
```

### Пример 2: Запуск агента

```
User: "Проверь качество кода в src/api/auth.js"

Claude: "Запускаю code-reviewer агент..."
[Загружает конфигурацию из agents/custom-agents/code-reviewer.yml]
[Выполняет анализ согласно workflow]

Result:
📊 Code Review Report:
- Found 3 issues
- Critical: 1 (SQL injection risk)
- High: 2 (Missing error handling)
+ Suggestions provided with code examples
```

### Пример 3: Использование скилла

```
User: "/refactor src/utils.py --type=simplify"

Claude: [Загружает скилл smart-refactor.md]
[Выполняет рефакторинг]

Result:
✨ Refactoring completed:
- Simplified 3 complex functions
- Reduced cyclomatic complexity by 40%
- Improved readability score
```

---

## 🔧 Настройка под ваш проект

### 1. Скопируйте систему в ваш проект

```bash
# Вариант A: Клонирование структуры
cp -r /path/to/.claude-knowledge /your/project/.claude-knowledge

# Вариант B: Создание симлинка (для использования в нескольких проектах)
ln -s /path/to/.claude-knowledge /your/project/.claude-knowledge

# Вариант C: Git submodule
cd /your/project
git submodule add <repo-url> .claude-knowledge
```

### 2. Настройте MCP (опционально)

```bash
# Создайте конфиг
mkdir -p ~/.config/claude
cat > ~/.config/claude/mcp-config.json << 'EOF'
{
  "mcpServers": {
    "project-knowledge": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/your/project/.claude-knowledge"
      ],
      "enabled": true
    }
  }
}
EOF
```

### 3. Добавьте в memory

```bash
# Откройте memory файл
nano ~/.claude/projects/your-project/memory/MEMORY.md

# Добавьте ссылки на знания
cat >> ~/.claude/projects/your-project/memory/MEMORY.md << 'EOF'

## Project Knowledge Base
Located at: .claude-knowledge/

Key resources:
- Setup guides: .claude-knowledge/instructions/deployment/
- Code standards: .claude-knowledge/instructions/best-practices/
- Custom agents: .claude-knowledge/agents/custom-agents/
EOF
```

### 4. Добавьте свои инструкции

```bash
# Создайте инструкцию специфичную для вашего проекта
cd /your/project/.claude-knowledge/instructions/

mkdir project-specific
cd project-specific

cat > setup-dev-environment.md << 'EOF'
---
type: "instruction"
category: "project-specific"
title: "Настройка окружения для [ваш проект]"
---

# Настройка окружения

## Требования
- Node.js 18+
- PostgreSQL 15+
- Redis

## Шаги
1. Клонировать репозиторий
2. Установить зависимости: npm install
3. Настроить .env файл
4. Запустить миграции: npm run migrate
5. Запустить сервер: npm run dev

## Проверка
- [ ] Сервер запущен на localhost:3000
- [ ] База данных подключена
- [ ] Тесты проходят
EOF
```

---

## 📚 Дальнейшее изучение

### Рекомендуемый порядок

1. **Основы** (30 мин)
   - [ ] README.md - главная документация
   - [ ] documentation/methodology/knowledge-formats.md - форматы

2. **Практика** (1 час)
   - [ ] Создайте свою первую инструкцию
   - [ ] Настройте кастомного агента
   - [ ] Создайте простой скилл

3. **Продвинутое** (2 часа)
   - [ ] documentation/guides/mcp-integration.md - MCP интеграция
   - [ ] Создайте кастомный MCP сервер
   - [ ] Настройте автоматизацию

### Полезные ссылки

- 📖 [Главный README](.claude-knowledge/README.md)
- 📝 [Форматы хранения](documentation/methodology/knowledge-formats.md)
- 🔌 [MCP интеграция](documentation/guides/mcp-integration.md)
- 💡 [Примеры](examples/use-cases/)

---

## ❓ FAQ

### Q: Где хранить секреты?

**A**: НЕ в knowledge base! Используйте:
- Environment variables
- Секретные менеджеры (1Password, Vault)
- .env файлы (добавить в .gitignore)

### Q: Можно ли использовать в нескольких проектах?

**A**: Да! Есть несколько вариантов:
1. Общая knowledge base через симлинк
2. MCP сервер для доступа из разных проектов
3. Git submodule для версионирования

### Q: Как часто обновлять?

**A**: Рекомендации:
- Инструкции: при изменении процессов
- Агенты: при добавлении новых возможностей
- Примеры: при появлении лучших решений
- Документация: минимум раз в квартал

### Q: Нужно ли версионировать в Git?

**A**: Да, рекомендуется:
```bash
cd .claude-knowledge
git init
git add .
git commit -m "Initialize knowledge base"
```

### Q: Как искать в knowledge base?

**A**: Варианты:
1. Прямое упоминание: "Найди инструкцию по Docker"
2. MCP search: автоматический поиск
3. Grep: `grep -r "keyword" .claude-knowledge/`
4. IDE: поиск по файлам

---

## ✅ Чеклист готовности

- [ ] Структура .claude-knowledge создана
- [ ] Прочитана основная документация
- [ ] Создана первая инструкция
- [ ] Настроен доступ (memory или MCP)
- [ ] Проверено использование Claude
- [ ] Добавлено в Git (если нужно)
- [ ] Настроена автоматизация (опционально)

---

## 🎉 Готово!

Теперь у вас есть мощная система расширения возможностей Claude Code!

**Следующие шаги:**
1. Начните добавлять свои инструкции
2. Экспериментируйте с агентами
3. Создавайте кастомные скиллы
4. Делитесь лучшими практиками

**Нужна помощь?**
- Смотрите примеры в `examples/`
- Читайте документацию в `documentation/`
- Задавайте вопросы Claude

---

**Версия**: 1.0.0
**Дата**: 2026-02-09
**Статус**: Ready to use

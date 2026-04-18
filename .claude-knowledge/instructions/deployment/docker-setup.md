---
type: "instruction"
category: "deployment"
title: "Настройка Docker для проекта"
difficulty: "beginner"
duration_minutes: 30
prerequisites:
  - Docker установлен
  - Базовое понимание контейнеров
tags:
  - docker
  - deployment
  - containerization
  - devops
version: "1.0.0"
author: "Claude Knowledge System"
created: "2026-02-09"
updated: "2026-02-09"
related_files:
  - "templates/code-templates/Dockerfile.template"
  - "examples/working-examples/docker-compose-example.yml"
tools_required:
  - Bash
  - Write
  - Read
success_criteria:
  - Docker контейнер успешно собран
  - Приложение запускается в контейнере
  - Порты корректно проброшены
---

# Настройка Docker для проекта

**Категория**: Deployment
**Уровень сложности**: Beginner
**Время выполнения**: ~30 минут
**Теги**: #docker #deployment #containerization

## 📋 Описание

Пошаговое руководство по настройке Docker для вашего проекта. Мы создадим Dockerfile, docker-compose.yml и настроим окружение для разработки и production.

## 🎯 Что вы получите

- Готовый Dockerfile для вашего проекта
- docker-compose.yml для локальной разработки
- .dockerignore для оптимизации образа
- Скрипты для быстрого запуска

## 📚 Предварительные требования

- [ ] Docker установлен и запущен
- [ ] Docker Compose установлен (v2.0+)
- [ ] Базовое понимание контейнеризации
- [ ] Проект с package.json или requirements.txt

## 🚀 Пошаговая инструкция

### Шаг 1: Создание Dockerfile

Создайте файл `Dockerfile` в корне проекта:

**Для Node.js проекта:**

```dockerfile
# Используем официальный Node.js образ
FROM node:18-alpine AS base

# Устанавливаем рабочую директорию
WORKDIR /app

# Копируем файлы зависимостей
COPY package*.json ./

# Стадия для зависимостей
FROM base AS dependencies
RUN npm ci --only=production

# Стадия для разработки
FROM base AS development
RUN npm ci
COPY . .
CMD ["npm", "run", "dev"]

# Стадия для production build
FROM base AS build
RUN npm ci
COPY . .
RUN npm run build

# Финальная стадия для production
FROM base AS production
COPY --from=dependencies /app/node_modules ./node_modules
COPY --from=build /app/dist ./dist
COPY package.json ./

# Создаем non-root пользователя
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

USER nodejs

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

**Для Python проекта:**

```dockerfile
# Используем официальный Python образ
FROM python:3.11-slim AS base

# Устанавливаем рабочую директорию
WORKDIR /app

# Устанавливаем системные зависимости
RUN apt-get update && apt-get install -y \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Копируем файлы зависимостей
COPY requirements.txt .

# Стадия для установки зависимостей
FROM base AS dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Финальная стадия
FROM base AS production
COPY --from=dependencies /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY . .

# Создаем non-root пользователя
RUN useradd -m -u 1001 appuser && \
    chown -R appuser:appuser /app

USER appuser

EXPOSE 8000

CMD ["python", "main.py"]
```

### Шаг 2: Создание .dockerignore

Создайте файл `.dockerignore` для исключения ненужных файлов:

```
# Зависимости
node_modules/
__pycache__/
*.pyc
venv/
.venv/

# Git
.git/
.gitignore

# IDE
.vscode/
.idea/
*.swp
*.swo

# Логи
*.log
logs/

# Тесты
coverage/
.pytest_cache/

# Документация
*.md
docs/

# CI/CD
.github/
.gitlab-ci.yml

# Environment
.env
.env.local

# Build артефакты
dist/
build/
*.egg-info/

# OS
.DS_Store
Thumbs.db
```

### Шаг 3: Создание docker-compose.yml

Создайте `docker-compose.yml` для локальной разработки:

```yaml
version: '3.9'

services:
  app:
    build:
      context: .
      target: development
    container_name: my-app-dev
    ports:
      - "3000:3000"
    volumes:
      # Монтируем код для hot-reload
      - .:/app
      # Исключаем node_modules
      - /app/node_modules
    environment:
      - NODE_ENV=development
      - PORT=3000
    env_file:
      - .env.development
    command: npm run dev
    restart: unless-stopped

  # База данных (если нужна)
  db:
    image: postgres:15-alpine
    container_name: my-app-db
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped

  # Redis (если нужен)
  redis:
    image: redis:7-alpine
    container_name: my-app-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:
```

### Шаг 4: Создание скриптов управления

Создайте файл `docker-dev.sh` для быстрого управления:

```bash
#!/bin/bash

# Скрипт для управления Docker окружением

set -e

case "$1" in
  start)
    echo "🚀 Запуск Docker контейнеров..."
    docker-compose up -d
    echo "✅ Контейнеры запущены"
    echo "📊 Приложение доступно на http://localhost:3000"
    ;;

  stop)
    echo "🛑 Остановка контейнеров..."
    docker-compose down
    echo "✅ Контейнеры остановлены"
    ;;

  restart)
    echo "🔄 Перезапуск контейнеров..."
    docker-compose restart
    echo "✅ Контейнеры перезапущены"
    ;;

  logs)
    docker-compose logs -f "${2:-app}"
    ;;

  shell)
    docker-compose exec app /bin/sh
    ;;

  build)
    echo "🔨 Сборка образа..."
    docker-compose build --no-cache
    echo "✅ Образ собран"
    ;;

  clean)
    echo "🧹 Очистка..."
    docker-compose down -v
    docker system prune -f
    echo "✅ Очистка завершена"
    ;;

  *)
    echo "Использование: $0 {start|stop|restart|logs|shell|build|clean}"
    echo ""
    echo "Команды:"
    echo "  start   - Запустить контейнеры"
    echo "  stop    - Остановить контейнеры"
    echo "  restart - Перезапустить контейнеры"
    echo "  logs    - Показать логи (logs app/db/redis)"
    echo "  shell   - Открыть shell в контейнере"
    echo "  build   - Пересобрать образ"
    echo "  clean   - Удалить контейнеры и volumes"
    exit 1
    ;;
esac
```

Сделайте скрипт исполняемым:

```bash
chmod +x docker-dev.sh
```

### Шаг 5: Создание production docker-compose

Создайте `docker-compose.prod.yml`:

```yaml
version: '3.9'

services:
  app:
    build:
      context: .
      target: production
    container_name: my-app-prod
    ports:
      - "80:3000"
    environment:
      - NODE_ENV=production
      - PORT=3000
    env_file:
      - .env.production
    restart: always
    healthcheck:
      test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  db:
    image: postgres:15-alpine
    container_name: my-app-db-prod
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=${DB_USER}
      - POSTGRES_PASSWORD=${DB_PASSWORD}
    volumes:
      - postgres_prod_data:/var/lib/postgresql/data
    restart: always

volumes:
  postgres_prod_data:
```

### Шаг 6: Тестирование

Проверьте что всё работает:

```bash
# 1. Сборка образа
docker build -t my-app:latest .

# 2. Запуск контейнера
docker run -p 3000:3000 my-app:latest

# 3. Проверка работы
curl http://localhost:3000

# 4. Или используйте docker-compose
./docker-dev.sh start

# 5. Проверьте логи
./docker-dev.sh logs

# 6. Откройте shell для отладки
./docker-dev.sh shell
```

## ✅ Результат

После выполнения всех шагов у вас будет:

- ✅ Оптимизированный Dockerfile с multi-stage build
- ✅ docker-compose.yml для разработки
- ✅ docker-compose.prod.yml для production
- ✅ .dockerignore для уменьшения размера образа
- ✅ Удобные скрипты для управления
- ✅ Настроенный hot-reload для разработки

## 📊 Проверка

- [ ] Образ успешно собирается
- [ ] Контейнер запускается без ошибок
- [ ] Приложение доступно на localhost:3000
- [ ] Hot-reload работает при изменении кода
- [ ] Логи отображаются корректно
- [ ] База данных подключается
- [ ] Health check проходит успешно

## ⚠️ Возможные проблемы и решения

### Проблема 1: Образ слишком большой

**Симптомы**: Размер образа >1GB

**Решение**:
- Используйте alpine базовые образы
- Применяйте multi-stage builds
- Очищайте кеш пакетных менеджеров
- Проверьте .dockerignore

```bash
# Посмотреть размер образа
docker images my-app

# Анализ слоев
docker history my-app:latest
```

### Проблема 2: Медленная сборка

**Симптомы**: Сборка занимает >5 минут

**Решение**:
- Используйте Docker BuildKit
- Оптимизируйте порядок инструкций
- Кешируйте зависимости

```bash
# Включить BuildKit
export DOCKER_BUILDKIT=1
docker build -t my-app:latest .
```

### Проблема 3: Контейнер не запускается

**Симптомы**: Контейнер сразу останавливается

**Решение**:
```bash
# Посмотреть логи
docker logs my-app-dev

# Запустить в интерактивном режиме
docker run -it my-app:latest /bin/sh

# Проверить CMD в Dockerfile
```

### Проблема 4: Hot-reload не работает

**Симптомы**: Изменения кода не применяются

**Решение**:
- Проверьте volume монтирование в docker-compose.yml
- Убедитесь что используете target: development
- Проверьте что dev-сервер настроен на hot-reload

## 🔧 Оптимизация

### Уменьшение размера образа

```dockerfile
# Используйте alpine образы
FROM node:18-alpine

# Удаляйте кеш после установки
RUN npm ci && npm cache clean --force

# Используйте .dockerignore
```

### Ускорение сборки

```dockerfile
# Копируйте зависимости отдельно
COPY package*.json ./
RUN npm ci

# Только потом копируйте код
COPY . .
```

### Безопасность

```dockerfile
# Не используйте root
USER nodejs

# Сканируйте уязвимости
RUN npm audit fix
```

## 📚 Дополнительные ресурсы

- [Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Multi-stage Builds](https://docs.docker.com/build/building/multi-stage/)
- [Docker Security](https://docs.docker.com/engine/security/)

## 📝 Примечания

- Для production используйте `docker-compose.prod.yml`
- Регулярно обновляйте базовые образы
- Используйте конкретные версии образов (не latest)
- Настройте CI/CD для автоматической сборки
- Храните секреты в переменных окружения, не в образе

## 🎓 Следующие шаги

1. Настройте CI/CD для автоматической сборки
2. Добавьте мониторинг контейнеров
3. Настройте логирование в централизованную систему
4. Изучите Kubernetes для оркестрации

---

**Версия**: 1.0.0
**Последнее обновление**: 2026-02-09
**Статус**: Проверено и работает

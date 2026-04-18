# Skill: Smart Refactor

**Type**: automation
**Trigger**: `/refactor` или автоматический
**Version**: 1.1.0
**Author**: Claude Knowledge System
**Created**: 2026-02-05
**Updated**: 2026-02-09

---

## 📋 Описание

Умный скилл для автоматического рефакторинга кода. Анализирует код, находит возможности для улучшения и применяет рефакторинг с сохранением функциональности.

### Возможности

- 🔍 Автоматическое обнаружение code smells
- 🔄 Применение паттернов рефакторинга
- ✅ Сохранение тестов и функциональности
- 📝 Генерация отчета об изменениях
- 🎯 Поддержка нескольких языков

---

## 🎯 Параметры

### Обязательные

- `target` (string): Файл или директория для рефакторинга
  - Примеры: `src/utils.py`, `src/components/`

### Опциональные

- `type` (string): Тип рефакторинга
  - `auto` (default): Автоматически выбрать подходящие рефакторинги
  - `extract-function`: Извлечение функций из длинного кода
  - `rename`: Улучшение именования
  - `simplify`: Упрощение сложной логики
  - `remove-duplication`: Удаление дублирования
  - `optimize`: Оптимизация производительности

- `aggressiveness` (string): Уровень агрессивности изменений
  - `safe` (default): Только безопасные изменения
  - `moderate`: Умеренные изменения
  - `aggressive`: Значительные изменения

- `backup` (boolean): Создать резервную копию
  - `true` (default): Создать backup
  - `false`: Не создавать backup

- `dry-run` (boolean): Показать изменения без применения
  - `false` (default): Применить изменения
  - `true`: Только показать что будет изменено

---

## 💻 Использование

### Базовый вызов

```bash
/refactor src/main.py
```

### С параметрами

```bash
/refactor src/api/handlers.js --type=extract-function --aggressiveness=moderate
```

### Dry-run режим

```bash
/refactor src/ --dry-run=true
```

### Для всей директории

```bash
/refactor src/components/ --type=auto --backup=true
```

---

## 🤖 Промпт

```markdown
Выполни умный рефакторинг кода:

Цель: ${target}
Тип рефакторинга: ${type}
Уровень: ${aggressiveness}
Dry-run: ${dry_run}

ШАГИ:

1. АНАЛИЗ
   - Прочитай код в ${target}
   - Определи язык программирования
   - Найди code smells и проблемы
   - Оцени сложность рефакторинга

2. ПЛАНИРОВАНИЕ
   - Выбери подходящие паттерны рефакторинга
   - Определи последовательность изменений
   - Оцени риски каждого изменения
   - Создай план рефакторинга

3. ПРОВЕРКА ТЕСТОВ
   - Найди существующие тесты
   - Запусти тесты до рефакторинга
   - Сохрани baseline результатов

4. ПРИМЕНЕНИЕ РЕФАКТОРИНГА

   ${if type == 'extract-function'}
   - Найди длинные функции (>50 строк)
   - Определи логические блоки
   - Извлеки в отдельные функции
   - Используй понятные имена
   ${endif}

   ${if type == 'rename'}
   - Найди плохо названные переменные (a, tmp, data, etc)
   - Определи их назначение из контекста
   - Переименуй в описательные имена
   - Обнови все использования
   ${endif}

   ${if type == 'simplify'}
   - Найди сложную вложенную логику
   - Упрости условия
   - Используй early returns
   - Разбей сложные выражения
   ${endif}

   ${if type == 'remove-duplication'}
   - Найди дублированный код
   - Извлеки в общие функции
   - Используй DRY принцип
   - Создай переиспользуемые компоненты
   ${endif}

   ${if type == 'optimize'}
   - Найди неэффективные алгоритмы
   - Оптимизируй циклы
   - Улучши структуры данных
   - Добавь кеширование где нужно
   ${endif}

   ${if type == 'auto'}
   - Примени все подходящие рефакторинги
   - Начни с самых безопасных
   - Постепенно применяй более сложные
   ${endif}

5. ВАЛИДАЦИЯ
   - Запусти тесты после рефакторинга
   - Сравни с baseline
   - Убедись что функциональность сохранена
   - Проверь что код стал лучше

6. ОТЧЕТ
   - Перечисли все изменения
   - Укажи метрики до/после
   - Покажи улучшения
   - Добавь рекомендации

ПРАВИЛА:

- ✅ Сохраняй функциональность
- ✅ Улучшай читаемость
- ✅ Следуй стандартам языка
- ✅ Документируй изменения
- ❌ Не меняй публичные API без необходимости
- ❌ Не удаляй тесты
- ❌ Не добавляй новые зависимости без согласования

${if backup == true}
СОЗДАЙ РЕЗЕРВНУЮ КОПИЮ перед изменениями:
- Скопируй ${target} в ${target}.backup.${timestamp}
${endif}

${if dry_run == true}
РЕЖИМ DRY-RUN:
- НЕ применяй изменения
- Только покажи что будет изменено
- Выведи diff предполагаемых изменений
${endif}

ФОРМАТ ОТЧЕТА:

# Refactoring Report: ${target}

## Summary
- Files analyzed: X
- Changes proposed: Y
- Improvements: Z

## Metrics Before/After

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Lines of Code | X | Y | -Z% |
| Cyclomatic Complexity | X | Y | -Z% |
| Function Length (avg) | X | Y | -Z% |
| Code Duplication | X% | Y% | -Z% |

## Changes Applied

### 1. [Type]: [Description]
**File**: path/to/file
**Lines**: 45-78

Before:
```language
[old code]
```

After:
```language
[new code]
```

**Reason**: [explanation]
**Impact**: [positive effects]

## Test Results

- ✅ All tests passed
- Coverage: X% → Y%

## Recommendations

[Additional suggestions]

---
Refactored by Smart Refactor v${version}
```

---

## 💡 Примеры

### Пример 1: Извлечение функций

**Input:**
```bash
/refactor src/payment.py --type=extract-function
```

**Анализ кода:**
```python
# Before
def process_payment(user_id, amount, card_number):
    # Валидация (15 строк)
    if not user_id:
        raise ValueError("User ID required")
    # ... много проверок ...

    # Обработка платежа (20 строк)
    api_url = "https://payment-api.com"
    headers = {"Authorization": f"Bearer {API_KEY}"}
    # ... много кода ...

    # Сохранение (10 строк)
    db.save_transaction(...)
    # ... много кода ...
```

**Output:**
```python
# After
def process_payment(user_id, amount, card_number):
    validate_payment_data(user_id, amount, card_number)
    transaction = execute_payment(amount, card_number)
    save_transaction(user_id, transaction)
    return transaction

def validate_payment_data(user_id, amount, card_number):
    """Валидация данных платежа"""
    if not user_id:
        raise ValueError("User ID required")
    # ... проверки ...

def execute_payment(amount, card_number):
    """Выполнение платежа через API"""
    api_url = "https://payment-api.com"
    # ... логика платежа ...
    return transaction

def save_transaction(user_id, transaction):
    """Сохранение транзакции в БД"""
    db.save_transaction(...)
```

### Пример 2: Упрощение условий

**Input:**
```bash
/refactor src/auth.js --type=simplify
```

**Before:**
```javascript
function checkAccess(user, resource) {
  if (user) {
    if (user.isActive) {
      if (user.role === 'admin') {
        return true;
      } else {
        if (resource.isPublic) {
          return true;
        } else {
          if (user.permissions.includes(resource.id)) {
            return true;
          } else {
            return false;
          }
        }
      }
    } else {
      return false;
    }
  } else {
    return false;
  }
}
```

**After:**
```javascript
function checkAccess(user, resource) {
  // Early returns для упрощения логики
  if (!user || !user.isActive) {
    return false;
  }

  if (user.role === 'admin') {
    return true;
  }

  return resource.isPublic || user.permissions.includes(resource.id);
}
```

### Пример 3: Удаление дублирования

**Input:**
```bash
/refactor src/api/ --type=remove-duplication
```

**Before:**
```python
def get_user(user_id):
    headers = {"Authorization": f"Bearer {token}"}
    response = requests.get(f"{API_URL}/users/{user_id}", headers=headers)
    if response.status_code != 200:
        raise APIError(response.text)
    return response.json()

def get_post(post_id):
    headers = {"Authorization": f"Bearer {token}"}
    response = requests.get(f"{API_URL}/posts/{post_id}", headers=headers)
    if response.status_code != 200:
        raise APIError(response.text)
    return response.json()

def get_comment(comment_id):
    headers = {"Authorization": f"Bearer {token}"}
    response = requests.get(f"{API_URL}/comments/{comment_id}", headers=headers)
    if response.status_code != 200:
        raise APIError(response.text)
    return response.json()
```

**After:**
```python
def _api_get(endpoint):
    """Общая функция для GET запросов к API"""
    headers = {"Authorization": f"Bearer {token}"}
    response = requests.get(f"{API_URL}/{endpoint}", headers=headers)
    if response.status_code != 200:
        raise APIError(response.text)
    return response.json()

def get_user(user_id):
    return _api_get(f"users/{user_id}")

def get_post(post_id):
    return _api_get(f"posts/{post_id}")

def get_comment(comment_id):
    return _api_get(f"comments/{comment_id}")
```

### Пример 4: Dry-run

**Input:**
```bash
/refactor src/utils.py --type=auto --dry-run=true
```

**Output:**
```diff
# Proposed changes for src/utils.py

## Change 1: Extract function 'validate_email'
@@ -45,12 +45,7 @@
 def register_user(email, password):
-    # Email validation
-    if not email or '@' not in email:
-        raise ValueError("Invalid email")
-    if not email.split('@')[1]:
-        raise ValueError("Invalid email domain")
+    validate_email(email)

## Change 2: Rename variable 'tmp' to 'sanitized_input'
@@ -78,5 +78,5 @@
-    tmp = input.strip().lower()
-    return tmp
+    sanitized_input = input.strip().lower()
+    return sanitized_input

Apply these changes? (y/n)
```

---

## 🔗 Интеграция с Claude Code

### Автоматический триггер

```yaml
integration:
  auto_trigger:
    # Срабатывает при коммите если сложность высокая
    on_commit:
      condition: "complexity > 10"
      action: "suggest_refactoring"

    # Срабатывает при открытии PR
    on_pr:
      condition: "code_smells > 5"
      action: "run_refactoring_analysis"
```

### Keyboard Shortcuts

- `Ctrl+Shift+R`: Запустить рефакторинг текущего файла
- `Ctrl+Alt+R`: Показать предложения по рефакторингу

---

## ✅ Проверочный список

Перед применением рефакторинга:

- [ ] Все тесты проходят
- [ ] Создана резервная копия
- [ ] Code review выполнен
- [ ] Изменения протестированы

После рефакторинга:

- [ ] Все тесты всё ещё проходят
- [ ] Код стал читабельнее
- [ ] Сложность уменьшилась
- [ ] Документация обновлена

---

## 🎯 Метрики успеха

Рефакторинг считается успешным если:

- ✅ Все тесты проходят
- ✅ Cyclomatic complexity снизилась на >20%
- ✅ Средняя длина функций <30 строк
- ✅ Дублирование кода <5%
- ✅ Код проще для понимания

---

## ⚠️ Предупреждения

- **Backup**: Всегда создавайте резервные копии
- **Tests**: Запускайте тесты до и после
- **Review**: Проверяйте изменения перед применением
- **API**: Будьте осторожны с публичными API
- **Dependencies**: Не добавляйте новые зависимости

---

## 📚 Связанные ресурсы

- [Инструкция по рефакторингу](../../instructions/best-practices/refactoring-guide.md)
- [Code Reviewer Agent](../../agents/custom-agents/code-reviewer.yml)
- [Паттерны рефакторинга](../../documentation/references/refactoring-patterns.md)

---

## 🔄 История версий

### v1.1.0 (2026-02-09)
- Добавлен dry-run режим
- Улучшена поддержка TypeScript
- Добавлены метрики качества

### v1.0.0 (2026-02-05)
- Первый релиз
- Базовые типы рефакторинга
- Поддержка Python и JavaScript

---

**Version**: 1.1.0
**Status**: Production Ready
**Maintenance**: Active

# Conventional Commits --- Шпаргалка

## Что такое Conventional Commits

Стандарт для оформления сообщений коммитов, который делает историю
проекта понятнее, удобнее для ревью и позволяет автоматически
формировать CHANGELOG.

Формат сообщения:

    <type>[optional scope]: <description>

    [optional body]

    [optional footer(s)]

## Основные типы коммитов

| Тип          | Значение                                         |
| ------------ | ------------------------------------------------ |
| **feat**     | Новая функциональность                           |
| **fix**      | Исправление ошибки                               |
| **docs**     | Документация                                     |
| **style**    | Форматирование без изменения логики              |
| **refactor** | Улучшение структуры кода без изменения поведения |
| **perf**     | Оптимизация производительности                   |
| **test**     | Добавление/обновление тестов                     |
| **build**    | Изменения в сборке                               |
| **ci**       | Изменения CI/CD                                  |
| **chore**    | Поддерживающие изменения                         |
| **revert**   | Откат изменений                                  |
## Правила хорошего коммита

1.  Короткое и ёмкое описание (до \~80 символов).
2.  Глагол в повелительном наклонении.
3.  Тело коммита - если требуется объяснение *почему*.
4.  Footer - для issues, BREAKING CHANGE и прочего.

## Примеры правильных коммитов

### Добавление функциональности

    feat: add 2ms jitter measurement loop

### Исправление багов

    fix: correct timerfd absolute timeout handling

### Документация

    docs: add explanation for ppoll atomic signal unblocking

### Форматирование

    style: unify indentation in sched_fifo_jitter.c

### Рефакторинг

    refactor: extract jitter stats computation into separate function

### Оптимизация

    perf: reduce latency spikes using CPU affinity pinning

### Откат

    revert: restore previous polling timeout implementation

## Шаблон сообщения коммита

    <type>(optional-scope): short description

    Optional body:
    Provide context, reasoning, or details if needed.

    Footer:
    BREAKING CHANGE: ...
    Closes #123

## Быстрые подсказки

-   Добавил → **feat**
-   Починил → **fix**
-   Изменил доки → **docs**
-   Переставил/почистил код → **refactor**
-   Ускорил → **perf**
-   Изменил инфраструктуру → **chore**

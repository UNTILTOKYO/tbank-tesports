---
title: REST API
sidebar_position: 1
---

# REST API — T-Esports

Base URL: `https://api.t-esports.ru/v1`

Аутентификация: `Bearer JWT`

---

## Турниры

### GET /tournaments

Получить список турниров.

**Query параметры:**

| Параметр | Тип | Обязательный | Описание |
|---|---|---|---|
| status | string | Нет | Фильтр по статусу: `DRAFT`, `PUBLISHED` |

**Ответ 200:**

```json
[
  {
    "id": "string",
    "name": "string",
    "status": "PUBLISHED",
    "format": "ONLINE",
    "date": "2025-10-01",
    "region": "RUSSIA",
    "max_teams": 16,
    "discipline": "CS2",
    "organizer": {
      "id": "string",
      "display_name": "string"
    }
  }
]
```

---

### POST /tournaments

Создать турнир.

**Request Body:**

```json
{
  "name": "string",
  "format": "ONLINE",
  "date": "2025-10-01",
  "region": "RUSSIA",
  "max_teams": 16,
  "discipline": "CS2"
}
```

**Валидация:**
- `name` — от 3 до 100 символов, обязательное
- `format` — `ONLINE` или `LAN`
- `date` — дата не раньше текущей
- `max_teams` — целое число не менее 2
- `region` — `RUSSIA`, `EUROPE`, `AMERICA`

**Ответ 201:** Tournament object со статусом `DRAFT`

---

### GET /tournaments/:id

Получить турнир по ID.

**Path параметры:**

| Параметр | Тип | Описание |
|---|---|---|
| id | string | ID турнира |

**Ответ 200:** Tournament object

---

### PATCH /tournaments/:id/publish

Опубликовать турнир — изменить статус с `DRAFT` на `PUBLISHED`.

**Path параметры:**

| Параметр | Тип | Описание |
|---|---|---|
| id | string | ID турнира |

**Ответ 200:** Tournament object со статусом `PUBLISHED`

---

## Схемы

### Tournament

| Поле | Тип | Описание |
|---|---|---|
| id | string | Уникальный идентификатор |
| name | string | Название турнира |
| status | TournamentStatus | Статус турнира |
| format | string | `ONLINE` или `LAN` |
| date | string (date) | Дата проведения |
| region | string | `RUSSIA`, `EUROPE`, `AMERICA` |
| max_teams | integer | Максимальное количество команд |
| discipline | string | Игровая дисциплина |
| organizer | Organizer | Организатор турнира |

### Organizer

| Поле | Тип | Описание |
|---|---|---|
| id | string | Уникальный идентификатор |
| display_name | string | Отображаемое имя |

### TournamentStatus

| Значение | Описание |
|---|---|
| `DRAFT` | Черновик — не виден в каталоге |
| `PUBLISHED` | Опубликован — виден в каталоге |

---

## Коды ошибок

| Код | Описание |
|---|---|
| 400 | Ошибка валидации |
| 401 | Не авторизован |
| 403 | Недостаточно прав доступа |
| 404 | Ресурс не найден |
| 500 | Внутренняя ошибка сервера |

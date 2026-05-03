---
title: Архитектура системы
sidebar_position: 1
---

# Архитектура системы

## Технологический стек

| Слой | Технология |
|---|---|
| Backend | Node.js / Python (Django / FastAPI) |
| Frontend | React / Vue |
| База данных | PostgreSQL |
| Очередь сообщений | RabbitMQ (AMQP) |
| Дизайн и прототипы | Figma |
| Хостинг | Облачная инфраструктура (VPS) |
| Система контроля версий | Git |

## Ограничения MVP

**Технические:**
- Только веб-версия (без мобильного приложения)
- Только один формат турнира — Single Elimination
- Нет интеграции с игровыми API
- Нет встроенной платёжной системы
- Нет автоматического античита

**Бизнесовые:**
- Только любительский и полупрофессиональный сегмент
- Пилот только в одном регионе
- Ограниченное количество дисциплин
- Нет сложной рейтинговой системы

## Модель данных

### Сущности

**Tournament:** id, name, status, format, date, region, discipline, max_teams, organizer_id, created_at

**Organizer:** id, display_name, phone

**Team:** id, name, sponsor, tournament_id (через TournamentTeam)

**Player:** id, name, email, team_id

**Match:** id, tournament_id, date

**TournamentTeam:** tournament_id, team_id (промежуточная таблица N:N)

### Характер данных

Все сущности используют **OLTP** (транзакционные операции), **PostgreSQL** с жёсткой схемой и поддержкой транзакций. Выбор обоснован: малый объём данных, сильная консистентность, простые фильтры для поиска.

## Асинхронное взаимодействие

При публикации турнира система отправляет событие `TournamentPublished` в RabbitMQ. Подробнее — в разделе [AsyncAPI](./async-api).

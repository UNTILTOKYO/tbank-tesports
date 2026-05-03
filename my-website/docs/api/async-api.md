---
title: AsyncAPI — асинхронное взаимодействие
sidebar_position: 2
---

# AsyncAPI — асинхронное взаимодействие

Для MVP используется **RabbitMQ** (AMQP). Выбор обоснован необходимостью гарантии доставки сообщений. NATS не подходит из-за отсутствия гарантии доставки, Kafka избыточен для текущего масштаба.

## Спецификация

```yaml
asyncapi: 3.1.0
info:
  title: T-Esports api
  version: 1.0.0
  description: События T-Esports

servers:
  rabbit:
    host: 'localhost:5672'
    protocol: amqp

channels:
  tournament.published:
    address: tournament.published
    messages:
      TournamentPublished:
        $ref: '#/components/messages/TournamentPublished'

components:
  messages:
    TournamentPublished:
      name: TournamentPublished
      payload:
        $ref: '#/components/schemas/TournamentPublishedPayload'

  schemas:
    TournamentPublishedPayload:
      type: object
      required:
        - id
        - name
        - status
        - format
        - date
        - region
        - discipline
        - max_teams
        - organizer
      properties:
        id:
          type: string
        name:
          type: string
        status:
          type: string
          enum:
            - PUBLISHED
        format:
          type: string
          enum:
            - ONLINE
            - LAN
        date:
          type: string
          format: date
        region:
          type: string
        discipline:
          type: string
        max_teams:
          type: integer
        organizer:
          type: object
          required:
            - id
            - display_name
          properties:
            id:
              type: string
            display_name:
              type: string
```

## Событие TournamentPublished

Событие публикуется в очередь `tournament.published` когда организатор публикует турнир. Консьюмеры могут использовать это событие для обновления каталога, отправки уведомлений игрокам и т.д.

### Обязательные поля

Все поля верхнего уровня (`id`, `name`, `status`, `format`, `date`, `region`, `discipline`, `max_teams`, `organizer`) обязательны. Внутри объекта `organizer` обязательны `id` и `display_name`.

Это гарантирует консьюмеру получение полного контракта и предотвращает NullPointerException при обработке сообщения в рантайме.

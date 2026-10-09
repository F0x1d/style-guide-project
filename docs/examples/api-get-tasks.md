# Метод GET /tasks

Возвращает список задач проекта. Задачи отсортированы по дате создания, сначала новые. Метод возвращает не больше 100 задач за один запрос.

## Запрос

```http
GET https://api.taskbook.example/v1/tasks
Authorization: Bearer YOUR_API_TOKEN
```

### Параметры запроса

| Параметр | Тип | Обязательный | Описание |
| --- | --- | --- | --- |
| `project_id` | `string` | Да | ID проекта |
| `status` | `string` | Нет | Статус задач. Возможные значения: `new`, `in_progress`, `done`. По умолчанию возвращаются задачи во всех статусах |
| `assignee_id` | `string` | Нет | ID исполнителя |
| `limit` | `integer` | Нет | Количество задач в ответе. По умолчанию: `20`. Максимум: `100` |
| `cursor` | `string` | Нет | Курсор следующей страницы из поля `next_cursor` предыдущего ответа |

### Пример запроса

Запрос возвращает первые 10 задач проекта в статусе «В работе»:

```bash
curl "https://api.taskbook.example/v1/tasks?project_id=prj_8Kd2&status=in_progress&limit=10" \
  -H "Authorization: Bearer YOUR_API_TOKEN"
```

Вместо `YOUR_API_TOKEN` укажите токен API из раздела **Настройки** → **API**.

## Ответ

| Поле | Тип | Описание |
| --- | --- | --- |
| `tasks` | `array` | Список задач |
| `tasks[].id` | `string` | ID задачи |
| `tasks[].title` | `string` | Название задачи |
| `tasks[].status` | `string` | Статус задачи |
| `tasks[].assignee_id` | `string` | ID исполнителя. `null`, если исполнитель не назначен |
| `tasks[].due_date` | `string` | Срок в формате ISO 8601. `null`, если срок не задан |
| `next_cursor` | `string` | Курсор следующей страницы. `null`, если это последняя страница |

### Пример ответа

```json
{
  "tasks": [
    {
      "id": "tsk_41Fa",
      "title": "Подготовить отчёт за квартал",
      "status": "in_progress",
      "assignee_id": "usr_02Lm",
      "due_date": "2026-10-20T18:00:00Z"
    }
  ],
  "next_cursor": "eyJpZCI6InRza180MUZhIn0"
}
```

## Ошибки

| Код | Причина |
| --- | --- |
| `400 Bad Request` | Не передан `project_id` или передано недопустимое значение параметра |
| `401 Unauthorized` | Токен API не передан или недействителен |
| `403 Forbidden` | У владельца токена нет доступа к проекту |
| `404 Not Found` | Проект с указанным `project_id` не найден |
| `429 Too Many Requests` | Превышен лимит запросов: 60 запросов в минуту |

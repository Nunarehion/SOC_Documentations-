ProfileReplies.svelte — ответы пользователя

Назначение
- Список ответов (комментариев) пользователя.

Пропсы
- user_id: string | number
- page_size?: number = 20

API
- GET /api/profiles/{id}/replies/?limit={page_size}&after={cursor}
- Ответ: { posts: Post[], next_cursor?: string }

Состояния
- loading/loadingMore/error/empty

Интеграция
- PostCard с выделением контекста ответа, ссылка на исходный пост

Тест‑план
- Переходы по курсорам
- Пустые состояния
- Ошибки
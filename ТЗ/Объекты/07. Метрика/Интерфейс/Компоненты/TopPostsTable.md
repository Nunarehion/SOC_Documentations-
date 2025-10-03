TopPostsTable.svelte — топ постов

Назначение
- Таблица топовых постов по просмотрам/вовлеченности.

Пропсы
- from: ISO date
- to: ISO date
- limit?: number = 20

API
- GET /api/metrics/posts/top/?from={from}&to={to}&limit={limit}
- Ответ: { items: [{ post, views, likes, comments, reposts }] }

Тест‑план
- Пагинация/лимит, корректная сортировка, переход на пост

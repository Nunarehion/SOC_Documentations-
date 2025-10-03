TopAuthorsTable.svelte — топ авторов

Пропсы
- from: string (ISO)
- to: string (ISO)
- limit?: number = 20

API
- GET /api/metrics/authors/top/?from={ISO}&to={ISO}&limit={limit}
- Ответ: { items: [{ author, posts, engagement }] }

a11y
- Таблица с читаемыми заголовками и форматированием чисел

Тест‑план
- Пагинация/лимит, корректность отображения engagement
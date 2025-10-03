ProfileMedia.svelte — медиа пользователя

Назначение
- Показать посты пользователя, содержащие медиа.

Пропсы
- user_id: string | number
- page_size?: number = 20

API
- GET /api/profiles/{id}/media/?limit={page_size}&after={cursor}
- Ответ: { posts: Post[], next_cursor?: string }

UI
- Сетка карточек/миниатюр, открытие MediaViewer по клику

Состояния и a11y
- Как в ProfilePosts: loading/empty/error, роли для списков

Тест‑план
- Фильтрация только постов с медиа
- Догрузка страниц, корректные курсоры

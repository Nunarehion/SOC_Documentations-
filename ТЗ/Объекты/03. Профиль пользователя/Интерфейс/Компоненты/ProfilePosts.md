ProfilePosts.svelte — список постов пользователя

Назначение
- Загружает и отображает посты пользователя с курсорной пагинацией.

Пропсы
- user_id: string | number
- page_size?: number = 20

Логика загрузки
- GET /api/profiles/{id}/posts/?limit={page_size}&after={cursor}
- Ответ: { posts: Post[], next_cursor?: string }
- При достижении конца списка next_cursor отсутствует

Состояния
- loading (первичная загрузка), loadingMore (догрузка), error, empty
- Скелетоны на первичной загрузке, Spinner при догрузке

Интеграции
- Использует PostCard.svelte, Toast, глобальные stores для обновления элементов
- Инвалидирует/обновляет элементы при лайках/удалениях через события PostCard

Accessibility
- Список с role=list, элементы с role=listitem

Тест‑план
- Первый запрос и рендер списка
- Бесконечная прокрутка и догрузка
- Обработка ошибок, повторная попытка
- Пустое состояние

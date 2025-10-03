WallFeed.svelte — лента стены пользователя

Назначение
- Загрузка и отображение постов конкретного пользователя с учетом фильтра и курсора.

Пропсы
- user_id: string | number
- filter: 'all' | 'posts' | 'replies' | 'reposts'
- page_size?: number = 20

API
- GET /api/wall/{user_id}/?filter={filter}&limit={page_size}&after={cursor}
- Ответ: { posts: Post[], next_cursor?: string }

Состояния
- loading/loadingMore/error/empty
- Сохранение и восстановление позиции прокрутки (опционально)

Интеграции
- Использует PostCard, Toast, Spinner; слушает события PostCard для обновления

a11y
- role=list/listitem, фокусируемые элементы в карточках

Тест‑план
- Первичная загрузка, смена фильтра, догрузка по курсору
- Обработка ошибок и повторная попытка
- Сохранение позиции при навигации назад

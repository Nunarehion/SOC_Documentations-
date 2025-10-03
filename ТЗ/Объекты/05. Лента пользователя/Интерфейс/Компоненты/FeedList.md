FeedList.svelte — список ленты

Назначение
- Отображает ленту с курсорной пагинацией и восстановлением позиции.

Пропсы
- filter: 'following' | 'all'
- page_size?: number = 20

API
- GET /api/posts/?filter={filter}&limit={page_size}&after={cursor}
- Ответ: { posts: Post[], next_cursor?: string }

Состояния
- loading/loadingMore/error/empty; skeletons и Spinner

Интеграции
- PostCard, Toast; подписка на события PostCard для обновления

Опции
- restoreScroll?: boolean — восстановление позиции при возвращении на экран

a11y
- role=list/listitem; фокусируемые кнопки действий внутри PostCard

Тест‑план
- Первичная загрузка, переключение фильтров, догрузка
- Ошибки/повторы, пустые состояния
- Восстановление позиции

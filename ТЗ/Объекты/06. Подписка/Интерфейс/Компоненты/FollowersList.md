FollowersList.svelte — список подписчиков

Назначение
- Отображение подписчиков пользователя с курсорной пагинацией.

Пропсы
- user_id: string | number
- page_size?: number = 30

API
- GET /api/profiles/{id}/followers/?limit={page_size}&after={cursor}
- Ответ: { users: User[], next_cursor?: string }

Элемент списка
- UserRow: { user, following? } + FollowButton

Состояния и a11y
- loading/loadingMore/error/empty, role=list/listitem

Тест‑план
- Догрузка, переходы по курсорам, follow toggle отражается в строке

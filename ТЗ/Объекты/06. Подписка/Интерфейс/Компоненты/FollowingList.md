FollowingList.svelte — список подписок

Назначение
- Список пользователей, на которых подписан владелец профиля.

Пропсы
- user_id: string | number
- page_size?: number = 30

API
- GET /api/profiles/{id}/following/?limit={page_size}&after={cursor}
- Ответ: { users: User[], next_cursor?: string }

Элемент списка
- UserRow: { user } + FollowButton (для взаимной подписки)

Состояния и a11y
- Аналогично FollowersList

Тест‑план
- Догрузка, корректность курсоров, отображение FollowButton

UserRow.svelte — строка пользователя в списках

Пропсы
- user: { id, display_name, username, avatar_url, following?: boolean }
- show_follow?: boolean = true

Состав
- Аватар, имя, @username, (опц.) FollowButton

События
- openProfile: { user_id }

Тест‑план
- Рендер данных пользователя, навигация в профиль, взаимодействие FollowButton

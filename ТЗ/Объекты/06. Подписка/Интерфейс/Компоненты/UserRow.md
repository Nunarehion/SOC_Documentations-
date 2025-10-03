UserRow.svelte — строка пользователя

Назначение
- Отображает краткую информацию о пользователе в списках Followers/Following.

Пропсы
- user: { id, username, display_name, avatar_url, bio? }
- show_follow?: boolean = true
- following?: boolean — текущее состояние для FollowButton

Слоты
- actions (опц.) — для расширяемых действий справа

Состав
- Аватар, имя, @username, краткое bio (обрезка)
- Кнопка FollowButton (если show_follow)

События
- click — переход в профиль

A11y
- Кнопки и ссылки доступны с клавиатуры; aria-label на аватаре/имени

Тест‑план
- Рендер с разными наборами данных
- Клик ведёт в профиль
- Наличие FollowButton по флагу
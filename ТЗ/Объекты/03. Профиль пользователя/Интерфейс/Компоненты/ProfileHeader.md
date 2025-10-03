ProfileHeader.svelte — заголовок профиля

Пропсы
- profile: { id, user_id, display_name, username, bio, avatar_url, banner_url, followers_count, following_count, posts_count }
- isOwner: boolean
- following: boolean
 - loading?: boolean — состояние загрузки профиля

События
- editProfile
- followToggle
 - openAvatar
 - openBanner

Поведение
- Кнопка Follow/Unfollow отображается, если !isOwner
- При toggle вызывает FollowButton или напрямую API, затем эмитит followToggle
- Для владельца показывает Edit Profile (открывает форму редактирования)
 - Клик по аватару/баннеру эмитит соответствующие события (или открывает Viewer)

Состав
- Использует: Avatar, Banner, FollowButton
- Для владельца: кнопка Edit (открывает EditProfileForm в модальном окне)

Accessibility
- Правильная иерархия заголовков; кнопки имеют aria‑label
- Фокус‑порядок: сначала интерактивные элементы заголовка

Тест‑план
- Рендер всех состояний (владелец/не владелец, following/не following)
- Обработка кликов по FollowButton, аватару, баннеру
- Открытие формы редактирования, эмит событий

ProfileHeader.svelte — заголовок профиля

Пропсы
- profile: { id, user_id, display_name, username, bio, avatar_url, banner_url, followers_count, following_count, posts_count }
- isOwner: boolean
- following: boolean

События
- editProfile
- followToggle

Поведение
- Кнопка Follow/Unfollow отображается, если !isOwner
- При toggle вызывает FollowButton или напрямую API, затем эмитит followToggle
- Для владельца показывает Edit Profile (открывает форму редактирования)

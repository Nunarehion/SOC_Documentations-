EditProfileForm.svelte — форма редактирования профиля

Назначение
- Редактирование полей профиля владельцем: display_name, bio, avatar_url, banner_url.

Пропсы
- profile: { id, display_name, username, bio, avatar_url, banner_url }
- submitting?: boolean

События
- save: { profile }
- cancel: {}
- error: { message, field_errors? }

Валидация
- display_name: 1..80 символов
- bio: 0..300 символов
- URL‑поля — валидный URL
- Санитайзинг от XSS для bio

API
- PATCH /api/profiles/{id}/ { display_name?, bio?, avatar_url?, banner_url? }
- Ответ: { profile }

Состояния
- pristine/dirty, submitting, error

Accessibility
- Метки label/for, описания ошибок, подсказки, фокус‑ловушка в модальном режиме

Тест‑план
- Валидация, успех, ошибки сервера, отмена, блокировка на submit
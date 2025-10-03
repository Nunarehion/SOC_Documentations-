FollowButton.svelte — кнопка подписки/отписки

Назначение
- Переключает состояние подписки на пользователя. Поддерживает оптимистичные обновления и блокировку во время запроса.

Пропсы
- user_id: string | number — целевой пользователь
- following: boolean — текущее состояние
- disabled?: boolean — заблокировать взаимодействие
- size?: 'sm' | 'md' | 'lg' — размер кнопки (по умолчанию 'md')

События
- toggle: { following: boolean, followers_count: number }
- error: { message: string, code?: string }

Поведение
- При клике, если following=false → PUT /api/profiles/{id}/follow/
- Если following=true → DELETE /api/profiles/{id}/follow/
- В режиме loading кнопка недоступна, показывает спиннер/состояние
- Успех: эмитит toggle и снимает loading; Ошибка: эмитит error, откатывает оптимистичное состояние

API
- PUT /api/profiles/{id}/follow/ → 200 { following: true, followers_count: number }
- DELETE /api/profiles/{id}/follow/ → 200 { following: false, followers_count: number }

Состояния
- normal | loading | disabled | error

Accessibility
- role=button, aria-pressed отражает состояние following
- Текстовые альтернативы: «Подписаться» / «Отписаться»

Тест‑план
- Рендер с разными размерами и состояниями
- Успешные PUT/DELETE (оптимистичный апдейт + подтверждение)
- Ошибки сети: откат состояния, показ ошибки
- Блокировка кнопки на время запроса

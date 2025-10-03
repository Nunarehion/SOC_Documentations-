ShareProfileModal.svelte — модал шаринга профиля

Назначение
- Предлагает варианты: копировать ссылку, открыть системное меню share (если доступно), QR (опц.).

Пропсы
- profile_url: string
- open: boolean

События
- close: {}
- copied: {}

Поведение
- Кнопка «Копировать» использует Clipboard API с fallback
- (Опц.) генерация QR по profile_url

a11y
- role=dialog, aria-modal=true, фокус‑ловушка, ESC закрывает

Тест‑план
- Открытие/закрытие, копирование, фокус‑менеджмент
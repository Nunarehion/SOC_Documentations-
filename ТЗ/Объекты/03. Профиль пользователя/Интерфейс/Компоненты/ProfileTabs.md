ProfileTabs.svelte — вкладки профиля

Назначение
- Переключение между разделами профиля: Posts | Media | Replies.

Пропсы
- active_tab: 'posts' | 'media' | 'replies'

События
- change: { tab: 'posts' | 'media' | 'replies' }

Accessibility
- Контейнер: role=tablist, кнопки: role=tab, aria-selected для активной вкладки, tabindex управляемый
- Клавиатура: ArrowLeft/ArrowRight — переключение вкладок, Home/End — крайние

Состояния
- Подсветка активной вкладки, фокус‑стили видимы

Тест‑план
- Клик по вкладкам вызывает change
- Навигация клавиатурой меняет active_tab
- Корректные aria‑атрибуты

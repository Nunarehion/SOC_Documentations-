WallFilters.svelte — фильтры стены

Назначение
- Переключение фильтра контента: all | posts | replies | reposts.

Пропсы
- active_filter: 'all' | 'posts' | 'replies' | 'reposts'

События
- change: { filter }

Accessibility
- role=tablist/segmented control; aria-pressed на кнопках
- Клавиатура: стрелки для перемещения, Enter/Space — выбор

Тест‑план
- Клик/клавиатура меняют фильтр, эмитится change
- Корректная подсветка и aria‑состояния

FeedTabs.svelte — вкладки ленты

Назначение
- Переключение между 'following' и 'all'.

Пропсы
- active: 'following' | 'all'

События
- change: { tab: 'following' | 'all' }

a11y
- role=tablist/tab, aria-selected, клавиатурная навигация

Тест‑план
- Клик/клавиатура меняют вкладку и эмитят change

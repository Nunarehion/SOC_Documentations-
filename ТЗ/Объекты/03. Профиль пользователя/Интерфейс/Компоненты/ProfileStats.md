ProfileStats.svelte — статистика профиля

Назначение
- Отображает счетчики: posts_count, followers_count, following_count; ведет к разделам.

Пропсы
- profile: { posts_count, followers_count, following_count }
- links?: { posts?: string, followers?: string, following?: string }

События
- open: { section: 'posts'|'followers'|'following' }

a11y
- Кнопки/ссылки имеют aria‑label, числа форматируются локально

Тест‑план
- Корректный рендер значений, переходы по кликам

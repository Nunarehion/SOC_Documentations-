MetricsOverview.svelte — сводка метрик

Назначение
- Показ ключевых агрегированных показателей за выбранный период.

Пропсы
- from: ISO date
- to: ISO date

API
- GET /api/metrics/overview/?from={from}&to={to}
- Ответ: { totals: { posts, views, likes, comments, reposts, dau } }

UI
- Карточки/виджеты показателей, мини‑графики (sparklines)

a11y
- role=region, aria-label описывает период и метрики

Тест‑план
- Запрос и рендер данных
- Изменение диапазона дат обновляет данные

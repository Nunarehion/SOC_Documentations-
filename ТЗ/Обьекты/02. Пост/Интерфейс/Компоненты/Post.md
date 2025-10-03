Карточка поста — описание структуры и поведения

1. Общая структура
- Карточка представляет один пост и состоит из трёх секций: Header, Контент, Нижняя панель действий.
- Карточка кликабельна только в пределах контента; действия выполняются через отдельные кнопки/меню.

2. Header
- Содержит информационную часть и меню действий (иконка «три вертикальные точки») в правом верхнем углу.
- Информационная часть:
  - Аватар: квадрат с округлёнными краями, фиксированный размер (рекомендация 40×40–48×48px).
  - Ник (display name): заметный, жирный шрифт.
  - Юзернейм (handle): под ником, меньшим шрифтом и серым цветом; формат @username.
- Поведение:
  - Клик по аватару или нику — переход на профиль пользователя.
  - Меню действий открывает PostActions (см. отдельный компонент) и не закрывает карточку.

3. Контент
- Текст поста:
  - Поддерживает переносы, URL-детектинг (linkify), упоминания и хэштеги (стилевые выделения, кликабельные).
  - Ограничение длины отображаемого текста с опцией «Показать полностью» (expand/collapse).
- Медиа:
  - Под текстом — сетка/карусель прикреплённых медиа (изображения, видео, audio, файлы).
  - Миниатюры с адаптивным layout (1–4 колонки в зависимости от количества и ширины).
  - Клик по медиa открывает просмотрщик (lightbox/modal) с навигацией.
- Поведение и fallback:
  - Загрузка изображений lazy; placeholder (blur) при загрузке.
  - Если медиа недоступно — показывать заглушку с иконкой ошибки.
  - Максимальный размер превью и аспект‑ratio для видео/изображений единые для консистентности.

4. Нижняя панель действий
- Элементы (слева направо):
  - Кнопка «Комментировать» (иконка + счётчик комментариев).
  - Кнопка «Репост/Поделиться» (иконка + счётчик репостов/ретвитов).
  - Кнопка «Лайк» (иконка сердечка + счётчик лайков).
- Правый край:
  - Дата/время создания поста (короткий формат: "2 ч" / "3 окт" / полный при ховере).
- Поведение:
  - Кнопки интуитивно изменяют состояние (toggled для лайка/репоста).
  - Кнопки показывают состояние loading при выполнении запроса; блокируются для повторных кликов.
  - При нажатии обновлять глобальный store/локально индицировать оптимистично; откат при ошибке.
  - Для незалогиненных пользователей показывать подсказку/модал для входа.

5. Accessibility (a11y)
- Семантика: header — role="group" с aria-labelledby; кнопки — button с aria-pressed/aria-label.
- Клавиатурная навигация: Tab для перехода по элементам; Enter/Space для активации; фокус видим.
- Скринридер: логические описания для кнопок (например, "Поставить лайк, 12 лайков").

6. State и интеграция
- Входные пропсы/данные:
  - postId, author { id, name, handle, avatarUrl }, content { text, media[] }, counts { likes, reposts, comments }, userState { liked, reposted, reported }, createdAt.
- Компоненты-детали: PostActions (меню), MediaViewer (lightbox), CommentsToggle/Composer.
- Store-интеграция: при действиях (лайк, репост, удалить) обновлять глобальный posts store и/или инвалидация маршрута.

7. Визуальные рекомендации
- Карточка: лёгкая тень и закругление, контрастные интерактивные элементы.
- Минимализм: иконки с чёткими состояниями (filled/outline) и анимацией при смене состояния.

8. Короткие UX-правила
- Не перезагружать страницу при действиях — делать AJAX.
- Подтверждение для destructive-операций (удаление).
- Показывать короткие toasts при успешных/ошибочных операциях.
- Кеширование медиа и публикаций для ускорения повторного открытия.

## API‑запросы

1) Получить пост
- GET /api/posts/{postId}
- Query: ?fields=summary|full|comments
- 200 OK data: { post: { id, author, content, media, counts, userState, createdAt } }

2) Создать пост
- POST /api/posts
- Body:
{
  "text": "string",
  "mediaIds": ["id", ...]
}
- 201 Created data: { post: { ... } }

3) Обновить пост (владелец)
- PATCH /api/posts/{postId}
- Body: { "text"?: "string", "mediaIds"?: ["id"] }
- 200 OK data: { post: { ... } }

4) Удалить пост (владелец)
- DELETE /api/posts/{postId}
- 200 OK data: { deleted: true, deletedAt: "ISO" }
- 403 if not owner, 404 if not found

5) Лайк / снять лайк
- POST /api/posts/{postId}/likes
- Body: { "action": "toggle" }  или use PUT/DELETE:
  - PUT /api/posts/{postId}/likes  — поставить лайк
  - DELETE /api/posts/{postId}/likes — убрать лайк
- 200 OK data: { liked: true|false, likesCount: number }

6) Репост / отмена репоста
- POST /api/posts/{postId}/reposts
- Body: { "action":"toggle" } или
  - PUT /api/posts/{postId}/reposts
  - DELETE /api/posts/{postId}/reposts
- 200 OK data: { reposted: true|false, repostsCount: number }

7) Комментарии
- GET /api/posts/{postId}/comments?limit=20&after={cursor}
- 200 OK data: { comments:[...], nextCursor: "..." }
- POST /api/posts/{postId}/comments
- Body: { "text": "string", "replyToCommentId"?: "id" }
- 201 Created data: { comment: { ... }, commentsCount: number }

8) Жалоба (report)
- POST /api/posts/{postId}/reports
- Body:
{
  "reason": "spam|irrelevant|violence|other",
  "comment"?: "string",
  "sendToModeration"?: true
}
- 201 Created data: { reportId: "uuid", hiddenForUser: true }
- 409 if already reported (idempotent handling)

9) Проверить жалобу пользователя
- GET /api/posts/{postId}/reports/me
- 200 OK data: { reported: boolean, report?: { id, reason, status } }

10) Отозвать жалобу (если разрешено)
- DELETE /api/posts/{postId}/reports/{reportId}
- 200 OK

11) Скрыть пост локально для пользователя (без жалобы)
- POST /api/posts/{postId}/hide
- Body: {}
- 200 OK data: { hiddenForUser: true }

12) Поделиться / генерация ссылки
- POST /api/posts/{postId}/share
- Body:
{ "type"?: "permalink|short", "expiresIn"?: number }
- 200 OK data: { url: "https://...", shortCode?: "...", expiresAt?: "ISO" }

13) Получить метаданные медиа для поста
- GET /api/media/{mediaId}
- 200 OK data: { id, url, type, width, height, duration? }

14) Список постов (ленивая загрузка / лента)
- GET /api/posts?limit=20&after={cursor}&filter={user|following|tag}
- 200 OK data: { posts:[...], nextCursor }

15) Вебхуки / события (опционально)
- POST /api/webhooks/events
- Body: { event: "post_deleted|post_reported|post_shared", data: {...} } — для внешней интеграции/аналитики

Рекомендации по поведению API
- Все мутации должны возвращать актуальные счётчики (likesCount, commentsCount, repostsCount).
- Идемпотентность: повторные POST на reports/likes должны корректно обрабатываться (409 или вернуть текущий статус).
- Авторизация: 401 если не авторизован; 403 при отсутствии прав.
- Ошибки: структурировать { success:false, code: "string", message: "human" }.
- Лимиты: rate-limit для жалоб и репостов, валидация размеров медиа.
- Audit: логировать report.create, post.delete, post.share с userId, postId, ip, userAgent.
Выпадающее меню действий

В правом верхнем углу карточки поста расположена иконка «три вертикальные точки». По нажатию открывается вертикальное выпадающее меню с тремя пунктами — действия применяются к текущему посту.

Для авторизованного пользователя, не являющегося владельцем поста:
1. **Пожаловаться** — открывает форму отправки жалобы на нерелевантный/неуместный контент. Форма предлагает список причин (несоответствующий контент, спам, насилие и т.п.) и поле для комментария. В тексте явно указано, что жалоба скрывает пост только для вас; при подтверждении жалобы пост навсегда скрывается только для текущего пользователя. При отмене в форме меню закрывается, никаких действий не выполняется. Если пользователь уже жаловался, вместо кнопки показывается статус «Жалоба отправлена» или кнопка отключена. Кнопка блокируется на время выполнения запроса; при ошибке показывается понятное сообщение и возможность повторить.
2. **Поделиться** — создаёт ссылку на пост и копирует её в буфер обмена. Пользователь получает уведомление (toast) об успешном копировании с опциональной кнопкой «Открыть»/«Поделиться». При сбое отображается сообщение об ошибке. Событие копирования логируется для аналитики.

Для владельца поста:
3. **Удалить** — отображается вместо пункта «Пожаловаться». При выборе открывается подтверждающее диалоговое окно с кратким описанием последствий и кнопками «Отмена» / «Удалить». Кнопка блокируется на время выполнения операции; при подтверждении отправляется запрос на удаление поста. После успешного удаления пользователь получает уведомление, что пост удалён. При ошибке показывается понятное сообщение и откат UI (optimistic UI rollback) при необходимости. Можно дополнительно предусмотреть кнопку «Редактировать» рядом с «Удалить», если доступна функция редактирования.

Дополнительные рекомендации (встроены в поведение элементов):
- Доступность: обеспечить фокусировку, клавиатурную навигацию и aria-атрибуты для меню и пунктов.
- Логирование: фиксировать события (жалоба, копирование, удаление) для аналитики и модерации.
- Конфиденциальность: при отправке жалобы не раскрывать личные данные жалующего модераторам.
- Поведение при повторных действиях: предотвращать дублирование запросов; при уже отправлённой жалобе отключать соответствующее действие.
- UI-оповещения: показывать уведомления об успехе/ошибке для каждой операции.

Если нужно, могу вставить этот текст прямо в ваш макет (тег меню/кнопки) или подготовить короткие сообщения для toast/диалогов.

## API — операции меню действий для поста

Используйте REST-эндпоинты ниже (JSON). Предполагается аутентификация (Bearer JWT или сессия). Все ответы возвращают стандартный объект { success: boolean, message?: string, data?: object } и корректные HTTP‑коды.

### 1) Жалоба (report)
- Метод: POST
- URL: /api/posts/{postId}/reports
- Описание: отправить жалобу на пост; сохраняет запись жалобы и помечает пост как скрытый для текущего пользователя.
- Тело (application/json):
```json
{
  "reason": "spam|irrelevant|violence|other",
  "comment": "текст (опционально)",
  "sendToModeration": true
}
```
- Успех: 201 Created
- Ответ data:
```json
{
  "reportId": "uuid",
  "hiddenForUser": true
}
```
- Ошибки: 400 (валидация), 409 (уже жаловался), 401/403

### 2) Состояние жалобы (optional)
- Метод: GET
- URL: /api/posts/{postId}/reports/me
- Описание: проверить, жаловался ли текущий пользователь на пост.
- Успех: 200 OK
- Ответ data:
```json
{
  "reported": true,
  "report": { "id":"uuid", "reason":"spam", "status":"pending" }
}
```

### 3) Отмена/удаление жалобы (optional)
- Метод: DELETE
- URL: /api/posts/{postId}/reports/{reportId}
- Описание: пользователь отменяет ранее отправленную жалобу (если разрешено).
- Успех: 200 OK

### 4) Скрыть пост локально (client-side state) — опционально, но сервер может хранить флаг
- Метод: POST
- URL: /api/posts/{postId}/hide
- Описание: пометить пост скрытым только для текущего пользователя (не отправляя жалобу).
- Тело: {}
- Успех: 200 OK

### 5) Поделиться — генерация короткой/публичной ссылки
- Метод: POST
- URL: /api/posts/{postId}/share
- Описание: сгенерировать публичную ссылку (или short token) для поста; можно вернуть сразу URL и метаданные.
- Тело:
```json
{
  "type": "permalink|short",
  "expiresIn": 86400    // опционально, в секундах
}
```
- Успех: 200 OK
- Ответ data:
```json
{
  "url": "https://example.com/p/abcd1234",
  "shortCode": "abcd1234",
  "expiresAt": "2025-10-04T12:00:00Z"
}
```

### 6) Удаление поста (владелец)
- Метод: DELETE
- URL: /api/posts/{postId}
- Описание: удалить пост владельцем. Требуется подтверждение клиентом; сервер возвращает окончательный статус удаления.
- Успех: 200 OK
- Ответ data:
```json
{
  "deleted": true,
  "deletedAt": "2025-10-03T10:00:00Z"
}
```
- Ошибки: 403 (не владелец), 404

### Поведение и идемпотентность
- POST /reports должен быть идемпотентен: повторная отправка той же жалобы возвращает 409 или обновляет существующую запись.
- DELETE /posts/{postId} — при повторном запросе возвращать 200 и подтверждение удаления (идемпотентность).

### Авторизация и аудит
- Все эндпоинты требуют авторизации (401).
- Логировать события: report.create, post.share, post.delete (audit) с userId, postId, ip, userAgent.
- Для модерации хранить reason, comment, attached media, статус (pending/handled/rejected) и ссылки на действия модераторов.

### Примеры кода ответа на ошибки
- 400:
```json
{ "success": false, "message": "Invalid reason" }
```
- 403:
```json
{ "success": false, "message": "Not authorized" }
```
- 500:
```json
{ "success": false, "message": "Server error" }
```

Дополнительно — реализация как Svelte‑приложение

Приложение включает компонент меню действий для поста и сопутствующую логику (API, store, уведомления, маршрутизация). Ниже описана структура проекта и поведение, необходимое для полноценной реализации в Svelte (SvelteKit совместимы рекомендации).


## Компонент PostActions.svelte — API пропсы/события
- Props:
  - postId: string
  - isOwner: boolean
  - isReported: boolean
  - postUrl?: string
- Events (dispatch):
  - reportSent (detail: { postId, reportId })
  - postDeleted (detail: { postId })
  - postShared (detail: { postId, url })
- Локальный state:
  - open: boolean
  - loadingReport: boolean
  - loadingDelete: boolean
  - showReportForm: boolean
  - showConfirmDelete: boolean
  - toastMessage: string

## Поведение и UX
- Открытие меню:
  - aria-haspopup="menu", aria-expanded, фокус на первом пункте.
  - Esc закрывает; ArrowUp/Down навигация; Enter/Space активирует элемент.
- Пункты меню:
  - «Пожаловаться» — виден, если !isOwner && !isReported.
  - «Поделиться» — всегда доступен для авторизованных пользователей.
  - «Удалить» — виден, если isOwner (заменяет «Пожаловаться»).
- Loading/Blocking:
  - Пока выполняется запрос, соответствующая кнопка disabled и показывает Spinner.
  - Предотвращение повторных нажатий.

## Взаимодействие с API (поведение)
- reportPost(postId, { reason, comment, sendToModeration })
  - Идемпотентность: если сервер вернул 409 (уже есть жалоба) — отработать как успех (показать «Жалоба отправлена»).
  - Оптимистичный UI: скрыть пост локально сразу (hidePostLocal) и восстановить при ошибке.
  - Логирование события analytics.track('post_reported', …).
- sharePost(postId, { type, expiresIn })
  - Генерирует ссылку через API; сразу копирует в буфер через clipboard.writeText(url).
  - На успех: toast "Ссылка скопирована", dispatch('postShared').
  - Fallback: показать modal с выделенной ссылкой для ручного копирования.
- deletePost(postId)
  - Показать ConfirmDialog с описанием последствий.
  - На подтверждение: оптимистично удалить из store (removePost) и вызвать API.
  - При ошибке: восстановить пост в store, показать ошибку.
  - Сервер должен проверять права (403 если не владелец).
- hidePost (локальное скрытие)
  - Локальная отметка в store и (опционально) сохранение на сервере /api/posts/{id}/hide.

## Accessibility (A11y)
- menu: role="menu"; items: role="menuitem" / aria-disabled.
- aria-live="polite" div для toast сообщений.
- При открытии меню фокусировать первый элемент; при закрытии — вернуть фокус на кнопку «три точки».
- Поддержать keyboard only UX (Tab/Shift+Tab, Arrow keys, Enter, Esc).
- Тестировать с NVDA/VoiceOver.

## SSR / SvelteKit
- Все Web API (navigator.clipboard, window) вызывать только на клиенте (if (browser) { ... }).
- API вызовы через fetch с credentials (если нужно) или использовать endpoints сервера, чтобы скрывать токены.
- Инвалидация кэша/рефетч после удаления/жалобы: invalidate(`/posts`) или update store вручную.

## Error handling и откат
- Унифицированный error handler: showToast(error.message || 'Ошибка сети'); логирование.
- Для optimistic операций — реализовать rollback при ошибке.
- Retry UI: показывать кнопку «Повторить» в toast при сетевых ошибках.

## Тестирование
- Unit: svelte-testing-library — открыть меню, навигация клавишами, блокировка кнопок при загрузке, события dispatch.
- Integration/E2E: Cypress — сценарии жалобы, удаления, копирования ссылки, rollback при ошибке.
- Accessibility audits: axe-core интеграция в CI.

## Analytics и аудит
- Отправлять события: post_reported, post_deleted, post_shared с метаданными (postId, userId, reason).
- Серверный аудит: хранить report.reason, comment, reporterId, ip (в рамках политики конфиденциальности).

## Security и права
- Клиент только показывает/скрывает UI по флагам (isOwner/isReported) — сервер обязательно проверяет права.
- Share ссылки не должны содержать JWT/секреты. Для временных ссылок — short token на сервере.
- Ограничение частоты запросов/спама жалоб на стороне сервера.

## Integration со store и маршрутизацией
- При удалении/жалобе — обновлять глобальный store.posts: removePost(postId) или hidePostLocal(postId).
- Если используется SvelteKit load: вручную mutate или invalidate маршрут/кэш.

# Связи компонентов и файлов Svelte-приложения

PostActions.svelte
- Взаимодействует с:
  - ReportForm.svelte — открывает модальную форму (showReportForm = true); получает из неё submit событие с payload { reason, comment, sendToModeration }.
  - ConfirmDialog.svelte — открывает подтверждение удаления; получает событие confirm для запуска удаления.
  - Toast.svelte — вызывает функцию показа тоста (showToast) при успехе/ошибке.
  - lib/api/posts.js — вызывает reportPost, sharePost, deletePost, hidePost.
  - lib/stores/posts.js — вызывает removePost(postId) при успешном удалении и hidePostLocal(postId) при жалобе/локальном скрытии.
  - lib/utils/clipboard.js — для копирования ссылки (fallback внутри).
  - lib/utils/clickOutside.js и lib/utils/a11y.js — используют actions/helpers для фокуса, клавиатуры и закрытия меню.
- События/пропсы:
  - Props: postId, isOwner, isReported, postUrl.
  - Dispatch: reportSent, postDeleted, postShared.
- Локальный state: open, loadingReport, loadingDelete, showReportForm, showConfirmDelete.

ReportForm.svelte
- Взаимодействует с:
  - PostActions.svelte — вызывающий компонент открывает форму; ReportForm.emit('submit', payload) при подтверждении; emit('cancel') при отмене.
  - lib/api/posts.js — может вызывать reportPost напрямую (альтернатива: оставить вызов в PostActions).
  - Toast.svelte — показывает сообщения об ошибке/успехе.
- Внутреннее:
  - Формы: радиокнопки причин, textarea комментария, sendToModeration чекбокс.
  - Валидация и локальный блок submit (loading).

ConfirmDialog.svelte
- Взаимодействует с:
  - PostActions.svelte — открывается для подтверждения удаления; emit('confirm') / emit('cancel').
  - Toast.svelte — показывает результат операции при ошибке/успехе.
- Параметры: title, message, confirmLabel, cancelLabel, destructive (флаг для стилей).

Toast.svelte
- Взаимодействует с:
  - Вызовы из PostActions, ReportForm, ConfirmDialog и других компонентов через глобальную утилиту showToast(message, { type, actionLabel }).
  - Может быть связан с глобальным store/notification service (lib/stores/notifications.js).
- Поведение: aria-live region, авто-скрытие, опция отката действия (например, «Отменить» для удаления).

lib/api/posts.js
- Экспортирует: reportPost(postId, payload), getMyReport(postId), deletePost(postId), sharePost(postId, options), hidePost(postId).
- Используется в: PostActions.svelte, ReportForm.svelte (если форма отправляет сама), ConfirmDialog.svelte (если диалог сам вызывает удаление), серверные routes (если SSR вызывает API).
- Отдаёт ошибки в стандартной форме; идемпотентность реализуется на сервере и отражается в ответах (409 для уже отправлённой жалобы и т.д.).

lib/stores/posts.js
- Экспортирует: posts store (writable), функции removePost(postId), hidePostLocal(postId), updatePost(postId, patch).
- Используется в: PostActions.svelte (remove/hide), другие компоненты списка постов (Feed.svelte, PostCard.svelte) для реактивного обновления UI.
- При optimistic-удалении PostActions вызывает removePost, затем при ошибке restorePost (реализация отката в store).

lib/utils/clipboard.js
- Экспортирует: writeText(text) — пытается navigator.clipboard.writeText, fallback через временное textarea + document.execCommand('copy').
- Используется в: PostActions.svelte при share; ReportForm может использовать для копирования id жалобы.

lib/utils/a11y.js
- Экспортирует: focusFirst(node), trapFocus(node), handleArrowNav(node, options).
- Используется в: PostActions.svelte (при открытии меню), ReportForm.svelte и ConfirmDialog.svelte (для модалей).

lib/utils/clickOutside.js
- Экспортируется как Svelte action: use:clickOutside={() => close()}.
- Используется в: PostActions.svelte (для закрытия меню), ReportForm.svelte/ConfirmDialog.svelte (закрытие по клику вне).

routes/api/... (SvelteKit endpoints)
- Endpointы: POST /api/posts/:id/reports, DELETE /api/posts/:id, POST /api/posts/:id/share, POST /api/posts/:id/hide, GET /api/posts/:id/reports/me.
- Взаимодействие: lib/api/posts.js вызывает эти endpoints; сервер проверяет авторизацию/права и логирует аудит.

Связки поведения (сценарии)
- Жалоба:
  1. Пользователь кликает «Пожаловаться» в PostActions → открывается ReportForm.
  2. В ReportForm пользователь заполняет и подтверждает → emit submit.
  3. PostActions закрывает форму, показывает локально hidePostLocal(postId) и вызывает reportPost API.
  4. При успехе: showToast('Жалоба отправлена'), dispatch('reportSent'), store помечает пост скрытым. При ошибке: откат hide и showToast(error).
- Поделиться:
  1. Пользователь нажимает «Поделиться» → PostActions вызывает sharePost API.
  2. API возвращает URL → PostActions вызывает clipboard.writeText(url).
  3. При успехе: showToast('Ссылка скопирована'), dispatch('postShared'); при неудаче — открывает modal с ссылкой.
- Удаление:
  1. Владелец нажимает «Удалить» → PostActions открывает ConfirmDialog.
  2. На confirm → PostActions optimistically removePost(postId) и вызывает deletePost API.
  3. При успехе: showToast('Пост удален'), dispatch('postDeleted'). При ошибке: restore post в store, showToast(error).

Это краткая карта связей — при желании могу отрисовать диаграмму зависимостей (PlantUML/mermaid) или выдать шаблоны кода для каждой связки (включая сигнатуры функций API и подписки на события).
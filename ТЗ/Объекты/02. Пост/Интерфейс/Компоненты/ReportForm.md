# ReportForm — описание компонента

Модальная форма для отправки жалобы на пост. Используется внутри PostActions. Поддерживает выбор причины, опциональный комментарий, опцию отправки модерации, валидацию, загрузку состояния и accessibility.

Props
- postId: string — ID поста (обязательный)
- open: boolean — управление видимостью модали (двусторонний или через событие)
- initialReason?: string — предустановленная причина (опционально)
- sendToModerationDefault?: boolean — значение чекбокса по умолчанию

События (dispatch)
- submit — { reason: string, comment?: string, sendToModeration: boolean }
- cancel — (при отмене/закрытии)
- error — { message: string } (опционально для внешней обработки)

UI / Поля
- Заголовок: «Пожаловаться на пост»
- Список причин (radio group, обязательно выбрать одну):
  - spam — «Спам»
  - irrelevant — «Нерелевантный контент»
  - violence — «Насилие / опасный контент»
  - harassment — «Буллинг / домогательства»
  - other — «Другое» (при выборе — показать короткое текстовое поле для пояснения)
- Текстовое поле (textarea) — опционально, placeholder «Дополнительные детали (необязательно)»
- Чекбокс: «Отправить модераторам» (по умолчанию true/false в зависимости от props)
- Кнопки:
  - Отправить (primary) — disabled если нет выбранной причины или уже идет отправка
  - Отмена (secondary) — закрывает форму

Поведение
- Валидация:
  - Причина обязательна.
  - Если reason === "other", рекомендуется потребовать короткий комментарий (min 5 символов) — опционально.
- Отправка:
  - При клике «Отправить» выставляется loading flag; вызывается либо локальный api.reportPost(postId, payload), либо эмитируется событие submit.
  - UI: кнопка показывает spinner; остальные элементы disabled.
  - По успешной отправке: закрыть форму и dispatch('submit', payload) / показать toast «Жалоба отправлена».
  - При ошибке: показать inline-ошибку и dispatch('error', { message }).
- Идемпотентность:
  - Если сервер вернул 409 (уже жаловался), считать как успешную отправку и показать соответствующее сообщение.
- Закрытие:
  - По клику «Отмена», нажатию Esc или клику вне модали (use:clickOutside) — dispatch('cancel') и закрыть без действий.
- Accessibility:
  - role="dialog", aria-modal="true", aria-labelledby для заголовка.
  - Радио-группа — role="radiogroup" + aria-required.
  - При открытии фокусировать первый интерактивный элемент (reason первый radio).
  - Esc закрывает; focus trap внутри модали.
  - Inline ошибки и status messages в aria-live region.

Структура HTML (логически)
- Overlay (clickOutside action)
  - Dialog (role=dialog, aria-modal)
    - Header: title + close button (aria-label="Закрыть")
    - Form:
      - Radio group — list of options
      - If other selected — input for short reason
      - Textarea for details
      - Checkbox sendToModeration
      - Actions: Cancel, Submit (with spinner)

Интеграция с API
- Варианты:
  - В компоненте вызвать lib/api/posts.reportPost(postId, payload) и обрабатывать результат локально.
  - Или передавать данные наружу через событие submit и позволить PostActions обрабатывать API/optimistic UI.
- Рекомендуется: emit submit и позволить родителю выполнять API, чтобы централизовать state/rollback и логику store обновления.

UX-подсказки
- Показать краткое пояснение под заголовком: «Ваша жалоба поможет улучшить ленту. Жалоба скрывает пост только для вас.»
- После отправки показать toast «Жалоба отправлена» и заменить пункт меню «Пожаловаться» на «Жалоба отправлена».
- Ограничение частоты: при попытке отправить слишком часто — показать сообщение и заблокировать кнопку на некоторое время.

Пример payload при submit
{
  reason: "spam" | "irrelevant" | "violence" | "harassment" | "other",
  comment?: "string",
  sendToModeration: boolean
}

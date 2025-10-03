#ConfirmDialog — описание компонента

Переиспользуемый модальный диалог подтверждения для destructive/critical действий (удаление, сброс, выход и т.п.). Доступен для использования внутри PostActions, других компонентов и страниц.

Props
- open: boolean — управление видимостью модали
- title?: string — заголовок (по умолчанию: «Подтвердите действие»)
- message?: string — текст с описанием последствий
- confirmLabel?: string — текст кнопки подтверждения (по умолчанию: «Удалить» / «Подтвердить»)
- cancelLabel?: string — текст кнопки отмены (по умолчанию: «Отмена»)
- destructive?: boolean — флаг для стилизации подтверждающей кнопки (красная/warning)
- disableConfirm?: boolean — отключить кнопку подтверждения (например, пока идут проверки)
- allowEscape?: boolean — закрывать по Esc (по умолчанию true)
- trapFocus?: boolean — фокус-трап внутри диалога (по умолчанию true)
- showSecondaryAction?: boolean — опциональная дополнительная кнопка (например, «Сохранить черновик»)
- secondaryLabel?: string — текст вторичной кнопки
- ariaDescribedBy?: string — id элемента с подробным описанием (опционально)

Events (dispatch)
- confirm — вызывается при подтверждении (detail: optional payload)
- cancel — вызывается при отмене/закрытии
- close — общий event при любом закрытии

UI / Элементы
- Overlay (полупрозрачный фон)
- Dialog container (role="dialog", aria-modal="true", aria-labelledby -> title)
  - Header: title + close (×) button
  - Body: message / дополнительный контент (slot)
  - Footer: кнопки Cancel и Confirm (опционально Secondary)
    - Confirm: primary style, если destructive — красный/attention
    - Cancel: secondary style

Поведение
- Фокус:
  - При открытии фокус переходит на первый фокусируемый элемент (обычно Cancel или Confirm в зависимости от UX).
  - Если trapFocus=true — фокус не выходит за пределы модали.
  - При закрытии фокус возвращается на элемент, который открыл модал (caller should manage returning focus if needed).
- Клавиатура:
  - Enter — активирует Confirm (если фокус не в текстовом поле).
  - Esc — если allowEscape true — вызывает cancel.
  - Tab/Shift+Tab — циклическая навигация при trapFocus.
- Accessibility:
  - role="dialog", aria-modal="true", aria-labelledby и aria-describedby.
  - Кнопки имеют aria-label; Confirm имеет aria-pressed/aria-disabled при disableConfirm.
  - Ошибки/статус в aria-live region при необходимости.
- Loading / Blocking:
  - При длительной операции (удаление) выставлять loading state: disable все кнопки, показывать Spinner внутри Confirm.
  - При ошибке — отображать inline сообщение и вернуть управление.
- Confirm safety:
  - Для особо рискованных операций можно требовать дополнительные подтверждения:
    - чекбокс «Я понимаю, что действие необратимо»
    - ввод текста (type-to-confirm) — props для включения (confirmInput?: { requiredText: string })
- Идемпотентность:
  - Компонент сам по себе не делает запросы; родитель отвечает за idempotency и откат. ConfirmDialog лишь уведомляет о намерении.

Slots / Customization
- slot header — для кастомного заголовка
- slot body — для дополнительного контента (например, список последствий)
- slot footer — полностью кастомная группа кнопок (если нужны нестандартные варианты)

Примеры использования (логика)
- Удаление поста:
  1. PostActions открывает ConfirmDialog с title="Удалить пост?" message="Это действие необратимо."
  2. На confirm: PostActions вызывает store.removePost (optimistic), вызывает API deletePost, показывает toast; при ошибке — restorePost и show error.
- Сохранение черновика: использовать secondary action.


Варианты расширения
- Добавить prop timeoutConfirm — автоматическое подтверждение через X секунд с обратным отсчетом (редко используется).
- Поддержать confirmInput (ввод строки для подтверждения) для критичных операций.
- Локализация: все подписи через i18n-ключи.

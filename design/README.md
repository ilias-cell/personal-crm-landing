# Handoff: лендинг «Личной CRM» — Astro + Tailwind 4

## Overview
Одностраничный статичный лендинг продукта «Личная CRM». Единственное целевое действие — перейти в приложение (кнопка «Открыть приложение»). Одна адаптивная страница: мобильная раскладка — `Landing.dc.html`, десктопная — `Landing Desktop.dc.html` (обе — финальные версии).

## About the Design Files
Файлы в пакете — **дизайн-референсы в HTML**, не продакшен-код. Задача — воссоздать дизайн на **Astro + Tailwind CSS 4** как статичную страницу. `.dc.html` открываются в браузере напрямую (рядом должен лежать `support.js`); стили в них инлайновые — в проекте переводи их в утилиты Tailwind.

## Fidelity
**High-fidelity.** Цвета, шрифты, размеры и отступы финальные. Тексты — дословно из `landing-copy.md`, не переписывать.

## Стек и структура
- Astro (`output: 'static'`), Tailwind 4 через `@tailwindcss/vite`.
- Без JS на клиенте: интерактива нет, `client:*` директивы не нужны.
- Шрифты: `@fontsource-variable/literata` и `@fontsource-variable/commissioner` (или Google Fonts `<link>` в layout). Нужны Literata 400/500 + italic 400, Commissioner 400/500/600.

Предлагаемая структура:
```
src/
  layouts/Base.astro          # <html lang="ru">, <head>, title, description, шрифты
  pages/index.astro           # собирает секции
  components/
    Header.astro
    Hero.astro
    ContactsMock.astro        # макет списка контактов (props: rows, compact?)
    Problem.astro
    ContactCard.astro         # секция «Одна карточка…»
    HowItWorks.astro
    Facts.astro               # «На сервере» + «Не для продаж»
    FinalCta.astro
    AppButton.astro           # кнопка «Открыть приложение», props: variant ('primary'|'on-dark'|'header'), class
  data/landing.ts             # APP_URL, тестовые контакты, шаги, пункты
  styles/global.css
```
`APP_URL` — одна константа (или `import.meta.env.PUBLIC_APP_URL`); в прототипе стоит заглушка `#app`, **подставь реальный адрес**.

## Tailwind 4: `src/styles/global.css`
```css
@import "tailwindcss";

@theme {
  --font-serif: "Literata Variable", "Literata", Georgia, serif;
  --font-sans: "Commissioner Variable", "Commissioner", system-ui, sans-serif;

  --color-paper: #f3eee5;          /* фон страницы */
  --color-surface: #fbf8f2;        /* карточки-макеты */
  --color-field: #f8f4ec;          /* поле поиска на мобильном */
  --color-ink: #231e18;            /* основной текст, тёмная секция */
  --color-muted: #5e554a;          /* абзацы */
  --color-subtle: #7c7264;         /* подписи */
  --color-placeholder: #958a7a;
  --color-line: #ddd4c5;
  --color-line-soft: #e3dbcd;
  --color-line-input: #d3c9b8;
  --color-underline: #b8ad9c;
  --color-accent: oklch(0.48 0.13 255);
  --color-accent-on-dark: oklch(0.72 0.12 255);
  --color-on-dark-muted: #cfc6b8;
  --color-on-accent-dark: #14110d;

  --shadow-card: 0 30px 60px -40px rgba(40, 30, 15, 0.35);
  --breakpoint-lg: 56rem;          /* 896px — переключение на десктоп */
}

@layer base {
  html { scroll-behavior: smooth; }
  body { @apply bg-paper text-ink font-sans antialiased; }
  h1, h2 { text-wrap: balance; }
  p { text-wrap: pretty; }
  a:hover { opacity: .8; }
  :focus-visible { outline: 2px solid var(--color-accent); outline-offset: 2px; }
}
```
Ниже везде: «моб.» = без префикса, «деск.» = `lg:`.

## Responsive
- Контейнер: моб. `max-w-[430px] px-6`, деск. `lg:max-w-[1200px] lg:px-12`, `mx-auto`.
- Двухколоночные блоки на десктопе: `lg:grid lg:grid-cols-2 lg:gap-18` (72px).
- Ритм между секциями: `pt-18` (72px) / `lg:pt-40` (160px).

## Типографическая шкала (моб. → деск.)
| Элемент | Классы |
|---|---|
| H1 | `font-serif font-normal text-[42px] leading-[1.06] tracking-[-0.02em] lg:text-[68px] lg:leading-[1.02] lg:tracking-[-0.025em]` |
| H2 | `font-serif font-normal text-[32px] leading-[1.1] tracking-[-0.015em] lg:text-5xl lg:leading-[1.08] lg:tracking-[-0.02em]` |
| H2 секций 5–6 | как H2, но `lg:text-[36px] lg:leading-[1.12] lg:tracking-[-0.015em]` |
| H2 финальный | `font-serif text-[36px] leading-[1.08] tracking-[-0.02em] lg:text-[56px] lg:leading-[1.04]` |
| Лид под H1 | `text-base leading-[1.55] text-muted lg:text-[19px] lg:max-w-[520px]` |
| Абзац | `text-base leading-[1.6] text-muted lg:text-[19px]` (в секциях 5–6 `lg:text-[17px]`) |
| Заголовок шага | `font-serif text-[22px] lg:text-[26px]` |
| «Шаг N» | `font-serif italic text-[15px] text-accent lg:text-[17px]` |
| Пункт: заголовок | `font-semibold text-base lg:text-lg` |
| Пункт: текст | `text-[15px] leading-normal text-muted lg:text-base lg:leading-[1.55]` |
| Логотип | `font-serif font-medium text-lg lg:text-xl` |

## Кнопки и ссылки
- **Primary** «Открыть приложение»: `inline-flex items-center h-13 px-[22px] rounded bg-accent text-white text-base font-medium lg:h-14 lg:px-7 lg:text-[17px]`.
- **Header** (только деск.): `h-10 px-4 rounded bg-accent text-white text-sm font-medium`. На мобильном в шапке вместо кнопки текстовая ссылка `text-[13px] font-medium text-accent`.
- **On-dark** (финальная секция): `flex items-center justify-center h-14 w-full rounded bg-accent-on-dark text-on-accent-dark text-base font-semibold lg:h-15 lg:w-auto lg:px-8 lg:text-[17px]`.
- **Ссылка «Как это работает»** (hero): `text-[15px] font-medium underline underline-offset-4 decoration-underline lg:text-base lg:underline-offset-[5px]` → `href="#how"`.
- Hover: `opacity .8` (base-стиль). Радиус везде `rounded` (4px).

## Секции

### Header (sticky)
`sticky top-0 z-10 bg-paper/92 backdrop-blur-[8px] border-b border-line-soft`. Внутри контейнер `h-15 lg:h-18 flex items-center justify-between`. Слева логотип «Личная CRM». Справа: моб. — текстовая ссылка «Открыть приложение»; деск. — ссылка «Как это работает» (`text-[15px] font-medium`) + header-кнопка, `gap-8`.

### 01 Hero
`pt-8 lg:pt-24`, деск.: `lg:grid lg:grid-cols-2 lg:gap-18 lg:items-center`.
- Левая колонка, `flex flex-col gap-5 lg:gap-7`: H1 «Помни, кто есть кто и <em class="italic text-accent">о чём договорились</em>»; лид; ряд кнопок `flex flex-wrap items-center gap-5 lg:gap-7 mt-1 lg:mt-2`.
- `ContactsMock` (декоративный, `aria-hidden` не нужен — это читаемый текст, но без интерактива):
  - моб.: `mt-10 border-t-2 border-ink`, без фона; строки — имя над подписью (`flex flex-col gap-0.5 py-3 border-b border-line`), имя `font-serif text-[17px]`, подпись `text-[13px] text-subtle`.
  - деск.: карточка `bg-surface border border-line rounded-md px-7 pt-2 pb-3 shadow-card`; шапка «Контакты» с `border-b-2 border-ink`; строки в одну линию `flex justify-between items-baseline gap-4 py-3.5 border-b border-line-soft`, имя `text-lg`, подпись `text-sm`.
  - Шапка макета: «Контакты» (`font-serif text-[22px] lg:text-[26px]`) + «Добавить контакт» (`text-[13px] lg:text-sm font-medium text-accent`).
  - Поле «Поиск по имени»: `h-[38px] lg:h-[42px] border border-line-input rounded px-3 text-[13px] text-placeholder bg-field lg:bg-white lg:text-sm`.
  - Строки (последняя без нижней границы): Анна Смирнова — однокурсница; Дмитрий Королёв — сосед по даче; Мария Верещагина — познакомились на конференции; Игорь Белов — стоматолог; Ольга Кузнецова — коллега по прошлой работе.

### 02 Проблема
H2 «Номер есть, а остального нет» + абзац + строка-иллюстрация `border-y border-line py-3.5 lg:py-[18px] flex justify-between items-baseline`: слева «Дмитрий Королёв» (`text-[15px] lg:text-[17px]`), справа «кто это?» (`font-serif italic text-placeholder`). Деск.: 2 колонки, H2 слева, абзац + строка справа (`gap-7`).

### 03 Карточка
H2 «Одна карточка на каждого человека», ниже (`gap-6 lg:gap-12`) — деск. 2 колонки:
- Макет карточки: `bg-surface border border-line rounded-md p-5 lg:p-8 lg:shadow-card flex flex-col gap-3.5 lg:gap-5`. Имя «Анна Смирнова» `font-serif text-2xl lg:text-[30px]`. Сетка `grid grid-cols-[auto_minmax(0,1fr)] gap-x-4 gap-y-2.5 lg:gap-x-6 lg:gap-y-3 text-sm lg:text-[15px]`: Кто это — Однокурсница; Откуда знакомы — Учились вместе; Телефон — +7 000 123-45-67; Почта — anna@example.com (подписи `text-subtle`). Блок заметки `border-t border-line-soft pt-3.5 lg:pt-5`: подпись «Что обсудили, о чём договорились» (`text-xs lg:text-[13px] text-subtle`), поле `bg-white border border-line-input rounded p-3 lg:p-3.5` с текстом «Обсудили поездку на конференцию. Обещала прислать контакты организаторов.», кнопка-макет «Сохранить заметку» `h-10 lg:h-[42px] px-4 rounded bg-ink text-paper text-sm font-medium self-start` (это `<div>`, не ссылка).
- `<ol>` из 4 пунктов: `grid grid-cols-[28px_1fr] lg:grid-cols-[40px_1fr] gap-2 lg:gap-3 py-4 lg:py-[22px] border-t border-line` (у последнего ещё `border-b`). Цифра `font-serif text-[17px] lg:text-xl text-accent`. Тексты пунктов — из `landing-copy.md`.

### 04 Как это работает
`id="how" scroll-mt-15 lg:scroll-mt-18`. H2 + шаги: моб. `flex flex-col gap-7`; деск. `lg:grid lg:grid-cols-3 lg:gap-12`, у каждого шага `lg:border-t-2 lg:border-ink lg:pt-5`. Шаг: «Шаг N», заголовок, абзац (`text-[15px] lg:text-base leading-[1.55] text-muted`), `gap-1.5 lg:gap-2.5`.

### 05 Контакты на сервере + 06 Не для продаж
Моб.: две секции подряд. Деск.: одна обёртка `lg:grid lg:grid-cols-2 lg:gap-18`. В каждой H2 (малый) + абзац, `gap-[18px] lg:gap-5`.

### 07 Финальный призыв
Полная ширина: `mt-20 lg:mt-40 bg-ink text-paper`. Внутри контейнер `py-14 lg:py-28 flex flex-col gap-[18px] lg:flex-row lg:flex-wrap lg:justify-between lg:items-end lg:gap-10`. Текст (деск. `max-w-[640px]`, `gap-5`): H2 «Добавь первого человека», абзац `text-on-dark-muted text-base lg:text-[19px]`. On-dark кнопка: моб. на всю ширину с `mt-2`, деск. справа. **Подвала нет** — страница заканчивается этой секцией.

## Interactions & Behavior
- Все «Открыть приложение» → `APP_URL` (один источник).
- «Как это работает» → плавная прокрутка к `#how`.
- Hover ссылок/кнопок: `opacity .8`; фокус — `:focus-visible` outline accent.
- Макеты списка и карточки — статичные, без интерактива. Форм, загрузок и ошибок нет.

## State Management
Нет.

## SEO (`Base.astro`)
- `<html lang="ru">`
- `<title>`: «Личная CRM — контакты людей, которых знаешь лично»
- `<meta name="description">`: «Записывай, кто это, откуда знакомы и о чём договорились. Контакты людей, которых знаешь лично, и заметки после разговоров — в одном месте.»
- `<meta name="viewport" content="width=device-width, initial-scale=1">`

## Assets
Изображений и иконок нет. Только шрифты Literata и Commissioner.

## Files
- `Landing.dc.html` — мобильная версия (финальная).
- `Landing Desktop.dc.html` — десктопная версия (финальная).
- `support.js` — рантайм для просмотра `.dc.html`; в проект не переносить.
- `landing-copy.md` — исходные тексты.

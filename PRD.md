# План оптимизации и унификации UI MusicVerse AI

## Контекст

MusicVerse AI — крупный проект (935+ компонентов, 57 страниц) с хорошей базовой дизайн-системой (токены, tailwind config, shadcn/ui), но страдающий от накопленного технического долга в UI-слое: 15+ deprecated компонентов всё ещё в использовании, 10+ компонентов >700 строк, разрозненные паттерны анимаций и загрузки, 4 варианта хедеров. Цель — систематически унифицировать и оптимизировать UI без нарушения функциональности.

---

## Фаза 1: Удаление deprecated-компонентов (Cleanup)

**Цель:** Убрать мёртвый код и deprecated shims, которые маскируют реальные зависимости.

### 1.1 Удалить deprecated shims EmptyState
- `src/components/ui/EmptyState.tsx` → заменить все импорты на `unified-empty-state.tsx`
- `src/components/ui/empty-state.tsx` → аналогично
- Обновить все файлы-потребители (найти через grep `EmptyState` / `empty-state`)

### 1.2 Удалить deprecated skeleton/loading компоненты
- `src/components/ui/skeleton-loader.tsx` → заменить на `skeleton.tsx` + `skeleton-components.tsx`
- `src/components/ui/ContentSkeleton.tsx` → заменить на компоненты из `skeleton-components.tsx`
- `src/components/ui/loading-state.tsx` → заменить на `LoadingSpinner.tsx` или skeleton
- Обновить все импорты

### 1.3 Удалить deprecated RefinedCard
- `src/components/ui/RefinedCard.tsx` (340 строк) → заменить на `card.tsx` с вариантами (glass, interactive)
- Мигрировать все использования на базовый Card с нужным variant

### 1.4 Верификация
- `npm run lint` — нет ошибок
- `npm test` — все тесты проходят
- `grep -r "RefinedCard\|skeleton-loader\|ContentSkeleton\|loading-state\|EmptyState" src/` — 0 результатов

---

## Фаза 2: Унификация компонентов-дубликатов

### 2.1 Touch-компоненты (4 → 1)
- Объединить `touch-target.tsx`, `TouchFeedback.tsx`, `touch-friendly.tsx` в единый `TouchTarget`
- API: `<TouchTarget haptic={true} scale="sm"|"md" minSize={44}>`
- Мигрировать потребителей

### 2.2 Хедеры (4 → 2)
- Оставить `MobileHeaderBar` (мобильные страницы) и `AppHeader` (desktop/универсальный)
- `UnifiedPageHeader` → мигрировать на `AppHeader` с пропсами `floating`, `transparent`
- `HomeHeader` → оставить как специализацию (слишком уникальный), но наследовать от `AppHeader`

### 2.3 Voice Input Button (2 → 1)
- `voice-input-button.tsx` vs `VoiceInputButton.tsx` — удалить дубль, оставить один

### 2.4 Верификация
- `npm test` + `npm run build` без ошибок
- Визуальная проверка через `npm run dev` на ключевых страницах

---

## Фаза 3: Стандартизация анимаций

**Проблема:** Микс Tailwind (`active:scale-[0.96]`), Framer Motion (`motion.div`), CSS keyframes — с разными duration/scale значениями.

### 3.1 Единые scale-значения
- Определить в `design-tokens.ts`:
  - `press`: `scale(0.97)` (кнопки, карточки)
  - `hover`: `scale(1.02)` (интерактивные элементы)
  - `pop`: `scale(1.1)` (like, notification badges)
- Обновить все inline scale-значения (0.96, 0.98, 0.99, 1.05, 1.3) на стандартные

### 3.2 Duration-стандартизация
- Все Tailwind → `duration-200` (по умолчанию) или `duration-300` (сложные)
- Все Framer Motion → использовать `smoothTransition`/`quickTransition` из `@/lib/motion`
- Запретить произвольные duration через ESLint правило

### 3.3 Верификация
- Визуальная проверка анимаций на мобильных экранах
- Performance check: нет layout thrashing (только transform/opacity)

---

## Фаза 4: Рефакторинг крупных компонентов (>700 строк)

### 4.1 Приоритетные компоненты для разбиения

| Компонент | Строк | Стратегия |
|-----------|-------|-----------|
| `GlobalAudioProvider.tsx` | 940 | Извлечь `useAudioEffects`, `useAudioQueue`, `useAudioAnalytics` хуки |
| `SectionNotesPanel.tsx` | 882 | Извлечь `NotesList`, `NotesFilter`, `NoteEditor` подкомпоненты |
| `IntegratedStemTracks.tsx` | 875 | Извлечь `StemTrackItem`, `StemControls`, `useStemProcessing` |
| `MobileFullscreenPlayer.tsx` | 831 | Извлечь `PlayerControls`, `PlayerProgress`, `PlayerQueue` |
| `AudioActionDialog.tsx` | 797 | Композиция из step-компонентов |

### 4.2 Правило: max 400 строк на компонент
- Добавить ESLint правило `max-lines` (warning на 400, error на 600)

### 4.3 Верификация
- `npm test` — тесты проходят
- Функциональная проверка Studio, Player, Generation flow

---

## Фаза 5: Унификация паттернов загрузки

### 5.1 Стандартный Loading-паттерн для страниц
```tsx
// Каждая страница должна следовать:
if (isLoading) return <PageSkeleton variant="..." />;
if (error) return <ErrorState error={error} onRetry={refetch} />;
if (!data?.length) return <UnifiedEmptyState type="..." />;
return <Content />;
```

### 5.2 Унификация Suspense vs isLoading
- Страницы: `isLoading` из TanStack Query (контроль скелетонов)
- Lazy-компоненты (диалоги/шиты): `Suspense` с fallback={null}
- Документировать паттерн в CLAUDE.md

### 5.3 Верификация
- Проверить 5 ключевых страниц: Home, Library, Projects, Profile, Studio

---

## Фаза 6: Устранение hardcoded цветов и токен-дрифта

### 6.1 Мигрировать hardcoded цвета
- `BotMenuPreview.tsx`: заменить `bg-[#1a1a2e]` и другие hex на semantic tokens
- `like-button.tsx`: `text-red-500` → `text-state-danger`
- Прочие 14 инстансов → semantic tokens

### 6.2 Синхронизировать design-tokens.ts с tailwind.config.ts
- Добавить недостающие fractional spacing (`4.5`, `5.5`, `13`, `15`, `18`)
- Подключить safeAreaVars к tailwind theme

### 6.3 Верификация
- `grep -rE "bg-\[#|text-\[#|border-\[#" src/components/` — 0 результатов
- Dark mode проверка на всех мигрированных компонентах

---

## Фаза 7: Документация и enforcement

### 7.1 Обновить CLAUDE.md
- Добавить секцию "UI Component Decision Tree" (какой компонент использовать когда)
- Обновить Common Pitfalls с результатами оптимизации

### 7.2 ESLint правила
- Запрет импорта deprecated компонентов (error)
- Запрет `framer-motion` напрямую (только `@/lib/motion`)
- Warning на компоненты >400 строк
- Запрет hardcoded hex в className

---

## Порядок выполнения и приоритеты

| Приоритет | Фаза | Оценка усилий | Влияние |
|-----------|-------|---------------|---------|
| P0 | Фаза 1 (Cleanup deprecated) | 2-3 часа | Высокое — убирает confusion |
| P0 | Фаза 6 (Hardcoded цвета) | 1-2 часа | Среднее — консистентность |
| P1 | Фаза 2 (Унификация дублей) | 4-6 часов | Высокое — сокращает API surface |
| P1 | Фаза 3 (Анимации) | 2-3 часа | Среднее — визуальная консистентность |
| P2 | Фаза 4 (Рефакторинг крупных) | 8-12 часов | Высокое — maintainability |
| P2 | Фаза 5 (Loading паттерны) | 3-4 часа | Среднее — UX консистентность |
| P3 | Фаза 7 (Документация + lint) | 2-3 часа | Высокое — предотвращает регрессии |

**Общий объём:** ~25-35 часов работы, разбитых на независимые фазы.

## Верификация (end-to-end)

1. `npm run lint` — 0 ошибок
2. `npm test` — все unit-тесты проходят
3. `npm run build` — билд без ошибок
4. `npm run size` — bundle <950KB
5. `npm run dev` → визуальная проверка: Home, Library, Projects, Studio, Player, Settings
6. Mobile проверка через Playwright: `npm run test:e2e:mobile`
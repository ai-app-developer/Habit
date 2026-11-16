# Syllabus Tracker

## 1. App Idea & Full Feature List

**Syllabus Tracker** is an offline-first personal study and syllabus tracking Android application with no login and no backend. Users organize their study material into Books and Chapters, track chapter completion, view computed overall and per-book progress, set a daily chapter goal, monitor weekly study trends, and receive daily study reminder notifications.

### Features
- **Books & Chapters Management**: Create, rename, and delete Books (with confirmation dialog and cascade chapter deletion). Add, edit, check/uncheck, and delete Chapters (with confirmation dialog).
- **Computed Progress**: Overall syllabus percentage, book count, total chapters, completed chapters, remaining chapters, and per-book completion percentages are computed reactively from Room DB.
- **Home Summary Dashboard**: Circular progress ring, Books/Chapters/Completed stats row, Today's Plan card with editable daily chapter goal, and Continue Studying card linking directly to the last opened book and next incomplete chapter.
- **Search, Filter & Sort**: Search books by title, filter by `All` / `In Progress` / `Completed`, and sort by recently added or alphabetical order.
- **Quick Add Chapter Sheet**: Accessible from the center `+` button in the bottom navigation bar across primary screens, with book dropdown, chapter name validation, numeric chapter number validation, and "Mark as Completed" switch.
- **Progress Analytics**: `Overall` and `By Book` tabs; overall percentage card with cumulative progress chart and Completed/Remaining/Total breakdown; weekly `Study Trend` bar chart (`This Week` / `Last Week`) computed from `completedAt` timestamps.
- **Daily Study Reminders**: Scheduled via `AlarmManager` at a user-chosen time (default 8:00 PM), showing remaining chapters for the day (e.g., `"2 chapters left today. Keep going!"`). Requests `POST_NOTIFICATIONS` at runtime on Android 13+ and reschedules after reboot via `BootCompletedReceiver`.
- **Empty States**: Clean empty states with call-to-action buttons when no books or no chapters exist.

---

## 2. Complete Prompt (Verbatim)

```text
Build a native Android app "Syllabus Tracker" using Kotlin + Jetpack Compose (Material 3). The attached image is the exact UI reference: recreate all 4 screens pixel-faithfully (layout, spacing, corner radius, icons, typography, bottom nav).

TARGET: Android 14 (API 34), minSdk 26. Offline-first, no login, no backend.

CORE IDEA
Personal syllabus tracker. I add Books, add Chapters inside each book, mark chapters completed, and the app shows overall % of syllabus finished. Daily push notification reminds me to study. Home screen shows everything in summary so I never need to open details.

SCREENS (match the image)
1. Home: title + subtitle, bell icon, circular progress ring (overall %), stats row (Books / Chapters / Completed), "Today's Plan" card (e.g. "Complete 2 chapters" — user-settable daily chapter goal), "Continue Studying" card (last opened book + next incomplete chapter, tap to open).
2. Books: search, filter chips (All / In Progress / Completed), book cards with icon, "x / y chapters", percent, thin progress bar, chevron. "Add Book" button at bottom. Tapping a book opens its chapter list where each chapter can be checked/unchecked, edited, deleted.
3. Add Chapter (bottom sheet): book dropdown, Chapter Name, Chapter Number, "Mark as Completed" switch, "Add Chapter" button.
4. Progress: Overall / By Book tabs, % card with simple chart, Completed / Remaining / Total, "Study Trend" weekly bar chart (chapters completed per day, This Week).
Bottom nav: Home, Books, center "+" button (quick add chapter), Progress, More (settings: notification time, daily goal, about).

FUNCTIONALITY
- Room database: Book(id, name, createdAt), Chapter(id, bookId, name, number, isCompleted, completedAt). Progress is always computed from DB.
- MVVM + StateFlow + Hilt (or manual DI), single-activity, Navigation Compose.
- Daily push notification via WorkManager/AlarmManager at user-chosen time (default 8:00 PM). Message example: "2 chapters left today. Keep going!" Request POST_NOTIFICATIONS permission at runtime (Android 13+). Re-schedule after reboot.
- Empty states for no books / no chapters (simple text + button).
- Delete with confirmation. Input validation (name required, number numeric).

DESIGN RULES (very important)
- Must look like it was made by a real human developer, NOT AI-generated. Clean, minimal, nothing extra.
- Colors: white / very light grey-blue background (#F6F7FB), cards white with subtle 1dp elevation or hairline border, single primary indigo (#4F46E5), light indigo tint for chips/icon backgrounds (#EEF0FF), success green (#22A06B) only for completed checks. Text: near-black #1A1A2E, secondary grey #6B7280.
- NO glow, NO neon, NO gradients on UI, NO glassmorphism, NO decorative blobs. Flat, solid colors only.
- Animations: minimal and fast (max 150–200ms, standard fade/ripple only). No slow or looping animations.
- Use system font (Roboto), standard Material icons. Corner radius 16dp cards, 12dp inputs.
- No extra features, banners, ads, or placeholder content beyond what is listed.
```

---

## 3. Design Tokens

### Colors (Flat, Solid Only)
- `AppBackground`: `#F6F7FB`
- `CardSurface`: `#FFFFFF`
- `HairlineBorder`: `#E8EAF0`
- `PrimaryIndigo`: `#4F46E5`
- `LightIndigoTint`: `#EEF0FF`
- `SecondaryIndigoBar`: `#C7D2FE`
- `ProgressTrack`: `#E5E7EB`
- `SuccessGreen`: `#22A06B`
- `LightGreenTint`: `#E8F7F0`
- `TextPrimary`: `#1A1A2E`
- `TextSecondary`: `#6B7280`
- `ErrorRed`: `#DC2626`
- `LightRedTint`: `#FEE2E2`

### Corner Radius & Elevation
- Cards / Dialogs: `16.dp` corner radius, `1.dp` hairline border (`#E8EAF0`), `1.dp` subtle shadow elevation
- Inputs / Dropdowns / Icon Containers: `12.dp` corner radius
- Modal Bottom Sheet Top Corners: `24.dp`
- Primary Pill Buttons / Filter Chips: `26.dp` – `50%`
- Study Trend Bar Top Corners: `6.dp`

### Typography
- Font Family: System default (`Roboto`)
- Headline Large: `28.sp` Bold
- Headline Medium: `24.sp` Bold
- Title Large: `18.sp` Bold
- Title Medium: `16.sp` SemiBold
- Body Large: `15.sp` Normal
- Body Medium: `13.sp` Normal
- Label Large/Medium/Small: `14.sp` SemiBold / `12.sp` Medium / `11.sp` Medium

### Animation Rules
- Max duration `150–180ms` (`tween(150)` / `tween(180)`)
- Standard fades, short ring arc sweep, and Material ripples only
- No glow, no neon, no gradients, no glassmorphism, no decorative blobs, no looping animations

---

## 4. Tech Stack, Package Structure & DB Schema

### Tech Stack
- **Language**: Kotlin
- **UI**: Jetpack Compose + Material 3
- **Navigation**: Navigation Compose (`@Serializable` type-safe routes)
- **Persistence**: Room (`BookEntity`, `ChapterEntity`, `SyllabusDao`, `SyllabusDatabase`) + DataStore Preferences (`UserPreferencesRepository`)
- **Architecture**: MVVM + `StateFlow` + Repository + Manual DI (`SyllabusApplication`)
- **Notifications**: `AlarmManager` + `BroadcastReceiver` (`StudyReminderReceiver`, `BootCompletedReceiver`) + `NotificationCompat`

### Package / Folder Structure
```text
app/src/main/java/com/example/
├── MainActivity.kt
├── SyllabusApplication.kt
├── data/
│   ├── local/
│   │   ├── SyllabusDatabase.kt
│   │   ├── dao/SyllabusDao.kt
│   │   └── entity/
│   │       ├── BookEntity.kt
│   │       └── ChapterEntity.kt
│   ├── preferences/
│   │   └── UserPreferencesRepository.kt
│   └── repository/
│       └── SyllabusRepository.kt
├── notifications/
│   ├── BootCompletedReceiver.kt
│   ├── NotificationHelper.kt
│   ├── StudyReminderReceiver.kt
│   └── StudyReminderScheduler.kt
└── ui/
    ├── components/
    │   ├── AddChapterSheet.kt
    │   ├── BottomNavBar.kt
    │   └── Dialogs.kt
    ├── navigation/
    │   └── Routes.kt
    ├── screens/
    │   ├── BookDetailScreen.kt
    │   ├── BooksScreen.kt
    │   ├── HomeScreen.kt
    │   ├── MoreScreen.kt
    │   └── ProgressScreen.kt
    ├── theme/
    │   ├── Color.kt
    │   ├── Theme.kt
    │   └── Type.kt
    └── viewmodel/
        └── SyllabusViewModel.kt
```

### Room DB Schema
- **`books` table**: `id: Long` (PK, autoGenerate), `name: String`, `createdAt: Long`
- **`chapters` table**: `id: Long` (PK, autoGenerate), `bookId: Long` (FK -> `books.id`, `ON DELETE CASCADE`, indexed), `name: String`, `number: Int`, `isCompleted: Boolean`, `completedAt: Long?`

---

## 5. Screen-by-Screen Description

1. **Home (`HomeScreen`)**: Displays `"Syllabus Tracker"` title + subtitle, notification bell icon with pending-goal dot indicator, centered circular progress ring showing overall syllabus %, 3-column stats card (`Books`, `Chapters`, `Completed`), clickable `"Today's Plan"` card showing daily chapter goal progress, and `"Continue Studying"` card pointing to the last opened book and next incomplete chapter (or empty state CTA when no books exist).
2. **Books (`BooksScreen`) & Book Chapters (`BookDetailScreen`)**: Header with inline expandable search field and sort/add overflow menu; pill filter chips (`All`, `In Progress`, `Completed`); book cards with icon box, title, `x / y chapters`, percentage, progress bar, and chevron; bottom `"+ Add Book"` pill button. Tapping a book opens `BookDetailScreen`, showing book progress summary and ordered chapters with green completion check toggles, edit/delete actions (with confirmation), and empty state.
3. **Add Chapter Sheet (`AddChapterBottomSheet`)**: Modal bottom sheet opened via the center `+` bottom nav button or from `BookDetailScreen`. Features close button, book dropdown selector (plus quick `"New Book"` action if no books exist), validated `Chapter Name` input, validated numeric `# Chapter Number` input, `"Mark as Completed"` switch, and full-width `"Add Chapter"` pill button (also supports editing existing chapters).
4. **Progress (`ProgressScreen`)**: Segmented `Overall` / `By Book` pill switcher. `Overall` shows the percentage summary card with flat solid cumulative curve, `Completed / Remaining / Total` row, and `Study Trend` 7-day bar chart (`This Week` / `Last Week` dropdown). `By Book` lists per-book progress cards.
5. **More (`MoreScreen`)**: Settings screen with Daily Chapter Goal editor, Daily Study Reminder switch (handles Android 13+ `POST_NOTIFICATIONS` permission), Reminder Time picker (default 8:00 PM), and About section.
6. **Bottom Navigation (`SyllabusBottomBar`)**: 5-slot white bar with hairline top border: `Home`, `Books`, circular indigo `+` button, `Progress`, and `More`.

---

## 6. Current Progress Checklist

### Done
- [x] Project Gradle configuration (`minSdk 26`, `applicationId`, Room, DataStore, Navigation Compose, Kotlin Serialization)
- [x] Custom adaptive launcher icon and monochrome notification small icon
- [x] Flat Material 3 design system (`Color.kt`, `Type.kt`, `Theme.kt`)
- [x] Room database (`BookEntity`, `ChapterEntity`, `SyllabusDao`, `SyllabusDatabase`)
- [x] DataStore preferences (`UserPreferencesRepository`) and reactive `SyllabusRepository`
- [x] Daily reminder notifications (`NotificationHelper`, `StudyReminderScheduler`, `StudyReminderReceiver`, `BootCompletedReceiver`)
- [x] `SyllabusViewModel` and `MainActivity` navigation host
- [x] All screens & modals (`HomeScreen`, `BooksScreen`, `BookDetailScreen`, `AddChapterBottomSheet`, `ProgressScreen`, `MoreScreen`, `Dialogs`, `SyllabusBottomBar`)

- [x] `compile_applet` build verification and `gradle :app:testDebugUnitTest` Robolectric test suite (Room DB, progress calculations, cascade delete, weekly trend, reminder scheduler)

### Pending
- None (all core features and verifications complete)

### Known Bugs
- None identified.

---

## 7. Resume Prompt

Read this file fully and continue building this exact app from the pending items without changing the design.

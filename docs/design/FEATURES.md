# Feature inventory — RPG Habit Tracker

The complete list of what the app does today, before the device-shell redesign.
This is the regression checklist: after every later phase, walk it and confirm
nothing here is missing or broken.

Baseline: `main` at `6c8c209`, **v1.9.0**, single file `index.html`, 5,570 lines (wc -l).
Line numbers are from that commit and will drift as work proceeds; names are the
reliable reference.

> **Retired in the clean-up phase (before Phase 6):** the pixel-art avatar and its
> colours, class titles and total level, cosmetics and their unlock popup, the item and
> equipment system (including its attribute bonuses), and the six colour themes. They are
> gone from the app, so skip them when walking this list. Their saved data fields are
> left untouched in storage. See decisions 2, 3, 11, 12 and 13 in DECISIONS.md.
>
> **Moved in Phase 11:**
> - The header's name field, the footer's IMPORT / EXPORT / BACKUPS / RESET ALL,
>   the account bar and the sound switch now live in the Settings popup.
> - Every `confirm()` is the app's own popup.
> - The unreachable legacy project editor (`modal-project`) was removed.

Names below are the app's **current** ones. The rename to Daily / Goals / Tools,
Reserve, Marks, Records, Awards and Tasks happens in Phase 4, and this file is
not rewritten when it does.

---

## 1. Shell of the app

| Piece | Where | What it does |
|---|---|---|
| Header | `#header`, markup ~805 | Title, editable character name (`#char-name`, saves on change), subtitle |
| Tab bar | `#tab-bar`, `switchTab` 2346 | Three tabs: Daily, Quests, Inventory. `TAB_NAMES` 2345 |
| Account bar | `#user-info-bar` | Sync dot, signed-in email, SIGN OUT |
| Footer | `#footer`, `updateFooter` 3669 | Date, IMPORT, EXPORT, BACKUPS, RESET ALL, version, last sync time |
| Sound toggle | `toggleSound` 2330, `#sound-btn` | Mute/unmute; four tones from `playCheck` 2326 onward. Persisted in `rpg_sound` |
| Theme | `applyTheme` 1643, `COLOR_THEMES` 1635 | Six colour themes; chosen in Customise; `data.appTheme` |

## 2. Daily tab (`#tab-daily`)

Three columns: character, tasks, attributes.

**Character panel** (`#daily-char-panel`, `renderDailyCharacter` 4521)
- Canvas avatar, four layers: base plus headwear, accessory, weapon overlays
  (`#d-base` `#d-cosm-hw` `#d-cosm-ac` `#d-cosm-wp` `#d-aura`), drawn by
  `drawBase` 3689 / `drawSprite` 3713 / `renderCosmeticOverlays` 4768
- Mood badge `#daily-mood-badge` and thought bubble `#daily-thought-bubble`
  (`updateDailyThoughtBubble` 4748), both open the Wellness report
- Equipped title and total level (`#daily-class-title-lg`, `#daily-level-lg`)
- Buttons: WELLNESS, CUSTOMIZE, STATS (manage active attributes)
- Equipped cosmetics strip (`updateDailyCosmeticsDisplay` 4803)
- Goals to watch (`renderDailyGoalsWatch` 4717): at-risk and failing goals with
  progress bars

**Tasks panel** (`#tasks-list`, `renderTasks` 2381)
- One row per task: icon, name, attribute tag, streak, points, checkbox
- `toggleTask` 3114 ticks and unticks, updates stats, history, streaks,
  `lastCompleted`, and fires float text, sound and achievement checks
- Yesterday reminder `#yesterday-reminder` (`updateYesterdayReminder` 4023)
- LOG PAST opens Backdate; MANAGE opens the task manager; ? opens the tutorial

**Attributes panel** (`#stats-list`, `renderStats` 2419)
- Nine attributes (`STATS_DEF` 1613): strength, focus, intelligence, charisma,
  creativity, initiative, discipline, agility, vitality
- Each: level bar, value, tier pips, neglect badge, item bonus line, and an
  inactive badge for attributes switched off
- Clicking an attribute opens its history (`openTaskStats` 4554)

## 3. Quests tab (`#tab-quests`)

Three panels: quests, goals, achievements.

**Quests** (`renderCustomQuests` 3290, `renderCompletedQuests` 3332)
- Sub-tabs QUESTS / COMPLETED (`switchQuestTab` 3250)
- Long-running projects with name, description, start date, hours logged,
  average per day, and attribute rewards
- Time tracking optional per quest (`trackTime`); average over all days or work
  days only (`averageMode`)
- Log hours (`logQuestHours` 3349), complete (`completeQuest` 3358, pays out the
  rewards), delete (`deleteQuest` 3375, `deleteCompletedQuest` 3380)
- + NEW opens the quest editor

**Goals** (`#goals-list-quests`, `renderGoals` 4338 / `renderGoalsInto` 4342)
- Three types: task frequency, attribute increase, activity time
- Weekly or monthly period, target count or hours
- Status derived: on track, at risk, failing (`calculateGoalProgress` 4158)
- Weekly streak per goal, grace period (`isGoalInGracePeriod` 4073)
- Progress bar, required pace per day, why-line (`whyGoals` 2165)
- Clicking a goal opens its history (`openGoalStats` 4612); delete `deleteGoal` 4397

**Achievements** (`renderAchievements` 3264)
- Sub-tabs IN PROGRESS / COMPLETED (`switchAchvTab` 3257)
- 11 built-in achievements (`ACHIEVEMENT_QUESTS` 4083): dedication-1 and 2,
  perfectionist, renaissance, specialist-1, balanced, achiever-1 and 2,
  consistent, fashion-icon, completionist
- Live progress (`calcAchievementProgress` 3161), auto-completion and payout
  (`checkAchievements` 3224)

## 4. Inventory tab (`#tab-inventory`)

**Treasury** (`renderInventoryTab` 2535)
- Gold total `#gold-display`
- Month history `#budget-history-list`
- Opens the budget ledger

**Stopwatch** (`swAction` 2896 and neighbours, state `_swState` 2868)
- States: idle, running, paused. Display `#sw-display`, ticking twice a second
- START picks a target from time-trackable quests and logbook activities
- PAUSE / RESUME (`swResume` 2937), RESET (`swReset` 2998)
- Stopping confirms, then writes the session to the quest or activity
  (`swConfirmStop` 2966)

**Logbook** (`renderLogbook` 2570)
- Index of activities (`renderLogbookIndex` 2576) with total and average per week
- Per-activity view (`lbOpenActivity` 2602, `renderLogbookActivity` 2611): date
  range, day/week/month buckets, bar chart, page-flip animation (`lbFlip` 2593)
- Manual time entry (`lbSubmitTime` 2666), which can auto-complete a linked task
  (`autoCheckLinkedTasks` 3565)
- Manage activities: add, edit, delete (`openManageActivities` 2688 and
  neighbours)

**Note:** the item/equipment grid is dead code. `renderInventoryGrid` 2730,
`openAddItem` 3042, `saveItem` 3053 and the item tooltip still exist, and
`getItemBonuses` 2364 still grants attribute bonuses from saved items, but no inventory
grid element exists in the markup, so nothing renders or can be added.
Saved items in existing data still count toward attribute totals. Decide in a
later phase whether to remove it or restore it.

## 5. Popups (20)

| Id | Opened by | Purpose |
|---|---|---|
| `modal-taskedit` | `openAddTask` 3522 | Add or edit a task: name, icon, attribute, points, linked activity |
| `modal-taskmgr` | `openTaskMgr` 3495 | List, edit and delete tasks |
| `modal-managestats` | `openManageStats` 3864 | Switch attributes on and off |
| `modal-backdate` | `openBackdate` 3897 | Tick tasks for an earlier day, up to 30 days back |
| `modal-task-stats` | `openTaskStats` 4554 | Attribute and task history with a chart |
| `modal-quest` | `openCreateQuest` 3387 | Add or edit a quest, with repeatable reward rows |
| `modal-project` | `openAddProject` 3598 | Legacy project editor, still reachable via `openEditProject` |
| `modal-goal` | `openAddGoal` 4416 | Add or edit a goal; fields follow the goal type |
| `modal-goal-stats` | `openGoalStats` 4612 | Goal history, weekly performance, streak |
| `modal-budget` | `openBudgetModal` 2752 | Ledger: income, expenses, balance, transactions, close month |
| `modal-txn` | `openAddTxn` 2800 | Add or edit a transaction |
| `modal-item` | `openAddItem` 3042 | Item editor, unreachable, see the note above |
| `modal-sw-select` | `swAction` 2896 | Pick what to time |
| `modal-sw-stop` | `swConfirmStop` 2966 | Confirm stopping, then register the session |
| `modal-manage-activities` | `openManageActivities` 2688 | Add, edit, delete logbook activities |
| `modal-customize` | `openCustomize` 3778 | Presets, hair, skin, clothing, titles, cosmetics, app theme |
| `modal-wellness` | `openWellnessReport` 4293 | Why the character's mood is what it is |
| `modal-backups` | `openBackups` 2191 | Local snapshots, with restore |
| `modal-tutorial` | `showTutorial` 3462 | Per-tab help: daily, quests, inventory |
| `modal-welcome` | first run | Introduction on first visit |

Plus four non-modal overlays: level-up `#lu-overlay` (`showLU` 3622), cosmetic
unlock `#cosm-unlock-overlay` (`showCosmeticUnlock` 4835), emoji picker
`#emoji-picker` (`openEmojiPicker` 2063), item tooltip `#item-tooltip`
(`showItemTooltip` 3013), and the sign-in overlay `#auth-overlay`.

## 6. Character progression

- **Titles**: `CLASS_MILESTONES` 1624, nine tiers, unlocked by attribute
  thresholds (`checkTitleUnlocks` 3098), equipped via `equipTitle` 4912
- **Cosmetics**: 36 items (`COSMETICS_DEF` 1664) in four slots, unlocked by
  title (`checkCosmeticUnlock` 4816), equipped in Customise
- **Appearance**: hair, skin and clothing colours, five presets (`PRESETS` 3767)
- **Mood**: five states from attribute neglect (`calculateMood` 4222,
  `updateMoodDisplay` 4253), shown on the avatar, the badge and the thought
  bubble. This is the existing mood, separate from the six sprite moods the
  redesign introduces
- **Level**: total level is the number of unlocked titles

## 7. Background behaviour

| Behaviour | Where | Notes |
|---|---|---|
| Day rollover | `checkNewDay` 2105 | Rebuilds today's checkboxes from history, rolls weekly goal streaks, resets per-day state. Also runs on a 60-second timer started in `init` 4946 |
| Month rollover | `checkMonthRollover` 3079 | Archives the month and banks the surplus |
| Streak recalculation | `_syncStreaks` 2152, `calcStreak` 3730 | Runs on every load. Streaks have a one-day grace period |
| Checkbox reconciliation | `_reconcileTodayChecked` 2297 | Keeps checkboxes and history in step |
| Future-dated cleanup | `findFutureEntries` 2234, `cleanFutureEntries` 2257 | Fixes entries dated ahead of today |
| Achievement and title checks | `checkAchievements` 3224, `checkTitleUnlocks` 3098 | Run after each completion |
| Attribute backfill | `initStatLastIncreased` 4063 | Fills in neglect dates from history |
| Legacy migrations | `load` 1715 | v1 storage, projects to quests, new attributes, cosmetic backfill |

## 8. Data and sync

- **Storage**: `rpg_tracker_v2` is the save, `rpg_tracker_v1` the legacy save,
  `rpg_sound` the sound setting, `hasVisitedBefore` the first-run flag,
  `rpg_backups_v2` the snapshots, `rpg_sync_base` the sync base
- **Save shape**: `freshData` 1826 lists every field
- **Accounts**: email and password, or Google; `#auth-overlay` and the `fb*`
  functions from 5432. Play offline is possible (`fbSkip` 5494)
- **Sync** (v1.9.0, do not disturb): three-way merge engine `_syncMergeDocs`
  5238 and the driver from 5266. Every cloud write is a transaction merged
  against the live value. Writes carry `_syncV:2`, which a Firebase rule
  requires, so older versions cannot write
- **Snapshots**: daily for 14 days, 10 event snapshots, plus migrated v1.8.0
  ones (`_makeBackup` 1916, `_dailyBackup` 1926, `_loadBackups` 1902). Restore
  replaces the data on every device (`restoreBackup` 2213)
- **Import and export**: JSON file out (`exportData` 3641), in (`handleImport`
  3646). Import replaces everywhere
- **Reset**: `resetAllData` 3662, snapshots first

## 9. Cross-cutting

- Emoji picker on every icon field (`attachEmojiPicker` 2057, `EMOJI_CATEGORIES` 2048)
- Float text and animations: `spawnFloat` 3635, `spawnFloatCenter` 3636,
  `flashTask` 3632, `pulseBar` 3633
- Number steppers (`numStep` 1963), used in 20 places
- Watchdog banner if the script fails to parse, ~5530
- Responsive layout via `@media` queries; works on phone and desktop today

---

## How to use this list

After each phase, check every line above still holds. The ones most likely to be
disturbed by the redesign, and worth re-testing every time:

1. Ticking and unticking a task updates attributes, streaks and history
2. Backdating writes to the right day
3. Stopwatch start, pause, stop, and the session landing in the right place
4. Logging hours manually, and a linked task auto-completing
5. Goal status and streaks after a week rolls over
6. Month close moving the balance into gold
7. Awards unlocking and paying out their stat rewards
8. Sync between two devices, and snapshot restore
9. Import and export round-tripping
10. Everything reachable on both phone and desktop

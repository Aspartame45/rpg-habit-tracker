# Redesign decisions

Settled questions, so they are not reopened. Raised from the Phase 0 inventory and the Phase 3 review.

| # | Question | Decision |
|---|---|---|
| 1 | The spec bans emoji, but task icons are emoji chosen by the user | **Keep emoji as task icons.** They are user data. A different icon system may be considered later, separately |
| 2 | The item and equipment subsystem is dead code, but still grants attribute bonuses | **Remove it.** Attribute totals will change for anyone holding saved items, and that is accepted |
| 3 | Six app colour themes exist today | **Retire them.** Shell materials replace them |
| 4 | The mobile Daily screen needs a "highlighted task" flag that does not exist | **Build it**, as a new feature, set manually by the user |
| 5 | Two mood systems would otherwise coexist | **Keep the current mood behaviour**, driven by attribute neglect and feeding the Wellness report, but **replace its five states with the six from the spec**: thriving, happy, steady, concerned, frustrated, drained |
| 6 | Should destructive actions be red? | **No.** Red is reserved for the stopwatch STOP key. Destructive actions are plain membrane pills |
| 7 | The spec includes a d-pad, but nothing navigates between days, quests or slots yet | **No d-pad for now.** Add it when a feature needs it, or if testing shows an existing screen would benefit |
| 8 | Keys sitting on the screen currently depress, because Phase 3 asked for it | **Accepted for now.** They move onto the shell as each layout is rebuilt |
| 9 | Which keys are tactile? | **The shell is tactile, the screen is flat.** On mobile, any key not built into the shell is flat. On desktop, each panel follows the same rule: the sprite unit's WELLNESS, CUSTOMISE and ADD TASK keys sit on its shell and stay tactile, while everything inside its screen is flat. Applied as one rule on the screen, so it holds as pages move in. Supersedes 8 |
| 10 | Should stats keep a bar that can always be filled further? | **Yes, the tier pips are back.** Each stat's bar shows progress through its current tier of ten points (never full, at least 4%), and below it one pip per completed tier, up to ten, then "+N". `LV` is the sum of completed tiers, and the rack footer reads `NEXT TIER IN N POINTS`. Supersedes the Phase 5 note on stat bars |
| 11 | The old pixel character | **Removed completely.** The sprite replaces it |
| 12 | The old title system | **Removed.** It may be replaced by something else later, but not in its current form |
| 13 | The old cosmetics (hats, weapons, capes), unlocked by titles and worn by the pixel character | **Removed with them.** New sprite and shell cosmetics arrive with Customise (9b) and the award rewards (Phase 6, Phase 10) |

## Phase 5 — how the Daily page was reconciled

Where the README and turn 3 of the reference disagree, turn 3 wins, because the
phase is accepted against it:
- the sprite window is **5:7 portrait on desktop** (the README says 4:3, which is
  kept for the one-column layout),
- unit padding is **14/14/16**,
- the stat rack sweeps **bottom-right**, like the sprite unit.

- **The sprite covers its window.** It takes the next whole pixel scale up and the
  window crops the few spare pixels at its edges, so pixels stay exactly square and
  the grid still reaches every edge. The reference stretched its canvas instead.
- **Points, not XP.** The reference says `+120 XP TODAY` and `NEXT LEVEL IN 380 XP`.
  The app has no XP, so these read `+N POINTS TODAY`. (The title-based `LV` and
  `NEXT TITLE` readouts that first shipped here were replaced by tiers in 5.5,
  decisions 10 and 12.)
- ~~Stat bars show the value, clamped to 4–100%.~~ Superseded by decision 10.
- **Homes for controls the reference does not draw:** LOG PAST, MANAGE TASKS and
  help sit on the task unit's shell; MANAGE STATS sits on the stat unit's shell.
- **The old character panel left Daily.** The pixel avatar and the equipped title
  remain in Customise; the old mood badge and thought bubble are replaced by the
  sprite's badge, with Wellness holding the explanation; goals to watch and the
  yesterday reminder moved into the task list's screen.

## Phase 5.5 — the phone is the device

Built against turn 6a. On a phone the whole app is one device filling the screen
with a 16px gutter; on desktop the same markup draws nothing extra and the three
units stand as before.
- **Keys at the foot, on the shell:** DAILY / GOALS / TOOLS and a full-width action
  key that follows the page — `+ ADD TASK` on Daily, `+ ADD GOAL` on Goals (until
  the Goals segments in Phase 6), hidden on Tools (its instruments bring their own
  keys in Phase 8). They are the only tactile keys on a phone (decision 9).
- **The sprite collapses as you scroll**, interpolated, from full width at 4:3 to a
  132×58 pane in a frozen strip beside the mood badge and `N OF M TODAY`. The list
  moves one-to-one with the finger. In the strip the sprite fits inside rather than
  covering, so the brows are never cropped. Badge opens Wellness and the sprite
  opens Customise at both sizes.
- **Highlighted tasks (decision 4):** a toggle in the task editor. When any are set,
  a HIGHLIGHTED section leads the list with `SCROLL FOR ALL N ▾`; the full list
  follows. Phone only; desktop shows the one list.
- **Page change:** the incoming page slides in from the right over 220ms. The
  reference also slides the outgoing page out; that is approximated for now.
- **Everything else stays reachable on the screen:** LOG PAST, MANAGE TASKS, help
  and MANAGE STATS as flat buttons; the stat rack at the end of Daily until the
  Wellness rebuild takes it (9b); then account, data tools, name and sound.
- **Rows are larger for thumbs:** 14/13 padding, 20px checkbox, 16px headline.

## Clean-up phase — before Phase 6

Removed from the app: the pixel character and its colour controls, class titles,
cosmetics and their unlock popup, the item and equipment system, and the six colour
themes (decisions 2, 3, 11, 12, 13).
- **Saved data is left alone.** The fields `unlockedTitles`, `equippedTitle`,
  `unlockedCosmetics`, `equippedCosmetics`, `inventory`, `characterCustomization` and
  `appTheme` stay in storage and in sync exactly as they are. The app no longer reads
  or creates them. Deleting them now would sync the deletion to devices still on the
  live version and break them there. They can be dropped once the redesign ships.
- **Stat totals are the stat values.** Item bonuses no longer count (decision 2).
- **Two awards went with the cosmetics:** Fashion Icon (unlock 10 cosmetics) and
  Completionist (unlock all). Stat points already earned from them are kept.
- **Level-up popup** now only marks a stat reaching a multiple of ten, the same moment
  a tier pip fills. "New Title" lines are gone.
- **Customise** is a placeholder until it is rebuilt in 9b. The CUSTOMISE keys, and
  tapping the sprite, still open it.
- The mood badge and thought-bubble code is left for the mood swap in Phase 9.

## Phase 6 — the Goals page

Built against turn 4b on desktop and 6b on the phone.
- **Desktop:** three equal units (Projects, Goals, Awards), each a fixed 548px screen
  with its own scroll, footer counts and a `SCROLL ▾` hint that shows only while
  there is more below. Filters and the ADD keys are tactile keys on the shell; every
  card and button inside the screens is flat. Between 601 and 1099px the units stack.
- **Phone:** one segment at a time, switched by three flat cells at the top of the
  screen (GOALS first). Filters sit inside the screen. The shell's header names the
  page and reads out the segment; the action key takes its verb: `+ ADD GOAL`,
  `+ ADD PROJECT`, and none on Awards.
- **Goal state** (on track / at risk / failing) is the existing live logic from
  `calculateGoalProgress`. New goals (the grace period) read `· NEW` on the type line.
- **Closing a period is now decided from history** (`_closeGoalPeriods`), for weekly
  and monthly goals alike. When a week or month ends, the app checks whether the goal
  was met in it, instead of trusting whatever status was last shown. Every period
  that passed while the app was closed is judged separately. A goal set up part-way
  through a period isn't blamed for missing it. Each goal remembers whether its last
  period was met, so `STREAK BROKEN` now shows for monthly goals too.
- **Achiever I and II** count every time a weekly goal is met (they never
  progressed before: nothing increased the count). Weeks already met are counted
  once, from history, when this version first runs.
- **Consistent** completes when the same weekly goal is met four weeks in a row
  (it used to require every goal to be on track at once).
- **Goal names are generated** from the goal ("Exercise 5 times a week", "7 hours
  of Reading a week", "Raise INT by 8"), because goals have no name field.
- **Where the controls went:** a goal card opens its history, which now holds
  EDIT GOAL and DELETE. A project card has LOG HOURS (a stepper opens in the card),
  COMPLETE and EDIT; DELETE moved into the project editor. The completed archive
  keeps REMOVE.
- **Cosmetic awards.** The big ones now reward a shell material instead of stat
  points; the rest keep their points. There are five awards for the five
  unlockable materials:

  | Award | Why it's big | Material |
  |---|---|---|
  | First project (new) | first use of Projects | BRUSHED ALUMINIUM |
  | Consistent | four weeks of goals | CAST CONCRETE |
  | Renaissance | all nine stats in one day | POLISHED CHROME |
  | Perfectionist | every task, three days running | BRUSHED GOLD |
  | Dedication III (new) | a 30-day run | TRANSLUCENT GRAPE |

  Owned materials are **derived** (starters plus completed cosmetic awards), not
  stored, so they can't drift from the awards. Phase 10 reads `unlockedMaterials()`.
- **Awards are checked at start-up** as well as on every tick, so an award that is
  already earned (a new one, or one met on another device) completes straight away.

## Working arrangement

- The redesign lives on `redesign/device-v2`, branched from the live v1.9.0.
  It is **not deployed** until it is finished and approved. `main` stays live.
- The earlier `redesign/device-shell` branch is reference only. It predates the
  sync fix and the restructured handoff. Reusable parts are lifted from it per
  phase rather than merged.
- The app name is read from `APP_NAME`. `HABITRON` is a placeholder.

## Deferred, with a reason

| Item | Why it waits |
|---|---|
| ~~The legacy `zoom: 1.25`~~ | **Done in Phase 2.** Removed, with all 1,220 legacy pixel values multiplied by 1.25 so the existing app kept its size. New work uses the handoff's real pixel values |
| The old five-state mood constants | They stay until the moods are rewired, so the current avatar and Wellness report keep working meanwhile |

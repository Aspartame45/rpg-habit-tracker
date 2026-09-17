# Redesign decisions

Settled questions, so they are not reopened. Raised from the Phase 0 inventory.

| # | Question | Decision |
|---|---|---|
| 1 | The spec bans emoji, but task icons are emoji chosen by the user | **Keep emoji as task icons.** They are user data. A different icon system may be considered later, separately |
| 2 | The item and equipment subsystem is dead code, but still grants attribute bonuses | **Remove it.** Attribute totals will change for anyone holding saved items, and that is accepted |
| 3 | Six app colour themes exist today | **Retire them.** Shell materials replace them |
| 4 | The mobile Daily screen needs a "highlighted task" flag that does not exist | **Build it**, as a new feature, set manually by the user |
| 5 | Two mood systems would otherwise coexist | **Keep the current mood behaviour**, driven by attribute neglect and feeding the Wellness report, but **replace its five states with the six from the spec**: thriving, happy, steady, concerned, frustrated, drained |

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

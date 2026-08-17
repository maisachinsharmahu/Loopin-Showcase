# Loopin

**Showcase repository — screenshots and project overview only. Source code is closed.**

Most habit trackers die the same way: download it, feel motivated for
four or five days, one streak breaks, and the whole thing feels
pointless — so you delete it. Loopin is built around two things most
habit apps get wrong: your data doesn't need to live on someone else's
server just to remind you to drink water, and checking off a habit
shouldn't feel like homework.

<p align="center">
  <img src="assets/app_preview/home.png" width="260" alt="Loopin home screen" />
</p>

[Download the APK from Releases](https://github.com/maisachinsharmahu/Loopin-Showcase/releases/tag/v1.0.0)

## What it does

**A calendar that keeps today centered** — most habit apps make you
scroll to find today; Loopin's timeline keeps today's card centered no
matter how far back you scroll.

**An RPG layer, not a checklist** — completing a habit earns XP, levels
you up, and drops rewards. Finish several habits back to back and the
rewards queue and play out one after another instead of piling on top
of each other.

**Stats computed on-device** — streaks and completion rates update
instantly with no loading spinner, because nothing has to round-trip to
a server first.

**Achievements and a journey log**, so long-term progress shows up
somewhere, not just today's checklist.

**Rivals, without giving up privacy** — compare progress and share wins
with friends, while your actual habit data stays private.

**Local-first storage**, with optional backup straight to your own
Google Drive — not a company database.

## Screenshots

<table>
<tr>
<td><img src="assets/app_preview/home.png" width="200" alt="Home with centered timeline" /><br/><sub>Home</sub></td>
<td><img src="assets/app_preview/Daily%20Routine.png" width="200" alt="Daily routine view" /><br/><sub>Daily routine</sub></td>
<td><img src="assets/app_preview/Level%20Up.png" width="200" alt="Level up reward" /><br/><sub>Level up</sub></td>
<td><img src="assets/app_preview/XP%20Boost.png" width="200" alt="XP boost reward" /><br/><sub>XP boost</sub></td>
</tr>
<tr>
<td><img src="assets/app_preview/Bonus%20Drop.png" width="200" alt="Bonus loot drop" /><br/><sub>Loot drop</sub></td>
<td><img src="assets/app_preview/Stats%201.png" width="200" alt="Stats view" /><br/><sub>Stats</sub></td>
<td><img src="assets/app_preview/achievements.png" width="200" alt="Achievements" /><br/><sub>Achievements</sub></td>
<td><img src="assets/app_preview/Rival.png" width="200" alt="Rivals comparison" /><br/><sub>Rivals</sub></td>
</tr>
</table>

## Built with

Flutter — one codebase feeling native on both iOS and Android.

| Concern | Package |
|---|---|
| State management | `provider` |
| Navigation | `go_router` |
| Local database | `hive` |
| Auth & backend | `supabase_flutter` |
| Local notifications | `flutter_local_notifications` + `workmanager` |
| Encryption | `encrypt` |

## System architecture

Loopin's core (habits, XP, stats) is entirely local-first — Hive on
device, no round-trip required to check off a habit or see a stat
update. The **Rivals** social feature is the one piece that needs a
server, and it's deliberately kept separate: a small, typed Cloudflare
Worker with a KV store, handling only invite/connect/sync for that one
feature, so a person's core habit data is never routed through a shared
backend to make social comparison work.

This is architecture-level information, not the implementation — the
RPG reward-sequencing logic, the exact sync protocol, and the rest of
the app's internals stay out of this repo.

## What's next

- [x] Local-first storage and Drive backup
- [x] RPG mechanics and XP system
- [ ] On-device suggestions based on your own habit patterns
- [ ] Challenges between friends

## About this repository

This is a **showcase-only** repo — screenshots and a description of what
the app does, published to share the project publicly without exposing
the implementation. The actual source (both the app and its Rivals
backend) lives in separate private repositories. Want to see it running
properly? Download the APK above.

## License

All rights reserved. The name, design, screenshots, and underlying
application are proprietary; nothing in this repository is licensed for
reuse.

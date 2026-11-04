# FitRank is paused

**Paused on:** 2026-11-04

The live site is back to the local-only app: no sign-in, data stays in this browser's localStorage. The accounts and Supabase work (step 7, PR #5) was reverted on `main` in a single revert commit from the `revert-accounts` branch. The code is still in git history and on the `step-7-accounts` branch.

## Done (live)

- Steps 1–6 in docs/ROADMAP.md: workout logger, ELO and ranks, goals, history and stats, PWA (install, offline, iOS safe areas).
- Kept from PR #5 because it isn't account code: effort bonus +6 (stored ELO on old workouts is unchanged), the "once per day session bonus" note, and the `.DS_Store` ignore rule.
- Added after PR #5 and kept: the green redesign with tier rank badges, the Muscle map on Progress, the new app icon and logo, and link previews (title, description, image).

## In progress (reverted, not live)

Step 7, accounts and cloud storage:
- Supabase schema with row-level security, merge-based sync, per-account local storage (`u.<uid>.` key prefix), one-time device import.
- Google sign-in (default) and email one-time-code sign-in (built, off).
- Account settings: sync status, sign out, export, delete account.
- Privacy and Terms pages.
- Daily Supabase keep-alive GitHub Action.

## Left

- Everything in the step 7 merge gate (docs/ROADMAP.md): production flags, cross-device sign-in testing, publishing the Google consent screen, production env vars and redirect URLs.
- Then the optional items in the roadmap (App Store release, battles, food logging).

## Data note

Workouts logged on the live site while signed in (Oct 18 to now) are stored under `u.<uid>.fitrank.data.v3` in localStorage, and in Supabase. The local-only app reads only the unscoped keys (`fitrank.data.v3`, then `fitrank.data.v2`, then `fitrank.workouts.v1`), so those signed-in workouts won't show until accounts come back. Nothing was deleted. Don't clear site data in that browser and don't delete the Supabase project if you want them.

## How to resume

1. **Revert the revert (recommended).** On a new branch from `main`: `git revert 5253abf`, then fix any conflicts with work done since. This brings back all the step 7 code on top of the current app.
2. **Re-merge `step-7-accounts`: this won't work by itself.** Its commits are already in `main`'s history, so git treats the branch as merged and a plain merge brings back nothing. Use option 1, or cherry-pick the step 7 commits (`d7f25ab..a39334a`, minus the effort-bonus commit `34e9f4b`, plus the keep-alive `b799727`).
3. After restoring: `npm install` (brings back `@supabase/supabase-js`), set the env vars below, then work through the merge gate in docs/ROADMAP.md before merging.

## External services

| Service | Role | State while paused |
|---|---|---|
| Supabase | Postgres + auth for accounts | Pause the project in the Supabase dashboard (the keep-alive was removed, so a free project also auto-pauses after a week). Paused free projects can be restored for a limited time, so check Supabase's current policy. |
| Netlify | Hosting, deploy previews | Still hosts the live local-only site. Env vars can stay set; the local-only build doesn't read them. |
| Google Cloud | OAuth client and consent screen for Google sign-in | Unused while paused. Leave it as is or disable the OAuth client. |
| GitHub | Repo secrets for the keep-alive Action | The Action was removed, so the secrets are unused. |

### Env var names (no values here)

- Netlify / `.env.local` (build time): `VITE_SUPABASE_URL`, `VITE_SUPABASE_ANON_KEY`, `VITE_GOOGLE_SIGNIN`, `VITE_EMAIL_SIGNIN`
- GitHub repository secrets: `SUPABASE_URL`, `SUPABASE_ANON_KEY`

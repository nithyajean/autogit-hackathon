---
title: Habit Streaks, Kitchen Edition
app_type: habit-streaks
wallet: 0xf3344960A9b5C6b9d58a1D896C9023C54015422a
---

A habit tracker for people who have already tried five habit trackers and quit each one. The whole app sits on a single warm dark page that feels like a kitchen counter at night. You check things off, you watch a year of squares fill in, and you read your own numbers back to yourself. Nothing else. No login, no cloud sync, no streaks shaming you the second you miss a day.

The mood. Think low light, generous padding, soft type. Background is a warm brown almost black, the panels are slightly lifted, accents are a real plant green not a neon highlighter. The check boxes are large enough to feel tactile on touch. There is no glass morphism, no gradients, no skeletons. If a friend left this open on their laptop on the counter at 10 pm, it should look like something they live with.

## Layout

One page, three regions stacked top to bottom, max width 880px, centered.

Region 1, the top, is today. A header line that says the date in a long form like "Saturday, 11 May", with the day of the week in regular weight and the rest in a slightly softer color. Below that, a list of habits for today, one per row. Each row is around 64px tall and has, left to right, a large round check button (28px), the habit name in 18px, a small identity line in 13px slightly muted, and then on the right an inline streak number with a tiny flame glyph drawn as inline SVG. Tapping or clicking the check button completes the habit for today, fills the button with the habit color, and triggers a small spring animation on the row that scales it to 1.02 for 120ms and back. Tapping a completed check button uncompletes it.

Region 2, the middle, is the year. One block per habit, stacked. Each block has the habit name on top in 15px, then a GitHub style grid below it, 52 columns of weeks across by 7 rows of days down. Square cells are 11px with 2px gap. Cells are colored by completion intensity. For binary habits, a cell is either empty (background dark) or filled with the habit color at full saturation. For habits with a session duration, color the cell at 30%, 60%, or 100% based on whether the session was short, normal, or long compared to that habit's target. Empty cells use the panel background. Hovering a cell shows a tooltip with the absolute date and the completion details for that day. The current day's cell has a thin 1px ring in cream around it.

Region 3, the bottom, is the numbers. One small card per habit, three columns on desktop, single column on mobile. Each card holds five lines stacked vertically, each line is a label in 11px small caps in muted cream and a value in 22px in a tight monospaced figure. The five lines are, in order, current streak in days, longest streak in days, comebacks (the count of times this habit was restarted after a break of 3 or more days), total completions, and total hours (only if the habit has a session duration, otherwise show total days). Below the five lines, a one line note that says something like "you came back 4 times after long breaks" in italic 13px, only shown if comebacks is greater than zero. This line is the whole point of the section. Streak shaming is replaced with this acknowledgement.

## Habits

A floating button in the bottom right of the page, 48px round, plain plus glyph drawn inline. Click it to open a small panel that slides up from the bottom on mobile or appears as a centered sheet on desktop. The panel has five inputs.

Name, a text input. Required.

Identity statement, a text input. Optional. Prefilled with the placeholder "I am someone who..." in italic muted text.

Cadence, a segmented control with two options. Daily, or weekly with a number stepper from 1 to 7. Default daily.

Session duration in minutes, a number input. Optional. If filled, the cell intensity uses session length compared to this target. Default empty.

Color, a row of seven colored circles. Pick one. The colors are sage, ochre, brick, slate, plum, dust, and chestnut. Hex values listed below in the visual section.

Save adds the habit to the bottom of the today list and adds a year block in the middle region. Delete removes the habit from everything. There is no archive flow, deleted habits are gone. A confirm prompt asks "delete this habit and all its history" with a small cancel button.

Editing an existing habit reopens the same panel with values prefilled. The fields are the same. There is no separate edit page.

## Demo data

The app loads with four habits prefilled and six months of plausible history. The data lives in localStorage under the key habit.streaks.kitchen.v1. On first load, seed it with this exact shape.

```json
{
  "habits": [
    {
      "id": "h1",
      "name": "Read 20 pages",
      "identity": "I am someone who reads daily",
      "cadence": "daily",
      "weeklyTarget": null,
      "minutesPerSession": 30,
      "color": "sage",
      "createdAt": "2025-11-14"
    },
    {
      "id": "h2",
      "name": "Move my body",
      "identity": "I am someone who shows up for my body",
      "cadence": "daily",
      "weeklyTarget": null,
      "minutesPerSession": 45,
      "color": "brick",
      "createdAt": "2025-11-14"
    },
    {
      "id": "h3",
      "name": "Long walk with my partner",
      "identity": "I am someone who builds time with the people I love",
      "cadence": "weekly",
      "weeklyTarget": 3,
      "minutesPerSession": 60,
      "color": "ochre",
      "createdAt": "2025-12-01"
    },
    {
      "id": "h4",
      "name": "No phone before 9am",
      "identity": "I am someone who owns my mornings",
      "cadence": "daily",
      "weeklyTarget": null,
      "minutesPerSession": null,
      "color": "slate",
      "createdAt": "2026-01-10"
    }
  ],
  "completions": [
    /* generate six months of plausible entries here at codegen time.
       Each entry: { habitId, date (YYYY MM DD), minutes (number or null) }.
       Patterns to produce:
         h1: roughly 80 percent of days completed, two breaks of 5 days each, then comebacks. Minutes vary between 18 and 42.
         h2: very consistent first three months at near 95 percent, then a long break of 12 days in March, then a comeback of 30 days straight.
         h3: weekly cadence, hit 3 per week most weeks, miss one week entirely once in February.
         h4: started January 10, near perfect since, current streak still alive.
    */
  ]
}
```

If localStorage already has saved data under that key, do not overwrite it.

## Numbers

Computed values, all derived from completions on render.

Current streak. The longest continuous run of consecutive days ending today (or yesterday if today is not yet checked). For weekly habits, count consecutive weeks where the weekly target was met.

Longest streak. The longest such run ever recorded.

Comebacks. The number of distinct restarts after a gap of 3 or more days for daily habits, or 2 or more missed weeks for weekly habits. A restart is the first completion after the gap.

Total completions. Simple count.

Total hours. Sum of all minutes for that habit divided by 60, shown to one decimal place.

## Interactions

Checking off a habit, the check button fills, the row scales briefly, and a soft tick sound plays. The sound is an inline base64 data URL of a very short, very soft wooden tick, around 80ms long. If the browser blocks autoplay, fail silently.

Unchecking removes the completion for today. No confirm.

Pressing space when a habit row is focused checks or unchecks. Arrow up and down move focus between rows. Tab follows the normal order.

The year grid is read only. Hover for the tooltip. On mobile, tap a cell to show the tooltip as a small popover above the cell, dismiss on second tap or by tapping elsewhere.

The numbers cards animate their values from zero to their final value over 400ms on first paint, with an easeOutCubic curve. After first paint, they update instantly.

If a user has completed every habit for today, the page shows a quiet line at the bottom of region 1 in 13px italic muted text saying "done for today, see you tomorrow". This line is the most pleasant moment of the app. It does not move, it does not animate, it just appears.

## Edge cases

A new habit with no completions yet shows an empty year block (all cells the panel background) and a numbers card with zeroes and no italic comeback line.

A habit deleted today still shows in past year blocks until removed. Removal clears everything.

If the device timezone changes, all dates are interpreted in the local timezone at render time. There is no UTC stored anywhere.

Long habit names wrap to two lines on the row and keep the row height growing if needed.

Pasting an identity statement longer than 80 characters truncates at the input level with a visible counter under the field.

Loading state. Since data lives in localStorage, render is synchronous. There should be no spinner, no skeleton, no flash of unstyled content. The whole page paints once.

If localStorage is unavailable (Safari private mode), fall back to in memory state and show a tiny line in the footer that says "history is not being saved on this device". The app still works for the session.

## Visual

Colors.

Background, the page itself, is the warm dark brown #1A1814.
Panels and the year grid base cell color, #241F1A.
Borders, very faint, #2E2823.
Text cream, the primary text color, #F0EBE4.
Text muted cream, #B5AEA4.
Text dim, used for placeholders and small labels, #7A7268.

Habit colors. Each habit picks one. The full saturated cell uses this color. Filled check button uses this color.
sage, #7FB069.
ochre, #D4A24C.
brick, #C26A5A.
slate, #6F8B9F.
plum, #9B6B8E.
dust, #C9B68C.
chestnut, #A1684D.

Type. Use the Lora serif for the date header and the italic comeback line. Use Inter for everything else. Load both from Google Fonts. Numeric values in cards use IBM Plex Mono. Tabular figures everywhere there are numbers.

Spacing. Use a generous vertical rhythm. 32px between regions, 16px between rows inside the today list, 24px between year blocks. No card has padding less than 20px.

Corners. 12px radius on panels. 999px on the round check buttons and the add habit button. 4px on the year grid cells.

Motion. No long animations. The longest is the 400ms count up on first paint. Row scale is 120ms. No looping animations anywhere.

## Stack

React 18 with TypeScript. Tailwind CSS for all styling, with the custom palette extended in tailwind.config.ts. Vite as the build tool. One default export from src/App.tsx. No state libraries, useState and useReducer only. No date library, native Date and Intl.DateTimeFormat only. No icon packs, all glyphs inline SVG. No external UI libraries.

## Files to produce

index.html with Google Fonts links in the head.
src/main.tsx.
src/App.tsx.
src/components/Today.tsx.
src/components/HabitRow.tsx.
src/components/YearGrid.tsx.
src/components/NumbersCard.tsx.
src/components/HabitSheet.tsx (the add and edit panel).
src/lib/storage.ts (localStorage with try catch).
src/lib/dates.ts (week index, day index, format helpers).
src/lib/stats.ts (current streak, longest streak, comebacks, totals).
src/lib/seed.ts (the demo data generator producing the six months of completions described above).
tailwind.config.ts.
package.json with react, react dom, vite, tailwind only.
README.md, two short paragraphs covering what it is and how to run it.

## Done when

A first time visitor opens the app and within ten seconds sees four habits, a year of squares filled with real looking history, today's checkboxes ready to tap, and a comeback line on at least one card. They tap one habit, the row springs, the year grid gets a new cell, the numbers update. They reload the tab and everything persists. They close the tab and reopen tomorrow and the day rolls over correctly with no broken streaks unless they earned the break.

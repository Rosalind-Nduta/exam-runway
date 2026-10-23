# Exam Runway

**Your exams. Your hours. One plan that keeps up with you.**

Exam Runway is a free, general-purpose study planner. Tell it what you are sitting, when, which topics worry you and how many hours you really have. It builds a day-by-day revision timetable, gives weak topics more time, counts down to every exam, and reshuffles itself when your situation changes.

It works for school, university, professional certifications and language tests. There is no account, no server and no install. Everything runs in the browser and stays on the user's device.

---

## Why it exists

Most revision timetables fail for the same reasons: they are static, they treat every topic the same, and they fall apart the first time a session is missed. Exam Runway is built around three ideas:

1. **Time follows weakness.** Weak and flagged topics get more revision passes than strong ones, but strong topics are never dropped.
2. **The plan adapts.** Missed sessions roll forward. A session marked "Shaky" adds extra time for that topic. Changing an exam date or your hours rebuilds the plan around the work you have already done.
3. **Progress stays visible.** Countdowns, checkpoints and a weekly check-in keep the finish line in view and feed what you learn about yourself back into the plan.

---

## Features

| Area | What you get |
|---|---|
| **Setup** | Four-step form: you, exams and topics, time and style, review. Or load a sample student in one click. |
| **Inputs** | Any number of subjects with exam dates, topics rated Weak / Okay / Strong, "flagged by lecturer" markers, confidence (1 to 5), latest grade, weekday and weekend hours, preferred time of day, days off, session length, break pattern, solo or group, preferred revision methods. |
| **Timetable** | Dated sessions with clock times, a subject, a topic, a pass type (Learn, Practise, Revise, Recall, Final recap, Mock exam) and a specific how-to for the chosen method. |
| **Countdown** | A card per exam with days left, a phase label (for example "Mock papers and weak spots") and sessions completed. |
| **Runway** | A scrollable strip of days from today to the last exam showing planned and finished sessions and exam days. Tap a day to open it. |
| **Focus timer** | A built-in focus and break timer that follows your own break settings and ends with a prompt to mark the session done. |
| **Adaptive re-plan** | Missed sessions roll forward on their own. "Shaky" feedback, weekly flags, edited setup or a changed exam date all trigger a reshuffle that keeps finished sessions. |
| **Tight-schedule warning** | If the work does not fit before the exams, the app says how many hours are short and offers to add 30 minutes a day. |
| **Progress** | Completion percentage, hours studied, day streak, checkpoints per subject, a 14-day load chart and per-topic mastery dots. |
| **Weekly check-in** | Reflection prompts at the end of each week: what felt easier, what still needs work, one win, energy level. Topics flagged as needing work get extra sessions. |
| **Motivation** | Context-aware notes that change with exam proximity, weak topics, rest days and exam day. |
| **Export and backup** | Copy the plan as plain text. Copy and restore a full backup as text. |
| **Design** | Responsive from phone to desktop, with light and dark themes. |

---

## Quick start

The whole app is one file: `exam-runway.html`.

1. Open `exam-runway.html` in any modern browser (Chrome, Edge, Firefox, Safari).
2. Click **Try a sample student** to see a full plan, or **Build my plan** to enter your own.
3. Work through the four steps and press **Build my plan**.

There is nothing to install or build. To host it, upload the file to any static host (GitHub Pages, Netlify, an S3 bucket) or drop it into an existing site.

**Network note:** the only external request is the Google Fonts stylesheet. If it is blocked or offline, the app falls back to system fonts and works the same.

---

## How to use it

1. **You.** Enter your name (optional) and course.
2. **Exams.** Add each subject with its exam date.
   - Add topics by typing and pressing Enter, or paste a comma-separated list.
   - Click a topic's level to cycle **Okay → Weak → Strong**.
   - Use **Flag** on topics your lecturer or syllabus stressed.
   - Set your confidence and, if you have it, your latest grade.
   - Leave topics empty and the subject is planned as a single "Whole syllabus" block.
3. **Time and style.** Set weekday and weekend hours, preferred time, days off, session length, break pattern, solo or group, and the revision methods you like.
4. **Build.** Review the summary and generate the plan.

Day to day:

- Open **Today**, read the note, do the sessions, press **Mark done**.
- Rate how it felt. **Shaky** adds revision time for that topic and reshuffles immediately.
- Use **Start focus** to run the timer.
- At the end of each week, open **Reflect**, answer the prompts and tap any topics that still need work.
- If something changes (exam moved, a bad week, new weak area), open **Edit setup** or press **Reshuffle plan**.

---

## How the planner works

The scheduler is plain JavaScript in the `replan()` function. It is deterministic for a given state and date.

### 1. Work per topic

Each topic gets a target number of revision passes:

| Factor | Passes |
|---|---|
| Base: Weak / Okay / Strong | 3 / 2 / 1 |
| Flagged by lecturer or syllabus | +1 |
| Latest grade below 55% (not for Strong topics) | +1 |
| Subject confidence 1 or 2 (not for Strong topics) | +1 |
| Extra passes earned from "Shaky" feedback or weekly flags | +1 each |

Passes are capped at 6 per topic (8 for a "Whole syllabus" block). Passes already completed are subtracted, so a re-plan only schedules what is left.

Each subject also gets mock exams: two if the exam is 21 or more days away, one if it is 6 or more days away.

If the total work is well under the time available, bonus passes are added to the weakest topics until the plan uses about 72% of capacity. This keeps a light workload from collapsing into a few early days.

### 2. Capacity

Hours per day come from the weekday and weekend values, with days off set to zero. On an exam day capacity is halved, and a subject is never scheduled on or after its own exam date. For today, the schedule only uses the time that is left from the current moment, so an evening plan does not place sessions in the past.

Each day is split into blocks of the chosen session length, with a 10-minute gap and a shorter last block if the remainder allows.

### 3. Choosing what goes where

For every block the planner picks a subject by **pressure**: remaining work divided by the capacity left before that subject's exam. It avoids the same subject twice in a row when another subject is nearly as urgent. Within a subject it prefers:

1. Topics not yet studied at all, so everything is learned once early.
2. Topics with more passes remaining.
3. Weak and flagged topics.
4. Topics not seen for the longest time, for spacing.

Daily load is paced so work spreads evenly, and rises to full capacity when any subject is under heavy time pressure.

### 4. Pass types and the exam run-in

Passes cycle through Learn, Practise, Revise, Recall, Practise, Revise. Mock papers are placed 2 to 4 days before an exam. The day before an exam every session becomes a light **Final recap**.

### 5. Methods

Each pass type maps to your preferred methods:

- Learn: summaries, mind maps, flashcards
- Practise: practice questions, flashcards
- Revise: flashcards, mind maps, summaries
- Recall: flashcards, practice questions
- Final recap: flashcards, summaries
- Mock exam: practice questions

Group learners get a prompt to swap questions and explain answers aloud.

### 6. Re-planning

On every re-plan, finished sessions are kept and everything else is rebuilt from today. Missed sessions are therefore re-queued automatically. The app also re-plans on load if the day has changed or sessions were missed.

---

## Data and privacy

- All data is stored in the browser under `localStorage` key `exam-runway-v1`.
- Nothing is sent to any server. There are no accounts, cookies or analytics.
- Clearing site data deletes the plan. Use **Settings → Copy backup** first if you want to keep it, and **Restore** to load it again.
- If browser storage is unavailable (for example in a private window), the app still works for the current session.

---

## Tech

- **HTML5, CSS3 and vanilla JavaScript (ES2015+).** No framework, no build step, no dependencies.
- **Fonts:** Bricolage Grotesque, Instrument Sans and JetBrains Mono through Google Fonts, with system fallbacks.
- **Theming:** CSS custom properties with automatic light and dark themes and a manual toggle.
- **State:** a single object persisted to `localStorage`, rendered by string templates with event delegation.

### Code map (`exam-runway.html`)

| Section | Purpose |
|---|---|
| `<style>` | Design tokens, layout and components |
| `helpers` | Date arithmetic, escaping, formatting |
| `state` | Defaults, load and save |
| `planner` | `replan()` scheduling engine, pass targets, method choice |
| `motivation` | Phase labels and daily notes |
| `views` | Wizard, Today, Plan, Progress, Reflect, Settings |
| `mutations` and `events` | Sample data, validation, click and input handlers |
| `focus timer dock` | Pomodoro-style timer |
| `boot` | Load state, auto roll-over of missed sessions, first render |

---

## Known limitations

- English only.
- Dates and times use the device's local time zone.
- The plan is per browser. Moving between devices needs the copy and paste backup.
- Calendar export (`.ics`) is not included.
- The scheduler is a heuristic, not an optimiser. It aims for a sensible, explainable plan rather than a mathematically optimal one.

## Roadmap ideas

- Calendar export and reminders
- Multi-language interface
- Optional account sync between devices
- Import of syllabus or past-paper lists
- Group plans that share a timetable with a study partner
- Smarter spacing based on self-rated recall

---

## Hackathon pitch in one paragraph

Exam Runway removes the hardest part of revision, deciding what to study today. A student enters exams, weak spots and real available hours, and gets a timetable that favours weak topics, counts down to each paper, and rebuilds itself when a session is missed or a topic proves harder than expected. It needs no sign-up, keeps data private on the device, and runs from a single file on any phone or laptop.

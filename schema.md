# Tutorial Data Schema (default.json)

This document defines the notation and schema for `default.json` (and any tutorial data file in the same format).

---

## 1. Overall structure

```json
{
  "format": "cubew-tutorial-v1",
  "version": 5,
  "levels": [ /* array of levels */ ]
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `format` | string | required | Fixed value `"cubew-tutorial-v1"` |
| `version` | number | required | Data version number. **Must be incremented every time the content is updated** (used for image cache-busting and the app's progress tracking) |
| `levels` | array | required | Array of levels (see below) |

---

## 2. Level

A top-level grouping for the whole tutorial (e.g. "Beginner (LBL)").

```json
{
  "id": "beginner",
  "title": { "ja": "初心者（LBL法）", "en": "Beginner (LBL)" },
  "steps": [ /* array of steps */ ]
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | required | Level identifier. **Must be unique within the `levels` array** |
| `title` | string or `{lang: text}` | required | Level title. A multi-language object is recommended (see section 3) |
| `steps` | array | required | Array of steps (see below) |

---

## 3. Step

A single unit of learning within a level (e.g. "Bottom (Yellow) Cross").

```json
{
  "id": "step1-yellow-cross",
  "title": { "ja": "下(黄色)クロス", "en": "Bottom Cross" },
  "description": { "ja": "説明文", "en": "Description" },
  "image": "LBL/goals/step1-goal.png",
  "items": [ /* array of items */ ]
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | required | Step identifier. **Must be unique within the same level** (it's fine for a step in a different level to reuse the same id) |
| `title` | string or `{lang: text}` | required | Step title |
| `description` | string or `{lang: text}` | optional | Step description. Use `<br>` in the string for line breaks (any other HTML tags are not interpreted and are shown as plain text) |
| `image` | string | optional | Path to an image showing the completed/goal state for this step |
| `items` | array | required | Array of items (see below). **An empty array is also valid** — see "Glossary steps" below |

### Glossary steps

Setting `items` to an empty array (`[]`) turns a step into a glossary-only step, with no lesson or exam content. It uses only `title` (the term), `description` (the explanation), and `image` (an illustration), and is displayed as a read-only entry with no cube interaction.

```json
{
  "id": "term-piece",
  "title": { "en": "Piece", "ja": "ピース" },
  "description": { "en": "The 26 cuboids that make up a 3x3 cube puzzle are called pieces.", "ja": "3x3キューブパズルを構成する全26個の直方体をピースと呼びます。" },
  "image": "terms/term1-piece.png",
  "items": []
}
```

### Multi-language support (applies to both `title` and `description`)

```json
{ "ja": "日本語", "en": "English", "zh": "中文", "fr": "Français", "ko": "한국어", "es": "Español", "pt": "Português", "de": "Deutsch", "ru": "Русский" }
```

The 9 supported languages are listed above. If you can't provide all languages, a plain string (e.g. `"title": "Test"`) is also accepted (in that case, the same text is shown for every language). If only some languages are missing, the app falls back to English, then Japanese.

---

## 4. Item

An individual piece of learning content within a step. There are two `type`s: `"lesson"` and `"exam"`.

### 4.1 Lesson (`type: "lesson"`)

Content that walks the user through a fixed sequence of moves, with guidance, starting from a fixed initial state.

```json
{
  "id": "step1-lesson-1a",
  "type": "lesson",
  "title": { "ja": "基本手順（コマンドなし）", "en": "Basic (no command)" },
  "initialState": "(a 54-character state string)",
  "moves": "R U R' U'",
  "hint1": "LBL/step1-1b-hint.png",
  "hint2": "LBL/step1-1b-hint2.png"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | required | Item identifier. **Must be unique within the same step** |
| `type` | string | required | `"lesson"` |
| `title` | string or `{lang: text}` | required | Item title |
| `initialState` | string | required | The cube's state at the start (54 characters, kociemba-style. See section 5) |
| `moves` | string | required | The sequence of moves to be performed (see section 6, "`moves` notation") |
| `hint1` | string | optional | Path to the primary hint image, shown at the bottom of the screen |
| `hint2` | string | optional | Path to an additional hint image, opened via the "+" button |

### 4.2 Exam (`type: "exam"`)

Content that only provides an initial state and a goal state, and lets the user operate the cube freely until they reach the goal.

```json
{
  "id": "step1-exam-1",
  "type": "exam",
  "title": { "ja": "試験1", "en": "Exam 1" },
  "initialState": "(a 54-character state string; wildcards allowed)",
  "goalState": "(a 54-character state string)",
  "hint1": "LBL/step1-exam1-hint.png"
}
```

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | required | Item identifier. Unique within the same step |
| `type` | string | required | `"exam"` |
| `title` | string or `{lang: text}` | required | Item title |
| `initialState` | string | required | The cube's state at the start (wildcards supported; see section 5) |
| `goalState` | string | required | The state considered "cleared" (54 characters; see section 5 for how wildcards behave here) |
| `hint1` / `hint2` | string | optional | Hint images (same as for lessons) |

---

## 5. State strings (`initialState` / `goalState`)

A 54-character string representing the cube's state. The faces are ordered `U R F D L B` (9 characters per face, 54 total).

The 9 characters of each face are read left-to-right, top-to-bottom, following this 3×3 grid:

```
1 2 3   ← top row (left → right)
4 5 6   ← middle row (left → right)
7 8 9   ← bottom row (left → right)
```

In `initialState`, in addition to the ordinary face letters, the following special characters can be used:

| Symbol | Meaning | How it's displayed |
|---|---|---|
| `U`/`R`/`F`/`D`/`L`/`B` (uppercase) | A normal face color. Used for goal matching. | Shown normally (no border) |
| `u`/`r`/`f`/`d`/`l`/`b` (lowercase) | Same color as the uppercase version, but shown with a yellow highlight border as a hint. The border disappears (and it looks identical to the uppercase version) under these conditions:<br>・Normal mode: the border never disappears<br>・During a lesson: disappears once you reach the end of the move sequence<br>・During an exam: disappears once the cube matches `goalState` (i.e. is cleared) | Shown with a border, until the condition above is met |
| `W` (uppercase) | Any color is fine here (excluded from matching). Always shown in gray. | Gray, no border |
| `w` (lowercase) | Same gray color as `W`, but shows a border only while no move has been made yet. As soon as a single move is made, it looks identical to `W` (gray, no border) | Bordered only in the initial state |

Matching against `goalState` is done by uppercasing both `cubeState` (the current state, evolved from `initialState`) and `goalState` at the moment of comparison, then comparing them as strings. Because of this, case is not distinguished, and matching works as follows:

- `U`/`R`/`F`/`D`/`L`/`B` (or lowercase `u`/`r`/`f`/`d`/`l`/`b`): passes only if the corresponding position in `cubeState` has the matching face color (checked in uppercase)
- `W` (or lowercase `w`): passes only if the corresponding position in `cubeState` is `W` or `w` (i.e. still holds the wildcard piece from `initialState`). In other words, `W` does **not** mean "any color is acceptable" here — it acts as a literal match condition meaning "the wildcard piece that originally started here has ended up in this position"

There's no functional difference between writing upper- or lowercase in `goalState` (the match result is the same either way). We recommend using uppercase throughout for readability.

---

## 6. `moves` notation

The notation you can use in the `moves` field.

| Notation | Meaning | Example |
|---|---|---|
| `R` `R'` `R2` | Standard WCA notation (clockwise / counter-clockwise / 180°) | `R U R' U'` |
| `R2'` | Directional 180° (guide arrow shown counter-clockwise) | `U2'` |
| `Rw` `x` `y` `z` | Wide moves / whole-cube rotation | `Rw U x` |
| `C_F3(seq)` | Places a "command": shows an aiming reticle at cell 3 of the F face, and executes `seq` on double-tap (or via the guide's ⏩ control) | `C_F3(U R U' R')` |
| `R(F)` | Shows the guide arrow only on face F (multiple faces can be listed: `R(F,U)`. If omitted, it's shown on all relevant faces) | `U(F) R(F)` |
| `CAM(θ,φ)` | Camera (viewpoint) movement. θ = vertical angle, φ = horizontal angle (in degrees) | `CAM(90,0)` |

**Unsupported notation**: `U'2`, `Rw'2` (`U2'` and `Rw2'` are supported)

### `CAM(θ,φ)` details

- Each `CAM(θ,φ)` sets the viewpoint for the section that follows it, up until the next `CAM` appears (one "CAM group")
- A section with no `CAM` at its start uses the default viewpoint (θ=65°, φ=36°)
- The same viewpoint is used whether moves are being played forward or undone
- The viewpoint does not move automatically while stepping forward/backward within the same CAM group (if the user has manually adjusted the view, that adjustment is preserved). It only moves automatically to the specified viewpoint at the moment you cross into the next group

Example:
```
CAM(65,36) U(F) R(F) U'(F) R'(F) CAM(65,54) U'(R) F'(R) U(R) F(R) CAM(115,36)
```

### `C_XX(seq)` (commands) details

Writing something like `C_F3(seq)` automatically registers the same command on several other cells internally, as follows (for the F/R/B/L faces):

- **Cross-face rotational copy**: registering on F3 also automatically registers on R3, B3, and L3 (each is automatically converted to account for that face's own orientation)
- **Left-right mirror copy**: registering on F3 also registers on F1 (its left-right mirror position). The same applies to R1, B1, L1
- In total, up to 8 cells receive a correctly-converted copy of the command, one per face

For the U face, the corresponding rotational-symmetry copies are made among the corners (1, 3, 7, 9) or among the edges (2, 4, 6, 8), whichever group the original cell belongs to.

---

## 7. Minimal example

```json
{
  "format": "cubew-tutorial-v1",
  "version": 1,
  "levels": [
    {
      "id": "sample",
      "title": { "ja": "サンプル", "en": "Sample" },
      "steps": [
        {
          "id": "sample-step1",
          "title": { "ja": "最初のステップ", "en": "First Step" },
          "items": [
            {
              "id": "sample-lesson-1",
              "type": "lesson",
              "title": { "ja": "サンプルレッスン", "en": "Sample Lesson" },
              "initialState": "UUUUUUUUURRRRRRRRRFFFFFFFFFDDDDDDDDDLLLLLLLLLBBBBBBBBB",
              "moves": "R U R' U'"
            }
          ]
        }
      ]
    }
  ]
}
```

---

## 8. A note on identifiers (`id`)

`level.id` / `step.id` / `item.id` only need to be **unique within their own immediate parent** (for example, it's fine for two steps in different levels to share the same `id`).

That said, the app's progress tracking (which lessons have been completed, etc.) is not based on these `id` strings, but on a separate identifier generated internally. Changing the structure of the data (adding, removing, or reordering levels/steps) may reset progress for whatever is affected by that change.

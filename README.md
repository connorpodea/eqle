# EQLE

A Wordle-style daily puzzle game built around math instead of words, built in SwiftUI.

Each day EQLE generates one valid 8-character equation (e.g. `12+57=69`). Players get six tries to guess it, with tile colors revealing how close each guess is — just like Wordle, but for arithmetic.

## Features

- **Daily Puzzle** — one procedurally generated equation per day, shared by all players until midnight
- **Wordle-Style Feedback** — green (correct position), yellow (correct digit/operator, wrong position), red (not in the solution), with a 3D flip-reveal animation per tile
- **Smart Equation Generator** — produces single- or double-operator equations (`+`, `-`, `*`, `/`) that are always exactly 8 characters and mathematically valid, evaluated left-to-right
- **On-Screen Keyboard** — numeric and operator keys recolor based on prior guesses (green/yellow/red priority logic), plus DELETE and SUBMIT
- **Streaks & Stats** — current streak, best streak, fewest tries, total played/won, and a win-distribution bar chart, all persisted across sessions
- **Guess Validation** — rejects incomplete, malformed, or mathematically incorrect equations before they're submitted, with toast notifications explaining why
- **Rules Modal** — in-app instructions and a worked example
- **End-of-Game Screen** — reveals the day's equation and counts down to the next puzzle

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Swift 5 |
| UI Framework | SwiftUI |
| State Management | `ObservableObject` / `@Published` (single `GameState` class) |
| Persistence | `UserDefaults` (daily equation, guesses, stats, streaks, key colors) |
| Platform | iOS 18.5+ |
| Testing | XCTest (unit) + XCUITest (UI) |

## Project Structure

```
EQLE/
├── EQLE.xcodeproj/          # Xcode project file
├── EQLE/
│   ├── EQLEApp.swift        # @main entry point (embedded in ContentView.swift)
│   ├── ContentView.swift    # Entire app: views, game logic, and state
│   └── Assets.xcassets/     # App icon, accent color
├── EQLETests/
│   └── EQLETests.swift      # Unit tests
├── EQLEUITests/
│   ├── EQLEUITests.swift            # UI tests
│   └── EQLEUITestsLaunchTests.swift # Launch performance tests
└── images/                  # Screenshots / marketing assets
```

`ContentView.swift` currently holds the full app — data models, game logic, and every view (`StartView`, `ContentView`, `EndGameView`, `RulesModalView`, `StatsDetailView`, keyboard, grid, and shared subviews).

## Getting Started

**Requirements:** Xcode 16+, iOS 18.5+ simulator or device

```bash
# Clone the repo
git clone <repo-url>
cd EQLE

# Open in Xcode
open EQLE.xcodeproj
```

Build and run (`Cmd+R`) on a simulator or device. No external dependencies, package manager, or backend — everything runs locally.

## Game Logic

- **Equation generation** — randomly builds either a single-operator (`A+B=C`) or double-operator (`A op1 B op2 C = D`) equation, retrying until the result is non-negative, single-digit (for the final result), and exactly 8 characters; falls back to a small hardcoded set if generation fails after 100 attempts
- **Guess evaluation** — a two-pass algorithm: first marks exact character matches (green), then uses a frequency map of remaining solution characters to mark misplaced-but-present characters (yellow) versus absent ones (red)
- **Daily reset** — a `LastEquationDate` check in `UserDefaults` regenerates the puzzle and clears progress once per calendar day; `canPlayToday` blocks a second attempt on the same day

## Persistence

All game state is stored in `UserDefaults` — no database or network layer:

| Key(s) | Purpose |
|---|---|
| `DailyEquation`, `LastEquationDate` | Today's puzzle and when it was generated |
| `SavedGuesses`, `CurrentGuessIndex`, `CurrentCharIndex`, `KeyColors` | In-progress game state (JSON-encoded where needed) |
| `TotalGamesPlayed`, `TotalGamesWon`, `WinDistribution`, `FewestTries` | Lifetime statistics |
| `CurrentStreak`, `BestStreak`, `LastWinDate` | Streak tracking |
| `LastGameCompletedDate`, `LastStatsUpdate` | Guards against replaying or double-counting stats on the same day |

## Author

Connor Podea — iOS project built to explore SwiftUI state management, custom animations, and daily-puzzle game design.

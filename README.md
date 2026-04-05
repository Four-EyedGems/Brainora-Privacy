# Brainora

Offline brain & puzzle games. Five fully playable games — puzzle, word, logic, arcade, and brain — built with React Native and Expo. 100% offline, no ads, no accounts.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | Expo SDK 54 (managed workflow) |
| UI | React Native 0.81+ |
| Language | TypeScript (strict mode) — 150 source files, zero errors |
| Navigation | React Navigation 7 (native stack + bottom tabs) |
| State | Zustand 4 |
| Persistence | react-native-mmkv 4 |
| Animations | react-native-reanimated 4 |
| Graphics | @shopify/react-native-skia 2.2 |
| Haptics | expo-haptics |
| Celebrations | lottie-react-native |

---

## Getting Started

### Prerequisites

- Node.js 18+
- npm or yarn
- Android Studio (for Android) or Xcode (for iOS)
- This app uses native modules (MMKV, Skia) and **cannot run in Expo Go**

### Install & Run

```bash
cd app
npm install
npx expo prebuild
npx expo run:android   # or run:ios
```

Or use EAS Build:

```bash
npx eas build --profile development --platform android
```

---

## The 5 Games

| Game | Category | Files | Key Feature |
|------|----------|-------|-------------|
| **Hex Flow** | Puzzle | 21 | Connect nodes on hex grid, fill every cell |
| **Word Forge** | Word | 27 | Swipe letters to spell words, 5 game modes |
| **Sudoku Neo** | Logic | 22 | Classic Sudoku with smart hints and pencil marks |
| **Gravity Drop** | Arcade | 21 | Tetris with rotating gravity |
| **Memory Matrix** | Brain | 21 | Pattern recall with Brain Score system |

Every game is a self-contained module in `src/games/{GameName}/` with its own engine, store, screens, and components. The shell handles navigation, theming, stats, and daily challenges automatically.

---

## Project Structure

```
app/
├── App.tsx                          # Root: providers + navigation
├── src/
│   ├── navigation/                  # Tab + stack navigators
│   ├── screens/                     # Hub, Daily, Stats, Settings
│   ├── registry/                    # Game registry (single source of truth)
│   ├── store/                       # Theme, stats, app stores
│   ├── theme/                       # Colors, typography, spacing
│   ├── hooks/                       # useTheme, useHaptics, useGameStats
│   ├── components/                  # Shared UI (PressableScale, Modal, Toast...)
│   ├── utils/                       # MMKV storage, date utils, formatters
│   ├── constants/                   # App-wide constants
│   │
│   └── games/
│       ├── HexFlow/                 # 21 files — hex puzzle
│       ├── WordForge/               # 27 files — word game
│       ├── SudokuNeo/               # 22 files — sudoku
│       ├── GravityDrop/             # 21 files — block stacking
│       └── MemoryMatrix/            # 21 files — pattern memory
```

---

## Hex Flow

Connect matching-colored nodes on a hexagonal grid by drawing paths. Fill every cell.

- **Engine**: Puzzle generation via Hamiltonian path (Warnsdorff's heuristic), guarantees solvability
- **Rendering**: Skia Canvas — hex grid, colored paths, node circles, hint dashes
- **Controls**: Pan to draw paths (snap to hex centers), tap to remove
- **Content**: 100 levels (Easy→Expert), daily puzzles seeded by date
- **Scoring**: 3-star system (par moves, no hints), streak tracking
- 8 path colors: Crimson, Sky, Lime, Amber, Violet, Coral, Teal, Rose

```
src/games/HexFlow/
├── engine/ (hexMath, gameLogic, puzzleGenerator)
├── data/ (100 levels, daily puzzles)
├── store/ (useHexFlowStore — MMKV persistence)
├── hooks/ (timer, path drawing gesture)
├── components/ (HexCanvas, GameTopBar, VictoryModal, ConfettiEffect, LevelCard)
└── screens/ (Home, LevelSelect, Game, Daily)
```

---

## Word Forge

Swipe across adjacent letters (including diagonals) on a grid to spell words.

- **Dictionary**: 8,679 curated English words with binary search O(log n) validation
- **Grid**: Frequency-weighted letter generation with vowel guarantees (4x4 to 7x7)
- **Word Finding**: DFS with prefix pruning finds all valid words in any grid
- **5 Modes**: Classic (2 min), Zen (no timer), Blitz (60s, 2x points), Daily, Word Hunt (10 targets, 10 lives)
- **Scoring**: Length-based (100-1500+ pts), streak multiplier (up to 2x), golden word bonus (3x)
- **Features**: Swipe line rendered via Skia, golden mystery words, grade system (S/A/B/C/D)

```
src/games/WordForge/
├── engine/ (dictionary, gridGenerator, wordFinder, scoring)
├── data/ (8,679-word sorted list)
├── store/ (useWordForgeStore)
├── hooks/ (timer, cell layouts, swipe detection)
├── components/ (LetterGrid, LetterCell, SwipeLine, CurrentWord, FoundWordsList,
│                TimerBar, ScoreDisplay, GoldenWordHints, WordHuntTargets, GameTopBar)
└── screens/ (Home, Game, Daily, Results)
```

---

## Sudoku Neo

Classic 9x9 Sudoku with premium UX — on-device puzzle generation, pencil marks, smart hints.

- **Engine**: Backtracking generator + solver, unique-solution validation, technique detection (naked/hidden singles)
- **Difficulties**: Easy (36-45 clues) → Expert (17-21 clues)
- **Smart Features**: Auto-check conflicts, auto-clear notes, fill-all-notes, hint with technique name
- **Controls**: Tap cell → number pad (1-9), pencil toggle for notes, undo (50 moves)
- **Mistake System**: Challenge mode (3 lives) or casual (unlimited)
- **Visual**: Cell highlighting (selected, same-digit, row/col/box), conflict shake animation
- **Settings**: Auto-check, auto-clear notes, digit font size (S/M/L), mistake limit

```
src/games/SudokuNeo/
├── engine/ (generator, solver with SRS, validator)
├── store/ (useSudokuStore — full game state + stats per difficulty)
├── hooks/ (timer, puzzle cache pre-generation)
├── components/ (SudokuGrid, SudokuCell, NumberPad, GameToolbar,
│                MistakeCounter, DifficultySelector, VictorySheet, StatsCharts)
└── screens/ (Home, Game, Daily, Stats, Settings)
```

---

## Gravity Drop

Tetris-inspired block stacking with a twist: gravity rotates 90 degrees at score milestones.

- **Gravity System**: Rotates CW every 1,000 points (down→left→up→right). Chaos mode: random every 30s
- **Pieces**: Standard 7 tetrominoes with SRS wall kicks, bag randomizer
- **Features**: Hold piece, next-3 queue, ghost projection, T-spin detection, back-to-back bonus
- **Scoring**: Line clears (100-800 x level), combos (+50 per consecutive), gravity flip bonus (+500)
- **4 Modes**: Classic (survive), Sprint (40 lines), Ultra (2 min), Zen (infinite)
- **Rendering**: Skia Canvas — 12x24 board with 3D bevel blocks, ghost piece outline
- **Controls**: Swipe L/R to move, swipe down for soft drop, swipe up for hard drop, tap halves to rotate

```
src/games/GravityDrop/
├── engine/ (pieces with SRS, board with gravity-relative line clears,
│            gravity direction, scoring with T-spins, game loop)
├── store/ (useGravityStore — leaderboard, settings)
├── hooks/ (game loop interval, gesture controls)
├── components/ (GameCanvas, NextQueue, HoldPiece, ScorePanel,
│                GameOverModal, PauseOverlay, FallingBlocksBg)
└── screens/ (Home, Game, Settings)
```

---

## Memory Matrix

Pattern memory brain-training game with Brain Score system.

- **Gameplay**: Grid flashes a pattern → remember it → tap the correct cells
- **Grid Progression**: 3x3 (level 1-3) → 4x4 (4-6) → 5x5 (7-10) → 6x6 (11+)
- **6 Modes**: Classic, Sequence (tap in order), Mirror, Reverse (tap unlit cells), Speed Run, Daily Brain Test
- **Brain Score**: 0-100 rating (Novice/Trained/Sharp/Elite/Master) based on accuracy + rounds + speed
- **Daily Brain Test**: Fixed 10 rounds, same puzzle for everyone, tracks score history
- **Accessibility**: Color blind mode (orange/blue instead of green/red), min 44dp cells
- **Display Time**: Easy (2.5s) → Expert (1s), countdown bar animates during display

```
src/games/MemoryMatrix/
├── engine/ (patternGenerator with seeded RNG, scoring, brainScore calculator)
├── store/ (useMemoryStore — game state, daily history, lifetime stats)
├── hooks/ (round timer, pattern display sequencer)
├── components/ (MemoryGrid, MemoryCell, LivesDisplay, CountdownBar,
│                BrainScoreGauge, ModeCard, RoundResult)
└── screens/ (Home, Game, Daily, Results, Settings)
```

---

## Adding a New Game

```typescript
// 1. Create src/games/YourGame/index.tsx
export function YourGameScreen() { ... }

// 2. Add to src/registry/gameRegistry.ts
{
  id: 'your-game',
  name: 'Your Game',
  component: YourGameScreen,
  category: 'puzzle',
  ...
}

// 3. Use shell utilities inside your game
import { gameStorage } from '../../utils/storage';
import { useGameStats } from '../../hooks/useGameStats';
import { useTheme } from '../../hooks/useTheme';
import { useHaptics } from '../../hooks/useHaptics';
```

Hub card, navigation, stats tracking, and daily integration are all automatic.

---

## App Architecture

### Navigation

```
TabNavigator (bottom tabs)
├── HubTab → games grid → game by ID
├── DailyTab → daily challenges → daily game by ID
├── StatsTab → global + per-game statistics
└── SettingsTab → theme, haptics, sound, reset
```

### State Management

| Store | Persisted | Purpose |
|-------|-----------|---------|
| Shell `useThemeStore` | Yes | Theme mode (system/light/dark) |
| Shell `useStatsStore` | Yes | Per-game stats, streaks, daily log |
| Shell `useAppStore` | Partial | Haptics & sound persisted |
| Each game's Zustand store | Yes | Game-specific state via `gameStorage()` |

### Storage Pattern

```typescript
const storage = gameStorage('hex-flow');
storage.set('level', 5);         // → MMKV key: game.hex-flow.level
storage.get('level', 1);
```

---

## Scripts

```bash
npm start              # Start Expo dev server
npm run android        # Run on Android
npm run ios            # Run on iOS
```

---

## Requirements

- **No internet required** — fully offline
- **No backend** — all data stored locally via MMKV
- **No auth** — no accounts or login
- **No ads** — clean experience

---

## License

Private project.

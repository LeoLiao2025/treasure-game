# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an **Interactive Treasure Box Game** – a React-based web game where players click on treasure chests to discover their contents. The game features:
- Three treasure chests (one contains treasure, two contain skeletons)
- Scoring system: +$100 for treasure, -$50 for skeleton
- Sound effects for opening chests
- Animations using Framer Motion (flip, hover, scale effects)
- Custom cursor (key icon) when hovering over closed chests
- Responsive design with Tailwind CSS

## Development Commands

```bash
# Install dependencies
npm install

# Run development server (opens at http://localhost:3000)
npm run dev

# Build for production
npm run build
```

The project uses **Vite 6.3.5** as the build tool with React 18 + TypeScript. Build output goes to the `build/` directory.

## Architecture

### Tech Stack
- **React 18** with TypeScript
- **Vite 6.3.5** for bundling and development
- **Tailwind CSS** for styling
- **Radix UI** components for accessible UI elements
- **Motion** (Framer Motion) for animations
- **Sonner** for toast notifications
- **Recharts** for charts (included but not currently used in core game)

### Vite Configuration (`vite.config.ts`)
- Path alias: `@` resolves to `./src/`
- Extensive Radix UI library version aliases to prevent resolution conflicts
- SWC compiler plugin (`@vitejs/plugin-react-swc`) for fast JSX transformation
- Dev server on port 3000 with auto-open in browser
- Build target: `esnext`, output directory: `build/`

### Core Game Logic (`src/App.tsx`)

**Game Flow:**
1. Game initializes automatically on mount with `useEffect`
2. Three treasure chests displayed in responsive grid (1 column mobile, 3 columns desktop)
3. One random chest contains treasure (hasTreasure flag), two contain skeletons
4. Players click chests to open them:
   - Treasure: +$100 score, plays `chest_open.mp3`
   - Skeleton: -$50 score, plays `chest_open_with_evil_laugh.mp3`
5. Game ends when treasure is found OR all boxes are opened
6. "Play Again" button resets the game

**Key State (React hooks):**
- `boxes`: Array of Box objects `{ id: number, isOpen: boolean, hasTreasure: boolean }`
- `score`: Current player score (number)
- `gameEnded`: Boolean flag to prevent clicks after game ends

**Key Functions:**
- `initializeGame()`: Creates 3 boxes with randomized treasure placement, resets score/gameEnded
- `openBox(boxId)`: Updates box state, plays sound, updates score, checks end conditions
- `resetGame()`: Calls `initializeGame()` to start new game

**Audio Integration:**
- Sound files imported at top: `chestOpenSound`, `evilLaughSound`
- Played in `openBox()` using `new Audio(...)` and `.play()`

**UI/Animation (Framer Motion):**
- `whileHover` and `whileTap`: Scale effects on closed chests (1.05 scale on hover, 0.95 on tap)
- `animate`: Flip animation when opening (rotateY 0° → 180°, 0.6s ease-in-out)
- Emoji indicators (✨💰✨ or 💀👻💀) fade in above opened chests with delay
- Custom cursor: Key icon (`key.png`) shown when hovering closed chests via inline style
- Game-over modal fades in from below (initial y: 20, animate to y: 0)

### Asset Structure

**Images** (`src/assets/`):
- `treasure_closed.png` – Closed chest (default state)
- `treasure_opened.png` – Opened chest with treasure
- `treasure_opened_skeleton.png` – Opened chest with skeleton
- `key.png` – Key icon (used for custom cursor via inline style)

**Audio** (`src/audios/`):
- `chest_open.mp3` – Plays when opening treasure chest
- `chest_open_with_evil_laugh.mp3` – Plays when opening skeleton chest

Both audio files are integrated in `openBox()` function using the Web Audio API.

### UI Component Library

Extensive Radix UI components are pre-configured in `src/components/ui/`. These are shadcn/ui-style React components wrapping Radix primitives with Tailwind styling. Currently the game only uses the `Button` component for "Play Again", but 30+ other components are available (accordion, alert-dialog, avatar, card, carousel, checkbox, dialog, dropdown-menu, form inputs, navigation, popover, tabs, tooltips, etc.).

### Styling Approach

- **Tailwind CSS** for all styling (configured in `src/index.css`)
- Amber/brown color palette for treasure theme (`amber-50` to `amber-900`, `amber-600` for buttons)
- Green for treasure/positive scores, red for skeleton/negative scores
- Gradient backgrounds (`bg-gradient-to-b from-amber-50 to-amber-100`)
- Backdrop blur effects (`backdrop-blur-sm`) for layered UI depth
- Responsive grid: `grid-cols-1 md:grid-cols-3` (1 column mobile, 3 columns desktop)
- Drop shadows on images and components for visual depth

## Development Patterns

### Code Style
- **Always add a one-line comment** at the top of new functions summarizing usage
- **Document input and output parameters** for all functions
- Use TypeScript interfaces for data structures (see `Box` interface in App.tsx)

### State Management
- Use React hooks (`useState`, `useEffect`) for component state
- All game state lives in the App component (single source of truth)
- Box state updates are immutable (use spread operators and `.map()`)
- State setters can accept functions for accessing previous state

### Animation Patterns (Framer Motion)
- Import `motion` from `motion/react` (not `framer-motion`)
- Use `motion.div` instead of regular `div` for animated elements
- Common props:
  - `initial`: Starting state
  - `animate`: Target state
  - `transition`: Timing config (duration, ease, delay)
  - `whileHover`: State during hover
  - `whileTap`: State during click
- Use `delay` in transitions for staggered/sequential animations

## Common Modifications

**Change Game Parameters:**
- Number of boxes: Modify `Array.from({ length: 3 }, ...)` in `initializeGame()`
- Scoring values: Change `score + 100` (treasure) and `score - 50` (skeleton) in `openBox()`
- Grid layout: Update `grid-cols-1 md:grid-cols-3` class for different responsive behavior

**Modify Game End Condition:**
- Current logic: ends when treasure found OR all boxes opened
- See the conditional checks in `openBox()` using `treasureFound` and `allOpened`

**Add New Animations:**
- Use `motion` component wrapper around the element
- Set `initial`, `animate`, and `transition` props
- For conditional animations, use ternary operators in `animate` prop values

**Add New Sound Effects:**
- Import sound file at top: `import soundName from './audios/sound.mp3'`
- Create Audio object: `const audio = new Audio(soundName)`
- Play: `audio.play()`

## Project Structure

```
claude_code_treasure_game-initial/
├── src/
│   ├── App.tsx              # Main game component (all game logic)
│   ├── main.tsx             # React app entry point
│   ├── index.css            # Tailwind CSS configuration
│   ├── assets/              # Images (chests, key icon)
│   ├── audios/              # Sound effects
│   ├── components/
│   │   ├── ui/              # Radix UI component library (30+ components)
│   │   └── figma/           # ImageWithFallback utility component
│   ├── guidelines/          # Guidelines.md template
│   ├── results/             # (Purpose not specified in codebase)
│   └── styles/              # (Additional style files if any)
├── vite.config.ts           # Vite bundler configuration
├── package.json             # Dependencies and scripts
├── index.html               # HTML entry point
└── CLAUDE.md                # This file

```

## Deployment

Custom deployment commands can be added to `.claude/commands/` folder. Examples from the project tutorial:
- `deploy_vercel.md` - Deploy to Vercel
- `deploy_github_page.md` - Deploy to GitHub Pages

After creating custom commands, restart Claude Code session to load them.

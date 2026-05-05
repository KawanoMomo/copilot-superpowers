# Janken Web App Implementation Plan

> **For agentic workers:** Use the subagent-driven-development skill (recommended) or the executing-plans skill to implement this plan task-by-task. Steps use checkbox syntax for tracking.

**Goal:** Build a Web-based one-player rock-paper-scissors game with score tracking and reset.

**Architecture:** Vanilla HTML/CSS/JS single-page app. UI interaction triggers JS game logic, which updates a state object and re-renders DOM.

**Tech Stack:** HTML5, CSS3, JavaScript (ES2020), Jest (unit tests), Playwright (optional E2E), Git.

---

### Task 1: Create static UI skeleton

**Files:**
- Create: `src/index.html`
- Create: `src/styles.css`
- Create: `src/app.js`

- [ ] **Step 1: Write failing test**
  - Not applicable (static file creation), but verify by loading in browser.

- [ ] **Step 2: Add skeleton markup in `src/index.html`**

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>じゃんけんアプリ</title>
    <link rel="stylesheet" href="styles.css" />
  </head>
  <body>
    <main class="app">
      <h1>じゃんけんアプリ</h1>
      <div id="scoreboard" aria-live="polite">
        <p>Player: <span id="player-score">0</span></p>
        <p>CPU: <span id="cpu-score">0</span></p>
        <p>Draws: <span id="draw-count">0</span></p>
      </div>

      <div id="controls">
        <button data-move="rock" id="move-rock">グー</button>
        <button data-move="paper" id="move-paper">パー</button>
        <button data-move="scissors" id="move-scissors">チョキ</button>
      </div>

      <section id="result-area" aria-live="polite">
        <p id="round-result">選択してください</p>
        <p id="player-choice">あなた: -</p>
        <p id="cpu-choice">CPU: -</p>
      </section>

      <button id="reset-game">リセット</button>
    </main>

    <script src="app.js"></script>
  </body>
</html>
```

- [ ] **Step 3: Write minimal CSS in `src/styles.css`**

```css
:root {
  font-family: "Segoe UI", Helvetica, Arial, sans-serif;
}

body {
  margin: 0;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #f4f4f8;
  color: #111;
}

.app {
  width: min(92vw, 400px);
  padding: 1rem;
  border-radius: 12px;
  background: white;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);
}

#controls {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 0.5rem;
  margin: 1rem 0;
}

button {
  font-size: 1rem;
  padding: 0.75rem;
  border: 1px solid #888;
  border-radius: 8px;
  cursor: pointer;
  background: #fff;
}

button:focus-visible {
  outline: 2px solid #0078d4;
}

#result-area {
  background: #eef;
  color: #113;
  padding: 0.75rem;
  border-radius: 8px;
}

#reset-game {
  margin-top: 1rem;
  width: 100%;
}
```

- [ ] **Step 4: Run sanity check**
  - Open `src/index.html` file in browser -> UI should display.

- [ ] **Step 5: Commit**

```bash
git add src/index.html src/styles.css src/app.js
git commit -m "feat: add janken app UI skeleton"
```

### Task 2: Implement game logic functions with unit tests

**Files:**
- Modify: `src/app.js`
- Create: `tests/app.test.js`

- [ ] **Step 1: Write failing tests for core functions**

```javascript
import { decideWinner, getCpuMove, updateScore } from '../src/app';

describe('decideWinner', () => {
  test('rock beats scissors', () => {
    expect(decideWinner('rock', 'scissors')).toBe('win');
  });
  test('rock loses to paper', () => {
    expect(decideWinner('rock', 'paper')).toBe('lose');
  });
  test('same move is draw', () => {
    expect(decideWinner('rock', 'rock')).toBe('draw');
  });
});

describe('getCpuMove', () => {
  test('returns a valid move', () => {
    const move = getCpuMove();
    expect(['rock','paper','scissors']).toContain(move);
  });
});

describe('updateScore', () => {
  test('increments player score on win', () => {
    const state = { playerScore: 0, cpuScore: 0, drawCount: 0, round: 0 };
    const next = updateScore(state, 'win');
    expect(next.playerScore).toBe(1);
  });
});
```

- [ ] **Step 2: Run tests and verify they fail**
  - `npm test -- tests/app.test.js` (or configured test runner)
  - Expected: FAIL for missing/undefined functions currently.

- [ ] **Step 3: Implement minimal logic in `src/app.js`**

```javascript
export const MOVES = ['rock', 'paper', 'scissors'];

export function getCpuMove() {
  return MOVES[Math.floor(Math.random() * MOVES.length)];
}

export function decideWinner(playerMove, cpuMove) {
  if (playerMove === cpuMove) return 'draw';

  const winsAgainst = {
    rock: 'scissors',
    scissors: 'paper',
    paper: 'rock',
  };

  return winsAgainst[playerMove] === cpuMove ? 'win' : 'lose';
}

export function updateScore(state, result) {
  const next = { ...state, round: state.round + 1 };
  if (result === 'win') next.playerScore += 1;
  else if (result === 'lose') next.cpuScore += 1;
  else next.drawCount += 1;
  return next;
}
```

- [ ] **Step 4: Run tests again and verify PASS**

- [ ] **Step 5: Commit**

```bash
git add src/app.js tests/app.test.js
git commit -m "feat: add core janken game logic and tests"
```

### Task 3: Connect UI interactivity to game logic

**Files:**
- Modify: `src/app.js`

- [ ] **Step 1: Add state object and render logic**

```javascript
const state = {
  playerScore: 0,
  cpuScore: 0,
  drawCount: 0,
  round: 0,
};

function getElement(id) {
  const el = document.getElementById(id);
  if (!el) throw new Error(`Element ${id} not found`);
  return el;
}

const playerScoreEl = getElement('player-score');
const cpuScoreEl = getElement('cpu-score');
const drawCountEl = getElement('draw-count');
const roundResultEl = getElement('round-result');
const playerChoiceEl = getElement('player-choice');
const cpuChoiceEl = getElement('cpu-choice');

function render() {
  playerScoreEl.textContent = state.playerScore;
  cpuScoreEl.textContent = state.cpuScore;
  drawCountEl.textContent = state.drawCount;
  roundResultEl.textContent = state.currentResult || '選択してください';
  playerChoiceEl.textContent = `あなた: ${state.playerMove || '-'} `;
  cpuChoiceEl.textContent = `CPU: ${state.cpuMove || '-'} `;
}

function resetGame() {
  state.playerScore = 0;
  state.cpuScore = 0;
  state.drawCount = 0;
  state.round = 0;
  state.playerMove = null;
  state.cpuMove = null;
  state.currentResult = null;
  render();
}

function playRound(playerMove) {
  const cpuMove = getCpuMove();
  const result = decideWinner(playerMove, cpuMove);
  state.playerMove = playerMove;
  state.cpuMove = cpuMove;
  state.currentResult = result === 'win' ? 'あなたの勝ち！' : result === 'lose' ? 'CPUの勝ち…' : '引き分け';
  Object.assign(state, updateScore(state, result));
  render();
}

function attachEventHandlers() {
  document.querySelectorAll('#controls button').forEach((btn) => {
    btn.addEventListener('click', () => playRound(btn.dataset.move));
  });

  getElement('reset-game').addEventListener('click', resetGame);
}

(function init() {
  attachEventHandlers();
  resetGame();
})();
```

- [ ] **Step 2: Write a failing E2E test (optional, but preferred)**

```javascript
// e2e/janken.spec.js
import { test, expect } from '@playwright/test';

test('play one round updates score and result text', async ({ page }) => {
  await page.goto('http://localhost:3000');
  await page.click('#move-rock');
  await expect(page.locator('#player-choice')).toContainText('あなた: rock');
  await expect(page.locator('#cpu-choice')).not.toContainText('CPU: -');
});
```

- [ ] **Step 3: Run test to confirm fail or setup check**

- [ ] **Step 4: Run browser and verify behavior manually**

- [ ] **Step 5: Commit**

```bash
git add src/app.js
git commit -m "feat: hook up UI to janken logic"
```

### Task 4: Add robustness and UX polish

**Files:**
- Modify: `src/app.js`, `src/styles.css`

- [ ] **Step 1: Disable input while processing (debounce)**
- [ ] **Step 2: Add visual states (`.winning`, `.losing`, `.draw`) in `styles.css`**
- [ ] **Step 3: Add keyboard accessibility: Enter/Space triggers selected button**
- [ ] **Step 4: Add local storage persistence (optional)**

- [ ] **Step 5: Commit**

```bash
git add src/app.js src/styles.css
git commit -m "chore: improve UX and robustness"
```


## Plan Review Loop
1. Dispatch spec-reviewer subagent for plan path.
2. Apply feedback and repeat (max 3 iterations).

## Next step
- Wait for your preference: **1) Subagent-driven execution** or **2) Inline implementation in this session**.

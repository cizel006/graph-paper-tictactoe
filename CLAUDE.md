# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Graph Paper Tic-Tac-Toe: the entire game is one self-contained file, `index.html` (inline CSS and JS, only external dependency is Google Fonts). There is no build step, package manager, linter, or test suite.

- **Run locally:** open `index.html` in a browser (`open index.html`).
- **Deploy:** GitHub Pages serves the root of `main` at https://cizel006.github.io/graph-paper-tictactoe/. Pushing to `main` redeploys; the file must stay named `index.html` so the bare URL loads the game.

## Git workflow

Commit and push to GitHub (`origin main`) regularly as you work — after each meaningful, working change, not just at the end — so no progress is ever lost. This is a standing instruction; no need to ask before each commit/push. Keep commits small with descriptive messages. Since pushes to `main` deploy live via Pages, don't push a half-broken game.

## Repo layout gotcha

The repo root is `Desktop/software`, which also contains `deneme/` — an unrelated Xcode/SwiftUI project with its own nested git repo. It is excluded via `.gitignore` (along with `.claude/`). Don't add it to this repo.

## Architecture of `index.html`

All game logic lives in one IIFE in the `<script>` block, built around a single mutable state object `s`:

- `fresh(prev)` builds the state for a new round. Passing the previous state carries over `mode`, `score`, and `history` and alternates `starter` (X/O take turns going first); passing `null` resets everything.
- `place(i)` mutates the board, checks `winner()`, and on game end updates `score` and appends to `history`. `play(i)` is the user-input entry point and schedules the computer's reply via `setTimeout`.
- `render()` is the single redraw path: it syncs labels, tallies, cell enabled/disabled state, status text, the win line, and the scoreboard table, then calls `save()`. Call it after any state change. Marks are drawn into cells by `place()`/`redrawMarks()`, not by `render()`.
- **AI** (`cpuMove`, `minimax`): the computer is always `O`, in `mode === 'cpu'`. Depth-scored minimax, but with a deliberate 20% random move and a random opening, so it is intentionally beatable.
- **Persistence:** `localStorage` key `ttt-scoreboard-v1` stores only `{mode, score, history}` (not the in-progress board). Changing that shape requires bumping the key or keeping `load()` tolerant of old data.
- **Input:** cells are buttons; keys 1–9 map to squares (top-left = 1), `N` starts a new round.

## Styling conventions

Colors are CSS custom properties on `:root`, with dark-mode values duplicated in both `@media (prefers-color-scheme: dark) :root:not([data-theme="light"])` and `:root[data-theme="dark"]` — update both when changing a color. Hand-drawn strokes animate via `pathLength="1"` + the `.draw` class; `prefers-reduced-motion` disables it.

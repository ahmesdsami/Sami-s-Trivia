# 🎯 Ultimate Trivia Showdown

A multiplayer trivia game with 40 categories, randomly picking 4 per match.
Each category has 10 questions of increasing difficulty (100–1000 points).

## Deploy on GitHub Pages
1. Create a new GitHub repo (e.g. `trivia-game`).
2. Upload all files preserving the folder structure.
3. Go to **Settings → Pages → Source: main / root** → Save.
4. Your game will be live at `https://<username>.github.io/trivia-game/`.

## How to Play
- Set the number of players (2–8) and their names.
- The board shows 4 random categories.
- Players take turns; the current player picks a tile.
- Higher point tile = harder question.
- Click **Reveal Answer**, then mark correct/wrong.
- After all 40 tiles, highest score wins!

## File Structure
- `index.html` – Layout and screens
- `css/style.css` – Styling
- `js/questions.js` – All 400 questions
- `js/game.js` – Game state & logic
- `js/app.js` – UI wiring and events
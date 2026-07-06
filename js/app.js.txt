// UI wiring
const $ = id => document.getElementById(id);

// --- Setup Screen ---
const playerCountInput = $("player-count");
const playersInputs = $("players-inputs");
const startBtn = $("start-btn");

function renderPlayerInputs() {
    const n = Math.max(2, Math.min(8, parseInt(playerCountInput.value) || 2));
    playersInputs.innerHTML = "";
    for (let i = 0; i < n; i++) {
        const label = document.createElement("label");
        label.textContent = `Player ${i + 1} Name`;
        const input = document.createElement("input");
        input.type = "text";
        input.value = `Player ${i + 1}`;
        input.dataset.idx = i;
        input.className = "player-name-input";
        playersInputs.appendChild(label);
        playersInputs.appendChild(input);
    }
}
playerCountInput.addEventListener("input", renderPlayerInputs);
renderPlayerInputs();

startBtn.addEventListener("click", () => {
    const inputs = document.querySelectorAll(".player-name-input");
    const names = [...inputs].map(i => i.value.trim() || `Player ${+i.dataset.idx + 1}`);
    Game.init(names);
    showScreen("board-screen");
    renderBoard();
    updateHUD();
});

// --- Screen Switching ---
function showScreen(id) {
    document.querySelectorAll(".screen").forEach(s => s.classList.remove("active"));
    $(id).classList.add("active");
}

// --- Board Rendering ---
function renderBoard() {
    const boardEl = $("board");
    boardEl.innerHTML = "";
    // Headers
    Game.categories.forEach(cat => {
        const h = document.createElement("div");
        h.className = "category-header";
        h.textContent = cat;
        boardEl.appendChild(h);
    });
    // Rows of 10 (points ascending); grid is 4 cols so we interleave column-wise
    for (let row = 0; row < 10; row++) {
        Game.categories.forEach(cat => {
            const tile = Game.board[cat][row];
            const el = document.createElement("div");
            el.className = "tile" + (tile.used ? " used" : "");
            el.textContent = tile.used ? "—" : tile.points;
            el.addEventListener("click", () => {
                if (tile.used) return;
                openQuestion(cat, row);
            });
            boardEl.appendChild(el);
        });
    }
}

// --- HUD ---
function updateHUD() {
    $("current-player").textContent = Game.currentPlayer().name;
    const sb = $("scoreboard");
    sb.innerHTML = "";
    Game.players.forEach((p, idx) => {
        const chip = document.createElement("div");
        chip.className = "score-chip" + (idx === Game.currentPlayerIndex ? " active" : "");
        chip.textContent = `${p.name}: ${p.score}`;
        sb.appendChild(chip);
    });
}

// --- Question Modal ---
const modal = $("question-modal");
const revealBtn = $("reveal-btn");
const correctBtn = $("correct-btn");
const wrongBtn = $("wrong-btn");

function openQuestion(cat, idx) {
    const q = Game.selectQuestion(cat, idx);
    if (!q) return;
    $("q-category").textContent = cat;
    $("q-points").textContent = `${q.points} pts`;
    $("q-text").textContent = q.q;
    $("q-answer").textContent = q.a;
    $("q-answer").classList.add("hidden");
    revealBtn.classList.remove("hidden");
    correctBtn.classList.add("hidden");
    wrongBtn.classList.add("hidden");
    modal.classList.remove("hidden");
}

revealBtn.addEventListener("click", () => {
    $("q-answer").classList.remove("hidden");
    revealBtn.classList.add("hidden");
    correctBtn.classList.remove("hidden");
    wrongBtn.classList.remove("hidden");
});

function closeAndResolve(correct) {
    Game.resolveQuestion(correct);
    modal.classList.add("hidden");
    renderBoard();
    updateHUD();
    if (Game.isGameOver()) endGame();
}

correctBtn.addEventListener("click", () => closeAndResolve(true));
wrongBtn.addEventListener("click", () => closeAndResolve(false));

// --- End Game ---
$("end-game-btn").addEventListener("click", () => {
    if (confirm("End game now?")) endGame();
});

function endGame() {
    const ranked = Game.finalRanking();
    const container = $("final-scores");
    container.innerHTML = "";
    ranked.forEach((p, i) => {
        const row = document.createElement("div");
        row.className = "final-row" + (i === 0 ? " winner" : "");
        row.innerHTML = `<span>${i === 0 ? "🏆 " : `#${i + 1} `}${p.name}</span><span>${p.score} pts</span>`;
        container.appendChild(row);
    });
    showScreen("end-screen");
}

$("restart-btn").addEventListener("click", () => location.reload());
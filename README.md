# Hi 👋 I'm Amit Sharma (Catalyst097)

> Data scientist focused on machine learning, healthcare analytics, and interpretable models.

[![GitHub followers](https://img.shields.io/github/followers/Catalyst097?label=Follow&style=social)](https://github.com/Catalyst097)

---

## 🎮 PLAY MY PAC-MAN GAME 🎮

🟨 **Eat all green squares (contributions)** • 👻 **Avoid red ghosts** • ⬆️⬇️⬅️➡️ **Arrow keys to move** • SPACE to start

<details open>
<summary><b>🕹️ CLICK HERE TO EXPAND AND PLAY THE GAME! 🕹️</b></summary>

<!DOCTYPE html>
<html>
<head>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%); font-family: Arial; }
.game-container { width: 100%; max-width: 100%; padding: 15px; }
.game-title { text-align: center; color: #ffd700; font-size: 24px; font-weight: bold; text-shadow: 0 0 20px rgba(255, 215, 0, 0.8); margin-bottom: 15px; animation: pulse 1.5s infinite; }
@keyframes pulse { 0%, 100% { text-shadow: 0 0 20px rgba(255, 215, 0, 0.8); } 50% { text-shadow: 0 0 40px rgba(255, 215, 0, 1); } }
.stats-bar { display: flex; justify-content: space-around; margin-bottom: 10px; padding: 8px; background: rgba(0, 0, 0, 0.7); border-radius: 5px; border: 2px solid #ffd700; }
.stat { color: #00ff00; font-weight: bold; font-size: 13px; text-shadow: 0 0 10px rgba(0, 255, 0, 0.8); }
.stat-label { color: #ffd700; margin-right: 5px; }
canvas { display: block; background: #000; border: 3px solid #ffd700; border-radius: 5px; box-shadow: 0 0 30px rgba(255, 215, 0, 0.6), inset 0 0 30px rgba(255, 0, 0, 0.1); width: 100%; height: auto; max-width: 100%; }
.game-info { text-align: center; color: #00ff00; margin-top: 10px; font-size: 11px; text-shadow: 0 0 10px rgba(0, 255, 0, 0.6); }
.game-over-screen { display: none; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); background: rgba(0, 0, 0, 0.95); padding: 30px; border-radius: 10px; border: 3px solid #ffd700; text-align: center; z-index: 100; }
.game-over-screen.show { display: block; animation: popIn 0.3s ease-out; }
@keyframes popIn { from { transform: translate(-50%, -50%) scale(0.8); opacity: 0; } to { transform: translate(-50%, -50%) scale(1); opacity: 1; } }
.game-over-text { color: #ff0000; font-size: 20px; font-weight: bold; margin-bottom: 15px; text-shadow: 0 0 20px rgba(255, 0, 0, 0.8); }
.final-score { color: #00ff00; font-size: 16px; margin-bottom: 15px; }
.restart-btn { background: #ffd700; color: #000; border: none; padding: 8px 20px; font-size: 12px; font-weight: bold; border-radius: 5px; cursor: pointer; }
.restart-btn:hover { background: #ffed4e; box-shadow: 0 0 20px rgba(255, 215, 0, 0.8); }
</style>
</head>
<body>
<div class="game-container">
<div class="stats-bar">
<div class="stat"><span class="stat-label">SCORE:</span><span id="score">0</span></div>
<div class="stat"><span class="stat-label">LEVEL:</span><span id="level">1</span></div>
<div class="stat"><span class="stat-label">PELLETS:</span><span id="pellets">0</span></div>
<div class="stat"><span class="stat-label">LIVES:</span><span id="lives">3</span></div>
</div>
<canvas id="gameCanvas" width="600" height="400"></canvas>
<div class="game-info">⬆️ ⬇️ ⬅️ ➡️ Arrow Keys or WASD to move • SPACE to Start/Pause</div>
<div class="game-over-screen" id="gameOverScreen">
<div class="game-over-text" id="gameOverText">GAME OVER!</div>
<div class="final-score">Score: <span id="finalScore">0</span></div>
<button class="restart-btn" onclick="location.reload()">Play Again</button>
</div>
</div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const GRID_SIZE = 20;
const COLS = Math.floor(canvas.width / GRID_SIZE);
const ROWS = Math.floor(canvas.height / GRID_SIZE);

let gameState = {
    pacman: { x: 5, y: 5, direction: 'right', nextDirection: 'right', mouthOpen: true },
    ghosts: [
        { x: Math.floor(COLS / 2), y: 2, color: '#ff0000', direction: 'left' },
        { x: Math.floor(COLS / 2) + 2, y: 2, color: '#ffb8ff', direction: 'right' },
        { x: Math.floor(COLS / 2) - 2, y: 2, color: '#00ffff', direction: 'left' }
    ],
    pellets: [],
    score: 0,
    level: 1,
    lives: 3,
    gameRunning: false,
    gameOverFlag: false,
    pelletsEaten: 0
};

function initPellets() {
    gameState.pellets = [];
    for (let i = 0; i < COLS; i++) {
        for (let j = 0; j < ROWS; j++) {
            if (Math.random() > 0.3 && !(i === gameState.pacman.x && j === gameState.pacman.y)) {
                gameState.pellets.push({ x: i, y: j, eaten: false });
            }
        }
    }
}

initPellets();

const keys = {};
window.addEventListener('keydown', (e) => {
    keys[e.key] = true;
    if (e.key === ' ') {
        e.preventDefault();
        gameState.gameRunning = !gameState.gameRunning;
    }
    const keyMap = {
        'ArrowUp': 'up', 'w': 'up', 'W': 'up',
        'ArrowDown': 'down', 's': 'down', 'S': 'down',
        'ArrowLeft': 'left', 'a': 'left', 'A': 'left',
        'ArrowRight': 'right', 'd': 'right', 'D': 'right'
    };
    if (keyMap[e.key]) gameState.pacman.nextDirection = keyMap[e.key];
});

function canMove(x, y) { return x >= 0 && x < COLS && y >= 0 && y < ROWS; }
function getNextPosition(x, y, direction) {
    switch(direction) {
        case 'up': return { x, y: y - 1 };
        case 'down': return { x, y: y + 1 };
        case 'left': return { x: x - 1, y };
        case 'right': return { x: x + 1, y };
    }
    return { x, y };
}

function updatePacman() {
    let nextPos = getNextPosition(gameState.pacman.x, gameState.pacman.y, gameState.pacman.nextDirection);
    if (canMove(nextPos.x, nextPos.y)) {
        gameState.pacman.direction = gameState.pacman.nextDirection;
        gameState.pacman.x = nextPos.x;
        gameState.pacman.y = nextPos.y;
    } else {
        nextPos = getNextPosition(gameState.pacman.x, gameState.pacman.y, gameState.pacman.direction);
        if (canMove(nextPos.x, nextPos.y)) {
            gameState.pacman.x = nextPos.x;
            gameState.pacman.y = nextPos.y;
        }
    }
    gameState.pacman.mouthOpen = !gameState.pacman.mouthOpen;
    for (let pellet of gameState.pellets) {
        if (pellet.x === gameState.pacman.x && pellet.y === gameState.pacman.y && !pellet.eaten) {
            pellet.eaten = true;
            gameState.score += 10;
            gameState.pelletsEaten++;
        }
    }
}

function updateGhosts() {
    for (let ghost of gameState.ghosts) {
        if (Math.random() > 0.97) {
            const directions = ['up', 'down', 'left', 'right'];
            ghost.direction = directions[Math.floor(Math.random() * directions.length)];
        }
        let nextPos = getNextPosition(ghost.x, ghost.y, ghost.direction);
        if (canMove(nextPos.x, nextPos.y)) {
            ghost.x = nextPos.x;
            ghost.y = nextPos.y;
        } else {
            const directions = ['up', 'down', 'left', 'right'];
            ghost.direction = directions[Math.floor(Math.random() * directions.length)];
        }
    }
}

function checkGhostCollision() {
    for (let ghost of gameState.ghosts) {
        if (ghost.x === gameState.pacman.x && ghost.y === gameState.pacman.y) {
            gameState.lives--;
            if (gameState.lives <= 0) endGame();
            else {
                gameState.pacman.x = 5;
                gameState.pacman.y = 5;
            }
        }
    }
}

function checkWinCondition() {
    if (gameState.pelletsEaten === gameState.pellets.length) {
        gameState.level++;
        gameState.score += 500;
        gameState.pelletsEaten = 0;
        gameState.pacman.x = 5;
        gameState.pacman.y = 5;
        initPellets();
    }
}

function drawPacman() {
    const x = gameState.pacman.x * GRID_SIZE + GRID_SIZE / 2;
    const y = gameState.pacman.y * GRID_SIZE + GRID_SIZE / 2;
    const radius = GRID_SIZE / 2.2;
    ctx.fillStyle = '#ffd700';
    ctx.beginPath();
    const mouthAngle = gameState.pacman.mouthOpen ? 0.3 : 0.1;
    const directionAngle = {
        'right': 0, 'down': Math.PI / 2, 'left': Math.PI, 'up': 3 * Math.PI / 2
    }[gameState.pacman.direction];
    ctx.arc(x, y, radius, directionAngle + mouthAngle, directionAngle - mouthAngle + 2 * Math.PI - 2 * mouthAngle, false);
    ctx.lineTo(x, y);
    ctx.fill();
    ctx.fillStyle = '#000';
    const eyeDistance = radius * 0.5;
    const eyeX = x + Math.cos(directionAngle) * eyeDistance;
    const eyeY = y + Math.sin(directionAngle) * eyeDistance;
    ctx.beginPath();
    ctx.arc(eyeX, eyeY, 2, 0, Math.PI * 2);
    ctx.fill();
}

function drawGhosts() {
    for (let ghost of gameState.ghosts) {
        const x = ghost.x * GRID_SIZE + GRID_SIZE / 2;
        const y = ghost.y * GRID_SIZE + GRID_SIZE / 2;
        const size = GRID_SIZE * 0.9;
        ctx.fillStyle = ghost.color;
        ctx.beginPath();
        ctx.moveTo(x - size / 2, y - size / 2);
        ctx.lineTo(x + size / 2, y - size / 2);
        ctx.quadraticCurveTo(x + size / 2, y + size / 2, x, y + size / 2);
        ctx.quadraticCurveTo(x - size / 2, y + size / 2, x - size / 2, y - size / 2);
        ctx.fill();
        ctx.fillStyle = '#fff';
        ctx.beginPath();
        ctx.arc(x - 5, y, 3, 0, Math.PI * 2);
        ctx.fill();
        ctx.beginPath();
        ctx.arc(x + 5, y, 3, 0, Math.PI * 2);
        ctx.fill();
        ctx.fillStyle = '#000';
        ctx.beginPath();
        ctx.arc(x - 5, y, 1.5, 0, Math.PI * 2);
        ctx.fill();
        ctx.beginPath();
        ctx.arc(x + 5, y, 1.5, 0, Math.PI * 2);
        ctx.fill();
    }
}

function drawPellets() {
    for (let pellet of gameState.pellets) {
        if (!pellet.eaten) {
            const x = pellet.x * GRID_SIZE + GRID_SIZE / 2;
            const y = pellet.y * GRID_SIZE + GRID_SIZE / 2;
            ctx.fillStyle = 'rgba(0, 255, 0, 0.3)';
            ctx.beginPath();
            ctx.arc(x, y, 6, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillStyle = '#00ff00';
            ctx.beginPath();
            ctx.arc(x, y, 3, 0, Math.PI * 2);
            ctx.fill();
        }
    }
}

function endGame() {
    gameState.gameOverFlag = true;
    gameState.gameRunning = false;
    document.getElementById('gameOverText').textContent = '👾 GAME OVER! 👾';
    document.getElementById('finalScore').textContent = gameState.score;
    document.getElementById('gameOverScreen').classList.add('show');
}

function update() {
    if (gameState.gameRunning && !gameState.gameOverFlag) {
        updatePacman();
        updateGhosts();
        checkGhostCollision();
        checkWinCondition();
    }
}

function draw() {
    ctx.fillStyle = '#000';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    ctx.strokeStyle = 'rgba(255, 215, 0, 0.1)';
    ctx.lineWidth = 0.5;
    for (let i = 0; i <= COLS; i++) {
        ctx.beginPath();
        ctx.moveTo(i * GRID_SIZE, 0);
        ctx.lineTo(i * GRID_SIZE, canvas.height);
        ctx.stroke();
    }
    for (let j = 0; j <= ROWS; j++) {
        ctx.beginPath();
        ctx.moveTo(0, j * GRID_SIZE);
        ctx.lineTo(canvas.width, j * GRID_SIZE);
        ctx.stroke();
    }
    drawPellets();
    drawPacman();
    drawGhosts();
}

function updateUI() {
    document.getElementById('score').textContent = gameState.score;
    document.getElementById('level').textContent = gameState.level;
    document.getElementById('pellets').textContent = gameState.pelletsEaten;
    document.getElementById('lives').textContent = gameState.lives;
}

function gameLoop() {
    update();
    draw();
    updateUI();
    requestAnimationFrame(gameLoop);
}

gameLoop();
</script>
</body>
</html>

</details>

---

## 👩‍🔬 About Me
- I'm a data scientist who builds and evaluates ML models, performs rigorous EDA, and focuses on reproducible, interpretable results.
- Interests: Healthcare analytics, predictive modeling, feature engineering, model explainability, and deploying ML pipelines.

## 🔭 Current Work
- Working on: [breast_cancer](https://github.com/Catalyst097/breast_cancer) — breast cancer prediction & analysis (EDA, feature engineering, classification models, evaluation).
- Learning: advanced model interpretability (SHAP/Integrated Gradients), MLOps patterns for reproducible training and deployment.

## 🛠️ Skills
- Languages & tools: Python, pandas, NumPy, scikit-learn, PyTorch, TensorFlow, SQL
- Data & infra: Jupyter, MLflow, Docker, GitHub Actions, PostgreSQL
- Methods: supervised learning, classification, feature engineering, cross-validation, model explainability

## 🚀 Featured Projects
- [breast_cancer](https://github.com/Catalyst097/breast_cancer) — Predicts whether a tumor is Malignant or Benign using the UCI Breast Cancer Wisconsin (Diagnostic) dataset. Includes EDA, outlie[...]
- [heart-disease-app](https://github.com/Catalyst097/heart-disease-app) — Heart disease classifier (model and supporting code). Focuses on tabular ML workflows for clinical risk prediction.
- [plant-diseases-classifier-](https://github.com/Catalyst097/plant-diseases-classifier-) — Transfer learning image classifier for 38 plant disease classes using MobileNetV2 on the PlantVillage [...]
- [titanic-survival-prediction](https://github.com/Catalyst097/titanic-survival-prediction) — ML project using Logistic Regression and Random Forest to predict Titanic survivors; achieved ~82% a[...]
- [Word-Guessing-Game](https://github.com/Catalyst097/Word-Guessing-Game) — A simple Python command-line word guessing game (guess letters, limited lives) — great small project for beginners.


## ✨ Tech & Tools (visual)

<p align="left">
  <img src="https://www.tensorflow.org/images/tf_logo_social.png" alt="TensorFlow" width="80" style="margin-right:10px;"/>
  <img src="https://www.python.org/static/community_logos/python-logo.png" alt="Python" width="120" style="margin-right:10px;"/>
  <img src="https://scikit-learn.org/stable/_static/scikit-learn-logo-small.png" alt="scikit-learn" width="140" style="margin-right:10px;"/>
  <img src="https://media.giphy.com/media/26BRzozw4V0aB7QXK/giphy.gif" alt="data-science-gif" width="140"/>
</p>


## 📈 GitHub Stats
[![Amit's GitHub stats](https://github-readme-stats.vercel.app/api?username=Catalyst097&show_icons=true&theme=tokyonight&count_private=true)](https://github.com/Catalyst097)

[![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=Catalyst097&layout=compact&theme=tokyonight)](https://github.com/Catalyst097)

## 📫 Contact
- Email: [aasharma982@gmail.com](mailto:aasharma982@gmail.com)

---

Tips
- Pin 4–6 repositories (including breast_cancer and the new projects) to highlight your best work.
- Consider adding a small project GIF or a custom banner under an `assets/` folder for visual flair — you can add your own animated GIFs to the repo and reference them as `assets/banner.gif`.
- Add a GitHub Action to update a "Recently updated" or "Quote of the day" section if you want dynamic content.

Thanks for visiting — feel free to open issues on any repo or reach out via email!

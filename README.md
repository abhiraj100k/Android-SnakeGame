# Android-SnakeGame
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Pro Snake Game</title>

<style>
body {
  margin: 0;
  background: #0f0f0f;
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: Arial;
  color: white;
}

h2 { color: lime; margin: 10px; }

canvas {
  background: black;
  border: 3px solid lime;
}

button {
  margin-top: 10px;
  padding: 10px 20px;
  background: lime;
  border: none;
  font-size: 16px;
  border-radius: 8px;
}
</style>
</head>

<body>

<h2>🐍 Pro Snake Game</h2>
<h3>Score: <span id="score">0</span></h3>
<h4>High Score: <span id="highScore">0</span></h4>

<canvas id="game" width="350" height="350"></canvas>
<button onclick="restartGame()">Restart</button>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

const box = 15;
let snake, direction, food, score, speed, game;

let highScore = localStorage.getItem("highScore") || 0;
document.getElementById("highScore").innerText = highScore;

function init() {
  snake = [{ x: 150, y: 150 }];
  direction = "RIGHT";
  score = 0;
  speed = 120;
  document.getElementById("score").innerText = score;

  food = {
    x: Math.floor(Math.random() * 20) * box,
    y: Math.floor(Math.random() * 20) * box
  };

  if (game) clearInterval(game);
  game = setInterval(draw, speed);
}

function draw() {
  ctx.fillStyle = "black";
  ctx.fillRect(0, 0, 350, 350);

  snake.forEach((part, i) => {
    ctx.fillStyle = i === 0 ? "lime" : "green";
    ctx.fillRect(part.x, part.y, box, box);
  });

  ctx.fillStyle = "red";
  ctx.fillRect(food.x, food.y, box, box);

  let headX = snake[0].x;
  let headY = snake[0].y;

  if (direction === "LEFT") headX -= box;
  if (direction === "UP") headY -= box;
  if (direction === "RIGHT") headX += box;
  if (direction === "DOWN") headY += box;

  if (
    headX < 0 || headX >= 350 ||
    headY < 0 || headY >= 350 ||
    collision(headX, headY)
  ) {
    clearInterval(game);

    if (score > highScore) {
      localStorage.setItem("highScore", score);
    }

    alert("Game Over 😢 Score: " + score);
  }

  let newHead = { x: headX, y: headY };

  if (headX === food.x && headY === food.y) {
    score++;
    document.getElementById("score").innerText = score;

    // Speed increase
    speed *= 0.95;
    clearInterval(game);
    game = setInterval(draw, speed);

    food = {
      x: Math.floor(Math.random() * 20) * box,
      y: Math.floor(Math.random() * 20) * box
    };
  } else {
    snake.pop();
  }

  snake.unshift(newHead);
}

function collision(x, y) {
  return snake.some(part => part.x === x && part.y === y);
}

// Swipe Control
let startX, startY;

canvas.addEventListener("touchstart", e => {
  startX = e.touches[0].clientX;
  startY = e.touches[0].clientY;
});

canvas.addEventListener("touchmove", e => {
  if (!startX || !startY) return;

  let diffX = e.touches[0].clientX - startX;
  let diffY = e.touches[0].clientY - startY;

  if (Math.abs(diffX) > Math.abs(diffY)) {
    if (diffX > 0 && direction !== "LEFT") direction = "RIGHT";
    else if (diffX < 0 && direction !== "RIGHT") direction = "LEFT";
  } else {
    if (diffY > 0 && direction !== "UP") direction = "DOWN";
    else if (diffY < 0 && direction !== "DOWN") direction = "UP";
  }

  startX = null;
  startY = null;
});

function restartGame() {
  init();
}

init();
</script>

</body>
</html>

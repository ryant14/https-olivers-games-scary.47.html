# https-oliversgames.47.html
<!DOCTYPE html>
<html>
<head>
  <title>My Website</title>
</head>
<body>
  <h1>Hello World!</h1>
  <p>This is my GitHub website.</p>
</body>
</html>
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Click the Dot!</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      display: grid;
      place-items: center;
      height: 100vh;
      background: #f5f5f5;
    }
    .box {
      width: 360px;
      background: white;
      border-radius: 14px;
      padding: 16px;
      box-shadow: 0 8px 25px rgba(0,0,0,0.12);
    }
    h1 { margin: 0 0 8px; font-size: 22px; }
    .stats { display: flex; justify-content: space-between; margin-bottom: 10px; }
    button {
      width: 100%;
      padding: 10px;
      border: none;
      border-radius: 10px;
      background: #111;
      color: white;
      font-size: 16px;
      cursor: pointer;
    }
    button:disabled { opacity: 0.5; cursor: not-allowed; }
    #arena {
      position: relative;
      height: 260px;
      margin-top: 12px;
      border: 2px dashed #ddd;
      border-radius: 12px;
      background: #fafafa;
      overflow: hidden;
    }
    #dot {
      position: absolute;
      width: 36px;
      height: 36px;
      border-radius: 50%;
      background: #ff3b30;
      display: none;
      cursor: pointer;
    }
    .tip { margin-top: 10px; color: #444; font-size: 14px; }
  </style>
</head>
<body>
  <div class="box">
    <h1>🎯 Click the Dot</h1>

    <div class="stats">
      <div>Score: <strong id="score">0</strong></div>
      <div>Time: <strong id="time">30</strong>s</div>
    </div>

    <button id="startBtn">Start Game</button>

    <div id="arena">
      <div id="dot" title="Click me!"></div>
    </div>

    <div class="tip">Click the red dot as many times as you can in 30 seconds.</div>
  </div>

  <script>
    const startBtn = document.getElementById("startBtn");
    const arena = document.getElementById("arena");
    const dot = document.getElementById("dot");
    const scoreEl = document.getElementById("score");
    const timeEl = document.getElementById("time");

    let score = 0;
    let timeLeft = 30;
    let timer = null;
    let playing = false;

    function randomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    function moveDot() {
      const arenaRect = arena.getBoundingClientRect();
      const dotSize = 36;

      const maxX = arenaRect.width - dotSize;
      const maxY = arenaRect.height - dotSize;

      dot.style.left = randomInt(0, Math.max(0, Math.floor(maxX))) + "px";
      dot.style.top  = randomInt(0, Math.max(0, Math.floor(maxY))) + "px";
    }

    function startGame() {
      score = 0;
      timeLeft = 30;
      playing = true;

      scoreEl.textContent = score;
      timeEl.textContent = timeLeft;

      dot.style.display = "block";
      moveDot();

      startBtn.disabled = true;
      startBtn.textContent = "Game Running...";

      timer = setInterval(() => {
        timeLeft--;
        timeEl.textContent = timeLeft;

        if (timeLeft <= 0) {
          endGame();
        }
      }, 1000);
    }

    function endGame() {
      playing = false;
      clearInterval(timer);
      timer = null;

      dot.style.display = "none";

      startBtn.disabled = false;
      startBtn.textContent = "Play Again";

      alert("Time's up! Your score: " + score);
    }

    dot.addEventListener("click", () => {
      if (!playing) return;
      score++;
      scoreEl.textContent = score;
      moveDot();
    });

    startBtn.addEventListener("click", startGame);
  </script>
</body>
</html>

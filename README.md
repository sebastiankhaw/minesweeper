<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Simple Game Zone - Minesweeper</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f9f9f9;
      margin: 0;
      padding: 20px;
      text-align: center;
      color: #333;
    }

    h1 {
      margin-bottom: 5px;
    }

    #description {
      margin-top: 0;
      font-size: 18px;
      color: #666;
    }

    #grid {
      display: grid;
      grid-template-columns: repeat(5, 60px);
      grid-gap: 5px;
      justify-content: center;
      margin: 20px auto;
    }

    .cell {
      width: 60px;
      height: 60px;
      background-color: #ddd;
      border: none;
      font-size: 20px;
      cursor: pointer;
      transition: background-color 0.2s;
    }

    .cell:hover {
      background-color: #ccc;
    }

    .safe {
      background-color: #a7f3d0 !important;
    }

    .mine {
      background-color: #f87171 !important;
    }

    #status {
      margin-top: 10px;
      font-weight: bold;
    }

    #restart {
      margin-top: 15px;
      padding: 8px 16px;
      font-size: 16px;
      background-color: #3b82f6;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    }

    #restart:hover {
      background-color: #2563eb;
    }
  </style>
</head>
<body>
  <h1>Simple Game Zone 🎮</h1>
  <p id="description">Try not to hit the mines!</p>

  <div id="grid"></div>
  <p id="status"></p>
  <button id="restart" onclick="location.reload()">🔄 Restart</button>

  <script>
    const gridSize = 5;
    const mineCount = 5;
    let minePositions = new Set();
    let gameOver = false;
    let safeClicked = 0;
    const totalSafe = gridSize * gridSize - mineCount;

    // Random mine positions
    while (minePositions.size < mineCount) {
      minePositions.add(Math.floor(Math.random() * gridSize * gridSize));
    }

    const grid = document.getElementById("grid");
    const status = document.getElementById("status");

    for (let i = 0; i < gridSize * gridSize; i++) {
      const button = document.createElement("button");
      button.className = "cell";
      button.dataset.index = i;
      button.onclick = function () {
        if (gameOver || button.disabled) return;

        if (minePositions.has(i)) {
          button.classList.add("mine");
          button.textContent = "💣";
          status.textContent = "Boom! You hit a mine. Game over.";
          revealMines();
          gameOver = true;
        } else {
          button.classList.add("safe");
          button.disabled = true;
          const count = getAdjacentMineCount(i);
          button.textContent = count > 0 ? count : "✅";
          safeClicked++;
          if (safeClicked === totalSafe) {
            status.textContent = "You win! 🎉 All safe cells revealed.";
            gameOver = true;
            revealMines();
          }
        }
      };
      grid.appendChild(button);
    }

    function revealMines() {
      document.querySelectorAll(".cell").forEach(btn => {
        if (minePositions.has(parseInt(btn.dataset.index))) {
          btn.classList.add("mine");
          btn.textContent = "💣";
        }
        btn.disabled = true;
      });
    }

    function getAdjacentMineCount(index) {
      const row = Math.floor(index / gridSize);
      const col = index % gridSize;
      let count = 0;
      for (let r = -1; r <= 1; r++) {
        for (let c = -1; c <= 1; c++) {
          if (r === 0 && c === 0) continue;
          const newRow = row + r;
          const newCol = col + c;
          if (
            newRow >= 0 && newRow < gridSize &&
            newCol >= 0 && newCol < gridSize
          ) {
            const newIndex = newRow * gridSize + newCol;
            if (minePositions.has(newIndex)) count++;
          }
        }
      }
      return count;
    }
  </script>
</body>
</html>

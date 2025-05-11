<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Crash Game - Controlled</title>
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', sans-serif;
      background: #121212;
      color: #ffffff;
      text-align: center;
      padding: 40px;
    }

    h1 {
      color: #0ff;
      margin-bottom: 10px;
    }

    #multiplier {
      font-size: 72px;
      color: #0f0;
      margin: 40px 0;
    }

    .controls {
      margin-bottom: 30px;
    }

    input, button {
      padding: 10px 15px;
      font-size: 16px;
      margin: 10px;
      border: none;
      border-radius: 5px;
    }

    input {
      width: 100px;
    }

    .button {
      background-color: #0ff;
      color: #000;
      cursor: pointer;
      transition: background 0.3s;
    }

    .button:hover {
      background-color: #0cc;
    }

    .cashout {
      background-color: #f90;
      color: #000;
    }

    #status {
      font-size: 24px;
      margin-top: 20px;
    }

    #balance {
      font-size: 20px;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <h1>Controlled Crash Game</h1>

  <div id="balance">Balance: $1000</div>

  <div class="controls">
    <label>Bet Amount: <input type="number" id="betAmount" value="50" min="1"></label>
    <label>Crash At: <input type="number" step="0.1" id="crashPoint" value="5.0" min="1.01"></label>
    <button class="button" onclick="startGame()">Start Game</button>
  </div>

  <div id="multiplier">1.00x</div>
  <div id="status">Waiting to start...</div>
  <button class="button cashout" onclick="cashOut()">Cash Out</button>

  <script>
    let multiplier = 1.00;
    let crashAt = 5.00;
    let interval;
    let isRunning = false;
    let hasCashedOut = false;
    let balance = 1000;
    let bet = 0;

    function updateUI() {
      document.getElementById('multiplier').textContent = multiplier.toFixed(2) + 'x';
      document.getElementById('balance').textContent = 'Balance: $' + balance.toFixed(2);
    }

    function startGame() {
      if (isRunning) return;

      bet = parseFloat(document.getElementById('betAmount').value);
      crashAt = parseFloat(document.getElementById('crashPoint').value);

      if (bet > balance || bet <= 0) {
        alert("Invalid bet amount");
        return;
      }

      balance -= bet;
      multiplier = 1.00;
      hasCashedOut = false;
      isRunning = true;

      updateUI();
      document.getElementById('status').textContent = 'Game running...';
      document.getElementById('status').style.color = '#0f0';

      clearInterval(interval);
      interval = setInterval(() => {
        multiplier += 0.01;
        updateUI();
        if (multiplier >= crashAt) crash();
      }, 50);
    }

    function cashOut() {
      if (!isRunning || hasCashedOut) return;

      clearInterval(interval);
      const winnings = bet * multiplier;
      balance += winnings;
      hasCashedOut = true;
      isRunning = false;
      document.getElementById('status').textContent = `Cashed out at ${multiplier.toFixed(2)}x! Won $${winnings.toFixed(2)}.`;
      document.getElementById('status').style.color = '#ff0';
      updateUI();
    }

    function crash() {
      clearInterval(interval);
      isRunning = false;
      if (!hasCashedOut) {
        document.getElementById('status').textContent = `Crashed at ${multiplier.toFixed(2)}x. You lost $${bet.toFixed(2)}.`;
        document.getElementById('status').style.color = '#f00';
      }
      updateUI();
    }

    updateUI();
  </script>
</body>
</html>

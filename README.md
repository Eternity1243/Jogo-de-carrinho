<!DOCTYPE html><html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Jogo de Corrida</title>
  <style>
    body { margin: 0; overflow: hidden; background: #222; }
    canvas { display: block; margin: 0 auto; background: #444; }
  </style>
</head>
<body>
<canvas id="gameCanvas" width="800" height="400"></canvas>
<script>
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');// Jogador const car = { x: 100, y: 300, width: 50, height: 30, color: 'red', speed: 0, accel: 0.2, maxSpeed: 5, friction: 0.05, turboActive: false, turboTime: 0 };

// Obstáculos e adversários const obstacles = []; const enemies = []; const powerUps = []; let frameCount = 0; let score = 0; let level = 1; let laps = 0; const maxLaps = 3;

function drawCar() { ctx.fillStyle = car.color; ctx.fillRect(car.x, car.y, car.width, car.height); }

function drawObstacles() { ctx.fillStyle = 'yellow'; for (let obs of obstacles) { ctx.fillRect(obs.x, obs.y, obs.width, obs.height); } }

function drawEnemies() { ctx.fillStyle = 'blue'; for (let enemy of enemies) { ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height); } }

function drawPowerUps() { ctx.fillStyle = 'lime'; for (let power of powerUps) { ctx.beginPath(); ctx.arc(power.x + 10, power.y + 10, 10, 0, 2 * Math.PI); ctx.fill(); } }

function updateCar() { if (keys.ArrowRight) car.speed += car.accel; if (keys.ArrowLeft) car.speed -= car.accel;

if (car.turboActive) {
  car.maxSpeed = 10;
  car.turboTime--;
  if (car.turboTime <= 0) {
    car.turboActive = false;
    car.maxSpeed = 5;
  }
}

car.speed = Math.max(Math.min(car.speed, car.maxSpeed), -car.maxSpeed);
car.speed *= (1 - car.friction);
car.x += car.speed;

if (car.x >= canvas.width - car.width) {
  car.x = 0;
  laps++;
  if (laps >= maxLaps) {
    alert('🏁 Corrida Finalizada! Pontuação: ' + score);
    document.location.reload();
  } else {
    level++;
  }
}

car.x = Math.max(0, Math.min(canvas.width - car.width, car.x));

}

function updateObstacles() { for (let obs of obstacles) { obs.x -= 4 + level; } while (obstacles.length > 0 && obstacles[0].x + obstacles[0].width < 0) { obstacles.shift(); score++; } if (frameCount % 100 === 0) { const height = 30 + Math.random() * 50; obstacles.push({ x: canvas.width, y: 300, width: 30, height: height }); } }

function updateEnemies() { for (let enemy of enemies) { enemy.x -= enemy.speed + level; if (frameCount % 60 === 0) { enemy.y += (Math.random() - 0.5) * 20; enemy.y = Math.max(0, Math.min(canvas.height - enemy.height, enemy.y)); } } while (enemies.length > 0 && enemies[0].x + enemies[0].width < 0) { enemies.shift(); } if (frameCount % 200 === 0) { enemies.push({ x: canvas.width, y: Math.random() * (canvas.height - 30), width: 50, height: 30, speed: 2 + Math.random() * 2 }); } }

function updatePowerUps() { for (let power of powerUps) { power.x -= 3; } while (powerUps.length > 0 && powerUps[0].x + 20 < 0) { powerUps.shift(); } if (frameCount % 500 === 0) { powerUps.push({ x: canvas.width, y: Math.random() * (canvas.height - 20), width: 20, height: 20 }); } }

function detectCollision() { for (let obs of obstacles) { if ( car.x < obs.x + obs.width && car.x + car.width > obs.x && car.y < obs.y + obs.height && car.y + car.height > obs.y ) { alert('Game Over! Pontuação: ' + score); document.location.reload(); } } for (let enemy of enemies) { if ( car.x < enemy.x + enemy.width && car.x + car.width > enemy.x && car.y < enemy.y + enemy.height && car.y + car.height > enemy.y ) { alert('Game Over! Pontuação: ' + score); document.location.reload(); } } for (let i = powerUps.length - 1; i >= 0; i--) { let power = powerUps[i]; if ( car.x < power.x + power.width && car.x + car.width > power.x && car.y < power.y + power.height && car.y + car.height > power.y ) { car.turboActive = true; car.turboTime = 200; powerUps.splice(i, 1); } } }

function drawScore() { ctx.fillStyle = 'white'; ctx.font = '18px Arial'; ctx.fillText('Pontuação: ' + score, 10, 25); ctx.fillText('Voltas: ' + laps + '/' + maxLaps, 10, 50); ctx.fillText('Nível: ' + level, 10, 75); if (car.turboActive) { ctx.fillText('TURBO ATIVO!', 10, 100); } }

const keys = {}; window.addEventListener('keydown', (e) => keys[e.key] = true); window.addEventListener('keyup', (e) => keys[e.key] = false);

function gameLoop() { ctx.clearRect(0, 0, canvas.width, canvas.height); drawCar(); updateCar();

drawObstacles();
updateObstacles();

drawEnemies();
updateEnemies();

drawPowerUps();
updatePowerUps();

detectCollision();
drawScore();

frameCount++;
requestAnimationFrame(gameLoop);

}

gameLoop(); </script>

</body>
</html>

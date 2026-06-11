# game.html
<!DOCTYPE html>
<html>
<head>
    <title>Shooting Game</title>
    <style>
        body {
            margin: 0;
            background: #222;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            font-family: Arial, sans-serif;
            overflow: hidden;
        }
        #gameArea {
            width: 800px;
            height: 500px;
            background: #333;
            border: 4px solid #0f0;
            position: relative;
            overflow: hidden;
        }
        #player {
            width: 40px;
            height: 40px;
            background: #0f0;
            position: absolute;
            bottom: 10px;
            left: 380px;
        }
        .enemy {
            width: 40px;
            height: 40px;
            background: #f00;
            position: absolute;
            top: 0;
        }
        .bullet {
            width: 10px;
            height: 20px;
            background: #ff0;
            position: absolute;
        }
        #score {
            position: absolute;
            top: 10px;
            left: 10px;
            color: white;
            font-size: 20px;
        }
        #gameOver {
            display: none;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: white;
            text-align: center;
        }
        button {
            padding: 10px 20px;
            font-size: 20px;
            cursor: pointer;
            background: #0f0;
            border: none;
        }
    </style>
</head>
<body>

<div id="gameArea">
    <div id="score">Score: 0</div>
    <div id="player"></div>
    <div id="gameOver">
        <h1>GAME OVER</h1>
        <p id="finalScore">Score: 0</p>
        <button onclick="restartGame()">Play Again</button>
    </div>
</div>

<script>
    const gameArea = document.getElementById('gameArea');
    const player = document.getElementById('player');
    const scoreDisplay = document.getElementById('score');
    const gameOverScreen = document.getElementById('gameOver');
    const finalScoreDisplay = document.getElementById('finalScore');

    let playerX = 380;
    let score = 0;
    let gameRunning = true;
    let bullets = [];
    let enemies = [];
    let enemySpeed = 2;

    document.addEventListener('keydown', (e) => {
        if (e.key === 'ArrowLeft' && playerX > 0) playerX -= 20;
        if (e.key === 'ArrowRight' && playerX < 760) playerX += 20;
        if (e.key === ' ') shoot();
        player.style.left = playerX + 'px';
    });

    function shoot() {
        if (!gameRunning) return;
        const bullet = document.createElement('div');
        bullet.className = 'bullet';
        bullet.style.left = (playerX + 15) + 'px';
        bullet.style.top = '460px';
        gameArea.appendChild(bullet);
        bullets.push({ element: bullet, y: 460 });
    }

    function createEnemy() {
        if (!gameRunning) return;
        const enemy = document.createElement('div');
        enemy.className = 'enemy';
        enemy.style.left = Math.floor(Math.random() * 760) + 'px';
        gameArea.appendChild(enemy);
        enemies.push({ element: enemy, y: 0 });
    }

    function gameLoop() {
        if (!gameRunning) return;

        // Move bullets
        bullets.forEach((bullet, index) => {
            bullet.y -= 10;
            bullet.element.style.top = bullet.y + 'px';
            if (bullet.y < 0) {
                bullet.element.remove();
                bullets.splice(index, 1);
            }
        });

        // Move enemies
        enemies.forEach((enemy, index) => {
            enemy.y += enemySpeed;
            enemy.element.style.top = enemy.y + 'px';

            // Check collision with bullets
            bullets.forEach((bullet, bIndex) => {
                if (Math.abs(bullet.y - enemy.y) < 40 && 
                    Math.abs(parseInt(bullet.element.style.left) - parseInt(enemy.element.style.left)) < 40) {
                    enemy.element.remove();
                    bullet.element.remove();
                    enemies.splice(index, 1);
                    bullets.splice(bIndex, 1);
                    score += 10;
                    scoreDisplay.innerText = 'Score: ' + score;
                    enemySpeed += 0.1;
                }
            });

            // Game over if enemy reaches bottom
            if (enemy.y > 460) {
                gameOver();
            }
        });

        requestAnimationFrame(gameLoop);
    }

    function gameOver() {
        gameRunning = false;
        gameOverScreen.style.display = 'block';
        finalScoreDisplay.innerText = 'Score: ' + score;
    }

    function restartGame() {
        location.reload();
    }

    setInterval(createEnemy, 1500);
    gameLoop();
</script>

</body>
</html>

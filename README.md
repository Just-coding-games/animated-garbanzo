# animated-garbanzo
ping pong
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ping Pong Game</title>
    <style>
        body {
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background-color: #1a1a1a;
            font-family: Arial, sans-serif;
        }

        #gameContainer {
            text-align: center;
        }

        canvas {
            background-color: #000;
            border: 2px solid #fff;
            display: block;
            margin: 0 auto;
        }

        #score {
            color: #fff;
            font-size: 24px;
            margin-bottom: 20px;
        }

        #instructions {
            color: #aaa;
            font-size: 14px;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <div id="score">Player 1: 0 | Player 2: 0</div>
        <canvas id="pongCanvas" width="800" height="400"></canvas>
        <div id="instructions">
            <p>Player 1 (Left): W / S keys | Player 2 (Right): Arrow Up / Arrow Down</p>
            <p>Press SPACE to start/reset</p>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('pongCanvas');
        const ctx = canvas.getContext('2d');
        const scoreDisplay = document.getElementById('score');

        // Game Objects
        const paddleWidth = 10;
        const paddleHeight = 100;
        const ballSize = 5;
        const paddleSpeed = 5;
        const ballSpeed = 4;

        let player1 = {
            x: 10,
            y: canvas.height / 2 - paddleHeight / 2,
            width: paddleWidth,
            height: paddleHeight,
            dy: 0,
            score: 0
        };

        let player2 = {
            x: canvas.width - paddleWidth - 10,
            y: canvas.height / 2 - paddleHeight / 2,
            width: paddleWidth,
            height: paddleHeight,
            dy: 0,
            score: 0
        };

        let ball = {
            x: canvas.width / 2,
            y: canvas.height / 2,
            size: ballSize,
            dx: ballSpeed,
            dy: ballSpeed
        };

        let gameRunning = false;
        const keys = {};

        // Event Listeners
        window.addEventListener('keydown', (e) => {
            keys[e.key] = true;
            if (e.key === ' ') {
                e.preventDefault();
                gameRunning = !gameRunning;
            }
        });

        window.addEventListener('keyup', (e) => {
            keys[e.key] = false;
        });

        // Update game state
        function update() {
            if (!gameRunning) return;

            // Player 1 movement (W/S)
            if (keys['w'] || keys['W']) {
                player1.y = Math.max(0, player1.y - paddleSpeed);
            }
            if (keys['s'] || keys['S']) {
                player1.y = Math.min(canvas.height - player1.height, player1.y + paddleSpeed);
            }

            // Player 2 movement (Arrow keys)
            if (keys['ArrowUp']) {
                player2.y = Math.max(0, player2.y - paddleSpeed);
            }
            if (keys['ArrowDown']) {
                player2.y = Math.min(canvas.height - player2.height, player2.y + paddleSpeed);
            }

            // Ball movement
            ball.x += ball.dx;
            ball.y += ball.dy;

            // Ball collision with top/bottom
            if (ball.y - ball.size <= 0 || ball.y + ball.size >= canvas.height) {
                ball.dy = -ball.dy;
            }

            // Ball collision with paddles
            if (
                ball.x - ball.size <= player1.x + player1.width &&
                ball.y >= player1.y &&
                ball.y <= player1.y + player1.height
            ) {
                ball.dx = -ball.dx;
                ball.x = player1.x + player1.width + ball.size;
            }

            if (
                ball.x + ball.size >= player2.x &&
                ball.y >= player2.y &&
                ball.y <= player2.y + player2.height
            ) {
                ball.dx = -ball.dx;
                ball.x = player2.x - ball.size;
            }

            // Scoring
            if (ball.x - ball.size < 0) {
                player2.score++;
                resetBall();
            }
            if (ball.x + ball.size > canvas.width) {
                player1.score++;
                resetBall();
            }

            updateScore();
        }

        // Reset ball to center
        function resetBall() {
            ball.x = canvas.width / 2;
            ball.y = canvas.height / 2;
            ball.dx = (Math.random() > 0.5 ? 1 : -1) * ballSpeed;
            ball.dy = (Math.random() > 0.5 ? 1 : -1) * ballSpeed;
        }

        // Draw everything
        function draw() {
            // Clear canvas
            ctx.fillStyle = '#000';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw center line
            ctx.strokeStyle = '#fff';
            ctx.setLineDash([5, 5]);
            ctx.beginPath();
            ctx.moveTo(canvas.width / 2, 0);
            ctx.lineTo(canvas.width / 2, canvas.height);
            ctx.stroke();
            ctx.setLineDash([]);

            // Draw paddles
            ctx.fillStyle = '#fff';
            ctx.fillRect(player1.x, player1.y, player1.width, player1.height);
            ctx.fillRect(player2.x, player2.y, player2.width, player2.height);

            // Draw ball
            ctx.fillStyle = '#fff';
            ctx.fillRect(ball.x - ball.size, ball.y - ball.size, ball.size * 2, ball.size * 2);
        }

        // Update score display
        function updateScore() {
            scoreDisplay.textContent = `Player 1: ${player1.score} | Player 2: ${player2.score}`;
        }

        // Game loop
        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        // Start the game loop
        gameLoop();
    </script>
</body>
</html>

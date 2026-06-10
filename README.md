<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Retro Snake Game</title>
    <style>
        body {
            background-color: #1a1a2e;
            color: #ffffff;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            overflow: hidden;
        }

        h1 {
            margin-bottom: 10px;
            font-size: 2.5rem;
            color: #00fff5;
            text-shadow: 0 0 10px rgba(0, 255, 245, 0.5);
        }

        #score-board {
            font-size: 1.2rem;
            margin-bottom: 20px;
            color: #e2e8f0;
        }

        #score {
            font-weight: bold;
            color: #00fff5;
        }

        canvas {
            border: 4px solid #00fff5;
            background-color: #162447;
            box-shadow: 0 0 20px rgba(0, 255, 245, 0.3);
            border-radius: 8px;
        }

        .instructions {
            margin-top: 15px;
            font-size: 0.9rem;
            color: #818cf8;
        }
    </style>
</head>
<body>

    <h1>SNAKE GAME</h1>
    <div id="score-board">Điểm số: <span id="score">0</span></div>
    <canvas id="gameCanvas" width="400" height="400"></canvas>
    <div class="instructions">Sử dụng các phím mũi tên (↑, ↓, ←, →) để điều khiển</div>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        const scoreElement = document.getElementById("score");

        const gridSize = 20;
        const tileCount = canvas.width / gridSize;

        let snake = [{x: 10, y: 10}];
        let food = {x: 15, y: 7};
        let dx = 1;
        let dy = 0;
        let score = 0;
        let gameInterval;
        let gameSpeed = 100; // Tốc độ game (ms)

        function main() {
            if (hasGameEnded()) {
                alert("Game Over! Điểm của bạn là: " + score);
                resetGame();
                return;
            }

            changingDirection = false;
            clearCanvas();
            drawFood();
            moveSnake();
            drawSnake();
        }

        // Chạy vòng lặp game
        gameInterval = setInterval(main, gameSpeed);
        document.addEventListener("keydown", changeDirection);

        // Xóa màn hình sau mỗi khung hình
        function clearCanvas() {
            ctx.fillStyle = "#162447";
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        }

        // Vẽ rắn
        function drawSnake() {
            snake.forEach((part, index) => {
                // Đầu rắn màu khác thân
                ctx.fillStyle = index === 0 ? '#00fff5' : '#00adb5';
                ctx.strokeStyle = '#162447';
                ctx.fillRect(part.x * gridSize, part.y * gridSize, gridSize, gridSize);
                ctx.strokeRect(part.x * gridSize, part.y * gridSize, gridSize, gridSize);
            });
        }

        // Di chuyển rắn
        function moveSnake() {
            const head = {x: snake[0].x + dx, y: snake[0].y + dy};
            snake.unshift(head);

            // Kiểm tra xem rắn có ăn mồi không
            if (snake[0].x === food.x && snake[0].y === food.y) {
                score += 10;
                scoreElement.innerText = score;
                generateFood();
            } else {
                snake.pop();
            }
        }

        // Tạo mồi ngẫu nhiên
        function generateFood() {
            food.x = Math.floor(Math.random() * tileCount);
            food.y = Math.floor(Math.random() * tileCount);
            
            // Đảm bảo mồi không sinh ra trùng trên người rắn
            snake.forEach(part => {
                if (part.x === food.x && part.y === food.y) generateFood();
            });
        }

        // Vẽ mồi
        function drawFood() {
            ctx.fillStyle = '#ff2e63';
            ctx.fillRect(food.x * gridSize, food.y * gridSize, gridSize, gridSize);
        }

        // Điều khiển hướng đi
        function changeDirection(event) {
            const LEFT_KEY = 37;
            const UP_KEY = 38;
            const RIGHT_KEY = 39;
            const DOWN_KEY = 40;

            const keyPressed = event.keyCode;
            const goingUp = dy === -1;
            const goingDown = dy === 1;
            const goingRight = dx === 1;
            const goingLeft = dx === -1;

            if (keyPressed === LEFT_KEY && !goingRight) { dx = -1; dy = 0; }
            if (keyPressed === UP_KEY && !goingDown) { dx = 0; dy = -1; }
            if (keyPressed === RIGHT_KEY && !goingLeft) { dx = 1; dy = 0; }
            if (keyPressed === DOWN_KEY && !goingUp) { dx = 0; dy = 1; }
        }

        // Kiểm tra va chạm để kết thúc game
        function hasGameEnded() {
            // Va chạm tường
            if (snake[0].x < 0 || snake[0].x >= tileCount || snake[0].y < 0 || snake[0].y >= tileCount) {
                return true;
            }
            // Va chạm chính mình
            for (let i = 4; i < snake.length; i++) {
                if (snake[i].x === snake[0].x && snake[i].y === snake[0].y) return true;
            }
            return false;
        }

        // Chơi lại từ đầu
        function resetGame() {
            snake = [{x: 10, y: 10}];
            food = {x: 15, y: 7};
            dx = 1;
            dy = 0;
            score = 0;
            scoreElement.innerText = score;
        }
    </script>
</body>
</html>

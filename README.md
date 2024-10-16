<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pong Game with Text</title>
    <style>
        body {
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: black;
        }
        canvas {
            border: 2px solid white;
        }
    </style>
</head>
<body>
    <canvas id="pong" width="600" height="400"></canvas>
    
    <script>
        const canvas = document.getElementById("pong");
        const context = canvas.getContext("2d");

        // Create the paddles and text ball
        const user = {
            x: 0,
            y: (canvas.height - 100) / 2,
            width: 10,
            height: 100,
            color: "WHITE",
            score: 0
        };
        
        const com = {
            x: canvas.width - 10,
            y: (canvas.height - 100) / 2,
            width: 10,
            height: 100,
            color: "WHITE",
            score: 0
        };
        
        const ball = {
            x: canvas.width / 2,
            y: canvas.height / 2,
            text: "ALR",
            speed: 5,
            velocityX: 5,
            velocityY: 5,
            color: "WHITE"
        };

        // Draw a rectangle, will be used to draw paddles
        function drawRect(x, y, w, h, color) {
            context.fillStyle = color;
            context.fillRect(x, y, w, h);
        }

        // Draw text as the "ball"
        function drawTextBall(x, y, text, color) {
            context.fillStyle = color;
            context.font = "30px Arial";
            context.fillText(text, x, y);
        }

        // Draw a simple text, will be used to draw the score
        function drawText(text, x, y, color) {
            context.fillStyle = color;
            context.font = "35px Arial";
            context.fillText(text, x, y);
        }

        // Render the game
        function render() {
            // Clear the canvas
            drawRect(0, 0, canvas.width, canvas.height, "BLACK");
            
            // Draw the net
            drawRect(canvas.width / 2 - 1, 0, 2, canvas.height, "WHITE");

            // Draw the score
            drawText(user.score, canvas.width / 4, canvas.height / 5, "WHITE");
            drawText(com.score, 3 * canvas.width / 4, canvas.height / 5, "WHITE");

            // Draw the paddles
            drawRect(user.x, user.y, user.width, user.height, user.color);
            drawRect(com.x, com.y, com.width, com.height, com.color);

            // Draw the flying text as the ball
            drawTextBall(ball.x, ball.y, ball.text, ball.color);
        }

        // Move the paddles
        canvas.addEventListener("mousemove", movePaddle);

        function movePaddle(evt) {
            let rect = canvas.getBoundingClientRect();
            user.y = evt.clientY - rect.top - user.height / 2;
        }

        // Collision detection
        function collision(b, p) {
            p.top = p.y;
            p.bottom = p.y + p.height;
            p.left = p.x;
            p.right = p.x + p.width;

            b.top = b.y - 15; // Adjusted for text height
            b.bottom = b.y + 15;
            b.left = b.x - 15; // Adjusted for text width
            b.right = b.x + 45;

            return p.left < b.right && p.top < b.bottom && p.right > b.left && p.bottom > b.top;
        }

        // Reset the text ball
        function resetBall() {
            ball.x = canvas.width / 2;
            ball.y = canvas.height / 2;
            ball.speed = 5;
            ball.velocityX = -ball.velocityX;
        }

        // Update: position, score, movement
        function update() {
            // Move the text ball
            ball.x += ball.velocityX;
            ball.y += ball.velocityY;

            // Simple AI to move the computer paddle
            com.y += ((ball.y - (com.y + com.height / 2))) * 0.1;

            // Text ball collision with top and bottom walls
            if (ball.y + 15 > canvas.height || ball.y - 15 < 0) {
                ball.velocityY = -ball.velocityY;
            }

            // Check if the ball hits the user or com paddle
            let player = (ball.x < canvas.width / 2) ? user : com;

            if (collision(ball, player)) {
                // Ball hits the paddle
                let collidePoint = (ball.y - (player.y + player.height / 2));
                collidePoint = collidePoint / (player.height / 2);

                let angleRad = (Math.PI / 4) * collidePoint;

                // X direction of the ball when it hits a paddle
                let direction = (ball.x < canvas.width / 2) ? 1 : -1;
                ball.velocityX = direction * ball.speed * Math.cos(angleRad);
                ball.velocityY = ball.speed * Math.sin(angleRad);

                // Increase ball speed
                ball.speed += 0.5;
            }

            // Update the score
            if (ball.x - 15 < 0) {
                com.score++;
                resetBall();
            } else if (ball.x + 45 > canvas.width) {
                user.score++;
                resetBall();
            }
        }

        // Game loop
        function game() {
            update();
            render();
        }

        // Loop the game
        const framePerSecond = 50;
        setInterval(game, 1000 / framePerSecond);
    </script>
</body>
</html>

# Slope Game

A simple slope game where you control a ball rolling down an endless slope, avoiding obstacles.

<canvas id="gameCanvas" width="800" height="400" style="border: 2px solid #333; background: linear-gradient(to bottom, #87CEEB, #98FB98);"></canvas>

<div style="margin-top: 10px;">
  <button id="startBtn">Start Game</button>
  <button id="pauseBtn">Pause</button>
  <span style="margin-left: 20px;">Score: <span id="score">0</span></span>
  <span style="margin-left: 20px;">Speed: <span id="speed">1</span></span>
</div>

<div style="margin-top: 10px; font-size: 14px;">
  <strong>Controls:</strong> Use A/D or Left/Right arrow keys to move
</div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const startBtn = document.getElementById('startBtn');
const pauseBtn = document.getElementById('pauseBtn');
const scoreElement = document.getElementById('score');
const speedElement = document.getElementById('speed');

let gameState = 'menu'; // menu, playing, paused, gameOver
let score = 0;
let speed = 1;
let gameLoop;

// Game objects
const ball = {
    x: 100,
    y: 200,
    radius: 15,
    velX: 0,
    velY: 0,
    color: '#FF6B6B'
};

const obstacles = [];
const platforms = [];

// Input handling
const keys = {
    left: false,
    right: false
};

document.addEventListener('keydown', (e) => {
    switch(e.code) {
        case 'KeyA':
        case 'ArrowLeft':
            keys.left = true;
            e.preventDefault();
            break;
        case 'KeyD':
        case 'ArrowRight':
            keys.right = true;
            e.preventDefault();
            break;
    }
});

document.addEventListener('keyup', (e) => {
    switch(e.code) {
        case 'KeyA':
        case 'ArrowLeft':
            keys.left = false;
            break;
        case 'KeyD':
        case 'ArrowRight':
            keys.right = false;
            break;
    }
});

// Platform generation
function generatePlatforms() {
    platforms.length = 0;
    for (let x = -200; x < canvas.width + 1000; x += 100) {
        platforms.push({
            x: x,
            y: canvas.height - 50 + Math.sin(x * 0.01) * 100,
            width: 120,
            height: 20
        });
    }
}

// Obstacle generation
function generateObstacles() {
    if (Math.random() < 0.02) {
        const platformIndex = Math.floor(Math.random() * platforms.length);
        const platform = platforms[platformIndex];

        if (platform.x > canvas.width / 2) {
            obstacles.push({
                x: platform.x + Math.random() * platform.width,
                y: platform.y - 30,
                width: 20,
                height: 30,
                color: '#FF4444'
            });
        }
    }
}

// Collision detection
function checkCollisions() {
    // Ball with platforms
    let onPlatform = false;
    for (const platform of platforms) {
        if (ball.x + ball.radius > platform.x &&
            ball.x - ball.radius < platform.x + platform.width &&
            ball.y + ball.radius > platform.y &&
            ball.y + ball.radius < platform.y + platform.height + 10) {

            ball.y = platform.y - ball.radius;
            ball.velY = 0;
            onPlatform = true;
            break;
        }
    }

    if (!onPlatform) {
        ball.velY += 0.8; // gravity
    }

    // Ball with obstacles
    for (const obstacle of obstacles) {
        if (ball.x + ball.radius > obstacle.x &&
            ball.x - ball.radius < obstacle.x + obstacle.width &&
            ball.y + ball.radius > obstacle.y &&
            ball.y - ball.radius < obstacle.y + obstacle.height) {

            gameState = 'gameOver';
            return;
        }
    }

    // Ball falls off screen
    if (ball.y > canvas.height + 100) {
        gameState = 'gameOver';
    }
}

// Update game logic
function update() {
    if (gameState !== 'playing') return;

    // Ball movement
    if (keys.left) ball.velX -= 0.5;
    if (keys.right) ball.velX += 0.5;

    ball.velX *= 0.9; // friction
    ball.velX = Math.max(-15, Math.min(15, ball.velX)); // limit speed

    ball.x += ball.velX;
    ball.y += ball.velY;

    // Move world left (slope effect)
    const worldSpeed = speed * 2;
    for (const platform of platforms) {
        platform.x -= worldSpeed;
    }
    for (const obstacle of obstacles) {
        obstacle.x -= worldSpeed;
    }

    // Generate new platforms on the right
    const rightmostPlatform = platforms[platforms.length - 1];
    if (rightmostPlatform && rightmostPlatform.x < canvas.width + 500) {
        for (let x = rightmostPlatform.x + 100; x < canvas.width + 1000; x += 100) {
            platforms.push({
                x: x,
                y: canvas.height - 50 + Math.sin(x * 0.01) * 100,
                width: 120,
                height: 20
            });
        }
    }

    // Remove old platforms and obstacles
    platforms.splice(0, platforms.filter(p => p.x < -200).length);
    obstacles.splice(0, obstacles.filter(o => o.x < -50).length);

    generateObstacles();
    checkCollisions();

    // Update score and speed
    score += Math.floor(speed);
    speed += 0.001;

    scoreElement.textContent = Math.floor(score);
    speedElement.textContent = speed.toFixed(1);
}

// Render game
function render() {
    // Clear canvas with gradient background
    const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
    gradient.addColorStop(0, '#87CEEB');
    gradient.addColorStop(1, '#98FB98');
    ctx.fillStyle = gradient;
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    if (gameState === 'menu') {
        ctx.fillStyle = '#333';
        ctx.font = '48px Arial';
        ctx.textAlign = 'center';
        ctx.fillText('SLOPE GAME', canvas.width/2, canvas.height/2 - 50);
        ctx.font = '24px Arial';
        ctx.fillText('Click Start to begin!', canvas.width/2, canvas.height/2 + 20);
        return;
    }

    if (gameState === 'gameOver') {
        ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = '#FF6B6B';
        ctx.font = '48px Arial';
        ctx.textAlign = 'center';
        ctx.fillText('GAME OVER', canvas.width/2, canvas.height/2 - 50);
        ctx.fillStyle = '#FFF';
        ctx.font = '24px Arial';
        ctx.fillText(`Final Score: ${Math.floor(score)}`, canvas.width/2, canvas.height/2 + 20);
        ctx.fillText('Click Start to play again', canvas.width/2, canvas.height/2 + 60);
        return;
    }

    // Draw platforms
    ctx.fillStyle = '#8B4513';
    for (const platform of platforms) {
        ctx.fillRect(platform.x, platform.y, platform.width, platform.height);

        // Platform edge highlight
        ctx.fillStyle = '#A0522D';
        ctx.fillRect(platform.x, platform.y, platform.width, 5);
        ctx.fillStyle = '#8B4513';
    }

    // Draw obstacles
    for (const obstacle of obstacles) {
        ctx.fillStyle = obstacle.color;
        ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, obstacle.height);

        // Obstacle highlight
        ctx.fillStyle = '#FF6666';
        ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, 5);
    }

    // Draw ball
    ctx.fillStyle = ball.color;
    ctx.beginPath();
    ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
    ctx.fill();

    // Ball highlight
    ctx.fillStyle = '#FF8E8E';
    ctx.beginPath();
    ctx.arc(ball.x - 5, ball.y - 5, ball.radius * 0.3, 0, Math.PI * 2);
    ctx.fill();

    // Game UI
    if (gameState === 'paused') {
        ctx.fillStyle = 'rgba(0, 0, 0, 0.5)';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
        ctx.fillStyle = '#FFF';
        ctx.font = '36px Arial';
        ctx.textAlign = 'center';
        ctx.fillText('PAUSED', canvas.width/2, canvas.height/2);
    }
}

// Game loop
function gameLoopFunction() {
    update();
    render();
    requestAnimationFrame(gameLoopFunction);
}

// Start game
function startGame() {
    if (gameState === 'gameOver' || gameState === 'menu') {
        // Reset game
        ball.x = 100;
        ball.y = 200;
        ball.velX = 0;
        ball.velY = 0;
        score = 0;
        speed = 1;
        obstacles.length = 0;
        generatePlatforms();
    }

    gameState = 'playing';
    startBtn.textContent = 'Restart';
}

function pauseGame() {
    if (gameState === 'playing') {
        gameState = 'paused';
        pauseBtn.textContent = 'Resume';
    } else if (gameState === 'paused') {
        gameState = 'playing';
        pauseBtn.textContent = 'Pause';
    }
}

// Event listeners
startBtn.addEventListener('click', startGame);
pauseBtn.addEventListener('click', pauseGame);

// Initialize
generatePlatforms();
gameLoopFunction();
</script>
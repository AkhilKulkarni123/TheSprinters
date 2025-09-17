---
title: Snake Game
description: Learn to edit code in snake game
comments: false
layout: post
permalink: /snakelesson67
---

# JavaScript Snake Game Development Lesson

## Overview
This lesson explores building a classic Snake game using HTML5 Canvas and JavaScript. We'll break down the code structure, game mechanics, and key programming concepts used in this implementation.

## Table of Contents
1. [Game Setup and Canvas](#game-setup-and-canvas)
2. [Game State Management](#game-state-management)
3. [Snake Data Structure](#snake-data-structure)
4. [Game Loop Architecture](#game-loop-architecture)
5. [Collision Detection](#collision-detection)
6. [Input Handling](#input-handling)
7. [Image and Asset Management](#image-and-asset-management)
8. [Local Storage for High Scores](#local-storage-for-high-scores)

---

## Game Setup and Canvas

### Canvas Initialization
```javascript
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const gridSize = 40;
const tileCount = canvas.width / gridSize;
```

**Key Concepts:**
- **HTML5 Canvas**: Provides a drawing surface for graphics
- **2D Context**: Allows us to draw shapes, images, and text
- **Grid System**: The game uses a 40x40 pixel grid for movement

**Why This Matters:**
The grid system simplifies collision detection and ensures smooth, predictable movement. Instead of pixel-by-pixel movement, the snake moves in discrete grid steps.

---

## Game State Management

### Core Game Variables
```javascript
let snake = [{x: 10, y: 10}];
let food = {};
let dx = 0, dy = 0;  // Direction vectors
let score = 0;
let gameRunning = true;
let gamePaused = false;
```

**State Management Pattern:**
- **Snake Array**: Each segment is an object with x,y coordinates
- **Direction Vectors**: `dx` and `dy` represent horizontal and vertical movement
- **Boolean Flags**: Control game flow (running, paused)

### State Transitions
```javascript
function gameOver() {
    gameRunning = false;
    finalScoreElement.textContent = score;

    if (score > highScore) {
        highScore = score;
        localStorage.setItem('snakeHighScore', highScore.toString());
    }

    gameOverElement.style.display = 'block';
}
```

---

## Snake Data Structure

### Snake Representation
The snake is represented as an array of coordinate objects:

```javascript
// Initial snake with one segment
let snake = [{x: 10, y: 10}];

// After eating food, new head is added
const head = {x: snake[0].x + dx, y: snake[0].y + dy};
snake.unshift(head);  // Add new head
// Tail removal handled conditionally
```

**Key Operations:**
- **`unshift()`**: Adds new head to front of array
- **`pop()`**: Removes tail from end of array (when no food eaten)
- **Array indexing**: `snake[0]` is always the head

### Growing the Snake
```javascript
// Check food collision
if (head.x === food.x && head.y === food.y) {
    score += 10;
    randomFood();
    // Don't remove tail (snake grows)
} else {
    snake.pop();  // Remove tail
}
```

---

## Game Loop Architecture

### The Main Game Loop
```javascript
function gameLoop() {
    moveSnake();
    drawGame();
}

// Run at 10 FPS (100ms intervals)
setInterval(gameLoop, 100);
```

**Game Loop Components:**
1. **Update Logic**: `moveSnake()` - Updates game state
2. **Render Logic**: `drawGame()` - Draws current state
3. **Timing**: `setInterval()` provides consistent frame rate

### Movement Logic
```javascript
function moveSnake() {
    if (!gameRunning || gamePaused) return;
    if (dx === 0 && dy === 0) return;  // No movement until input

    const head = {x: snake[0].x + dx, y: snake[0].y + dy};

    // Collision checks here...

    snake.unshift(head);
    // Handle growth/tail removal...
}
```

---

## Collision Detection

### Wall Collision
```javascript
const maxTileX = Math.floor(canvas.width / gridSize);
const maxTileY = Math.floor(canvas.height / gridSize);

if (head.x < 0 || head.x >= maxTileX || head.y < 0 || head.y >= maxTileY) {
    gameOver();
    return;
}
```

### Self Collision
```javascript
for (let segment of snake) {
    if (head.x === segment.x && head.y === segment.y) {
        gameOver();
        return;
    }
}
```

### Food Collision
```javascript
if (head.x === food.x && head.y === food.y) {
    score += 10;
    randomFood();
}
```

**Collision Detection Pattern:**
- Check boundaries first (most common collision)
- Use coordinate comparison for object collision
- Handle collision consequences immediately

---

## Input Handling

### Keyboard Event Listener
```javascript
document.addEventListener('keydown', (e) => {
    if (!gameRunning) return;

    const key = e.key.toLowerCase();

    // Pause functionality
    if (key === ' ') {
        e.preventDefault();
        gamePaused = !gamePaused;
        return;
    }

    if (gamePaused) return;

    // Movement controls...
});
```

### Direction Change Logic
```javascript
switch(key) {
    case 'arrowup':
    case 'w':
        if (dy !== 1) {  // Prevent reverse direction
            dx = 0;
            dy = -1;
        }
        break;
    // Additional cases...
}
```

**Important Features:**
- **Reverse Prevention**: Can't move directly backwards
- **Multiple Key Support**: Both WASD and arrow keys
- **State Checking**: Only respond to input when appropriate

---

## Image and Asset Management

### Preloading Images
```javascript
const snakeImage = new Image();
snakeImage.src = 'images/Greensquare.png';

const foodImages = [
    new Image(),
    new Image()
];
foodImages[0].src = 'images/food1.png';
foodImages[1].src = 'images/food2.png';
```

### Image Drawing with Fallbacks
```javascript
function drawGame() {
    // Draw snake segments
    for (let i = 0; i < snake.length; i++) {
        const segment = snake[i];

        if (snakeImage.complete && snakeImage.naturalWidth > 0) {
            ctx.drawImage(snakeImage,
                segment.x * gridSize,
                segment.y * gridSize,
                gridSize - 4,
                gridSize - 4);
        } else {
            // Fallback to colored rectangles
            ctx.fillStyle = i === 0 ? '#2ecc71' : '#27ae60';
            ctx.fillRect(segment.x * gridSize, segment.y * gridSize, gridSize - 4, gridSize - 4);
        }
    }
}
```

**Best Practices:**
- **Preload assets** before game starts
- **Check image loading** with `complete` and `naturalWidth`
- **Provide fallbacks** for failed image loads
- **Use consistent sizing** with `gridSize - 4` for visual spacing

---

## Local Storage for High Scores

### Reading from Local Storage
```javascript
let highScore = parseInt(localStorage.getItem('snakeHighScore')) || 0;
```

### Saving to Local Storage
```javascript
if (score > highScore) {
    highScore = score;
    localStorage.setItem('snakeHighScore', highScore.toString());
}
```

**Local Storage Pattern:**
- **Parse integer values** when reading
- **Provide default values** with `|| 0`
- **Convert to string** when saving
- **Update immediately** when new high score achieved

---

## Food Generation Algorithm

### Random Positioning
```javascript
function randomFood() {
    do {
        food = {
            x: Math.floor(Math.random() * Math.floor(canvas.width / gridSize)),
            y: Math.floor(Math.random() * Math.floor(canvas.height / gridSize))
        };
    } while (snake.some(segment => segment.x === food.x && segment.y === food.y));

    // Cycle food images
    currentFoodIndex = (currentFoodIndex + 1) % foodImages.length;
}
```

**Algorithm Features:**
- **Boundary-safe random generation**
- **Collision avoidance** with snake body
- **Do-while loop** ensures valid placement
- **Image cycling** for visual variety

---

## Key Programming Concepts Demonstrated

### 1. **Object-Oriented Thinking**
- Game state encapsulated in objects and arrays
- Clear separation of concerns (movement, drawing, collision)

### 2. **Event-Driven Programming**
- Keyboard events drive game interaction
- State changes trigger UI updates

### 3. **Canvas Graphics Programming**
- Direct pixel manipulation
- Image rendering and fallback strategies
- Coordinate system management

### 4. **Game Development Patterns**
- Game loop architecture
- State management
- Asset loading and management

### 5. **Error Handling and Robustness**
- Image loading fallbacks
- Input validation
- Boundary checking

---

## Exercises for Further Learning

### Beginner
1. **Modify Snake Speed**: Change the `setInterval` timing to make the game faster or slower
2. **Change Colors**: Modify the fallback colors for snake segments
3. **Add Sound**: Implement audio feedback for eating food or game over

### Intermediate
1. **Power-ups**: Add special food items that provide temporary abilities
2. **Obstacles**: Add static obstacles to the game field
3. **Multiple Lives**: Implement a lives system before game over

### Advanced
1. **AI Snake**: Create a computer-controlled snake using pathfinding
2. **Multiplayer**: Implement a two-player version
3. **Level System**: Create progressive difficulty with different level layouts

---

## Conclusion

This Snake game demonstrates fundamental game development concepts including:
- **Real-time game loops**
- **Collision detection algorithms**
- **State management patterns**
- **Input handling systems**
- **Graphics rendering techniques**

The code serves as an excellent foundation for understanding how classic arcade games work and can be extended with additional features and complexity.
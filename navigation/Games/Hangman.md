---
layout: post
title: Hangman Game
permalink: /hangman/
---


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hangman Game</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .game-container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 40px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(255, 255, 255, 0.2);
            max-width: 900px;
            width: 100%;
            color: white;
        }

        h1 {
            text-align: center;
            font-size: 2.5em;
            margin-bottom: 30px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        .game-area {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 40px;
            margin-bottom: 30px;
        }

        @media (max-width: 768px) {
            .game-area {
                grid-template-columns: 1fr;
            }
        }

        .hangman-container {
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .hangman-svg {
            width: 100%;
            max-width: 280px;
            height: 280px;
        }

        .hangman-part {
            stroke: #fff;
            stroke-width: 3;
            fill: none;
            stroke-linecap: round;
            opacity: 0;
            transition: opacity 0.3s ease;
        }

        .hangman-part.visible {
            opacity: 1;
        }

        .game-info {
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

        .category {
            text-align: center;
            margin-bottom: 20px;
            font-size: 1.2em;
            opacity: 0.9;
            background: rgba(255, 255, 255, 0.2);
            padding: 10px;
            border-radius: 10px;
            border: 1px solid rgba(255, 255, 255, 0.3);
        }

        .word-display {
            font-size: 2.5em;
            letter-spacing: 10px;
            text-align: center;
            margin-bottom: 30px;
            font-weight: bold;
            min-height: 60px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        .wrong-letters {
            margin-bottom: 20px;
            text-align: center;
            background: rgba(231, 76, 60, 0.2);
            padding: 15px;
            border-radius: 10px;
            border: 1px solid rgba(231, 76, 60, 0.3);
        }

        .wrong-letters h3 {
            margin-bottom: 10px;
            opacity: 0.9;
        }

        .wrong-letters-display {
            font-size: 1.3em;
            letter-spacing: 5px;
            min-height: 30px;
            font-weight: bold;
        }

        .keyboard {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(45px, 1fr));
            gap: 8px;
            margin-bottom: 30px;
            max-width: 700px;
            margin: 0 auto 30px;
        }

        .key {
            padding: 12px;
            font-size: 1.2em;
            border: 2px solid rgba(255, 255, 255, 0.3);
            background: rgba(255, 255, 255, 0.2);
            color: white;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: bold;
        }

        .key:hover:not(.disabled) {
            background: rgba(255, 255, 255, 0.3);
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }

        .key.correct {
            background: rgba(46, 204, 113, 0.4);
            border-color: rgba(46, 204, 113, 0.6);
        }

        .key.wrong {
            background: rgba(231, 76, 60, 0.4);
            border-color: rgba(231, 76, 60, 0.6);
        }

        .key.disabled {
            cursor: not-allowed;
            opacity: 0.5;
        }

        .game-status {
            text-align: center;
            margin-bottom: 20px;
            font-size: 1.4em;
            min-height: 40px;
            font-weight: bold;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }

        .game-status.win {
            color: #2ecc71;
        }

        .game-status.lose {
            color: #e74c3c;
        }

        .controls {
            display: flex;
            justify-content: center;
            gap: 20px;
            flex-wrap: wrap;
        }

        button {
            padding: 12px 30px;
            font-size: 1.1em;
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: bold;
            color: white;
            background: rgba(255, 255, 255, 0.2);
        }

        button:hover:not(:disabled) {
            background: rgba(255, 255, 255, 0.3);
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
        }

        .hint-btn {
            background: rgba(243, 156, 18, 0.3);
            border-color: rgba(243, 156, 18, 0.5);
        }

        .hint-btn:hover:not(:disabled) {
            background: rgba(243, 156, 18, 0.4);
        }

        button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .stats {
            display: flex;
            justify-content: center;
            gap: 40px;
            margin-top: 30px;
            padding-top: 30px;
            border-top: 2px solid rgba(255, 255, 255, 0.2);
        }

        .stat {
            text-align: center;
            background: rgba(255, 255, 255, 0.1);
            padding: 15px 25px;
            border-radius: 15px;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .stat-label {
            opacity: 0.8;
            font-size: 0.9em;
            margin-bottom: 5px;
        }

        .stat-value {
            font-size: 1.5em;
            font-weight: bold;
        }

        @media (max-width: 600px) {
            .stats {
                flex-direction: column;
                gap: 15px;
            }
            
            .controls {
                flex-direction: column;
                width: 100%;
            }
            
            button {
                width: 100%;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>🎮 Hangman Game 🎮</h1>
        
        <div class="game-area">
            <div class="hangman-container">
                <svg class="hangman-svg" viewBox="0 0 250 300">
                    <!-- Gallows -->
                    <line x1="10" y1="290" x2="150" y2="290" stroke="rgba(255,255,255,0.5)" stroke-width="4"/>
                    <line x1="50" y1="290" x2="50" y2="20" stroke="rgba(255,255,255,0.5)" stroke-width="4"/>
                    <line x1="50" y1="20" x2="150" y2="20" stroke="rgba(255,255,255,0.5)" stroke-width="4"/>
                    <line x1="150" y1="20" x2="150" y2="50" stroke="rgba(255,255,255,0.5)" stroke-width="4"/>
                    
                    <!-- Head -->
                    <circle cx="150" cy="75" r="25" class="hangman-part" id="part-1"/>
                    
                    <!-- Body -->
                    <line x1="150" y1="100" x2="150" y2="180" class="hangman-part" id="part-2"/>
                    
                    <!-- Left arm -->
                    <line x1="150" y1="120" x2="120" y2="140" class="hangman-part" id="part-3"/>
                    
                    <!-- Right arm -->
                    <line x1="150" y1="120" x2="180" y2="140" class="hangman-part" id="part-4"/>
                    
                    <!-- Left leg -->
                    <line x1="150" y1="180" x2="120" y2="220" class="hangman-part" id="part-5"/>
                    
                    <!-- Right leg -->
                    <line x1="150" y1="180" x2="180" y2="220" class="hangman-part" id="part-6"/>
                </svg>
            </div>
            
            <div class="game-info">
                <div class="category" id="category">Category: Loading...</div>
                <div class="word-display" id="word-display"></div>
                <div class="wrong-letters">
                    <h3>Wrong Letters:</h3>
                    <div class="wrong-letters-display" id="wrong-letters"></div>
                </div>
            </div>
        </div>
        
        <div class="game-status" id="game-status"></div>
        
        <div class="keyboard" id="keyboard"></div>
        
        <div class="controls">
            <button onclick="initGame()">New Game</button>
            <button class="hint-btn" id="hint-btn" onclick="getHint()">Get Hint (3)</button>
        </div>
        
        <div class="stats">
            <div class="stat">
                <div class="stat-label">Wins</div>
                <div class="stat-value" id="wins">0</div>
            </div>
            <div class="stat">
                <div class="stat-label">Losses</div>
                <div class="stat-value" id="losses">0</div>
            </div>
            <div class="stat">
                <div class="stat-label">Win Rate</div>
                <div class="stat-value" id="win-rate">0%</div>
            </div>
        </div>
    </div>

    <script>
        // Word categories and words
        const categories = {
            'Animals': ['ELEPHANT', 'GIRAFFE', 'PENGUIN', 'DOLPHIN', 'BUTTERFLY', 'CROCODILE', 'FLAMINGO', 'KANGAROO', 'OCTOPUS', 'RHINOCEROS'],
            'Countries': ['BRAZIL', 'CANADA', 'EGYPT', 'FRANCE', 'GERMANY', 'INDIA', 'JAPAN', 'MEXICO', 'RUSSIA', 'SPAIN'],
            'Movies': ['AVATAR', 'TITANIC', 'INCEPTION', 'GLADIATOR', 'MATRIX', 'SHREK', 'FROZEN', 'JOKER', 'BATMAN', 'SUPERMAN'],
            'Sports': ['FOOTBALL', 'BASKETBALL', 'TENNIS', 'CRICKET', 'BASEBALL', 'VOLLEYBALL', 'SWIMMING', 'BOXING', 'CYCLING', 'GOLF'],
            'Food': ['PIZZA', 'BURGER', 'PASTA', 'SUSHI', 'TACOS', 'SANDWICH', 'CHOCOLATE', 'STRAWBERRY', 'WATERMELON', 'PINEAPPLE'],
            'Technology': ['COMPUTER', 'INTERNET', 'SOFTWARE', 'KEYBOARD', 'MONITOR', 'SMARTPHONE', 'TABLET', 'LAPTOP', 'BROWSER', 'DATABASE'],
            'Music': ['GUITAR', 'PIANO', 'DRUMS', 'VIOLIN', 'TRUMPET', 'SAXOPHONE', 'FLUTE', 'HARMONICA', 'ACCORDION', 'ORCHESTRA'],
            'Space': ['GALAXY', 'PLANET', 'ASTEROID', 'COMET', 'NEBULA', 'SATELLITE', 'ASTRONAUT', 'TELESCOPE', 'UNIVERSE', 'BLACKHOLE']
        };

        let currentWord = '';
        let currentCategory = '';
        let guessedLetters = [];
        let wrongLetters = [];
        let hintsRemaining = 3;
        let gameOver = false;
        let wins = 0;
        let losses = 0;

        // Load stats from localStorage
        function loadStats() {
            wins = parseInt(localStorage.getItem('hangmanWins') || '0');
            losses = parseInt(localStorage.getItem('hangmanLosses') || '0');
            updateStats();
        }

        // Save stats to localStorage
        function saveStats() {
            localStorage.setItem('hangmanWins', wins);
            localStorage.setItem('hangmanLosses', losses);
        }

        // Update stats display
        function updateStats() {
            document.getElementById('wins').textContent = wins;
            document.getElementById('losses').textContent = losses;
            const total = wins + losses;
            const winRate = total > 0 ? Math.round((wins / total) * 100) : 0;
            document.getElementById('win-rate').textContent = winRate + '%';
        }

        // Initialize the game
        function initGame() {
            // Reset variables
            guessedLetters = [];
            wrongLetters = [];
            hintsRemaining = 3;
            gameOver = false;
            
            // Select random category and word
            const categoryKeys = Object.keys(categories);
            currentCategory = categoryKeys[Math.floor(Math.random() * categoryKeys.length)];
            const words = categories[currentCategory];
            currentWord = words[Math.floor(Math.random() * words.length)];
            
            // Update UI
            document.getElementById('category').textContent = '📚 Category: ' + currentCategory;
            document.getElementById('wrong-letters').textContent = '';
            document.getElementById('game-status').textContent = '';
            document.getElementById('game-status').className = 'game-status';
            document.getElementById('hint-btn').textContent = `Get Hint (${hintsRemaining})`;
            document.getElementById('hint-btn').disabled = false;
            
            // Reset hangman
            document.querySelectorAll('.hangman-part').forEach(part => {
                part.classList.remove('visible');
            });
            
            // Create keyboard
            createKeyboard();
            
            // Update word display
            updateWordDisplay();
        }

        // Create virtual keyboard
        function createKeyboard() {
            const keyboard = document.getElementById('keyboard');
            keyboard.innerHTML = '';
            
            for (let i = 65; i <= 90; i++) {
                const letter = String.fromCharCode(i);
                const key = document.createElement('button');
                key.className = 'key';
                key.textContent = letter;
                key.onclick = () => guessLetter(letter);
                keyboard.appendChild(key);
            }
        }

        // Update word display
        function updateWordDisplay() {
            const display = currentWord.split('').map(letter => {
                return guessedLetters.includes(letter) ? letter : '_';
            }).join(' ');
            
            document.getElementById('word-display').textContent = display;
            
            // Check if word is complete
            if (!display.includes('_') && !gameOver) {
                endGame(true);
            }
        }

        // Guess a letter
        function guessLetter(letter) {
            if (gameOver || guessedLetters.includes(letter) || wrongLetters.includes(letter)) {
                return;
            }
            
            const keys = document.querySelectorAll('.key');
            const key = Array.from(keys).find(k => k.textContent === letter);
            
            if (currentWord.includes(letter)) {
                guessedLetters.push(letter);
                key.classList.add('correct', 'disabled');
                updateWordDisplay();
            } else {
                wrongLetters.push(letter);
                key.classList.add('wrong', 'disabled');
                document.getElementById('wrong-letters').textContent = wrongLetters.join(' ');
                showNextHangmanPart();
            }
        }

        // Show next hangman part
        function showNextHangmanPart() {
            const part = document.getElementById(`part-${wrongLetters.length}`);
            if (part) {
                part.classList.add('visible');
            }
            
            if (wrongLetters.length >= 6) {
                endGame(false);
            }
        }

        // Get hint
        function getHint() {
            if (gameOver || hintsRemaining <= 0) {
                return;
            }
            
            const unguessedLetters = currentWord.split('').filter(letter => 
                !guessedLetters.includes(letter)
            );
            
            if (unguessedLetters.length > 0) {
                const randomLetter = unguessedLetters[Math.floor(Math.random() * unguessedLetters.length)];
                guessLetter(randomLetter);
                hintsRemaining--;
                document.getElementById('hint-btn').textContent = `Get Hint (${hintsRemaining})`;
                
                if (hintsRemaining === 0) {
                    document.getElementById('hint-btn').disabled = true;
                }
            }
        }

        // End game
        function endGame(won) {
            gameOver = true;
            const status = document.getElementById('game-status');
            
            if (won) {
                status.textContent = '🎉 Congratulations! You won! 🎉';
                status.className = 'game-status win';
                wins++;
            } else {
                status.textContent = `😢 Game Over! The word was: ${currentWord}`;
                status.className = 'game-status lose';
                losses++;
            }
            
            // Disable all keys
            document.querySelectorAll('.key').forEach(key => {
                key.classList.add('disabled');
            });
            
            document.getElementById('hint-btn').disabled = true;
            
            saveStats();
            updateStats();
        }

        // Keyboard input support
        document.addEventListener('keydown', (e) => {
            if (gameOver) return;
            
            const letter = e.key.toUpperCase();
            if (letter.length === 1 && letter >= 'A' && letter <= 'Z') {
                guessLetter(letter);
            }
        });

        // Initialize game on load
        loadStats();
        initGame();
    </script>
</body>
</html>
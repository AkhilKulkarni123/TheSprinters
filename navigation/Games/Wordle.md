---
layout: post
title: Wordle Game
permalink: /wordle/
---


<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wordle Game</title>
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
            justify-content: center;
            align-items: center;
            color: white;
        }

        .game-container {
            text-align: center;
            max-width: 500px;
            width: 100%;
            padding: 20px;
        }

        .title {
            font-size: 3rem;
            font-weight: bold;
            margin-bottom: 30px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
            letter-spacing: 2px;
        }

        .game-board {
            display: grid;
            grid-template-rows: repeat(6, 1fr);
            gap: 5px;
            margin-bottom: 30px;
            padding: 20px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            backdrop-filter: blur(10px);
        }

        .row {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 5px;
        }

        .cell {
            width: 60px;
            height: 60px;
            border: 2px solid rgba(255, 255, 255, 0.3);
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 1.8rem;
            font-weight: bold;
            color: white;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 8px;
            transition: all 0.3s ease;
            text-transform: uppercase;
        }

        .cell.filled {
            border-color: rgba(255, 255, 255, 0.6);
            background: rgba(255, 255, 255, 0.2);
            transform: scale(1.05);
        }

        .cell.correct {
            background: #6aaa64;
            border-color: #6aaa64;
            animation: flip 0.6s ease-in-out;
        }

        .cell.present {
            background: #c9b458;
            border-color: #c9b458;
            animation: flip 0.6s ease-in-out;
        }

        .cell.absent {
            background: #787c7e;
            border-color: #787c7e;
            animation: flip 0.6s ease-in-out;
        }

        @keyframes flip {
            0% { transform: rotateX(0); }
            50% { transform: rotateX(-90deg); }
            100% { transform: rotateX(0); }
        }

        @keyframes bounce {
            0%, 20%, 50%, 80%, 100% { transform: translateY(0); }
            40% { transform: translateY(-10px); }
            60% { transform: translateY(-5px); }
        }

        .cell.bounce {
            animation: bounce 0.5s;
        }

        .keyboard {
            display: flex;
            flex-direction: column;
            gap: 8px;
            margin-bottom: 20px;
        }

        .keyboard-row {
            display: flex;
            justify-content: center;
            gap: 6px;
        }

        .key {
            padding: 12px 8px;
            min-width: 43px;
            background: rgba(255, 255, 255, 0.2);
            border: none;
            border-radius: 6px;
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s ease;
            backdrop-filter: blur(5px);
        }

        .key:hover {
            background: rgba(255, 255, 255, 0.3);
            transform: scale(1.05);
        }

        .key:active {
            transform: scale(0.95);
        }

        .key.wide {
            min-width: 65px;
            font-size: 12px;
        }

        .key.correct {
            background: #6aaa64;
        }

        .key.present {
            background: #c9b458;
        }

        .key.absent {
            background: #787c7e;
        }

        .message {
            font-size: 1.2rem;
            margin: 20px 0;
            min-height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .win {
            color: #6aaa64;
            font-weight: bold;
            animation: bounce 0.5s;
        }

        .lose {
            color: #f87171;
            font-weight: bold;
        }

        .new-game-btn {
            padding: 12px 24px;
            background: rgba(255, 255, 255, 0.2);
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 25px;
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            backdrop-filter: blur(10px);
        }

        .new-game-btn:hover {
            background: rgba(255, 255, 255, 0.3);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }

        .invalid {
            animation: shake 0.5s;
        }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            10%, 30%, 50%, 70%, 90% { transform: translateX(-5px); }
            20%, 40%, 60%, 80% { transform: translateX(5px); }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1 class="title">WORDLE</h1>
        
        <div class="game-board" id="board">
            <!-- Rows will be generated by JavaScript -->
        </div>

        <div class="keyboard" id="keyboard">
            <div class="keyboard-row">
                <button class="key" data-key="Q">Q</button>
                <button class="key" data-key="W">W</button>
                <button class="key" data-key="E">E</button>
                <button class="key" data-key="R">R</button>
                <button class="key" data-key="T">T</button>
                <button class="key" data-key="Y">Y</button>
                <button class="key" data-key="U">U</button>
                <button class="key" data-key="I">I</button>
                <button class="key" data-key="O">O</button>
                <button class="key" data-key="P">P</button>
            </div>
            <div class="keyboard-row">
                <button class="key" data-key="A">A</button>
                <button class="key" data-key="S">S</button>
                <button class="key" data-key="D">D</button>
                <button class="key" data-key="F">F</button>
                <button class="key" data-key="G">G</button>
                <button class="key" data-key="H">H</button>
                <button class="key" data-key="J">J</button>
                <button class="key" data-key="K">K</button>
                <button class="key" data-key="L">L</button>
            </div>
            <div class="keyboard-row">
                <button class="key wide" data-key="ENTER">ENTER</button>
                <button class="key" data-key="Z">Z</button>
                <button class="key" data-key="X">X</button>
                <button class="key" data-key="C">C</button>
                <button class="key" data-key="V">V</button>
                <button class="key" data-key="B">B</button>
                <button class="key" data-key="N">N</button>
                <button class="key" data-key="M">M</button>
                <button class="key wide" data-key="BACKSPACE">⌫</button>
            </div>
        </div>

        <div class="message" id="message"></div>
        <button class="new-game-btn" id="newGameBtn" onclick="newGame()">New Game</button>
    </div>

    <script>
        // Word list - you can expand this
        const WORDS = [
            'ABOUT', 'ABOVE', 'ABUSE', 'ACTOR', 'ACUTE', 'ADMIT', 'ADOPT', 'ADULT', 'AFTER', 'AGAIN',
            'AGENT', 'AGREE', 'AHEAD', 'ALARM', 'ALBUM', 'ALERT', 'ALIEN', 'ALIGN', 'ALIKE', 'ALIVE',
            'ALLOW', 'ALONE', 'ALONG', 'ALTER', 'AMONG', 'ANGER', 'ANGLE', 'ANGRY', 'APART', 'APPLE',
            'APPLY', 'ARENA', 'ARGUE', 'ARISE', 'ARRAY', 'ARROW', 'ASIDE', 'ASSET', 'AVOID', 'AWAKE',
            'AWARD', 'AWARE', 'BADLY', 'BAKER', 'BASES', 'BASIC', 'BEACH', 'BEGAN', 'BEGIN', 'BEING',
            'BELOW', 'BENCH', 'BILLY', 'BIRTH', 'BLACK', 'BLAME', 'BLIND', 'BLOCK', 'BLOOD', 'BOARD',
            'BOOST', 'BOOTH', 'BOUND', 'BRAIN', 'BRAND', 'BREAD', 'BREAK', 'BREED', 'BRIEF', 'BRING',
            'BROAD', 'BROKE', 'BROWN', 'BUILD', 'BUILT', 'BUYER', 'CABLE', 'CALIF', 'CARRY', 'CATCH',
            'CAUSE', 'CHAIN', 'CHAIR', 'CHAOS', 'CHARM', 'CHART', 'CHASE', 'CHEAP', 'CHECK', 'CHEST',
            'CHIEF', 'CHILD', 'CHINA', 'CHOSE', 'CIVIL', 'CLAIM', 'CLASS', 'CLEAN', 'CLEAR', 'CLICK',
            'CLIMB', 'CLOCK', 'CLOSE', 'CLOUD', 'COACH', 'COAST', 'COULD', 'COUNT', 'COURT', 'COVER',
            'CRAFT', 'CRASH', 'CRAZY', 'CREAM', 'CRIME', 'CROSS', 'CROWD', 'CROWN', 'CRUDE', 'CURVE',
            'CYCLE', 'DAILY', 'DANCE', 'DATED', 'DEALT', 'DEATH', 'DEBUT', 'DELAY', 'DEPTH', 'DOING',
            'DOUBT', 'DOZEN', 'DRAFT', 'DRAMA', 'DRANK', 'DREAM', 'DRESS', 'DRILL', 'DRINK', 'DRIVE',
            'DROVE', 'DYING', 'EAGER', 'EARLY', 'EARTH', 'EIGHT', 'ELITE', 'EMPTY', 'ENEMY', 'ENJOY',
            'ENTER', 'ENTRY', 'EQUAL', 'ERROR', 'EVENT', 'EVERY', 'EXACT', 'EXIST', 'EXTRA', 'FAITH',
            'FALSE', 'FAULT', 'FIBER', 'FIELD', 'FIFTH', 'FIFTY', 'FIGHT', 'FINAL', 'FIRST', 'FIXED',
            'FLASH', 'FLEET', 'FLOOR', 'FLUID', 'FOCUS', 'FORCE', 'FORTH', 'FORTY', 'FORUM', 'FOUND',
            'FRAME', 'FRANK', 'FRAUD', 'FRESH', 'FRONT', 'FRUIT', 'FULLY', 'FUNNY', 'GIANT', 'GIVEN',
            'GLASS', 'GLOBE', 'GOING', 'GRACE', 'GRADE', 'GRAND', 'GRANT', 'GRASS', 'GRAVE', 'GREAT',
            'GREEN', 'GROSS', 'GROUP', 'GROWN', 'GUARD', 'GUESS', 'GUEST', 'GUIDE', 'HAPPY', 'HARRY',
            'HEART', 'HEAVY', 'HENCE', 'HENRY', 'HORSE', 'HOTEL', 'HOUSE', 'HUMAN', 'IDEAL', 'IMAGE',
            'INDEX', 'INNER', 'INPUT', 'ISSUE', 'JAPAN', 'JIMMY', 'JOINT', 'JONES', 'JUDGE', 'KNOWN',
            'LABEL', 'LARGE', 'LASER', 'LATER', 'LAUGH', 'LAYER', 'LEARN', 'LEASE', 'LEAST', 'LEAVE',
            'LEGAL', 'LEVEL', 'LEWIS', 'LIGHT', 'LIMIT', 'LINKS', 'LIVES', 'LOCAL', 'LOGIC', 'LOOSE',
            'LOWER', 'LUCKY', 'LUNCH', 'LYING', 'MAGIC', 'MAJOR', 'MAKER', 'MARCH', 'MARIA', 'MATCH',
            'MAYBE', 'MAYOR', 'MEANT', 'MEDIA', 'METAL', 'MIGHT', 'MINOR', 'MINUS', 'MIXED', 'MODEL',
            'MONEY', 'MONTH', 'MORAL', 'MOTOR', 'MOUNT', 'MOUSE', 'MOUTH', 'MOVED', 'MOVIE', 'MUSIC',
            'NEEDS', 'NEVER', 'NEWLY', 'NIGHT', 'NOISE', 'NORTH', 'NOTED', 'NOVEL', 'NURSE', 'OCCUR',
            'OCEAN', 'OFFER', 'OFTEN', 'ORDER', 'OTHER', 'OUGHT', 'PAINT', 'PANEL', 'PAPER', 'PARTY',
            'PEACE', 'PETER', 'PHASE', 'PHONE', 'PHOTO', 'PIANO', 'PICKED', 'PIECE', 'PILOT', 'PITCH',
            'PLACE', 'PLAIN', 'PLANE', 'PLANT', 'PLATE', 'POINT', 'POUND', 'POWER', 'PRESS', 'PRICE',
            'PRIDE', 'PRIME', 'PRINT', 'PRIOR', 'PRIZE', 'PROOF', 'PROUD', 'PROVE', 'QUEEN', 'QUICK',
            'QUIET', 'QUITE', 'RADIO', 'RAISE', 'RANGE', 'RAPID', 'RATIO', 'REACH', 'READY', 'REALM',
            'REBEL', 'REFER', 'RELAX', 'RELAY', 'REPLY', 'RIGHT', 'RIGID', 'RIVAL', 'RIVER', 'ROBIN',
            'ROGER', 'ROMAN', 'ROUGH', 'ROUND', 'ROUTE', 'ROYAL', 'RURAL', 'SCALE', 'SCENE', 'SCOPE',
            'SCORE', 'SENSE', 'SERVE', 'SEVEN', 'SHALL', 'SHAPE', 'SHARE', 'SHARP', 'SHEET', 'SHELF',
            'SHELL', 'SHIFT', 'SHINE', 'SHIRT', 'SHOCK', 'SHOOT', 'SHORT', 'SHOWN', 'SIGHT', 'SILLY',
            'SINCE', 'SIXTH', 'SIXTY', 'SIZED', 'SKILL', 'SLEEP', 'SLIDE', 'SMALL', 'SMART', 'SMILE',
            'SMITH', 'SMOKE', 'SNAKE', 'SNOW', 'SOLAR', 'SOLID', 'SOLVE', 'SORRY', 'SOUND', 'SOUTH',
            'SPACE', 'SPARE', 'SPEAK', 'SPEED', 'SPEND', 'SPENT', 'SPLIT', 'SPOKE', 'SPORT', 'SQUAD',
            'STAFF', 'STAGE', 'STAKE', 'STAND', 'START', 'STATE', 'STEAM', 'STEEL', 'STEEP', 'STEER',
            'STICK', 'STILL', 'STOCK', 'STONE', 'STOOD', 'STORE', 'STORM', 'STORY', 'STRIP', 'STUCK',
            'STUDY', 'STUFF', 'STYLE', 'SUGAR', 'SUITE', 'SUPER', 'SWEET', 'SWIFT', 'SWING', 'SWISS',
            'TABLE', 'TAKEN', 'TASTE', 'TAXES', 'TEACH', 'TEAM', 'TERRY', 'TEXAS', 'THANK', 'THEFT',
            'THEIR', 'THEME', 'THERE', 'THESE', 'THICK', 'THING', 'THINK', 'THIRD', 'THOSE', 'THREE',
            'THREW', 'THROW', 'THUMB', 'THUS', 'TIGHT', 'TIMER', 'TIRED', 'TITLE', 'TODAY', 'TOKEN',
            'TOMMY', 'TOPIC', 'TOTAL', 'TOUCH', 'TOUGH', 'TOWER', 'TRACK', 'TRADE', 'TRAIL', 'TRAIN',
            'TREAT', 'TREND', 'TRIAL', 'TRIBE', 'TRICK', 'TRIED', 'TRIES', 'TRUCK', 'TRULY', 'TRUNK',
            'TRUST', 'TRUTH', 'TWICE', 'TWIST', 'TYLER', 'ULTRA', 'UNCLE', 'UNDER', 'UNDUE', 'UNION',
            'UNITY', 'UNTIL', 'UPPER', 'UPSET', 'URBAN', 'USAGE', 'USUAL', 'VALID', 'VALUE', 'VIDEO',
            'VIRUS', 'VISIT', 'VITAL', 'VOCAL', 'VOICE', 'WASTE', 'WATCH', 'WATER', 'WHEEL', 'WHERE',
            'WHICH', 'WHILE', 'WHITE', 'WHOLE', 'WHOSE', 'WOMAN', 'WOMEN', 'WORLD', 'WORRY', 'WORSE',
            'WORST', 'WORTH', 'WOULD', 'WRITE', 'WRONG', 'WROTE', 'YIELD', 'YOUNG', 'YOURS', 'YOUTH'
        ];

        let currentWord = '';
        let currentGuess = '';
        let currentRow = 0;
        let gameEnded = false;
        let keyboardState = {};

        // Initialize the game
        function init() {
            createBoard();
            setupKeyboard();
            newGame();
        }

        function createBoard() {
            const board = document.getElementById('board');
            board.innerHTML = '';
            
            for (let i = 0; i < 6; i++) {
                const row = document.createElement('div');
                row.className = 'row';
                row.id = `row-${i}`;
                
                for (let j = 0; j < 5; j++) {
                    const cell = document.createElement('div');
                    cell.className = 'cell';
                    cell.id = `cell-${i}-${j}`;
                    row.appendChild(cell);
                }
                
                board.appendChild(row);
            }
        }

        function setupKeyboard() {
            const keys = document.querySelectorAll('.key');
            keys.forEach(key => {
                key.addEventListener('click', () => {
                    handleKeyPress(key.dataset.key);
                });
            });

            document.addEventListener('keydown', (e) => {
                const key = e.key.toUpperCase();
                if (key === 'ENTER') {
                    handleKeyPress('ENTER');
                } else if (key === 'BACKSPACE') {
                    handleKeyPress('BACKSPACE');
                } else if (key >= 'A' && key <= 'Z') {
                    handleKeyPress(key);
                }
            });
        }

        function newGame() {
            currentWord = WORDS[Math.floor(Math.random() * WORDS.length)];
            currentGuess = '';
            currentRow = 0;
            gameEnded = false;
            keyboardState = {};
            
            // Clear board
            const cells = document.querySelectorAll('.cell');
            cells.forEach(cell => {
                cell.textContent = '';
                cell.className = 'cell';
            });

            // Reset keyboard
            const keys = document.querySelectorAll('.key');
            keys.forEach(key => {
                key.className = key.classList.contains('wide') ? 'key wide' : 'key';
            });

            document.getElementById('message').textContent = '';
            console.log('New word:', currentWord); // For testing - remove in production
        }

        function handleKeyPress(key) {
            if (gameEnded) return;

            if (key === 'ENTER') {
                if (currentGuess.length === 5) {
                    if (isValidWord(currentGuess)) {
                        submitGuess();
                    } else {
                        showMessage('Not a valid word!');
                        animateRow('invalid');
                    }
                } else {
                    showMessage('Word must be 5 letters!');
                    animateRow('invalid');
                }
            } else if (key === 'BACKSPACE') {
                if (currentGuess.length > 0) {
                    currentGuess = currentGuess.slice(0, -1);
                    updateDisplay();
                }
            } else if (key >= 'A' && key <= 'Z') {
                if (currentGuess.length < 5) {
                    currentGuess += key;
                    updateDisplay();
                }
            }
        }

        function isValidWord(word) {
            return WORDS.includes(word);
        }

        function updateDisplay() {
            for (let i = 0; i < 5; i++) {
                const cell = document.getElementById(`cell-${currentRow}-${i}`);
                if (i < currentGuess.length) {
                    cell.textContent = currentGuess[i];
                    cell.classList.add('filled');
                    // Add bounce animation to the newly filled cell
                    if (i === currentGuess.length - 1) {
                        cell.classList.add('bounce');
                        setTimeout(() => cell.classList.remove('bounce'), 500);
                    }
                } else {
                    cell.textContent = '';
                    cell.classList.remove('filled');
                }
            }
        }

        function submitGuess() {
            const guess = currentGuess;
            const result = checkGuess(guess);
            
            // Animate the reveal
            for (let i = 0; i < 5; i++) {
                setTimeout(() => {
                    const cell = document.getElementById(`cell-${currentRow}-${i}`);
                    cell.classList.add(result[i]);
                    
                    // Update keyboard
                    updateKeyboard(guess[i], result[i]);
                }, i * 100);
            }

            if (guess === currentWord) {
                setTimeout(() => {
                    gameEnded = true;
                    showMessage('Congratulations! 🎉', 'win');
                }, 500);
            } else if (currentRow === 5) {
                setTimeout(() => {
                    gameEnded = true;
                    showMessage(`Game Over! The word was: ${currentWord}`, 'lose');
                }, 500);
            }

            currentRow++;
            currentGuess = '';
        }

        function checkGuess(guess) {
            const result = [];
            const wordArray = currentWord.split('');
            const guessArray = guess.split('');
            
            // First pass: check for correct positions
            for (let i = 0; i < 5; i++) {
                if (guessArray[i] === wordArray[i]) {
                    result[i] = 'correct';
                    wordArray[i] = null;
                    guessArray[i] = null;
                }
            }
            
            // Second pass: check for present letters
            for (let i = 0; i < 5; i++) {
                if (guessArray[i] !== null) {
                    const index = wordArray.indexOf(guessArray[i]);
                    if (index !== -1) {
                        result[i] = 'present';
                        wordArray[index] = null;
                    } else {
                        result[i] = 'absent';
                    }
                }
            }
            
            return result;
        }

        function updateKeyboard(letter, status) {
            const key = document.querySelector(`[data-key="${letter}"]`);
            if (!key) return;

            const currentStatus = keyboardState[letter];
            
            // Only update if the new status is "better" than the current one
            if (!currentStatus || 
                (status === 'correct') || 
                (status === 'present' && currentStatus !== 'correct')) {
                keyboardState[letter] = status;
                key.classList.remove('correct', 'present', 'absent');
                key.classList.add(status);
            }
        }

        function animateRow(animationType) {
            const row = document.getElementById(`row-${currentRow}`);
            row.classList.add(animationType);
            setTimeout(() => row.classList.remove(animationType), 500);
        }

        function showMessage(text, className = '') {
            const messageEl = document.getElementById('message');
            messageEl.textContent = text;
            messageEl.className = `message ${className}`;
            
            if (!className) {
                setTimeout(() => {
                    messageEl.textContent = '';
                }, 3000);
            }
        }

        // Initialize the game when the page loads
        init();
    </script>
</body>
</html>
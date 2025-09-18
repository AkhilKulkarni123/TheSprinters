---
layout: post
title: Word-Game
permalink: /WordGame
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Enhanced Word Typing Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            margin: 0;
            padding: 20px;
        }
        
        #wordCanvas { 
            border: 3px solid #fff;
            display: block;
            margin-left: auto;
            margin-right: auto;
            background: rgba(0, 0, 0, 0.8);
            border-radius: 10px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
        }
        
        h2 {
            text-align: center;
            margin-top: 20px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }
        
        #options, #difficultyToggle {
            margin: 10px 5px;
            padding: 10px 20px;
            font-size: 16px;
            border: none;
            background: linear-gradient(45deg, #FF6B6B, #4ECDC4);
            color: white;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }
        
        #options:hover, #difficultyToggle:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
        }
        
        .stats {
            text-align: center;
            font-size: 18px;
            margin: 10px 0;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.5);
        }
        
        .leaderboard {
            background: rgba(0, 0, 0, 0.7);
            border-radius: 15px;
            padding: 20px;
            margin: 20px auto;
            width: 300px;
            text-align: center;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
            transition: all 0.3s ease;
        }
        
        .leaderboard h3 {
            color: #FFD700;
            margin-bottom: 15px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.8);
        }
        
        .leaderboard-entry {
            display: flex;
            justify-content: space-between;
            padding: 8px 0;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            font-size: 14px;
        }
        
        .leaderboard-entry:last-child {
            border-bottom: none;
        }
        
        .current-char-highlight {
            background-color: rgba(255, 255, 0, 0.3);
            border-radius: 3px;
            padding: 2px;
        }
        
        .difficulty-indicator {
            text-align: center;
            font-size: 16px;
            margin: 10px 0;
            padding: 8px;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 10px;
            display: inline-block;
        }
        
        .easy { color: #00ff00; }
        .medium { color: #ffff00; }
        .hard { color: #ff6600; }
        .expert { color: #ff0000; }
    </style>
</head>
<body>
    <h2>Enhanced Word Typing Game</h2>
    <p style="text-align: center;">Select a game mode and type as quickly and accurately as possible!</p>
    
    <div style="text-align: center;">
        <button id="options">Options</button>
        <button id="difficultyToggle">Difficulty: Normal</button>
    </div>
    
    <div style="text-align: center;">
        <div class="difficulty-indicator" id="difficultyIndicator">
            <span class="easy">●</span> Normal Mode
        </div>
    </div>
    
    <div class="stats">
        <p>WPM: <span class="wpm">0</span> | Accuracy: <span class="accuracy">100%</span></p>
    </div>
    
    <canvas id="wordCanvas" width="800" height="200"></canvas>
    
    <!-- MAJOR FEATURE: Persistent Leaderboard -->
    <div class="leaderboard">
        <h3>🏆 Top Scores</h3>
        <div id="leaderboardContent">
            <div class="leaderboard-entry">
                <span>No scores yet</span>
                <span>--</span>
            </div>
        </div>
    </div>

    <script>
        const wordCanvas = document.getElementById('wordCanvas');
        const wordCtx = wordCanvas.getContext('2d');
        const optionsButton = document.getElementById('options');
        const difficultyToggle = document.getElementById('difficultyToggle');
        const difficultyIndicator = document.getElementById('difficultyIndicator');
        const leaderboardContent = document.getElementById('leaderboardContent');

        let currentString = "";
        let userInput = "";
        let startTime = null;
        let finished = false;
        let mistakes = 0;
        let currentDifficulty = 'normal';
        let currentCharIndex = 0;

        // Difficulty settings affect typing requirements
        const difficulties = {
            normal: { 
                name: 'Normal', 
                color: 'easy', 
                symbol: '●', 
                multiplier: 1,
                description: 'Standard typing with backspace allowed'
            },
            hard: { 
                name: 'Hard', 
                color: 'medium', 
                symbol: '◆', 
                multiplier: 1.5,
                description: 'No backspace - mistakes must be overtyped'
            },
            expert: { 
                name: 'Expert', 
                color: 'hard', 
                symbol: '★', 
                multiplier: 2,
                description: 'No backspace + case sensitive'
            },
            insane: { 
                name: 'Insane', 
                color: 'expert', 
                symbol: '⚡', 
                multiplier: 3,
                description: 'No backspace + case sensitive + no mistakes allowed'
            }
        };

        const short_strings = ["The quick brown fox jumps over the lazy dog", "Pack my box with five dozen liquor jugs", "How quickly daft jumping zebras vex", "Jinxed wizards pluck ivy from the quilt", "Bright vixens jump, dozy fowl quack", "Sphinx of black quartz, judge my vow", "Two driven jocks help fax my big quiz", "Five quacking zephyrs jolt my wax bed", "The five boxing wizards jump quickly", "Jackdaws love my big sphinx of quartz"];
        const medium_strings = ["Amazingly few discotheques provide jukeboxes", "Back in June we delivered oxygen equipment of the same size", "The public was amazed to view the quickness and dexterity of the juggler", "Jovial zanies quickly gave up their quest for the exotic fish", "The wizard quickly jinxed the gnomes before they vaporized", "All questions asked by five watched experts amaze the judge", "The job requires extra pluck and zeal from every young wage earner", "Crazy Frederick bought many very exquisite opal jewels", "We promptly judged antique ivory buckles for the next prize", "Sixty zippers were quickly picked from the woven jute bag"];
        const long_strings = ["The wizard quickly jinxed the gnomes before they vaporized just beyond the village gates", "Heavy boxes perform quick waltzes and jigs while the young fox plays his fiddle nearby", "My faxed joke won a pager in the cable TV quiz show, making everyone in the room laugh", "Back in the quaint valley, jovial hikers mixed exotic fruit juice and warm bread by the campfire", "The public was amazed to view the quickness and dexterity of the juggler as he performed his tricks", "Amazingly few discotheques provide jukeboxes, making it hard for music lovers to enjoy their favorite tunes", "We promptly judged antique ivory buckles for the next prize in the competition, impressing all the judges", "Crazy Frederick bought many very exquisite opal jewels from the ancient market in the old town square", "Sixty zippers were quickly picked from the woven jute bag by the skilled tailor in the bustling city", "Back in June we delivered oxygen equipment of the same size and shape to all the hospitals in the region"];

        // MAJOR FEATURE: Leaderboard System
        function loadLeaderboard() {
            // Since localStorage isn't available, use in-memory storage
            if (!window.gameLeaderboard) {
                window.gameLeaderboard = [
                    // Add some demo scores so users can see how it works
                    {wpm: 65, accuracy: 98, difficulty: 'normal', score: 64, stringType: 'medium'},
                    {wpm: 45, accuracy: 95, difficulty: 'hard', score: 64, stringType: 'short'},
                    {wpm: 52, accuracy: 100, difficulty: 'normal', score: 52, stringType: 'long'}
                ];
            }
            updateLeaderboardDisplay();
        }

        function saveScore(wpm, accuracy, difficulty, stringType) {
            const score = {
                wpm: parseInt(wpm),
                accuracy: parseInt(accuracy.replace('%', '')),
                difficulty: difficulty,
                stringType: stringType,
                score: Math.round(parseInt(wpm) * difficulties[difficulty].multiplier * (parseInt(accuracy.replace('%', '')) / 100)),
                timestamp: new Date().toLocaleString()
            };

            if (!window.gameLeaderboard) {
                window.gameLeaderboard = [];
            }
            
            window.gameLeaderboard.push(score);
            window.gameLeaderboard.sort((a, b) => b.score - a.score);
            window.gameLeaderboard = window.gameLeaderboard.slice(0, 5); // Keep top 5
            
            // Add visual feedback for leaderboard update
            updateLeaderboardDisplay();
            
            // Briefly highlight the leaderboard
            const leaderboard = document.querySelector('.leaderboard');
            leaderboard.style.border = '3px solid #FFD700';
            leaderboard.style.transform = 'scale(1.02)';
            setTimeout(() => {
                leaderboard.style.border = 'none';
                leaderboard.style.transform = 'scale(1)';
            }, 1500);
        }

        function updateLeaderboardDisplay() {
            if (!window.gameLeaderboard || window.gameLeaderboard.length === 0) {
                leaderboardContent.innerHTML = '<div class="leaderboard-entry"><span>No scores yet</span><span>--</span></div>';
                return;
            }

            leaderboardContent.innerHTML = '';
            window.gameLeaderboard.forEach((entry, index) => {
                const div = document.createElement('div');
                div.className = 'leaderboard-entry';
                const diffSymbol = difficulties[entry.difficulty].symbol;
                const isNew = entry.timestamp && new Date().getTime() - new Date(entry.timestamp).getTime() < 5000;
                
                div.innerHTML = `
                    <span style="${isNew ? 'color: #FFD700; font-weight: bold;' : ''}">${index + 1}. ${entry.wpm} WPM ${diffSymbol}</span>
                    <span style="${isNew ? 'color: #FFD700; font-weight: bold;' : ''}">${entry.accuracy}%</span>
                `;
                leaderboardContent.appendChild(div);
            });
        }

        function drawText(text) {
            wordCtx.clearRect(0, 0, wordCanvas.width, wordCanvas.height);
            wordCtx.font = '24px Arial';
            wordCtx.fillStyle = '#dededeff';
            wordCtx.textAlign = 'center';
        
            const maxWidth = wordCanvas.width - 20;
            const lineHeight = 30;
            const lines = wrapText(text, maxWidth);
        
            const startY = (wordCanvas.height - lines.length * lineHeight) / 2;
            lines.forEach((line, index) => {
                wordCtx.fillText(line, wordCanvas.width / 2, startY + index * lineHeight);
            });
        }
        
        function wrapText(text, maxWidth) {
            const words = text.split(' ');
            const lines = [];
            let currentLine = words[0];
        
            for (let i = 1; i < words.length; i++) {
                const word = words[i];
                const width = wordCtx.measureText(currentLine + ' ' + word).width;
                if (width < maxWidth) {
                    currentLine += ' ' + word;
                } else {
                    lines.push(currentLine);
                    currentLine = word;
                }
            }
            lines.push(currentLine);
            return lines;
        }

        // MINI FEATURE 1: Current Character Highlighting
        function drawUserText(prompt, input) {
            wordCtx.clearRect(0, 0, wordCanvas.width, wordCanvas.height);
            wordCtx.font = '24px Arial';
            wordCtx.textAlign = 'left';
        
            const maxWidth = wordCanvas.width - 20;
            const lineHeight = 30;
            const lines = wrapText(prompt, maxWidth);
            const startY = (wordCanvas.height - lines.length * lineHeight) / 2;
        
            lines.forEach((line, lineIndex) => {
                const lineY = startY + lineIndex * lineHeight;
                const lineX = (wordCanvas.width - wordCtx.measureText(line).width) / 2;
                
                let currentX = lineX;
                const startCharIndex = lines.slice(0, lineIndex).join(' ').length + (lineIndex > 0 ? 1 : 0);
                const endCharIndex = startCharIndex + line.length;
        
                // Draw each character individually for precise control
                for (let i = startCharIndex; i < endCharIndex; i++) {
                    const char = prompt[i] || '';
                    let color = '#dededeff'; // Default prompt color
                    
                    if (i < input.length) {
                        // Character has been typed
                        const typedChar = input[i];
                        color = typedChar === char ? '#00ff00' : '#ff0000';
                    } else if (i === input.length) {
                        // Current character to be typed - MINI FEATURE 1
                        wordCtx.fillStyle = 'rgba(255, 255, 0, 0.4)';
                        wordCtx.fillRect(currentX - 2, lineY - 22, wordCtx.measureText(char).width + 4, 26);
                        color = '#ffffff';
                    }
                    
                    wordCtx.fillStyle = color;
                    wordCtx.fillText(char, currentX, lineY);
                    currentX += wordCtx.measureText(char).width;
                }
            });
        }

        function updateStats(prompt, input, startTime) {
            // Calculate accuracy - EXACTLY like original
            const totalTyped = input.length;
            const accuracy = totalTyped > 0 ? Math.round(((totalTyped - mistakes) / totalTyped) * 100) : 100;
            document.querySelector('.accuracy').textContent = accuracy + '%';

            // Calculate WPM - EXACTLY like original  
            if (startTime) {
                const elapsed = (Date.now() - startTime) / 1000 / 60; // minutes
                const words = prompt.length / 5; // Use prompt length like original
                const wpm = elapsed > 0 ? Math.round(words / elapsed) : 0;
                document.querySelector('.wpm').textContent = wpm;
            } else {
                document.querySelector('.wpm').textContent = '0';
            }
        }

        function finishGame(prompt, input, startTime) {
            finished = true;
            updateStats(prompt, input, startTime);
            const finalWPM = document.querySelector('.wpm').textContent;
            const finalAccuracy = document.querySelector('.accuracy').textContent;
            
            // Save to leaderboard
            const stringType = currentString.replace('_strings', '');
            saveScore(finalWPM, finalAccuracy, currentDifficulty, stringType);
            
            // Show updated leaderboard position
            const currentScore = Math.round(parseInt(finalWPM) * difficulties[currentDifficulty].multiplier * (parseInt(finalAccuracy.replace('%', '')) / 100));
            let position = "Not in top 5";
            
            if (window.gameLeaderboard) {
                const rank = window.gameLeaderboard.findIndex(entry => entry.score === currentScore) + 1;
                if (rank > 0 && rank <= 5) {
                    position = `#${rank} on leaderboard!`;
                }
            }
            
            alert(`🎉 Game Complete! 🎉\nWPM: ${finalWPM}\nAccuracy: ${finalAccuracy}\nDifficulty: ${difficulties[currentDifficulty].name}\nScore: ${currentScore}\nRank: ${position}`);
        }

        // MINI FEATURE 2: Difficulty System
        function updateDifficultyDisplay() {
            const diff = difficulties[currentDifficulty];
            difficultyToggle.textContent = `Difficulty: ${diff.name}`;
            difficultyIndicator.innerHTML = `<span class="${diff.color}">${diff.symbol}</span> ${diff.name} - ${diff.description}`;
        }

        difficultyToggle.addEventListener('click', () => {
            const diffKeys = Object.keys(difficulties);
            const currentIndex = diffKeys.indexOf(currentDifficulty);
            currentDifficulty = diffKeys[(currentIndex + 1) % diffKeys.length];
            updateDifficultyDisplay();
        });

        function startGame() {
            if (currentString === "") {
                alert("Please select a string length from the options menu.");
                return;
            }

            let stringArray;
            if (currentString === "short_strings") {
                stringArray = short_strings;
            } else if (currentString === "medium_strings") {
                stringArray = medium_strings;
            } else if (currentString === "long_strings") {
                stringArray = long_strings;
            }

            const randomIndex = Math.floor(Math.random() * stringArray.length);
            const selectedString = stringArray[randomIndex];
            userInput = "";
            mistakes = 0;
            finished = false;
            startTime = Date.now();
            drawText(selectedString);
            document.querySelector('.wpm').textContent = '0';
            document.querySelector('.accuracy').textContent = '100%';

            document.onkeydown = function (e) {
                if (finished) return;

                if (e.key.length === 1 && userInput.length < selectedString.length) {
                    const nextChar = selectedString[userInput.length];
                    let isCorrect = false;
                    
                    // Apply difficulty rules
                    if (currentDifficulty === 'expert' || currentDifficulty === 'insane') {
                        // Case sensitive
                        isCorrect = e.key === nextChar;
                    } else {
                        // Case insensitive like original
                        isCorrect = e.key.toLowerCase() === nextChar.toLowerCase();
                    }
                    
                    if (currentDifficulty === 'insane' && !isCorrect) {
                        // Insane mode: Game over on first mistake
                        alert('Game Over! Insane mode requires perfect accuracy.');
                        return;
                    }
                    
                    if (!isCorrect) {
                        mistakes++;
                    }
                    
                    userInput += e.key;
                } else if (e.key === 'Backspace' && userInput.length > 0) {
                    // Backspace restrictions based on difficulty
                    if (currentDifficulty === 'normal') {
                        userInput = userInput.slice(0, -1);
                    }
                    // Hard, Expert, and Insane modes don't allow backspace
                }

                drawUserText(selectedString, userInput);
                updateStats(selectedString, userInput, startTime); // Real-time updates like original

                if (userInput === selectedString) {
                    finishGame(selectedString, userInput, startTime);
                }
            };
        }

        optionsButton.addEventListener('click', () => {
            const menu = document.createElement('div');
            menu.style.position = 'fixed';
            menu.style.width = '250px';
            menu.style.border = '2px solid #fff';
            menu.style.backgroundColor = 'rgba(0, 0, 0, 0.9)';
            menu.style.padding = '20px';
            menu.style.boxShadow = '0px 10px 30px rgba(0, 0, 0, 0.5)';
            menu.style.textAlign = 'center';
            menu.style.borderRadius = '15px';
            menu.style.color = 'white';
        
            menu.style.top = `${window.innerHeight / 2 - 100}px`;
            menu.style.left = `${window.innerWidth / 2 - 125}px`;
        
            const title = document.createElement('h3');
            title.textContent = 'Select Game Mode';
            title.style.margin = '0 0 15px 0';
            title.style.color = '#FFD700';
            menu.appendChild(title);
        
            const createButton = (text, value) => {
                const button = document.createElement('button');
                button.textContent = text;
                button.style.display = 'block';
                button.style.margin = '10px auto';
                button.style.width = '180px';
                button.style.padding = '12px';
                button.style.border = 'none';
                button.style.borderRadius = '20px';
                button.style.background = 'linear-gradient(45deg, #FF6B6B, #4ECDC4)';
                button.style.color = 'white';
                button.style.cursor = 'pointer';
                button.style.fontSize = '14px';
                button.style.transition = 'transform 0.2s ease';
                
                button.addEventListener('mouseenter', () => {
                    button.style.transform = 'scale(1.05)';
                });
                button.addEventListener('mouseleave', () => {
                    button.style.transform = 'scale(1)';
                });
                
                button.addEventListener('click', () => {
                    currentString = value;
                    startGame();
                    document.body.removeChild(menu);
                });
                return button;
            };
        
            menu.appendChild(createButton('Short Strings (Easy)', 'short_strings'));
            menu.appendChild(createButton('Medium Strings (Medium)', 'medium_strings'));
            menu.appendChild(createButton('Long Strings (Hard)', 'long_strings'));
        
            const closeButton = document.createElement('button');
            closeButton.textContent = '×';
            closeButton.style.position = 'absolute';
            closeButton.style.top = '10px';
            closeButton.style.right = '15px';
            closeButton.style.background = 'transparent';
            closeButton.style.border = 'none';
            closeButton.style.color = 'white';
            closeButton.style.fontSize = '20px';
            closeButton.style.cursor = 'pointer';
            closeButton.addEventListener('click', () => {
                document.body.removeChild(menu);
            });
            menu.appendChild(closeButton);
        
            document.body.appendChild(menu);
        });

        // Initialize
        updateDifficultyDisplay();
        loadLeaderboard();
    </script>
</body>
</html>
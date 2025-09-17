---
layout: post
title: Word Game
permalink: /word/
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Word Typing Game (Updated)</title>
    <style>
        #wordCanvas { 
            border: 10px solid #000;
            display: block;
            margin-left: auto;
            margin-right: auto;
        }
        
        h2 {
            text-align: center;
            margin-top: 20px;
        }
        #options {
            margin-top: 20px;
            margin-bottom: 10px;
            padding: 10px 20px;
            font-size: 16px;
            border: none;
            background-color: #007BFF;
            color: white;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <h2 style="display: inline-block; margin-right: auto;">Word Game</h2>
    <p>Select a game mode (string length) from the options menu and try to type the prompt as quickly and accurately as possible!</p>
    <button style="float: right;" id="options">Options</button>
    <p>WPM: <span class="wpm"></span></p>
    <p>Accuracy: <span class="accuracy"></span></p>

    <canvas id="wordCanvas" width="800" height="200"></canvas>

    <script>
        const wordCanvas = document.getElementById('wordCanvas');
        const wordCtx = wordCanvas.getContext('2d');
        const optionsButton = document.getElementById('options');

        let currentString = "";
        let userInput = "";
        let startTime = null;
        let finished = false;
        let mistakes = 0;

        // === Hack #1: Progress Bar ===
        const progressBar = document.createElement('div');
        progressBar.style.width = '0%';
        progressBar.style.height = '20px';
        progressBar.style.backgroundColor = '#28a745';
        progressBar.style.marginTop = '10px';
        progressBar.style.borderRadius = '5px';

        const progressContainer = document.createElement('div');
        progressContainer.style.width = '800px';
        progressContainer.style.height = '20px';
        progressContainer.style.backgroundColor = '#ddd';
        progressContainer.style.margin = '10px auto';
        progressContainer.style.borderRadius = '5px';
        progressContainer.appendChild(progressBar);

        document.body.insertBefore(progressContainer, wordCanvas.nextSibling);

        function updateProgress(prompt, input) {
            const progressPercent = (input.length / prompt.length) * 100;
            progressBar.style.width = progressPercent + '%';
        }

        // === Hack #3: Dark Mode Toggle ===
        const darkModeButton = document.createElement('button');
        darkModeButton.textContent = 'Toggle Dark Mode';
        darkModeButton.style.display = 'block';
        darkModeButton.style.margin = '20px auto';
        darkModeButton.style.padding = '10px 20px';
        darkModeButton.style.backgroundColor = '#444';
        darkModeButton.style.color = '#fff';
        darkModeButton.style.border = 'none';
        darkModeButton.style.borderRadius = '5px';
        document.body.insertBefore(darkModeButton, progressContainer);

        let darkMode = false;
        darkModeButton.addEventListener('click', () => {
            darkMode = !darkMode;
            document.body.style.backgroundColor = darkMode ? '#121212' : '#ffffff';
            document.body.style.color = darkMode ? '#e0e0e0' : '#000000';
            wordCanvas.style.borderColor = darkMode ? '#ffffff' : '#000000';
        });

        // String sets
        const short_strings = ["The quick brown fox jumps over the lazy dog", "Pack my box with five dozen liquor jugs", "How quickly daft jumping zebras vex", "Jinxed wizards pluck ivy from the quilt", "Bright vixens jump, dozy fowl quack", "Sphinx of black quartz, judge my vow", "Two driven jocks help fax my big quiz", "Five quacking zephyrs jolt my wax bed", "The five boxing wizards jump quickly", "Jackdaws love my big sphinx of quartz"];
        const medium_strings = ["Amazingly few discotheques provide jukeboxes", "Back in June we delivered oxygen equipment of the same size", "The public was amazed to view the quickness and dexterity of the juggler", "Jovial zanies quickly gave up their quest for the exotic fish", "The wizard quickly jinxed the gnomes before they vaporized", "All questions asked by five watched experts amaze the judge", "The job requires extra pluck and zeal from every young wage earner", "Crazy Frederick bought many very exquisite opal jewels", "We promptly judged antique ivory buckles for the next prize", "Sixty zippers were quickly picked from the woven jute bag"];
        const long_strings = ["The wizard quickly jinxed the gnomes before they vaporized just beyond the village gates", "Heavy boxes perform quick waltzes and jigs while the young fox plays his fiddle nearby", "My faxed joke won a pager in the cable TV quiz show, making everyone in the room laugh", "Back in the quaint valley, jovial hikers mixed exotic fruit juice and warm bread by the campfire", "The public was amazed to view the quickness and dexterity of the juggler as he performed his tricks", "Amazingly few discotheques provide jukeboxes, making it hard for music lovers to enjoy their favorite tunes", "We promptly judged antique ivory buckles for the next prize in the competition, impressing all the judges", "Crazy Frederick bought many very exquisite opal jewels from the ancient market in the old town square", "Sixty zippers were quickly picked from the woven jute bag by the skilled tailor in the bustling city", "Back in June we delivered oxygen equipment of the same size and shape to all the hospitals in the region"];

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

        // === Hack #2: Highlight next character ===
        function drawUserTextWithHighlight(prompt, input) {
            wordCtx.clearRect(0, 0, wordCanvas.width, wordCanvas.height);
            wordCtx.font = '24px Arial';
            wordCtx.textAlign = 'left';

            const maxWidth = wordCanvas.width - 20;
            const lineHeight = 30;
            const lines = wrapText(prompt, maxWidth);
            const startY = (wordCanvas.height - lines.length * lineHeight) / 2;

            let charIndex = 0;
            lines.forEach((line, lineIndex) => {
                const lineY = startY + lineIndex * lineHeight;
                const lineX = (wordCanvas.width - wordCtx.measureText(line).width) / 2;
                wordCtx.fillStyle = '#dededeff';
                wordCtx.fillText(line, lineX, lineY);

                // highlight box for the next character
                if (charIndex <= input.length && input.length < prompt.length) {
                    const nextChar = prompt[input.length];
                    const before = line.slice(0, input.length - charIndex);
                    const highlightX = lineX + wordCtx.measureText(before).width;

                    if (prompt[input.length] && line.includes(nextChar)) {
                        wordCtx.strokeStyle = 'orange';
                        wordCtx.lineWidth = 2;
                        wordCtx.strokeRect(
                            highlightX - 2,
                            lineY - 20,
                            wordCtx.measureText(nextChar).width + 4,
                            24
                        );
                    }
                }

                // draw typed characters
                let currentX = lineX;
                for (let i = 0; i < line.length && charIndex < input.length; i++, charIndex++) {
                    const char = input[charIndex];
                    const promptChar = prompt[charIndex];
                    const color = char === promptChar ? 'green' : 'red';
                    wordCtx.fillStyle = color;
                    wordCtx.fillText(char, currentX, lineY);
                    currentX += wordCtx.measureText(promptChar).width;
                }
                charIndex += Math.max(0, line.length - (input.length - charIndex));
            });
        }

        function updateStats(prompt, input, startTime) {
            const totalTyped = input.length;
            const accuracy = totalTyped > 0 ? Math.round(((totalTyped - mistakes) / totalTyped) * 100) : 100;
            document.querySelector('.accuracy').textContent = accuracy + '%';

            if (startTime) {
                const elapsed = (Date.now() - startTime) / 1000 / 60;
                const words = prompt.length / 5;
                const wpm = elapsed > 0 ? Math.round(words / elapsed) : 0;
                document.querySelector('.wpm').textContent = wpm;
            } else {
                document.querySelector('.wpm').textContent = '0';
            }
        }

        function finishGame(prompt, input, startTime) {
            finished = true;
            updateStats(prompt, input, startTime);
            alert('Finished! WPM: ' + document.querySelector('.wpm').textContent + ', Accuracy: ' + document.querySelector('.accuracy').textContent);
        }

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
            progressBar.style.width = '0%';

            document.onkeydown = function (e) {
                if (finished) return;

                if (e.key.length === 1 && userInput.length < selectedString.length) {
                    const nextChar = selectedString[userInput.length];
                    if (e.key !== nextChar) {
                        mistakes++;
                    }
                    userInput += e.key;
                } else if (e.key === 'Backspace' && userInput.length > 0) {
                    userInput = userInput.slice(0, -1);
                }

                drawUserTextWithHighlight(selectedString, userInput);
                updateStats(selectedString, userInput, startTime);
                updateProgress(selectedString, userInput);

                if (userInput === selectedString) {
                    finishGame(selectedString, userInput, startTime);
                }
            };
        }

        // Options menu
        optionsButton.addEventListener('click', () => {
            const menu = document.createElement('div');
            menu.style.position = 'absolute';
            menu.style.width = '200px';
            menu.style.border = '1px solid #ccc';
            menu.style.backgroundColor = '#fff';
            menu.style.padding = '10px';
            menu.style.boxShadow = '0px 4px 6px rgba(0, 0, 0, 0.1)';
            menu.style.textAlign = 'center';
        
            menu.style.top = `${window.innerHeight / 2 - 50}px`;
            menu.style.left = `${window.innerWidth / 2 - 100}px`;
        
            const shortOption = document.createElement('button');
            shortOption.textContent = 'Short Strings';
            shortOption.style.display = 'block';
            shortOption.style.margin = '10px 0';
            shortOption.addEventListener('click', () => {
                currentString = "short_strings";
                startGame();
                document.body.removeChild(menu);
            });
        
            const mediumOption = document.createElement('button');
            mediumOption.textContent = 'Medium Strings';
            mediumOption.style.display = 'block';
            mediumOption.style.margin = '10px 0';
            mediumOption.addEventListener('click', () => {
                currentString = "medium_strings";
                startGame();
                document.body.removeChild(menu);
            });
        
            const longOption = document.createElement('button');
            longOption.textContent = 'Long Strings';
            longOption.style.display = 'block';
            longOption.style.margin = '10px 0';
            longOption.addEventListener('click', () => {
                currentString = "long_strings";
                startGame();
                document.body.removeChild(menu);
            });
        
            menu.appendChild(shortOption);
            menu.appendChild(mediumOption);
            menu.appendChild(longOption);
            document.body.appendChild(menu);
        });
    </script>
</body>
</html>
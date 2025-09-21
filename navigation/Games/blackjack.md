---
layout: post
title: Blackjack Game
permalink: /blackjack
---

<style>
    body {
        background-image: url('images/SB1.png');
        background-size: cover;
        background-position: center;
        background-repeat: no-repeat;
        background-attachment: fixed;
        font-family: 'Arial', sans-serif;
        color: white;
        margin: 0;
        padding: 20px;
    }

    .game-container {
        max-width: 800px;
        margin: 0 auto;
        text-align: center;
        background: rgba(0, 0, 0, 0.7);
        border-radius: 15px;
        padding: 30px;
        box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
    }

    h1 {
        color: #ffd700;
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        margin-bottom: 30px;
        font-size: 2.5em;
    }

    .game-area {
        display: flex;
        justify-content: space-between;
        margin: 30px 0;
        gap: 20px;
    }

    .player-area, .dealer-area {
        flex: 1;
        background: rgba(255, 255, 255, 0.1);
        border-radius: 10px;
        padding: 20px;
        border: 2px solid #ffd700;
    }

    .cards {
        display: flex;
        justify-content: center;
        gap: 10px;
        margin: 15px 0;
        flex-wrap: wrap;
        min-height: 120px;
    }

    .card {
        width: 80px;
        height: 112px;
        background: white;
        border-radius: 8px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        padding: 8px;
        font-size: 14px;
        font-weight: bold;
        color: black;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
        position: relative;
    }

    .card.red {
        color: #d32f2f;
    }

    .card .top {
        text-align: left;
    }

    .card .bottom {
        text-align: right;
        transform: rotate(180deg);
    }

    .card .center {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        font-size: 24px;
    }

    .hidden-card {
        background: #2c5f2d;
        background-image: repeating-linear-gradient(
            45deg,
            transparent,
            transparent 5px,
            rgba(255,255,255,.1) 5px,
            rgba(255,255,255,.1) 10px
        );
    }

    .score {
        font-size: 1.5em;
        font-weight: bold;
        color: #ffd700;
        margin: 10px 0;
    }

    .controls {
        margin: 30px 0;
    }

    .btn {
        background: linear-gradient(135deg, #ffd700, #ffed4a);
        color: #2c5f2d;
        border: none;
        padding: 12px 24px;
        margin: 0 10px;
        border-radius: 25px;
        font-size: 16px;
        font-weight: bold;
        cursor: pointer;
        transition: all 0.3s ease;
        text-transform: uppercase;
    }

    .btn:hover {
        transform: translateY(-2px);
        box-shadow: 0 6px 20px rgba(255, 215, 0, 0.4);
    }

    .btn:disabled {
        background: #666;
        color: #999;
        cursor: not-allowed;
        transform: none;
        box-shadow: none;
    }

    .message {
        font-size: 1.3em;
        font-weight: bold;
        margin: 20px 0;
        min-height: 30px;
        color: #ffd700;
    }

    .chips {
        display: flex;
        justify-content: center;
        gap: 15px;
        margin: 20px 0;
        flex-wrap: wrap;
    }

    .chip {
        width: 50px;
        height: 50px;
        border-radius: 50%;
        border: 3px solid #fff;
        display: flex;
        align-items: center;
        justify-content: center;
        font-weight: bold;
        cursor: pointer;
        transition: transform 0.2s ease;
        font-size: 12px;
    }

    .chip:hover {
        transform: scale(1.1);
    }

    .chip-5 { background: #f44336; }
    .chip-10 { background: #2196f3; }
    .chip-25 { background: #4caf50; }
    .chip-100 { background: #ff9800; }
    .chip-all { background: #9c27b0; }

    .custom-bet {
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 10px;
        margin: 15px 0;
    }

    .custom-bet input {
        width: 100px;
        padding: 8px;
        border: 2px solid #ffd700;
        border-radius: 5px;
        background: rgba(255, 255, 255, 0.1);
        color: white;
        text-align: center;
        font-size: 16px;
    }

    .custom-bet input:focus {
        outline: none;
        box-shadow: 0 0 10px rgba(255, 215, 0, 0.5);
    }

    .custom-bet button {
        background: linear-gradient(135deg, #4caf50, #45a049);
        color: white;
        border: none;
        padding: 8px 16px;
        border-radius: 5px;
        cursor: pointer;
        font-weight: bold;
        transition: all 0.3s ease;
    }

    .custom-bet button:hover {
        transform: translateY(-1px);
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    }

    .betting-controls {
        margin: 20px 0;
    }

    .clear-bet-section {
        margin-top: 15px;
        padding-top: 15px;
        border-top: 1px solid rgba(255, 215, 0, 0.3);
    }

    .stats {
        display: flex;
        justify-content: center;
        gap: 30px;
        margin: 20px 0;
        font-size: 1.1em;
    }

    .stat {
        background: rgba(255, 255, 255, 0.1);
        padding: 10px 20px;
        border-radius: 10px;
        border: 1px solid #ffd700;
    }

    .player-setup {
        margin: 20px 0;
        text-align: center;
    }

    .player-count {
        display: flex;
        justify-content: center;
        gap: 10px;
        margin: 15px 0;
    }

    .player-count button {
        width: 40px;
        height: 40px;
        border-radius: 50%;
        border: 2px solid #ffd700;
        background: rgba(255, 255, 255, 0.1);
        color: white;
        font-weight: bold;
        cursor: pointer;
        transition: all 0.3s ease;
    }

    .player-count button.active {
        background: #ffd700;
        color: #2c5f2d;
    }

    .player-count button:hover {
        transform: scale(1.1);
    }

    .players-area {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
        gap: 15px;
        margin: 20px 0;
    }

    .player-section {
        background: rgba(255, 255, 255, 0.1);
        border-radius: 10px;
        padding: 15px;
        border: 2px solid #ffd700;
        min-height: 200px;
    }

    .player-section h4 {
        margin: 0 0 10px 0;
        color: #ffd700;
    }

    .player-section .cards {
        min-height: 80px;
        gap: 5px;
    }

    .player-section .card {
        width: 60px;
        height: 84px;
        font-size: 10px;
    }

    .player-section .card .center {
        font-size: 18px;
    }

    .current-player {
        border-color: #ff6b6b;
        box-shadow: 0 0 15px rgba(255, 107, 107, 0.5);
    }

    .player-name-input {
        width: 100%;
        max-width: 150px;
        padding: 5px;
        margin: 5px 0;
        border: 1px solid #ffd700;
        border-radius: 3px;
        background: rgba(255, 255, 255, 0.1);
        color: white;
        text-align: center;
        font-size: 14px;
    }

    .player-name-input:focus {
        outline: none;
        box-shadow: 0 0 5px rgba(255, 215, 0, 0.5);
    }

    .player-betting {
        display: flex;
        flex-direction: column;
        gap: 5px;
        margin: 10px 0;
    }

    .player-betting-chips {
        display: flex;
        justify-content: center;
        gap: 5px;
        flex-wrap: wrap;
    }

    .player-betting-chips .chip {
        width: 35px;
        height: 35px;
        font-size: 10px;
    }

    .player-custom-bet {
        display: flex;
        align-items: center;
        gap: 5px;
        justify-content: center;
    }

    .player-custom-bet input {
        width: 60px;
        padding: 3px;
        border: 1px solid #ffd700;
        border-radius: 3px;
        background: rgba(255, 255, 255, 0.1);
        color: white;
        text-align: center;
        font-size: 12px;
    }

    .player-custom-bet button {
        background: linear-gradient(135deg, #4caf50, #45a049);
        color: white;
        border: none;
        padding: 3px 8px;
        border-radius: 3px;
        cursor: pointer;
        font-size: 10px;
        font-weight: bold;
    }

    .player-bet-controls {
        font-size: 12px;
        margin: 5px 0;
    }

    .player-bet-controls button {
        background: linear-gradient(135deg, #ff9800, #f57c00);
        color: white;
        border: none;
        padding: 2px 6px;
        border-radius: 3px;
        cursor: pointer;
        font-size: 10px;
        margin: 0 2px;
    }
</style>

<div class="game-container">
    <h1>🃏 Blackjack 🃏</h1>

    <div class="player-setup">
        <h3>Number of Players:</h3>
        <div class="player-count">
            <button onclick="setPlayerCount(1)" class="active">1</button>
            <button onclick="setPlayerCount(2)">2</button>
            <button onclick="setPlayerCount(3)">3</button>
            <button onclick="setPlayerCount(4)">4</button>
            <button onclick="setPlayerCount(5)">5</button>
        </div>
    </div>

    <div class="game-area">
        <div class="dealer-area">
            <h3>Dealer</h3>
            <div class="score">Score: <span id="dealer-score">0</span></div>
            <div class="cards" id="dealer-cards"></div>
        </div>
    </div>

    <div class="players-area" id="players-area">
        <!-- Player sections will be dynamically generated -->
    </div>

    <div class="message" id="game-message">Place your bet to start!</div>

    <div class="controls">
        <button class="btn" id="deal-btn" onclick="startGame()" disabled>Start</button>
    </div>
</div>

<script>
class BlackjackGame {
    constructor() {
        this.deck = [];
        this.players = [];
        this.dealerHand = [];
        this.numPlayers = 1;
        this.currentPlayerIndex = 0;
        this.money = 1000;
        this.currentBet = 0;
        this.gameInProgress = false;
        this.dealerHidden = true;
        this.wins = 0;
        this.losses = 0;
        this.suits = ['♠', '♥', '♦', '♣'];
        this.ranks = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];
        this.createDeck();
        this.initializePlayers();
        this.updateDisplay();
    }

    initializePlayers() {
        this.players = [];
        const moneyPerPlayer = this.money > 0 ? Math.floor(this.money / this.numPlayers) : Math.floor(1000 / this.numPlayers);
        this.money = 0; // Reset global money since each player has their own now

        for (let i = 0; i < this.numPlayers; i++) {
            this.players.push({
                name: `Player ${i + 1}`,
                hand: [],
                score: 0,
                bet: 0,
                money: moneyPerPlayer,
                wins: 0,
                losses: 0,
                isFinished: false,
                isBust: false
            });
        }
        this.generatePlayerUI();
    }

    createDeck() {
        this.deck = [];
        for (let suit of this.suits) {
            for (let rank of this.ranks) {
                this.deck.push({
                    suit: suit,
                    rank: rank,
                    value: this.getCardValue(rank)
                });
            }
        }
        this.shuffleDeck();
    }

    shuffleDeck() {
        for (let i = this.deck.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [this.deck[i], this.deck[j]] = [this.deck[j], this.deck[i]];
        }
    }

    getCardValue(rank) {
        if (rank === 'A') return 11;
        if (['J', 'Q', 'K'].includes(rank)) return 10;
        return parseInt(rank);
    }

    calculateHandValue(hand) {
        let value = 0;
        let aces = 0;

        for (let card of hand) {
            if (card.rank === 'A') {
                aces++;
            }
            value += card.value;
        }

        while (value > 21 && aces > 0) {
            value -= 10;
            aces--;
        }

        return value;
    }

    generatePlayerUI() {
        const playersArea = document.getElementById('players-area');
        playersArea.innerHTML = '';

        for (let i = 0; i < this.numPlayers; i++) {
            const playerSection = document.createElement('div');
            playerSection.className = 'player-section';
            playerSection.id = `player-${i}`;

            playerSection.innerHTML = `
                <input type="text" class="player-name-input" id="player-${i}-name" value="Player ${i + 1}"
                       onchange="game.updatePlayerName(${i}, this.value)" placeholder="Enter name">

                <div class="score">Money: $<span id="player-${i}-money">0</span></div>
                <div class="score">Score: <span id="player-${i}-score">0</span></div>
                <div class="score">Bet: $<span id="player-${i}-bet">0</span></div>
                <div class="score">W: <span id="player-${i}-wins">0</span> L: <span id="player-${i}-losses">0</span></div>

                <div class="player-betting" id="player-${i}-betting">
                    <div class="player-betting-chips">
                        <div class="chip chip-5" onclick="game.placeBetForPlayer(${i}, 5)">$5</div>
                        <div class="chip chip-10" onclick="game.placeBetForPlayer(${i}, 10)">$10</div>
                        <div class="chip chip-25" onclick="game.placeBetForPlayer(${i}, 25)">$25</div>
                        <div class="chip chip-100" onclick="game.placeBetForPlayer(${i}, 100)">$100</div>
                    </div>

                    <div class="player-custom-bet">
                        <span>$</span>
                        <input type="number" id="player-${i}-custom" min="1" max="10000" placeholder="Amount">
                        <button onclick="game.placeCustomBetForPlayer(${i})">Bet</button>
                    </div>

                    <div class="player-bet-controls">
                        <button onclick="game.betAllForPlayer(${i})">All In</button>
                        <button onclick="game.clearBetForPlayer(${i})">Clear</button>
                    </div>
                </div>

                <div class="cards" id="player-${i}-cards"></div>

                <div class="player-controls" id="player-${i}-controls" style="display: none;">
                    <button class="btn" onclick="game.hitPlayer(${i})">Hit</button>
                    <button class="btn" onclick="game.standPlayer(${i})">Stand</button>
                    <button class="btn" onclick="game.doubleDownPlayer(${i})">Double</button>
                </div>
            `;

            playersArea.appendChild(playerSection);
        }
    }

    setPlayerCount(count) {
        if (this.gameInProgress) return;
        this.numPlayers = count;
        this.currentPlayerIndex = 0;
        this.initializePlayers();

        // Update button styling
        document.querySelectorAll('.player-count button').forEach(btn => {
            btn.classList.remove('active');
        });
        event.target.classList.add('active');

        this.updateDisplay();
    }

    updatePlayerName(playerIndex, name) {
        if (this.gameInProgress || playerIndex >= this.players.length) return;
        this.players[playerIndex].name = name || `Player ${playerIndex + 1}`;
    }

    placeBetForPlayer(playerIndex, amount) {
        if (this.gameInProgress || playerIndex >= this.players.length) return;
        const player = this.players[playerIndex];
        if (player.money >= amount) {
            player.bet += amount;
            player.money -= amount;
            this.updateDisplay();
            this.checkCanStart();
        }
    }

    placeCustomBetForPlayer(playerIndex) {
        if (this.gameInProgress || playerIndex >= this.players.length) return;
        const input = document.getElementById(`player-${playerIndex}-custom`);
        const amount = parseInt(input.value);
        const player = this.players[playerIndex];
        if (amount && amount > 0 && player.money >= amount) {
            player.bet += amount;
            player.money -= amount;
            input.value = '';
            this.updateDisplay();
            this.checkCanStart();
        }
    }

    betAllForPlayer(playerIndex) {
        if (this.gameInProgress || playerIndex >= this.players.length) return;
        const player = this.players[playerIndex];
        if (player.money > 0) {
            player.bet += player.money;
            player.money = 0;
            this.updateDisplay();
            this.checkCanStart();
        }
    }

    clearBetForPlayer(playerIndex) {
        if (this.gameInProgress || playerIndex >= this.players.length) return;
        const player = this.players[playerIndex];
        player.money += player.bet;
        player.bet = 0;
        this.updateDisplay();
        this.checkCanStart();
    }

    checkCanStart() {
        const hasAnyBets = this.players.some(player => player.bet > 0);
        document.getElementById('deal-btn').disabled = this.gameInProgress || !hasAnyBets;
    }

    getTotalBet() {
        return this.players.reduce((total, player) => total + player.bet, 0);
    }

    startGame() {
        const hasAnyBets = this.players.some(player => player.bet > 0);
        if (!hasAnyBets) return;

        this.gameInProgress = true;
        this.dealerHidden = true;
        this.currentPlayerIndex = 0;
        this.dealerHand = [];

        // Initialize player hands but keep their individual bets
        for (let i = 0; i < this.numPlayers; i++) {
            this.players[i].hand = [];
            this.players[i].isFinished = false;
            this.players[i].isBust = false;

            // Skip players with no bet
            if (this.players[i].bet === 0) {
                this.players[i].isFinished = true;
            }
        }

        if (this.deck.length < 20) {
            this.createDeck();
        }

        // Deal initial cards only to players with bets
        for (let i = 0; i < 2; i++) {
            for (let player of this.players) {
                if (player.bet > 0) {
                    player.hand.push(this.deck.pop());
                }
            }
            this.dealerHand.push(this.deck.pop());
        }

        this.updateDisplay();
        this.hideBettingControls();
        this.showPlayerControls();

        // Check for blackjacks
        this.checkForBlackjacks();
    }

    findFirstActivePlayer() {
        this.currentPlayerIndex = 0;
        while (this.currentPlayerIndex < this.numPlayers && this.players[this.currentPlayerIndex].isFinished) {
            this.currentPlayerIndex++;
        }
    }

    hideBettingControls() {
        for (let i = 0; i < this.numPlayers; i++) {
            const bettingDiv = document.getElementById(`player-${i}-betting`);
            if (bettingDiv) {
                bettingDiv.style.display = 'none';
            }
        }
    }

    showBettingControls() {
        for (let i = 0; i < this.numPlayers; i++) {
            const bettingDiv = document.getElementById(`player-${i}-betting`);
            const controlsDiv = document.getElementById(`player-${i}-controls`);
            if (bettingDiv) {
                bettingDiv.style.display = 'flex';
            }
            if (controlsDiv) {
                controlsDiv.style.display = 'none';
            }
        }
    }

    showPlayerControls() {
        for (let i = 0; i < this.numPlayers; i++) {
            const player = this.players[i];
            if (player.bet > 0 && !player.isFinished) {
                const controlsDiv = document.getElementById(`player-${i}-controls`);
                if (controlsDiv) {
                    controlsDiv.style.display = 'block';
                }
            }
        }
    }

    highlightCurrentPlayer() {
        // Remove highlight from all players
        document.querySelectorAll('.player-section').forEach(section => {
            section.classList.remove('current-player');
        });

        // Highlight current player
        if (this.currentPlayerIndex < this.numPlayers) {
            const currentPlayerSection = document.getElementById(`player-${this.currentPlayerIndex}`);
            if (currentPlayerSection) {
                currentPlayerSection.classList.add('current-player');
            }
        }
    }

    hitPlayer(playerIndex) {
        if (!this.gameInProgress || playerIndex >= this.numPlayers) return;

        const player = this.players[playerIndex];
        if (player.isFinished || player.bet === 0) return;

        player.hand.push(this.deck.pop());
        this.updateDisplay();

        const playerValue = this.calculateHandValue(player.hand);
        if (playerValue > 21) {
            player.isBust = true;
            player.isFinished = true;
            this.hidePlayerControls(playerIndex);
            this.checkAllPlayersFinished();
        } else if (playerValue === 21) {
            this.standPlayer(playerIndex);
        }
    }

    standPlayer(playerIndex) {
        if (!this.gameInProgress || playerIndex >= this.numPlayers) return;

        const player = this.players[playerIndex];
        if (player.bet === 0) return;

        player.isFinished = true;
        this.hidePlayerControls(playerIndex);
        this.checkAllPlayersFinished();
    }

    doubleDownPlayer(playerIndex) {
        if (!this.gameInProgress || playerIndex >= this.numPlayers) return;

        const player = this.players[playerIndex];
        if (player.hand.length !== 2 || player.isFinished || player.bet === 0) return;
        if (player.money < player.bet) return;

        player.money -= player.bet;
        player.bet *= 2;
        this.hitPlayer(playerIndex);

        if (!player.isBust && !player.isFinished) {
            setTimeout(() => {
                this.standPlayer(playerIndex);
            }, 1000);
        }
    }

    hidePlayerControls(playerIndex) {
        const controlsDiv = document.getElementById(`player-${playerIndex}-controls`);
        if (controlsDiv) {
            controlsDiv.style.display = 'none';
        }
    }

    checkAllPlayersFinished() {
        const activePlayers = this.players.filter(p => p.bet > 0);
        const allFinished = activePlayers.every(p => p.isFinished);

        if (allFinished) {
            this.dealerHidden = false;
            this.updateDisplay();
            document.getElementById('game-message').textContent = "All players finished. Dealer's turn...";

            setTimeout(() => {
                this.dealerPlay();
            }, 1000);
        }
    }

    // Keep old methods for backward compatibility but redirect to new ones
    hit() {
        // Deprecated - use hitPlayer instead
    }

    stand() {
        // Deprecated - use standPlayer instead
    }

    nextPlayer() {
        this.currentPlayerIndex++;

        if (this.currentPlayerIndex >= this.numPlayers) {
            // All players finished, dealer's turn
            this.dealerHidden = false;
            this.updateDisplay();
            document.getElementById('game-message').textContent = "Dealer's turn...";

            setTimeout(() => {
                this.dealerPlay();
            }, 1000);
        } else {
            this.highlightCurrentPlayer();
            document.getElementById('game-message').textContent = `${this.players[this.currentPlayerIndex].name}'s turn`;
        }

        this.updateGameControls();
    }

    doubleDown() {
        if (!this.gameInProgress || this.currentPlayerIndex >= this.numPlayers) return;

        const currentPlayer = this.players[this.currentPlayerIndex];
        if (currentPlayer.hand.length !== 2 || currentPlayer.isFinished) return;
        if (this.money < currentPlayer.bet) return;

        this.money -= currentPlayer.bet;
        currentPlayer.bet *= 2;
        this.hit();

        if (!currentPlayer.isBust && !currentPlayer.isFinished) {
            setTimeout(() => {
                this.stand();
            }, 1000);
        }
    }

    dealerPlay() {
        const dealerValue = this.calculateHandValue(this.dealerHand);

        if (dealerValue < 17) {
            this.dealerHand.push(this.deck.pop());
            this.updateDisplay();
            setTimeout(() => {
                this.dealerPlay();
            }, 1000);
        } else {
            this.determineWinners();
        }
    }

    checkForBlackjacks() {
        const dealerBlackjack = this.calculateHandValue(this.dealerHand) === 21;

        for (let i = 0; i < this.players.length; i++) {
            const player = this.players[i];
            if (player.bet > 0) {
                const playerBlackjack = this.calculateHandValue(player.hand) === 21;
                if (playerBlackjack) {
                    player.isFinished = true;
                    this.hidePlayerControls(i);
                }
            }
        }

        // Check if all active players are finished (either blackjack or no bet)
        const activePlayers = this.players.filter(p => p.bet > 0);
        const allFinished = activePlayers.every(p => p.isFinished);

        if (allFinished || dealerBlackjack) {
            this.dealerHidden = false;
            this.updateDisplay();
            setTimeout(() => {
                this.determineWinners();
            }, 1000);
        } else {
            document.getElementById('game-message').textContent = "Players can Hit, Stand, or Double Down!";
        }
    }

    findNextPlayer() {
        while (this.currentPlayerIndex < this.numPlayers && this.players[this.currentPlayerIndex].isFinished) {
            this.currentPlayerIndex++;
        }

        if (this.currentPlayerIndex >= this.numPlayers) {
            this.dealerHidden = false;
            this.updateDisplay();
            setTimeout(() => {
                this.dealerPlay();
            }, 1000);
        } else {
            this.highlightCurrentPlayer();
            document.getElementById('game-message').textContent = `${this.players[this.currentPlayerIndex].name}'s turn`;
        }
    }

    determineWinners() {
        const dealerValue = this.calculateHandValue(this.dealerHand);
        let totalWinnings = 0;
        let gameResults = [];

        for (let i = 0; i < this.players.length; i++) {
            const player = this.players[i];
            const playerValue = this.calculateHandValue(player.hand);

            if (player.bet === 0) {
                // Skip players who didn't bet
                continue;
            }

            if (player.isBust) {
                gameResults.push(`${player.name}: Bust (Lost $${player.bet})`);
                player.losses++;
                player.winnings = 0;
            } else if (dealerValue > 21) {
                const isBlackjack = player.hand.length === 2 && playerValue === 21;
                const payout = isBlackjack ? Math.floor(player.bet * 2.5) : player.bet * 2;
                totalWinnings += payout;
                gameResults.push(`${player.name}: Dealer busts! (Won $${payout})`);
                player.wins++;
                player.winnings = payout;
            } else if (playerValue > dealerValue) {
                const isBlackjack = player.hand.length === 2 && playerValue === 21;
                const payout = isBlackjack ? Math.floor(player.bet * 2.5) : player.bet * 2;
                totalWinnings += payout;
                gameResults.push(`${player.name}: Win! (Won $${payout})`);
                player.wins++;
                player.winnings = payout;
            } else if (playerValue === dealerValue) {
                totalWinnings += player.bet;
                gameResults.push(`${player.name}: Push (Returned $${player.bet})`);
                player.winnings = player.bet;
            } else {
                gameResults.push(`${player.name}: Lose (Lost $${player.bet})`);
                player.losses++;
                player.winnings = 0;
            }
        }

        this.endGame(gameResults.join(' | '), totalWinnings);
    }

    distributeWinnings(totalWinnings) {
        for (let player of this.players) {
            if (player.winnings !== undefined) {
                player.money += player.winnings;
                delete player.winnings; // Clean up temporary property
            }
        }
    }

    endGame(message, winnings = 0) {
        this.gameInProgress = false;
        this.dealerHidden = false;
        this.currentPlayerIndex = 0;

        // Distribute winnings to individual players and reset bets
        this.distributeWinnings(winnings);

        // Reset all player bets
        for (let player of this.players) {
            player.bet = 0;
        }

        // Remove all player highlights
        document.querySelectorAll('.player-section').forEach(section => {
            section.classList.remove('current-player');
        });

        // Show betting controls again
        this.showBettingControls();

        document.getElementById('game-message').textContent = message;
        this.updateDisplay();
        this.updateGameControls();
    }

    createCardElement(card, isHidden = false) {
        const cardDiv = document.createElement('div');
        cardDiv.className = 'card';

        if (isHidden) {
            cardDiv.className += ' hidden-card';
            cardDiv.innerHTML = '<div class="center">?</div>';
        } else {
            const isRed = card.suit === '♥' || card.suit === '♦';
            if (isRed) cardDiv.className += ' red';

            cardDiv.innerHTML = `
                <div class="top">${card.rank}<br>${card.suit}</div>
                <div class="center">${card.suit}</div>
                <div class="bottom">${card.rank}<br>${card.suit}</div>
            `;
        }

        return cardDiv;
    }

    updateDisplay() {

        // Update dealer display
        const dealerCardsDiv = document.getElementById('dealer-cards');
        dealerCardsDiv.innerHTML = '';
        this.dealerHand.forEach((card, index) => {
            const isHidden = this.dealerHidden && index === 1;
            dealerCardsDiv.appendChild(this.createCardElement(card, isHidden));
        });

        let dealerScore;
        if (this.dealerHidden && this.dealerHand.length > 0) {
            dealerScore = this.calculateHandValue([this.dealerHand[0]]);
        } else {
            dealerScore = this.calculateHandValue(this.dealerHand);
        }
        document.getElementById('dealer-score').textContent = dealerScore;

        // Update each player's display
        for (let i = 0; i < this.numPlayers; i++) {
            const player = this.players[i];
            const playerCardsDiv = document.getElementById(`player-${i}-cards`);
            const playerScoreSpan = document.getElementById(`player-${i}-score`);
            const playerBetSpan = document.getElementById(`player-${i}-bet`);
            const playerMoneySpan = document.getElementById(`player-${i}-money`);
            const playerWinsSpan = document.getElementById(`player-${i}-wins`);
            const playerLossesSpan = document.getElementById(`player-${i}-losses`);

            if (playerCardsDiv && playerScoreSpan && playerBetSpan && playerMoneySpan && playerWinsSpan && playerLossesSpan) {
                playerCardsDiv.innerHTML = '';
                player.hand.forEach(card => {
                    playerCardsDiv.appendChild(this.createCardElement(card));
                });

                const playerScore = this.calculateHandValue(player.hand);
                playerScoreSpan.textContent = playerScore;
                playerBetSpan.textContent = player.bet;
                playerMoneySpan.textContent = player.money;
                playerWinsSpan.textContent = player.wins;
                playerLossesSpan.textContent = player.losses;
            }
        }

        if (!this.gameInProgress && this.getTotalBet() === 0) {
            document.getElementById('game-message').textContent = 'Place your bets to start!';
        }
    }

    updateGameControls() {
        const dealBtn = document.getElementById('deal-btn');
        dealBtn.disabled = this.gameInProgress || this.getTotalBet() === 0;
    }
}

const game = new BlackjackGame();

function placeBet(amount) {
    game.placeBet(amount);
}

function resetBet() {
    game.resetBet();
}

function startGame() {
    game.startGame();
}

function hit() {
    game.hit();
}

function stand() {
    game.stand();
}

function doubleDown() {
    game.doubleDown();
}

function placeCustomBet() {
    const amount = parseInt(document.getElementById('custom-amount').value);
    if (amount && amount > 0) {
        game.placeBet(amount);
        document.getElementById('custom-amount').value = '';
    }
}

function betAllMoney() {
    game.betAllMoney();
}

function setPlayerCount(count) {
    game.setPlayerCount(count);
}
</script>

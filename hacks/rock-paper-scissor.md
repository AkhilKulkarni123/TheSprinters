---
title: Rock paper Scissors
comments: true
hide: true
layout: opencs
description: Learn how to experiment with the console, elements, and see OOP in action while playing Rock paper Scissors!
permalink: /rps2/
---

<style>
body {
  background-image: url('{{site.baseurl}}/images/SB1.png');
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
}
</style>


<div id="mainGameBox" style="max-width:700px;margin:64px auto 48px auto;position:relative;z-index:2;">
  <div id="gameContainer">
    <canvas id='gameCanvas' style="display:none"></canvas>
  </div>
</div>

<script type="module">
  // --- UI (purple box) ---
    const instructionsStyle = `
  position: relative;
  margin: 64px auto 48px auto;
    background: linear-gradient(135deg, black, red);
    color: white;
    padding: 30px;
    border-radius: 15px;
    z-index: 1000;
    max-width: 600px;
    width: 90%;
    max-height: 80vh;      /* added */
    overflow-y: auto;      /* added */
    font-family: 'Press Start 2P', cursive;
    border: 3px solid blue;
    box-shadow: 0 0 20px rgba(0, 0, 255, 0.5);
    text-align: center;
    `;

  const instructionsHTML = `
    <h2 style="color: blue; margin-bottom: 20px;">Rock Paper Scissors SHOOT!</h2>
    <div style="margin-bottom: 20px;">
      <p>Play the game from your browser console!</p>
      <p>Type <code>playRPS("rock")</code>, <code>playRPS("paper")</code>, or <code>playRPS("scissors")</code></p>
    </div>
    <div id="images" style="display:flex; justify-content:center; gap:20px; margin-bottom:14px;">
      <button id="rock-btn" style="background:none; border:none; padding:0; cursor:pointer;">
        <img id="rock-img" src="{{site.baseurl}}/images/rps/rock.jpg"
             style="width:100px; border:2px solid blue; border-radius:10px; box-shadow: 0 0 10px blue;">
      </button>
      <button id="paper-btn" style="background:none; border:none; padding:0; cursor:pointer;">
        <img id="paper-img" src="{{site.baseurl}}/images/rps/paper.jpeg"
             style="width:100px; border:2px solid blue; border-radius:10px; box-shadow: 0 0 10px blue;">
      </button>
      <button id="scissors-btn" style="background:none; border:none; padding:0; cursor:pointer;">
        <img id="scissors-img" src="{{site.baseurl}}/images/rps/scissors.jpeg"
             style="width:100px; border:2px solid blue; border-radius:10px; box-shadow: 0 0 10px blue;">
      </button>
    </div>
    <div style="margin-bottom:18px; font-size:1.1em; color:#ffd700;">
      Click any icon to customize using the console!
    </div>
    <!-- mount battle canvas INSIDE the purple box so you can see it -->
    <div id="battleMount" style="display:block; margin:12px auto;"></div>

    <div id="resultBox" style="margin-top: 16px; font-size: 16px; color: yellow;"></div>
  `;
  const container = document.createElement("div");
  container.setAttribute("style", instructionsStyle);
  container.innerHTML = instructionsHTML;
  document.getElementById("mainGameBox").appendChild(container);

  // Add PvP button in bottom right corner
  const pvpButton = document.createElement("button");
  pvpButton.innerHTML = "Player vs Player";
  pvpButton.style.cssText = `
    position: fixed;
    bottom: 20px;
    right: 20px;
    background: linear-gradient(135deg, #4a90e2, #007acc);
    color: white;
    padding: 12px 20px;
    border: none;
    border-radius: 8px;
    font-family: 'Press Start 2P', cursive;
    font-size: 12px;
    cursor: pointer;
    z-index: 9999;
    box-shadow: 0 4px 12px rgba(0, 122, 204, 0.3);
    transition: all 0.3s ease;
  `;
  pvpButton.addEventListener('mouseover', () => {
    pvpButton.style.background = 'linear-gradient(135deg, #5aa3f5, #0086e6)';
    pvpButton.style.transform = 'translateY(-2px)';
  });
  pvpButton.addEventListener('mouseout', () => {
    pvpButton.style.background = 'linear-gradient(135deg, #4a90e2, #007acc)';
    pvpButton.style.transform = 'translateY(0)';
  });
  document.body.appendChild(pvpButton);

  // --- helper: highlight chosen image ---
  function highlightImage(id){
    ["rock-img","paper-img","scissors-img"].forEach(i=>{
      const el = document.getElementById(i);
      if(el) el.style.boxShadow = "";
    });
    const picked = document.getElementById(id);
    if(picked) picked.style.boxShadow = "0 0 30px 10px gold";
  }

  // --- OOP classes ---
  class BattleBackground {
    constructor(image, width, height, speedRatio=0.1){
      this.image = image;
      this.width = width;
      this.height = height;
      this.x = 0; this.y = 0;
      this.speed = 2 * speedRatio;
    }
    update(){ this.x = (this.x - this.speed) % this.width; }
    draw(ctx){
      if(!this.image.complete || this.image.naturalWidth===0) return;
      ctx.drawImage(this.image, this.x, this.y, this.width, this.height);
      ctx.drawImage(this.image, this.x + this.width, this.y, this.width, this.height);
    }
  }

  class BattleSprite {
    constructor(image, width, height, x, y){
      this.image = image;
      this.width = width; this.height = height;
      this.homeX = x; this.homeY = y;
      this.x = x; this.y = y;
      this.targetX = x; this.targetY = y;
      this.opacity = 1; this.scale = 1; this.rotation = 0;
      this.animating = false;
    }
    update(){
      if(this.animating){
        this.x += (this.targetX - this.x)*0.12;
        this.y += (this.targetY - this.y)*0.12;
      } else {
        // drift gently back to home
        this.x += (this.homeX - this.x)*0.08;
        this.y += (this.homeY - this.y)*0.08;
      }
    }
    draw(ctx){
      if(!this.image.complete || this.image.naturalWidth===0) return;
      ctx.save();
      ctx.globalAlpha = this.opacity;
      ctx.translate(this.x + this.width/2, this.y + this.height/2);
      ctx.rotate(this.rotation);
      ctx.scale(this.scale, this.scale);
      ctx.drawImage(this.image, -this.width/2, -this.height/2, this.width, this.height);
      ctx.restore();
    }
    resetVisuals(){
      this.opacity = 1; this.scale = 1; this.rotation = 0;
    }
    resetPosition(){
      this.x = this.homeX; this.y = this.homeY;
      this.targetX = this.homeX; this.targetY = this.homeY;
      this.animating = false;
    }
  }

  // --- Canvas mounted inside purple box ---
  const battleCanvas = document.createElement('canvas');
  battleCanvas.width = 360;
  battleCanvas.height = 180;
  battleCanvas.style.display = 'block';
  battleCanvas.style.margin = '0 auto';
  battleCanvas.style.background = '#111';
  battleCanvas.style.borderRadius = '12px';
  battleCanvas.style.boxShadow = '0 2px 12px rgba(0,0,0,0.18)';
  document.getElementById('battleMount').appendChild(battleCanvas);
  const ctx = battleCanvas.getContext('2d');

  // --- assets ---
  const bgImage = new Image();
  bgImage.src = '{{site.baseurl}}/images/sunlay.png';

  const rockImg = new Image();
  rockImg.src = '{{site.baseurl}}/images/rps/rock.jpg';
  const paperImg = new Image();
  paperImg.src = '{{site.baseurl}}/images/rps/paper.jpeg';
  const scissorsImg = new Image();
  scissorsImg.src = '{{site.baseurl}}/images/rps/scissors.jpeg';

  const bg = new BattleBackground(bgImage, battleCanvas.width, battleCanvas.height, 0.12);

  const sprites = {
  rock:     new BattleSprite(rockImg,     96, 96,  10, 42),
  paper:    new BattleSprite(paperImg,    96, 96, 132, 42),
  scissors: new BattleSprite(scissorsImg, 96, 96, 254, 42)
  };

  function resetAll(){
    Object.values(sprites).forEach(s=>{
      s.resetVisuals();
    });
    sprites.rock.x = 10; sprites.rock.y = 42; sprites.rock.targetX = 10; sprites.rock.targetY = 42; sprites.rock.homeX = 10; sprites.rock.homeY = 42;
    sprites.paper.x = 132; sprites.paper.y = 42; sprites.paper.targetX = 132; sprites.paper.targetY = 42; sprites.paper.homeX = 132; sprites.paper.homeY = 42;
    sprites.scissors.x = 254; sprites.scissors.y = 42; sprites.scissors.targetX = 254; sprites.scissors.targetY = 42; sprites.scissors.homeX = 254; sprites.scissors.homeY = 42;
  }

  // --- global battle state, rendered by a continuous loop ---
  const battle = {
    active: false,
    winner: null,
    loser: null,
    frames: 0,
    max: 120,
    tie: null
  };

  function startBattle(winner, loser){
    battle.active = true;
    battle.tie = null;
    battle.winner = winner;
    battle.loser = loser;
    battle.frames = 0;

    // set targets for "winner moves toward loser"
    sprites[winner].animating = true;
    sprites[winner].targetX = sprites[loser].homeX;
    sprites[winner].targetY = sprites[loser].homeY;

    // loser will fade/scale/rotate in the render loop
    sprites[loser].animating = false; // stays put, gets affected visually
  }

  function startTie(choice){
    battle.active = true;
    battle.tie = choice;
    battle.winner = null;
    battle.loser = null;
    battle.frames = 0;

    // small wiggle, no target move
    Object.values(sprites).forEach(s=>{ s.animating = false; });
  }

  // --- continuous render loop (always runs) ---
  function render(){
  ctx.clearRect(0,0,battleCanvas.width,battleCanvas.height);
  bg.update();  bg.draw(ctx);
  // Draw 'Animated Battle: OOP' text (smaller)
  ctx.save();
  ctx.font = "bold 14px 'Press Start 2P', cursive";
  ctx.fillStyle = "cyan";
  ctx.textAlign = "center";
  ctx.fillText("Animated Battle: OOP", battleCanvas.width/2, 24);
  ctx.restore();

    if(battle.active){
      const t = battle.frames / battle.max; // 0..1

      if(battle.tie){
        const wobble = Math.sin(battle.frames*0.3)*4;
        sprites[battle.tie].rotation = wobble * Math.PI/180;
      } else {
        // winner punch-in / pulse
        const w = sprites[battle.winner];
        const l = sprites[battle.loser];

        // winner pulse scale up then down
        const pulse = (battle.frames < battle.max/2)
          ? 1 + (battle.frames/(battle.max/2))*0.2
          : 1.2 - ((battle.frames - battle.max/2)/(battle.max/2))*0.2;
        w.scale = pulse;

        // loser fades & shrinks
        l.opacity = Math.max(0.15, 1 - t*0.85);
        l.scale   = Math.max(0.6, 1 - t*0.4);

        // matchup-specific flair
        if(battle.winner === "rock" && battle.loser === "scissors"){
          l.rotation = -t * (Math.PI/4);
        }
        if(battle.winner === "paper" && battle.loser === "rock"){
          // paper "covers" rock by moving slightly past center
          w.targetX = l.homeX - 6; w.targetY = l.homeY - 6;
        }
        if(battle.winner === "scissors" && battle.loser === "paper"){
          w.rotation =  t * (Math.PI/10);
          l.rotation = -t * (Math.PI/10);
        }
      }

      battle.frames++;
      if(battle.frames >= battle.max){
        battle.active = false;
        Object.values(sprites).forEach(s=>{ s.resetVisuals(); s.animating = false; });
      }
    }

    // update/draw sprites every frame
    Object.values(sprites).forEach(s=>{ s.update(); s.draw(ctx); });

    requestAnimationFrame(render);
  }
  render(); // kick off the engine once

  // --- PvP game state ---
  const pvpState = {
    active: false,
    currentPlayer: 1,
    player1Choice: null,
    player2Choice: null,
    player1Score: 0,
    player2Score: 0
  };

  function togglePvPMode() {
    pvpState.active = !pvpState.active;
    if (pvpState.active) {
      pvpButton.innerHTML = "Exit PvP";
      pvpButton.style.background = 'linear-gradient(135deg, #e74c3c, #c0392b)';
      resetPvPGame();
      updatePvPStatus();
    } else {
      pvpButton.innerHTML = "Player vs Player";
      pvpButton.style.background = 'linear-gradient(135deg, #4a90e2, #007acc)';
      document.getElementById("resultBox").innerHTML = "";
    }
  }

  function resetPvPGame() {
    pvpState.currentPlayer = 1;
    pvpState.player1Choice = null;
    pvpState.player2Choice = null;
  }

  function updatePvPStatus() {
    if (!pvpState.active) return;

    if (!pvpState.player1Choice) {
      document.getElementById("resultBox").innerHTML = `
        <h3 style="color: cyan;">Player 1's Turn</h3>
        <p>Choose your move!</p>
        <p>Score: Player 1: ${pvpState.player1Score} | Player 2: ${pvpState.player2Score}</p>
      `;
    } else if (!pvpState.player2Choice) {
      document.getElementById("resultBox").innerHTML = `
        <h3 style="color: cyan;">Player 2's Turn</h3>
        <p>Choose your move!</p>
        <p>Score: Player 1: ${pvpState.player1Score} | Player 2: ${pvpState.player2Score}</p>
      `;
    }
  }

  function processPvPRound() {
    if (!pvpState.player1Choice || !pvpState.player2Choice) return;

    let resultText, winner = null, loser = null;
    const p1 = pvpState.player1Choice;
    const p2 = pvpState.player2Choice;

    if (p1 === p2) {
      resultText = "It's a tie!";
      startTie(p1);
    } else if (
      (p1 === "rock" && p2 === "scissors") ||
      (p1 === "paper" && p2 === "rock") ||
      (p1 === "scissors" && p2 === "paper")
    ) {
      resultText = "Player 1 Wins!";
      pvpState.player1Score++;
      winner = p1;
      loser = p2;
    } else {
      resultText = "Player 2 Wins!";
      pvpState.player2Score++;
      winner = p2;
      loser = p1;
    }

    document.getElementById("resultBox").innerHTML = `
      <p>Player 1 chose: <b>${p1.toUpperCase()}</b></p>
      <p>Player 2 chose: <b>${p2.toUpperCase()}</b></p>
      <h3 style="color: cyan;">${resultText}</h3>
      <p>Score: Player 1: ${pvpState.player1Score} | Player 2: ${pvpState.player2Score}</p>
      <p style="color: yellow; font-size: 12px;">Click any choice to start next round</p>
    `;

    if (winner && loser) startBattle(winner, loser);

    // Reset for next round
    setTimeout(() => {
      resetPvPGame();
      updatePvPStatus();
    }, 3000);
  }

  pvpButton.addEventListener('click', togglePvPMode);

  // --- game logic + console entry point ---
  window.playRPS = function(playerChoice){
    const choices = ["rock","paper","scissors"];
    if(!choices.includes(playerChoice)){
      console.log("Invalid choice. Use 'rock', 'paper', or 'scissors'.");
      return;
    }
    highlightImage(playerChoice+"-img");

    // Handle PvP mode
    if (pvpState.active) {
      if (!pvpState.player1Choice) {
        pvpState.player1Choice = playerChoice;
        updatePvPStatus();
        console.log(`Player 1 chose: ${playerChoice.toUpperCase()}`);
        return;
      } else if (!pvpState.player2Choice) {
        pvpState.player2Choice = playerChoice;
        console.log(`Player 2 chose: ${playerChoice.toUpperCase()}`);
        processPvPRound();
        return;
      }
    }

    // Original vs Computer logic
    const computerChoice = choices[Math.floor(Math.random()*choices.length)];
    let resultText, winner=null, loser=null;

    if(playerChoice === computerChoice){
      resultText = "Tie!";
      startTie(playerChoice);
    } else if(
      (playerChoice==="rock" && computerChoice==="scissors") ||
      (playerChoice==="paper" && computerChoice==="rock") ||
      (playerChoice==="scissors" && computerChoice==="paper")
    ){
      resultText = "You Win!";
      winner = playerChoice; loser = computerChoice;
    } else {
      resultText = "You Lose!";
      winner = computerChoice; loser = playerChoice;
    }

    document.getElementById("resultBox").innerHTML = `
      <p>You chose: <b>${playerChoice.toUpperCase()}</b></p>
      <p>Computer chose: <b>${computerChoice.toUpperCase()}</b></p>
      <h3 style="color: cyan;">${resultText}</h3>
    `;

    if(winner && loser) startBattle(winner, loser);

    console.log(`You chose: ${playerChoice.toUpperCase()}`);
    console.log(`Computer chose: ${computerChoice.toUpperCase()}`);
    console.log(`Result: ${resultText}`);
  };

  class GameObject {
    constructor(id) {
      this.el = document.getElementById(id);
      if (!this.el) throw new Error(`Element #${id} not found`);
    }

    rotate(deg) {
      this.el.style.transform = `rotate(${deg}deg)`;
      return this;
    }

    setBorder(style) {
      this.el.style.border = style;
      return this;
    }

    setWidth(px) {
      this.el.style.width = `${px}px`;
      return this;
    }

    setColor(color) {
      this.el.style.backgroundColor = color;
      return this;
    }

    reset() {
      this.el.style.transform = "";
      this.el.style.border = "";
      this.el.style.width = "";
      this.el.style.backgroundColor = "";
      return this;
    }
  }

  // --- Specialized classes (extend GameObject) ---
  class Rock extends GameObject {
    constructor() { super("rock-img"); }
  }

  class Paper extends GameObject {
    constructor() { super("paper-img"); }
  }

  class Scissors extends GameObject {
    constructor() { super("scissors-img"); }
  }

  // --- Instances (global) ---
  const rock = new Rock();
  const paper = new Paper();
  const scissors = new Scissors();

  window.rock = rock;
  window.paper = paper;
  window.scissors = scissors;

  // --- Button click handlers for direct gameplay ---
  document.getElementById("rock-btn").addEventListener("click", () => {
    playRPS("rock");
  });
  document.getElementById("paper-btn").addEventListener("click", () => {
    playRPS("paper");
  });
  document.getElementById("scissors-btn").addEventListener("click", () => {
    playRPS("scissors");
  });
</script>

---
layout: default
title: 3D Slope Game
permalink: /slope1
---

<style>
    body{
        background-image: url('images/SB1.png');
        background-size: cover;
        background-position: center;
        background-repeat: no-repeat;
        background-attachment: fixed;
        margin: 0;
        overflow: hidden;
    }
    .wrap{
        margin-left: auto;
        margin-right: auto;
    }

    #gameCanvas{
        display: none;
        border-style: solid;
        border-width: 10px;
        border-color: #00FF00;
        box-shadow: 0 0 20px #00FF00, 0 0 40px #00FF00, 0 0 60px #00FF00;
        animation: borderGlow 2s ease-in-out infinite alternate;
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        z-index: 500;
    }

    #gameCanvas canvas{
        display: block;
        margin: 0 auto;
    }

    @keyframes borderGlow {
        from {
            box-shadow: 0 0 20px #00FF00, 0 0 40px #00FF00, 0 0 60px #00FF00;
        }
        to {
            box-shadow: 0 0 30px #66FF66, 0 0 60px #66FF66, 0 0 90px #66FF66;
        }
    }
    #gameCanvas:focus{
        outline: none;
    }

    /* All screens style */
    #gameover p, #setting p, #menu p{
        font-size: 20px;
    }

    #gameover a, #setting a, #menu a{
        font-size: 30px;
        display: block;
    }

    #gameover a:hover, #setting a:hover, #menu a:hover{
        cursor: pointer;
    }

    #gameover a:hover::before, #setting a:hover::before, #menu a:hover::before{
        content: ">";
        margin-right: 10px;
    }

    #menu{
        display: block;
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: rgba(0, 0, 0, 0.95);
        padding: 40px 60px;
        border-radius: 20px;
        border: 3px solid #00FF00;
        box-shadow: 0 0 30px rgba(0, 255, 0, 0.5), 0 0 60px rgba(0, 255, 0, 0.3);
        z-index: 1000;
        min-width: 500px;
        text-align: center;
    }

    #menu p:first-child{
        font-size: 42px;
        font-weight: bold;
        color: #00FF00;
        text-shadow: 0 0 20px #00FF00;
        margin-bottom: 30px;
    }

    #gameover{
        display: none;
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: rgba(0, 0, 0, 0.95);
        padding: 40px 60px;
        border-radius: 20px;
        border: 3px solid #00FF00;
        box-shadow: 0 0 30px rgba(0, 255, 0, 0.5), 0 0 60px rgba(0, 255, 0, 0.3);
        z-index: 1000;
        min-width: 400px;
        text-align: center;
    }

    #gameover p{
        font-size: 24px;
        margin: 15px 0;
        color: #FFFFFF;
    }

    #gameover p:first-child{
        font-size: 48px;
        font-weight: bold;
        color: #FF0000;
        text-shadow: 0 0 20px #FF0000;
        margin-bottom: 30px;
    }

    #setting{
        display: none;
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: rgba(0, 0, 0, 0.95);
        padding: 40px 60px;
        border-radius: 20px;
        border: 3px solid #00FF00;
        box-shadow: 0 0 30px rgba(0, 255, 0, 0.5);
        z-index: 1000;
        min-width: 400px;
    }

    #setting input{
        display:none;
    }

    #setting label{
        cursor: pointer;
    }

    #setting input:checked + label{
        background-color: #FFF;
        color: #000;
    }
</style>

<h2>3D Slope Runner 🎮</h2>
<div class="container">
    <p class="fs-4">Distance: <span id="score_value">0</span> | High Score: <span id="high_score">0</span></p>
    <div class="container bg-secondary" style="text-align:center;">
        <!-- Main Menu -->
        <div id="menu" class="py-4 text-light">
            <p>3D SLOPE RUNNER 🎮</p>
            <p style="font-size: 20px; color: #FFFFFF; margin: 20px 0;">Press <span style="background-color: #FFFFFF; color: #000000; padding: 5px 10px; border-radius: 5px;">SPACE</span> to begin</p>
            <p style="font-size: 18px; color: #AAAAAA; margin: 15px 0;">Use <span style="background-color: #333333; color: #FFFFFF; padding: 5px 10px; border-radius: 5px;">← →</span> or <span style="background-color: #333333; color: #FFFFFF; padding: 5px 10px; border-radius: 5px;">A / D</span> to move</p>
            <p style="color: #FF4444; font-size: 18px; margin-top: 25px;">⚠ Avoid the red obstacles!</p>
            <p style="color: #FFAA00; font-size: 16px;">Speed increases over time!</p>
            <a id="new_game" class="link-alert" style="margin-top: 30px;">new game</a>
            <a id="setting_menu" class="link-alert">settings</a>
        </div>
        <!-- Game Over -->
        <div id="gameover" class="py-4 text-light">
            <p>GAME OVER</p>
            <p style="font-size: 32px; color: #00FF00;">Score: <span id="final_score">0</span></p>
            <p style="font-size: 20px; color: #AAAAAA;">Press <span style="background-color: #FFFFFF; color: #000000; padding: 5px 10px; border-radius: 5px;">SPACE</span> to try again</p>
            <a id="new_game1" class="link-alert" style="margin-top: 20px;">new game</a>
            <a id="setting_menu1" class="link-alert">settings</a>
        </div>
        <!-- Play Screen -->
        <div id="gameCanvas" class="wrap" tabindex="1"></div>
        <!-- Settings Screen -->
        <div id="setting" class="py-4 text-light">
            <p>Settings Screen, press <span style="background-color: #FFFFFF; color: #000000">space</span> to go back to playing</p>
            <a id="new_game2" class="link-alert">new game</a>
            <br>
            <p>Difficulty:
                <input id="diff1" type="radio" name="difficulty" value="easy" checked/>
                <label for="diff1">Easy</label>
                <input id="diff2" type="radio" name="difficulty" value="normal"/>
                <label for="diff2">Normal</label>
                <input id="diff3" type="radio" name="difficulty" value="hard"/>
                <label for="diff3">Hard</label>
            </p>
            <p>Ball Color:
                <input id="color1" type="radio" name="ballcolor" value="#00ff00" checked/>
                <label for="color1">Green</label>
                <input id="color2" type="radio" name="ballcolor" value="#ff0000"/>
                <label for="color2">Red</label>
                <input id="color3" type="radio" name="ballcolor" value="#0000ff"/>
                <label for="color3">Blue</label>
                <input id="color4" type="radio" name="ballcolor" value="#ffff00"/>
                <label for="color4">Yellow</label>
            </p>
        </div>
    </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
    (function(){
        /* Attributes of Game */
        /////////////////////////////////////////////////////////////
        // Three.js Scene Setup
        let scene, camera, renderer, ball, ground;
        const container = document.getElementById("gameCanvas");

        // HTML Game IDs
        const SCREEN_SLOPE = 0;
        const screen_slope = document.getElementById("gameCanvas");
        const ele_score = document.getElementById("score_value");
        const ele_high_score = document.getElementById("high_score");
        const ele_final_score = document.getElementById("final_score");
        const difficulty_setting = document.getElementsByName("difficulty");
        const ballcolor_setting = document.getElementsByName("ballcolor");
        // HTML Screen IDs (div)
        const SCREEN_MENU = -1, SCREEN_GAME_OVER=1, SCREEN_SETTING=2;
        const screen_menu = document.getElementById("menu");
        const screen_game_over = document.getElementById("gameover");
        const screen_setting = document.getElementById("setting");
        // HTML Event IDs (a tags)
        const button_new_game = document.getElementById("new_game");
        const button_new_game1 = document.getElementById("new_game1");
        const button_new_game2 = document.getElementById("new_game2");
        const button_setting_menu = document.getElementById("setting_menu");
        const button_setting_menu1 = document.getElementById("setting_menu1");

        // Game Control
        let SCREEN = SCREEN_MENU;
        let ballColor = 0x00ff00;
        let obstacles = [];
        let score = 0;
        let highScore = 0;
        let gameSpeed = 0.05;
        let difficulty = "easy";
        let leftPressed = false;
        let rightPressed = false;
        let gameRunning = false;
        let ballVelocityX = 0;

        // Game constants
        const BALL_RADIUS = 0.5;
        const OBSTACLE_SIZE = 0.8;
        const OBSTACLE_SPACING = 3;
        const LANE_WIDTH = 1.5;
        const NUM_LANES = 5;

        /* Initialize 3D Scene */
        /////////////////////////////////////////////////////////////
        let init3DScene = function(){
            // Create scene
            scene = new THREE.Scene();
            scene.fog = new THREE.Fog(0x1a1a2e, 10, 50);

            // Create camera
            camera = new THREE.PerspectiveCamera(75, 800 / 600, 0.1, 1000);
            camera.position.set(0, 3, 5);
            camera.lookAt(0, 0, -5);

            // Create renderer
            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(800, 600);
            renderer.setClearColor(0x1a1a2e);
            container.appendChild(renderer.domElement);

            // Add lights
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
            scene.add(ambientLight);

            const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
            directionalLight.position.set(5, 10, 5);
            scene.add(directionalLight);

            // Create ground/track
            const groundGeometry = new THREE.PlaneGeometry(NUM_LANES * LANE_WIDTH, 100);
            const groundMaterial = new THREE.MeshStandardMaterial({
                color: 0x0f3460,
                roughness: 0.8,
                metalness: 0.2
            });
            ground = new THREE.Mesh(groundGeometry, groundMaterial);
            ground.rotation.x = -Math.PI / 2;
            ground.position.z = -25;
            scene.add(ground);

            // Add lane lines
            for(let i = 1; i < NUM_LANES; i++){
                const lineGeometry = new THREE.BoxGeometry(0.1, 0.1, 100);
                const lineMaterial = new THREE.MeshBasicMaterial({ color: 0x16213e });
                const line = new THREE.Mesh(lineGeometry, lineMaterial);
                line.position.x = (i * LANE_WIDTH) - (NUM_LANES * LANE_WIDTH / 2);
                line.position.z = -25;
                scene.add(line);
            }

            // Start render loop
            requestAnimationFrame(mainLoop);
        }

        /* Display Control */
        /////////////////////////////////////////////////////////////
        let showScreen = function(screen_opt){
            SCREEN = screen_opt;
            switch(screen_opt){
                case SCREEN_SLOPE:
                    screen_slope.style.display = "block";
                    screen_menu.style.display = "none";
                    screen_setting.style.display = "none";
                    screen_game_over.style.display = "none";
                    break;
                case SCREEN_GAME_OVER:
                    screen_slope.style.display = "block";
                    screen_menu.style.display = "none";
                    screen_setting.style.display = "none";
                    screen_game_over.style.display = "flex";
                    screen_game_over.style.flexDirection = "column";
                    screen_game_over.style.alignItems = "center";
                    break;
                case SCREEN_SETTING:
                    screen_slope.style.display = "none";
                    screen_menu.style.display = "none";
                    screen_setting.style.display = "flex";
                    screen_setting.style.flexDirection = "column";
                    screen_setting.style.alignItems = "center";
                    screen_game_over.style.display = "none";
                    break;
                case SCREEN_MENU:
                    screen_slope.style.display = "none";
                    screen_menu.style.display = "flex";
                    screen_menu.style.flexDirection = "column";
                    screen_menu.style.alignItems = "center";
                    screen_setting.style.display = "none";
                    screen_game_over.style.display = "none";
                    break;
            }
        }

        /* Actions and Events  */
        /////////////////////////////////////////////////////////////
        window.onload = function(){
            // Initialize 3D scene
            init3DScene();

            // HTML Events to Functions
            button_new_game.onclick = function(){newGame();};
            button_new_game1.onclick = function(){newGame();};
            button_new_game2.onclick = function(){newGame();};
            button_setting_menu.onclick = function(){showScreen(SCREEN_SETTING);};
            button_setting_menu1.onclick = function(){showScreen(SCREEN_SETTING);};

            // Difficulty setting
            for(let i = 0; i < difficulty_setting.length; i++){
                difficulty_setting[i].addEventListener("click", function(){
                    for(let i = 0; i < difficulty_setting.length; i++){
                        if(difficulty_setting[i].checked){
                            difficulty = difficulty_setting[i].value;
                        }
                    }
                });
            }

            // Ball color setting
            for(let i = 0; i < ballcolor_setting.length; i++){
                ballcolor_setting[i].addEventListener("click", function(){
                    for(let i = 0; i < ballcolor_setting.length; i++){
                        if(ballcolor_setting[i].checked){
                            ballColor = parseInt(ballcolor_setting[i].value.replace('#', '0x'));
                        }
                    }
                });
            }

            // Activate window events
            window.addEventListener("keydown", function(evt) {
                if(evt.code === "Space" && SCREEN !== SCREEN_SLOPE)
                    newGame();
                handleKeyDown(evt.keyCode);
            }, true);

            window.addEventListener("keyup", function(evt) {
                handleKeyUp(evt.keyCode);
            }, true);
        }

        /* Main Game Loop */
        /////////////////////////////////////////////////////////////
        let mainLoop = function(){
            if(!gameRunning) {
                if(renderer) renderer.render(scene, camera);
                requestAnimationFrame(mainLoop);
                return;
            }

            // Move ball based on key presses
            const ballSpeed = 0.25;
            if(leftPressed && ball.position.x > -(NUM_LANES * LANE_WIDTH / 2) + BALL_RADIUS){
                ballVelocityX = -ballSpeed;
            } else if(rightPressed && ball.position.x < (NUM_LANES * LANE_WIDTH / 2) - BALL_RADIUS){
                ballVelocityX = ballSpeed;
            } else {
                ballVelocityX *= 0.85; // Less deceleration for faster movement
            }

            ball.position.x += ballVelocityX;

            // Rotate ball for rolling effect - much faster
            ball.rotation.z -= ballVelocityX * 2.5;
            ball.rotation.x += gameSpeed * 10;

            // Move obstacles toward camera
            for(let i = obstacles.length - 1; i >= 0; i--){
                obstacles[i].position.z += gameSpeed;

                // Remove obstacles that passed the camera
                if(obstacles[i].position.z > 5){
                    scene.remove(obstacles[i]);
                    obstacles.splice(i, 1);
                    score += 10;
                    altScore(score);

                    // Increase speed over time
                    if(score % 100 === 0){
                        gameSpeed += 0.01;
                    }
                }
            }

            // Add new obstacles
            if(obstacles.length === 0 || obstacles[obstacles.length - 1].position.z > -OBSTACLE_SPACING){
                addObstacle();
            }

            // Check collisions
            for(let i = 0; i < obstacles.length; i++){
                if(checkCollision(ball, obstacles[i])){
                    gameOver();
                    return;
                }
            }

            // Render the scene
            renderer.render(scene, camera);

            // Continue loop
            requestAnimationFrame(mainLoop);
        }

        /* Create 3D Ball */
        /////////////////////////////////////////////////////////////
        let createBall = function(){
            const geometry = new THREE.SphereGeometry(BALL_RADIUS, 32, 32);
            const material = new THREE.MeshStandardMaterial({
                color: ballColor,
                roughness: 0.3,
                metalness: 0.7,
                emissive: ballColor,
                emissiveIntensity: 0.3
            });
            ball = new THREE.Mesh(geometry, material);
            ball.position.set(0, BALL_RADIUS, 0);
            ball.castShadow = true;
            scene.add(ball);
        }

        /* Add Obstacle */
        /////////////////////////////////////////////////////////////
        let addObstacle = function(){
            // Random obstacle type: 0 = boxes, 1 = ramp, 2 = tunnel
            const obstacleType = Math.random();

            if(obstacleType < 0.5){
                // Regular box obstacles
                let numObstacles = 2;

                // Increase obstacle count based on difficulty
                if(difficulty === "easy") numObstacles = Math.random() > 0.3 ? 2 : 1;
                if(difficulty === "normal") numObstacles = Math.random() > 0.3 ? 3 : 2;
                if(difficulty === "hard") numObstacles = Math.random() > 0.5 ? 4 : 3;

                let positions = [];
                for(let i = 0; i < numObstacles; i++){
                    let lane = Math.floor(Math.random() * NUM_LANES);

                    // Avoid duplicate lanes
                    while(positions.includes(lane)){
                        lane = Math.floor(Math.random() * NUM_LANES);
                    }
                    positions.push(lane);

                    // Create 3D obstacle
                    const geometry = new THREE.BoxGeometry(OBSTACLE_SIZE, OBSTACLE_SIZE, OBSTACLE_SIZE);
                    const material = new THREE.MeshStandardMaterial({
                        color: 0xff0000,
                        roughness: 0.5,
                        metalness: 0.5,
                        emissive: 0xff0000,
                        emissiveIntensity: 0.5
                    });
                    const obstacle = new THREE.Mesh(geometry, material);

                    // Position obstacle in lane
                    const laneX = (lane * LANE_WIDTH) - (NUM_LANES * LANE_WIDTH / 2) + (LANE_WIDTH / 2);
                    obstacle.position.set(laneX, OBSTACLE_SIZE / 2, -50);
                    obstacle.castShadow = true;
                    obstacle.userData.type = 'box';

                    scene.add(obstacle);
                    obstacles.push(obstacle);
                }
            } else if(obstacleType < 0.75){
                // Ramp obstacle
                const rampGeometry = new THREE.BoxGeometry(LANE_WIDTH * 2, 0.3, 3);
                const rampMaterial = new THREE.MeshStandardMaterial({
                    color: 0xff6600,
                    roughness: 0.7,
                    metalness: 0.3,
                    emissive: 0xff6600,
                    emissiveIntensity: 0.4
                });
                const ramp = new THREE.Mesh(rampGeometry, rampMaterial);

                const randomLane = Math.floor(Math.random() * (NUM_LANES - 1));
                const laneX = (randomLane * LANE_WIDTH) - (NUM_LANES * LANE_WIDTH / 2) + LANE_WIDTH;
                ramp.position.set(laneX, 0.5, -50);
                ramp.rotation.x = -0.3;
                ramp.castShadow = true;
                ramp.userData.type = 'ramp';

                scene.add(ramp);
                obstacles.push(ramp);
            } else {
                // Tunnel obstacle
                const tunnel = new THREE.Group();
                tunnel.userData.type = 'tunnel';

                // Create tunnel frame (you must stay inside)
                const tunnelMaterial = new THREE.MeshStandardMaterial({
                    color: 0x0088ff,
                    roughness: 0.5,
                    metalness: 0.7,
                    emissive: 0x0088ff,
                    emissiveIntensity: 0.5,
                    side: THREE.DoubleSide
                });

                // Left wall
                const leftWall = new THREE.Mesh(
                    new THREE.BoxGeometry(0.2, 1.5, 4),
                    tunnelMaterial
                );
                leftWall.position.set(-1.5, 0.75, 0);
                leftWall.userData.isWall = true;
                tunnel.add(leftWall);

                // Right wall
                const rightWall = new THREE.Mesh(
                    new THREE.BoxGeometry(0.2, 1.5, 4),
                    tunnelMaterial
                );
                rightWall.position.set(1.5, 0.75, 0);
                rightWall.userData.isWall = true;
                tunnel.add(rightWall);

                // Top
                const top = new THREE.Mesh(
                    new THREE.BoxGeometry(3, 0.2, 4),
                    tunnelMaterial
                );
                top.position.set(0, 1.5, 0);
                top.userData.isWall = true;
                tunnel.add(top);

                tunnel.position.set(0, 0, -50);
                scene.add(tunnel);
                obstacles.push(tunnel);
            }
        }

        /* Collision Detection */
        /////////////////////////////////////////////////////////////
        let checkCollision = function(ball, obstacle){
            if(obstacle.userData.type === 'tunnel'){
                // Check if ball hits tunnel walls
                const tunnelWorldPos = new THREE.Vector3();
                obstacle.getWorldPosition(tunnelWorldPos);

                const relativeX = ball.position.x - tunnelWorldPos.x;
                const relativeZ = ball.position.z - tunnelWorldPos.z;

                // Only check collision if ball is within tunnel's Z range
                if(Math.abs(relativeZ) < 2){
                    // Check if ball hits left or right walls
                    if(relativeX < -1.3 || relativeX > 1.3){
                        return true;
                    }
                    // Check if ball hits top
                    if(ball.position.y > 1.3){
                        return true;
                    }
                }
                return false;
            } else if(obstacle.userData.type === 'ramp'){
                // Ramps don't cause collision, just visual
                return false;
            } else {
                // Regular box collision
                const distance = ball.position.distanceTo(obstacle.position);
                return distance < (BALL_RADIUS + OBSTACLE_SIZE / 2);
            }
        }

        /* New Game Setup */
        /////////////////////////////////////////////////////////////
        let newGame = function(){
            showScreen(SCREEN_SLOPE);
            screen_slope.focus();

            // Reset game state
            score = 0;
            altScore(score);
            gameSpeed = difficulty === "easy" ? 0.05 : difficulty === "normal" ? 0.07 : 0.1;

            // Remove old obstacles
            for(let i = 0; i < obstacles.length; i++){
                scene.remove(obstacles[i]);
            }
            obstacles = [];

            // Remove old ball if exists
            if(ball){
                scene.remove(ball);
            }

            // Create new ball
            createBall();

            ballVelocityX = 0;
            gameRunning = true;
        }

        /* Key Inputs */
        /////////////////////////////////////////////////////////////
        let handleKeyDown = function(key){
            switch(key) {
                case 37:    // left arrow
                case 65:    // A key
                    leftPressed = true;
                    break;
                case 39:    // right arrow
                case 68:    // D key
                    rightPressed = true;
                    break;
            }
        }

        let handleKeyUp = function(key){
            switch(key) {
                case 37:    // left arrow
                case 65:    // A key
                    leftPressed = false;
                    break;
                case 39:    // right arrow
                case 68:    // D key
                    rightPressed = false;
                    break;
            }
        }

        /* Update Score */
        /////////////////////////////////////////////////////////////
        let altScore = function(score_val){
            ele_score.innerHTML = String(score_val);
        }

        /* Game Over */
        /////////////////////////////////////////////////////////////
        let gameOver = function(){
            gameRunning = false;
            ele_final_score.innerHTML = String(score);

            // Update high score
            if(score > highScore){
                highScore = score;
                ele_high_score.innerHTML = String(highScore);
            }

            showScreen(SCREEN_GAME_OVER);
        }
    })();
</script>

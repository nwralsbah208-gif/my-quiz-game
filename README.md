<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Level Devil – رتيرو قديم + مؤثرات صوتية + رسوم HD</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background: linear-gradient(135deg, #0a0a0a, #1a0a1a);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Courier New', monospace;
            overflow: hidden;
            touch-action: none;
        }

        /* تأثير خلفية متحركة */
        @keyframes bgPulse {
            0% { background: rgba(0,0,0,0.3); }
            100% { background: rgba(255,0,0,0.05); }
        }

        .game-wrapper {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 10px;
            width: 100%;
            max-width: 100%;
        }

        .game-container {
            background: #000000aa;
            border-radius: 20px;
            padding: 8px;
            box-shadow: 0 0 50px rgba(255,0,0,0.5);
            backdrop-filter: blur(5px);
            border: 1px solid rgba(255,0,0,0.3);
        }

        canvas {
            display: block;
            margin: 0 auto;
            box-shadow: 0 0 30px rgba(255,0,0,0.3);
            border-radius: 10px;
            width: 100%;
            height: auto;
            touch-action: none;
        }

        .info-panel {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 15px;
            color: #ff4444;
            text-shadow: 0 0 5px red;
            font-weight: bold;
            background: #1a0a1aaa;
            border-radius: 10px;
            margin: 10px 0;
            backdrop-filter: blur(5px);
            flex-wrap: wrap;
            gap: 8px;
        }

        .level-badge {
            font-size: 1.2rem;
            background: #330000;
            padding: 5px 12px;
            border-radius: 30px;
            border: 1px solid #ff0000;
            box-shadow: 0 0 10px rgba(255,0,0,0.5);
        }

        .deaths-badge {
            font-size: 1rem;
            font-family: monospace;
        }

        button {
            background: #330000;
            border: 1px solid #ff4444;
            color: #ff8888;
            padding: 5px 15px;
            border-radius: 20px;
            cursor: pointer;
            font-family: monospace;
            font-weight: bold;
            transition: all 0.3s;
            touch-action: manipulation;
        }

        button:active {
            background: #660000;
            color: white;
            box-shadow: 0 0 10px red;
        }

        .controls {
            display: flex;
            flex-direction: column;
            align-items: center;
            margin-top: 15px;
            gap: 12px;
        }

        .horizontal-controls {
            display: flex;
            justify-content: center;
            gap: 25px;
        }

        .ctrl-btn {
            width: 70px;
            height: 70px;
            background: rgba(30, 0, 0, 0.85);
            border: 2px solid #ff0000;
            border-radius: 50px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.5rem;
            font-weight: bold;
            color: #ff4444;
            text-shadow: 0 0 5px red;
            backdrop-filter: blur(8px);
            cursor: pointer;
            transition: all 0.05s linear;
            touch-action: manipulation;
            box-shadow: 0 0 15px rgba(255,0,0,0.3);
        }

        .ctrl-btn:active {
            background: #ff0000;
            color: white;
            transform: scale(0.92);
            box-shadow: 0 0 25px red;
        }

        .jump-btn {
            width: 100px;
            height: 70px;
            background: rgba(80, 0, 0, 0.9);
            border: 3px solid #ff6600;
            font-size: 1.6rem;
            font-weight: bold;
            color: #ffaa44;
        }

        .action-buttons {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 5px;
        }

        .action-btn {
            width: 80px;
            height: 50px;
            background: rgba(30, 0, 0, 0.85);
            border: 2px solid #ff4444;
            border-radius: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1rem;
            font-weight: bold;
            color: #ff8888;
            backdrop-filter: blur(8px);
            touch-action: manipulation;
        }

        .action-btn:active {
            background: #660000;
            transform: scale(0.95);
        }

        .whisper {
            margin-top: 12px;
            color: #ff000033;
            font-size: 10px;
            font-style: italic;
            text-align: center;
            transition: all 0.3s;
        }

        @keyframes shake {
            0%,100% { transform: translateX(0); }
            10%,30%,50%,70%,90% { transform: translateX(-3px); }
            20%,40%,60%,80% { transform: translateX(3px); }
        }

        .shake-effect {
            animation: shake 0.2s ease-in-out 0s 2;
        }

        @media (max-width: 600px) {
            .ctrl-btn { width: 60px; height: 60px; font-size: 2rem; }
            .jump-btn { width: 85px; height: 60px; font-size: 1.3rem; }
            .action-btn { width: 70px; height: 45px; font-size: 0.85rem; }
        }

        /* مؤشر الصوت */
        .sound-indicator {
            position: fixed;
            bottom: 10px;
            left: 10px;
            background: rgba(0,0,0,0.5);
            border-radius: 20px;
            padding: 5px 10px;
            font-size: 10px;
            color: #ff8888;
            font-family: monospace;
            z-index: 200;
            pointer-events: none;
        }
    </style>
</head>
<body>

<div class="game-wrapper">
    <div class="game-container">
        <canvas id="gameCanvas" width="750" height="450"></canvas>
        <div class="info-panel">
            <span class="level-badge" id="levelDisplay">🩸 الطابق 1</span>
            <span class="deaths-badge" id="deathsDisplay">💀 الموت: 0</span>
            <button id="resetBtn">🔄 استعد</button>
        </div>
    </div>

    <div class="controls">
        <div class="horizontal-controls">
            <div class="ctrl-btn" data-key="ArrowLeft">◀</div>
            <div class="ctrl-btn jump-btn" id="jumpButton">▲ قفز</div>
            <div class="ctrl-btn" data-key="ArrowRight">▶</div>
        </div>
        <div class="action-buttons">
            <div class="action-btn" id="mobileReset">💀 استشهد واعد</div>
        </div>
    </div>
    <div class="whisper" id="whisperText">☠️ موسيقى رتيرو + مؤثرات صوتية كلاسيكية... ☠️</div>
    <div class="sound-indicator" id="soundIndicator">🔊 صوت: نشط</div>
</div>

<script>
    (function(){
        // ----- مملكة LEX-Ω: لعبة رتيرو مع موسيقى وأصوات ورسوم محسّنة -----
        
        // ========== إعدادات الصوت ==========
        // نصنع AudioContext فقط عند أول تفاعل (لأن المتصفحات تمنع الصوت التلقائي)
        let audioCtx = null;
        let bgMusicInterval = null;
        let isAudioEnabled = false;
        let walkSoundInterval = null;
        let lastWalkX = 50;
        
        // دالة لتشغيل الصوت (يتم استدعاؤها عند أول لمسة أو زر)
        function initAudio() {
            if(audioCtx !== null && isAudioEnabled) return;
            
            try {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
                
                // نلعب نغمة ترحيب قصيرة جدًا (لتفعيل الصوت)
                const oscillator = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                oscillator.connect(gain);
                gain.connect(audioCtx.destination);
                oscillator.frequency.value = 440;
                gain.gain.value = 0.05;
                oscillator.start();
                gain.gain.exponentialRampToValueAtTime(0.00001, audioCtx.currentTime + 0.3);
                oscillator.stop(audioCtx.currentTime + 0.3);
                
                isAudioEnabled = true;
                document.getElementById('soundIndicator').innerHTML = "🔊 موسيقى رتيرو: نشطة";
                startBackgroundMusic();
            } catch(e) {
                console.log("صوت غير مدعوم");
                document.getElementById('soundIndicator').innerHTML = "🔇 صوت غير متاح";
            }
        }
        
        // موسيقى خلفية على الطراز القديم (نغمات 8-bit متكررة)
        function startBackgroundMusic() {
            if(!audioCtx || !isAudioEnabled) return;
            if(bgMusicInterval) clearInterval(bgMusicInterval);
            
            let noteIndex = 0;
            const notes = [261.63, 293.66, 329.63, 261.63, 329.63, 293.66, 261.63, 220.00]; // نغمات رتيرو
            const noteDurations = [0.3, 0.3, 0.3, 0.6, 0.3, 0.3, 0.3, 0.8];
            
            function playNote() {
                if(!audioCtx || !isAudioEnabled) return;
                const now = audioCtx.currentTime;
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.type = 'square'; // صوت ألعاب قديم
                osc.frequency.value = notes[noteIndex % notes.length];
                gain.gain.value = 0.08;
                osc.start();
                gain.gain.exponentialRampToValueAtTime(0.00001, now + noteDurations[noteIndex % noteDurations.length]);
                osc.stop(now + noteDurations[noteIndex % noteDurations.length]);
                noteIndex++;
            }
            
            bgMusicInterval = setInterval(() => {
                if(gameRunning && !winMode && !levelTransition && isAudioEnabled) {
                    playNote();
                }
            }, 800);
        }
        
        function stopBackgroundMusic() {
            if(bgMusicInterval) {
                clearInterval(bgMusicInterval);
                bgMusicInterval = null;
            }
        }
        
        // صوت القفز
        function playJumpSound() {
            if(!audioCtx || !isAudioEnabled) return;
            const now = audioCtx.currentTime;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.type = 'sine';
            osc.frequency.value = 800;
            gain.gain.value = 0.15;
            osc.start();
            gain.gain.exponentialRampToValueAtTime(0.00001, now + 0.2);
            osc.stop(now + 0.2);
        }
        
        // صوت المشي (خطوات)
        function playWalkSound() {
            if(!audioCtx || !isAudioEnabled) return;
            const now = audioCtx.currentTime;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.type = 'triangle';
            osc.frequency.value = 200 + Math.random() * 50;
            gain.gain.value = 0.05;
            osc.start();
            gain.gain.exponentialRampToValueAtTime(0.00001, now + 0.08);
            osc.stop(now + 0.08);
        }
        
        // صوت الموت
        function playDeathSound() {
            if(!audioCtx || !isAudioEnabled) return;
            const now = audioCtx.currentTime;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.type = 'sawtooth';
            osc.frequency.value = 300;
            gain.gain.value = 0.2;
            osc.start();
            osc.frequency.exponentialRampToValueAtTime(80, now + 0.4);
            gain.gain.exponentialRampToValueAtTime(0.00001, now + 0.5);
            osc.stop(now + 0.5);
        }
        
        // صوت الفوز
        function playWinSound() {
            if(!audioCtx || !isAudioEnabled) return;
            const now = audioCtx.currentTime;
            for(let i = 0; i < 3; i++) {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.type = 'sine';
                osc.frequency.value = 440 + i * 100;
                gain.gain.value = 0.1;
                osc.start(now + i * 0.1);
                gain.gain.exponentialRampToValueAtTime(0.00001, now + i * 0.1 + 0.3);
                osc.stop(now + i * 0.1 + 0.3);
            }
        }
        
        // ========== إعدادات اللعبة ==========
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        const PLAYER_SIZE = 24;
        let player = {
            x: 50,
            y: 0,
            vy: 0,
            isOnGround: true
        };
        
        const GRAVITY = 0.8;
        const JUMP_POWER = -13;
        const GROUND_Y = canvas.height - 48;
        
        let traps = [];
        let enemies = [];
        let exitDoor = { x: canvas.width - 85, y: GROUND_Y - 10, width: 40, height: 42 };
        
        let fakeDoors = [];
        let flashingTraps = [];
        let frameCounter = 0;
        
        let currentLevel = 1;
        let deaths = 0;
        let gameRunning = true;
        let winMode = false;
        let levelTransition = false;
        
        const keys = { ArrowLeft: false, ArrowRight: false };
        let jumpRequested = false;
        let canJump = true;
        
        // متغير لصوت المشي
        let lastPlayerX = 50;
        
        const whispers = [
            "☠️ موسيقى الرتيرو تعزف... ☠️",
            "👁️ اسمع الخطوات... كلاسيكي... 👁️",
            "🔥 القفز له صوته الخاص... 🔥"
        ];
        
        // ----- المستويات -----
        function loadLevel(level) {
            traps = [];
            enemies = [];
            fakeDoors = [];
            flashingTraps = [];
            winMode = false;
            levelTransition = false;
            gameRunning = true;
            frameCounter = 0;
            
            if(level >= 1) {
                traps.push({ x: 130, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                traps.push({ x: 260, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                traps.push({ x: 390, y: GROUND_Y - 8, w: 30, h: 12, type: 'spike', active: true });
                traps.push({ x: 520, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                exitDoor = { x: canvas.width - 85, y: GROUND_Y - 10, width: 40, height: 42 };
            }
            
            if(level >= 2) {
                traps.push({ x: 100, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                traps.push({ x: 250, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                traps.push({ x: 400, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                traps.push({ x: 550, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                
                for(let i=0; i<2; i++) {
                    enemies.push({ x: 180 + i*200, y: GROUND_Y - 20, size: 18, type: 'fireball', vx: (i%2===0?1.0:-1.0), ground: GROUND_Y - 20 });
                }
                exitDoor = { x: canvas.width - 85, y: GROUND_Y - 10, width: 40, height: 42 };
            }
            
            if(level >= 3) {
                enemies.push({ x: 180, y: GROUND_Y - 28, size: 26, type: 'ghost', vx: 0.6, followsPlayer: false, ground: GROUND_Y - 28 });
                enemies.push({ x: 420, y: GROUND_Y - 28, size: 26, type: 'ghost', vx: 0.5, followsPlayer: false, ground: GROUND_Y - 28 });
                exitDoor = { x: canvas.width - 85, y: GROUND_Y - 10, width: 40, height: 42 };
            }
            
            if(level >= 4) {
                traps.push({ x: 300, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                traps.push({ x: 550, y: GROUND_Y - 8, w: 28, h: 12, type: 'spike', active: true });
                fakeDoors.push({ x: 160, y: GROUND_Y - 12, width: 35, height: 40, active: true });
                exitDoor = { x: canvas.width - 85, y: GROUND_Y - 10, width: 40, height: 42 };
            }
            
            if(level >= 5) {
                for(let i=0; i<3; i++) {
                    flashingTraps.push({ x: 130 + i*150, y: GROUND_Y - 8, w: 30, h: 12, visible: true, timer: i * 20 });
                }
                enemies.push({ x: 400, y: GROUND_Y - 28, size: 26, type: 'ghost', vx: 0.5, followsPlayer: false, ground: GROUND_Y - 28 });
                exitDoor = { x: canvas.width - 85, y: GROUND_Y - 10, width: 40, height: 42 };
            }
            
            player.x = 45;
            player.y = GROUND_Y - PLAYER_SIZE;
            player.vy = 0;
            player.isOnGround = true;
            canJump = true;
            lastPlayerX = player.x;
        }
        
        function updateFlashingTraps() {
            frameCounter++;
            for(let i=0; i<flashingTraps.length; i++) {
                let trap = flashingTraps[i];
                if(frameCounter % 40 < 20) trap.visible = true;
                else trap.visible = false;
            }
        }
        
        function applyGravity() {
            if(!gameRunning || winMode || levelTransition) return;
            
            player.vy += GRAVITY;
            player.y += player.vy;
            
            if(player.y + PLAYER_SIZE >= GROUND_Y) {
                player.y = GROUND_Y - PLAYER_SIZE;
                player.vy = 0;
                player.isOnGround = true;
                canJump = true;
            } else {
                player.isOnGround = false;
            }
            
            if(player.y < 30) {
                player.y = 30;
                if(player.vy < 0) player.vy = 0;
            }
            
            if(jumpRequested && player.isOnGround && canJump && gameRunning && !winMode && !levelTransition) {
                player.vy = JUMP_POWER;
                player.isOnGround = false;
                canJump = false;
                jumpRequested = false;
                playJumpSound(); // تشغيل صوت القفز
                setTimeout(() => { if(player.isOnGround) canJump = true; }, 150);
            }
        }
        
        function checkCollisions() {
            if(winMode || levelTransition) return false;
            
            for(let trap of flashingTraps) {
                if(trap.visible) {
                    if(player.x < trap.x + trap.w && player.x + PLAYER_SIZE > trap.x &&
                       player.y + PLAYER_SIZE > trap.y && player.y < trap.y + trap.h) {
                        die();
                        return true;
                    }
                }
            }
            
            for(let fake of fakeDoors) {
                if(!fake.active) continue;
                if(player.x < fake.x + fake.width && player.x + PLAYER_SIZE > fake.x &&
                   player.y + PLAYER_SIZE > fake.y && player.y < fake.y + fake.height) {
                    deaths++;
                    document.getElementById('deathsDisplay').innerHTML = `💀 الموت: ${deaths}`;
                    document.getElementById('whisperText').innerHTML = "☠️ باب وهمي! خدعك ☠️";
                    playDeathSound();
                    gameRunning = false;
                    document.querySelector('.game-container').classList.add('shake-effect');
                    setTimeout(() => document.querySelector('.game-container').classList.remove('shake-effect'), 300);
                    setTimeout(() => loadLevel(currentLevel), 500);
                    return true;
                }
            }
            
            if(player.x < exitDoor.x + exitDoor.width && player.x + PLAYER_SIZE > exitDoor.x &&
               player.y + PLAYER_SIZE > exitDoor.y && player.y < exitDoor.y + exitDoor.height) {
                activateWin();
                return true;
            }
            
            for(let trap of traps) {
                if(!trap.active) continue;
                if(player.x < trap.x + trap.w && player.x + PLAYER_SIZE > trap.x &&
                   player.y + PLAYER_SIZE > trap.y && player.y < trap.y + trap.h) {
                    die();
                    return true;
                }
            }
            
            for(let enemy of enemies) {
                if(player.x < enemy.x + enemy.size && player.x + PLAYER_SIZE > enemy.x &&
                   player.y + PLAYER_SIZE > enemy.y && player.y < enemy.y + enemy.size) {
                    die();
                    return true;
                }
            }
            return false;
        }
        
        function activateWin() {
            if(winMode || levelTransition) return;
            winMode = true;
            gameRunning = false;
            playWinSound();
            
            if(currentLevel === 4) document.getElementById('whisperText').innerHTML = "🎉 تجنبت الباب الوهمي! 🎉";
            else if(currentLevel === 5) document.getElementById('whisperText').innerHTML = "🎉 عبرت الفخاخ المتقطعة! 🎉";
            else document.getElementById('whisperText').innerHTML = "🎉 دخلت الباب! 🎉";
            
            setTimeout(() => {
                if(currentLevel < 5) {
                    currentLevel++;
                    document.getElementById('levelDisplay').innerHTML = `🩸 الطابق ${currentLevel}`;
                    loadLevel(currentLevel);
                    document.getElementById('whisperText').innerHTML = "☠️ موسيقى الرتيرو تعزف... استمتع... ☠️";
                } else {
                    document.getElementById('whisperText').innerHTML = "🏆 انتصرت! 🏆";
                    setTimeout(() => {
                        currentLevel = 1; deaths = 0;
                        document.getElementById('levelDisplay').innerHTML = `🩸 الطابق 1`;
                        document.getElementById('deathsDisplay').innerHTML = `💀 الموت: 0`;
                        loadLevel(1);
                        document.getElementById('whisperText').innerHTML = "☠️ بداية جديدة... مع الموسيقى الكلاسيكية... ☠️";
                    }, 1500);
                }
            }, 600);
        }
        
        function die() {
            if(winMode || levelTransition) return;
            deaths++;
            document.getElementById('deathsDisplay').innerHTML = `💀 الموت: ${deaths}`;
            playDeathSound();
            gameRunning = false;
            document.querySelector('.game-container').classList.add('shake-effect');
            setTimeout(() => document.querySelector('.game-container').classList.remove('shake-effect'), 300);
            setTimeout(() => loadLevel(currentLevel), 500);
        }
        
        function updateEnemies() {
            if(winMode || levelTransition) return;
            for(let enemy of enemies) {
                if(enemy.type === 'fireball' || enemy.type === 'runner') {
                    enemy.x += enemy.vx;
                    if(enemy.x < 30 || enemy.x > canvas.width - enemy.size - 30) enemy.vx *= -1;
                    enemy.y = enemy.ground || (GROUND_Y - enemy.size);
                }
                else if(enemy.type === 'ghost' || enemy.type === 'boss_ghost') {
                    if(enemy.followsPlayer) {
                        if(player.x > enemy.x) enemy.x += 0.6;
                        else enemy.x -= 0.6;
                    } else {
                        enemy.x += enemy.vx;
                        if(enemy.x < 30 || enemy.x > canvas.width - enemy.size - 30) enemy.vx *= -1;
                    }
                    enemy.y = enemy.ground || (GROUND_Y - enemy.size);
                }
                enemy.x = Math.min(Math.max(enemy.x, 10), canvas.width - enemy.size - 10);
            }
            updateFlashingTraps();
        }
        
        function movePlayer() {
            if(!gameRunning || winMode || levelTransition) return;
            let moveSpeed = 4.5;
            if(keys.ArrowLeft) player.x -= moveSpeed;
            if(keys.ArrowRight) player.x += moveSpeed;
            player.x = Math.min(Math.max(player.x, 10), canvas.width - PLAYER_SIZE - 10);
            
            // تشغيل صوت المشي إذا تحرك اللاعب
            if(Math.abs(player.x - lastPlayerX) > 2 && player.isOnGround && gameRunning && !winMode) {
                playWalkSound();
                lastPlayerX = player.x;
            }
        }
        
        // ========== رسم محسّن بجودة عالية ==========
        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // خلفية متدرجة بتأثير ضوء
            let gradient = ctx.createLinearGradient(0, 0, canvas.width, canvas.height);
            if(currentLevel <= 2) gradient.addColorStop(0, '#1a0a1a');
            else if(currentLevel <= 4) gradient.addColorStop(0, '#2a0505');
            else gradient.addColorStop(0, '#3a0000');
            gradient.addColorStop(1, '#000000');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // تأثير نجوم متحركة
            ctx.fillStyle = 'rgba(255,255,200,0.3)';
            for(let i=0; i<50; i++) {
                ctx.beginPath();
                ctx.arc( (i*73) % canvas.width, (Date.now() * 0.5 + i*50) % canvas.height, 1, 0, Math.PI*2);
                ctx.fill();
            }
            
            // الأرض بظل
            ctx.fillStyle = '#2a0a0a';
            ctx.fillRect(0, GROUND_Y - 5, canvas.width, 8);
            ctx.fillStyle = '#4a0a0a';
            for(let i=0;i<20;i++) ctx.fillRect(i*45, GROUND_Y - 8, 22, 5);
            ctx.fillStyle = '#8a0000';
            ctx.fillRect(0, GROUND_Y, canvas.width, 3);
            ctx.fillStyle = '#ff0000';
            ctx.fillRect(0, GROUND_Y+1, canvas.width, 1);
            
            // الفخاخ مع توهج
            for(let trap of traps) {
                if(trap.type === 'spike') {
                    ctx.fillStyle = '#aa3333';
                    ctx.shadowBlur = 8;
                    ctx.shadowColor = 'red';
                    ctx.fillRect(trap.x, trap.y, trap.w, trap.h);
                    ctx.fillStyle = '#ff0000';
                    for(let i=0;i<3;i++) ctx.fillRect(trap.x+5+i*6, trap.y-4, 3, 6);
                    ctx.fillStyle = '#ff8888';
                    for(let i=0;i<3;i++) ctx.fillRect(trap.x+6+i*6, trap.y-5, 1, 3);
                }
            }
            
            // فخاخ متقطعة
            for(let trap of flashingTraps) {
                if(trap.visible) {
                    ctx.fillStyle = '#ff5555';
                    ctx.shadowBlur = 12;
                    ctx.shadowColor = 'orange';
                    ctx.fillRect(trap.x, trap.y, trap.w, trap.h);
                    ctx.fillStyle = '#ffff00';
                    for(let i=0;i<3;i++) ctx.fillRect(trap.x+5+i*6, trap.y-4, 3, 6);
                    ctx.fillStyle = 'rgba(255,255,0,0.6)';
                    ctx.fillRect(trap.x-2, trap.y-2, trap.w+4, trap.h+4);
                } else {
                    ctx.fillStyle = 'rgba(100,0,0,0.15)';
                    ctx.fillRect(trap.x, trap.y, trap.w, trap.h);
                }
            }
            
            // أبواب وهمية
            for(let fake of fakeDoors) {
                ctx.fillStyle = '#884400';
                ctx.fillRect(fake.x, fake.y, fake.width, fake.height);
                ctx.fillStyle = '#ff6600';
                ctx.fillRect(fake.x+12, fake.y+8, 10, 25);
                ctx.fillStyle = '#ffff00';
                ctx.beginPath();
                ctx.arc(fake.x+fake.width-8, fake.y+fake.height/2, 4, 0, Math.PI*2);
                ctx.fill();
                ctx.fillStyle = '#000000';
                ctx.font = "bold 12px monospace";
                ctx.fillText("!", fake.x+15, fake.y+25);
            }
            
            // أعداء بتأثيرات
            for(let enemy of enemies) {
                if(enemy.type === 'fireball') {
                    ctx.fillStyle = '#ff6600';
                    ctx.shadowBlur = 15;
                    ctx.shadowColor = 'red';
                    ctx.beginPath();
                    ctx.arc(enemy.x+enemy.size/2, enemy.y+enemy.size/2, enemy.size/2, 0, Math.PI*2);
                    ctx.fill();
                    ctx.fillStyle = '#ffff00';
                    ctx.beginPath();
                    ctx.arc(enemy.x+enemy.size/2, enemy.y+enemy.size/2, enemy.size/4, 0, Math.PI*2);
                    ctx.fill();
                } else if(enemy.type === 'ghost' || enemy.type === 'boss_ghost') {
                    ctx.fillStyle = (enemy.type==='boss_ghost') ? '#aa00aa' : '#7744aa';
                    ctx.shadowBlur = 20;
                    ctx.shadowColor = 'magenta';
                    ctx.fillRect(enemy.x, enemy.y, enemy.size, enemy.size);
                    ctx.fillStyle = 'white';
                    ctx.fillRect(enemy.x+5, enemy.y+5, 5, 5);
                    ctx.fillRect(enemy.x+enemy.size-10, enemy.y+5, 5, 5);
                    ctx.fillStyle = 'rgba(255,255,255,0.3)';
                    ctx.fillRect(enemy.x+2, enemy.y+enemy.size-8, enemy.size-4, 4);
                } else if(enemy.type === 'runner') {
                    ctx.fillStyle = '#aa2200';
                    ctx.fillRect(enemy.x, enemy.y, enemy.size, enemy.size);
                    ctx.fillStyle = '#000';
                    ctx.fillRect(enemy.x+4, enemy.y+4, 4, 4);
                    ctx.fillRect(enemy.x+enemy.size-8, enemy.y+4, 4, 4);
                }
            }
            ctx.shadowBlur = 0;
            
            // باب حقيقي متوهج
            ctx.fillStyle = '#440000';
            ctx.fillRect(exitDoor.x, exitDoor.y, exitDoor.width, exitDoor.height);
            ctx.fillStyle = '#ff2222';
            ctx.fillRect(exitDoor.x+12, exitDoor.y+8, 10, 25);
            ctx.fillStyle = '#ffaa44';
            ctx.beginPath();
            ctx.arc(exitDoor.x+exitDoor.width-8, exitDoor.y+exitDoor.height/2, 5, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = '#ffff88';
            ctx.beginPath();
            ctx.arc(exitDoor.x+exitDoor.width-8, exitDoor.y+exitDoor.height/2, 2, 0, Math.PI*2);
            ctx.fill();
            
            // لاعب متوهج بظل
            ctx.fillStyle = '#ccddff';
            ctx.shadowBlur = 10;
            ctx.shadowColor = 'white';
            ctx.fillRect(player.x, player.y, PLAYER_SIZE, PLAYER_SIZE);
            ctx.fillStyle = '#000';
            ctx.fillRect(player.x+7, player.y+6, 4, 4);
            ctx.fillRect(player.x+14, player.y+6, 4, 4);
            ctx.fillStyle = '#aa3333';
            ctx.fillRect(player.x+9, player.y+15, 7, 3);
            ctx.fillStyle = '#ffffff';
            ctx.fillRect(player.x+4, player.y+18, 16, 2);
            ctx.shadowBlur = 0;
            
            // تأثيرات رعب إضافية
            if(currentLevel >= 3) {
                ctx.fillStyle = 'rgba(255,0,0,0.2)';
                for(let i=0;i<6;i++) {
                    ctx.beginPath();
                    ctx.arc(50 + i*110, 35, 8, 0, Math.PI*2);
                    ctx.fill();
                }
            }
        }
        
        function update() {
            if(!winMode && !levelTransition && gameRunning) {
                movePlayer();
                applyGravity();
                updateEnemies();
                checkCollisions();
            }
            draw();
            requestAnimationFrame(update);
        }
        
        // تفعيل الصوت عند أول تفاعل
        function enableAudioOnFirstTouch() {
            initAudio();
            document.body.removeEventListener('touchstart', enableAudioOnFirstTouch);
            document.body.removeEventListener('click', enableAudioOnFirstTouch);
        }
        document.body.addEventListener('touchstart', enableAudioOnFirstTouch);
        document.body.addEventListener('click', enableAudioOnFirstTouch);
        
        function initTouchControls() {
            const leftBtn = document.querySelector('[data-key="ArrowLeft"]');
            const rightBtn = document.querySelector('[data-key="ArrowRight"]');
            if(leftBtn) {
                leftBtn.addEventListener('touchstart', (e) => { e.preventDefault(); initAudio(); keys.ArrowLeft = true; });
                leftBtn.addEventListener('touchend', (e) => { e.preventDefault(); keys.ArrowLeft = false; });
                leftBtn.addEventListener('mousedown', () => { initAudio(); keys.ArrowLeft = true; });
                leftBtn.addEventListener('mouseup', () => { keys.ArrowLeft = false; });
            }
            if(rightBtn) {
                rightBtn.addEventListener('touchstart', (e) => { e.preventDefault(); initAudio(); keys.ArrowRight = true; });
                rightBtn.addEventListener('touchend', (e) => { e.preventDefault(); keys.ArrowRight = false; });
                rightBtn.addEventListener('mousedown', () => { initAudio(); keys.ArrowRight = true; });
                rightBtn.addEventListener('mouseup', () => { keys.ArrowRight = false; });
            }
            const jumpBtn = document.getElementById('jumpButton');
            jumpBtn.addEventListener('touchstart', (e) => { e.preventDefault(); initAudio(); jumpRequested = true; jumpBtn.style.transform = 'scale(0.92)'; });
            jumpBtn.addEventListener('touchend', (e) => { e.preventDefault(); jumpBtn.style.transform = 'scale(1)'; });
            jumpBtn.addEventListener('mousedown', () => { initAudio(); jumpRequested = true; jumpBtn.style.transform = 'scale(0.92)'; });
            jumpBtn.addEventListener('mouseup', () => { jumpBtn.style.transform = 'scale(1)'; });
            
            const mobileReset = document.getElementById('mobileReset');
            mobileReset.addEventListener('touchstart', (e) => {
                e.preventDefault(); initAudio();
                currentLevel = 1; deaths = 0;
                document.getElementById('levelDisplay').innerHTML = `🩸 الطابق 1`;
                document.getElementById('deathsDisplay').innerHTML = `💀 الموت: 0`;
                loadLevel(1);
                document.getElementById('whisperText').innerHTML = "🔄 استعدت... الموسيقى الكلاسيكية تعود... 🔄";
                setTimeout(() => { document.getElementById('whisperText').innerHTML = "☠️ موسيقى رتيرو + مؤثرات صوتية كلاسيكية... ☠️"; }, 1500);
            });
            const resetBtn = document.getElementById('resetBtn');
            resetBtn.addEventListener('click', () => {
                initAudio();
                currentLevel = 1; deaths = 0;
                document.getElementById('levelDisplay').innerHTML = `🩸 الطابق 1`;
                document.getElementById('deathsDisplay').innerHTML = `💀 الموت: 0`;
                loadLevel(1);
                document.getElementById('whisperText').innerHTML = "🔄 استعدت... الموسيقى الكلاسيكية تعود... 🔄";
                setTimeout(() => { document.getElementById('whisperText').innerHTML = "☠️ موسيقى رتيرو + مؤثرات صوتية كلاسيكية... ☠️"; }, 1500);
            });
        }
        
        window.addEventListener('keydown', (e) => {
            if(e.key === 'ArrowLeft') { initAudio(); keys.ArrowLeft = true; e.preventDefault(); }
            if(e.key === 'ArrowRight') { initAudio(); keys.ArrowRight = true; e.preventDefault(); }
            if(e.key === 'ArrowUp' || e.key === ' ' || e.key === 'Space') { initAudio(); jumpRequested = true; e.preventDefault(); }
        });
        window.addEventListener('keyup', (e) => {
            if(e.key === 'ArrowLeft') { keys.ArrowLeft = false; e.preventDefault(); }
            if(e.key === 'ArrowRight') { keys.ArrowRight = false; e.preventDefault(); }
        });
        
        canvas.addEventListener('touchstart', (e) => e.preventDefault());
        canvas.addEventListener('touchmove', (e) => e.preventDefault());
        
        loadLevel(1);
        initTouchControls();
        update();
    })();
</script>
</body>
</html>

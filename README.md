<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حصار القلعة - المعركة النهائية</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            touch-action: manipulation;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #000;
            color: #fff;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
        }

        /* شاشة التحميل */
        #loadingScreen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #0a0a2a 0%, #1a1a40 100%);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            transition: opacity 0.5s;
        }

        .loader-container {
            background: rgba(0, 0, 0, 0.85);
            border: 4px solid #ffd700;
            border-radius: 20px;
            padding: 40px;
            max-width: 500px;
            width: 90%;
            text-align: center;
            box-shadow: 0 0 50px rgba(255, 215, 0, 0.3);
        }

        .game-title {
            color: #ffd700;
            font-size: 2.5rem;
            margin-bottom: 30px;
            text-shadow: 0 0 20px #ffd700;
            font-weight: bold;
        }

        .progress-container {
            background: rgba(255, 255, 255, 0.1);
            height: 20px;
            border-radius: 10px;
            overflow: hidden;
            margin: 20px 0;
            border: 2px solid #ffd700;
        }

        .progress-bar {
            height: 100%;
            background: linear-gradient(90deg, #ff0000, #ffd700, #00ff00);
            width: 0%;
            transition: width 0.5s;
        }

        .loading-status {
            color: #ffd700;
            font-size: 1.2rem;
            margin-top: 10px;
            min-height: 30px;
        }

        /* الشاشة الرئيسية */
        #gameScreen {
            display: none;
            position: fixed;
            width: 100%;
            height: 100%;
            overflow: hidden;
        }

        canvas {
            display: block;
            position: absolute;
            top: 0;
            left: 0;
        }

        /* واجهة المستخدم */
        .ui-container {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 10;
        }

        /* شريط المعلومات العلوي */
        .top-bar {
            display: flex;
            justify-content: space-between;
            padding: 15px;
            background: linear-gradient(to bottom, rgba(0,0,0,0.8), transparent);
            pointer-events: all;
        }

        .player-stats {
            display: flex;
            gap: 20px;
            flex-wrap: wrap;
        }

        .stat {
            display: flex;
            align-items: center;
            background: rgba(0, 0, 0, 0.7);
            padding: 10px 15px;
            border-radius: 10px;
            border: 2px solid;
            min-width: 150px;
        }

        .health-stat {
            border-color: #ff0000;
        }

        .stamina-stat {
            border-color: #00ff00;
        }

        .gold-stat {
            border-color: #ffd700;
        }

        .stat-icon {
            font-size: 1.5rem;
            margin-right: 10px;
        }

        .stat-info {
            flex-grow: 1;
        }

        .stat-bar {
            height: 10px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 5px;
            overflow: hidden;
            margin-top: 5px;
        }

        .stat-fill {
            height: 100%;
            transition: width 0.3s;
        }

        .health-fill {
            background: linear-gradient(90deg, #ff0000, #ff4444);
        }

        .stamina-fill {
            background: linear-gradient(90deg, #00ff00, #44ff44);
        }

        .wave-info {
            background: rgba(139, 0, 0, 0.8);
            padding: 15px;
            border-radius: 10px;
            border: 2px solid #ffd700;
            text-align: center;
            min-width: 200px;
        }

        /* منطقة التحكم */
        .controls {
            position: absolute;
            bottom: 120px;
            left: 50%;
            transform: translateX(-50%);
            width: 400px;
            height: 400px;
            pointer-events: none;
        }

        .joystick-area {
            position: absolute;
            width: 200px;
            height: 200px;
            border-radius: 50%;
            background: rgba(0, 0, 0, 0.5);
            border: 3px solid rgba(255, 215, 0, 0.5);
            backdrop-filter: blur(10px);
            pointer-events: all;
        }

        .joystick {
            position: absolute;
            width: 80px;
            height: 80px;
            background: rgba(255, 215, 0, 0.8);
            border-radius: 50%;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            cursor: move;
        }

        .action-buttons {
            position: absolute;
            right: 0;
            top: 50%;
            transform: translateY(-50%);
            display: flex;
            flex-direction: column;
            gap: 20px;
            pointer-events: all;
        }

        .action-btn {
            width: 80px;
            height: 80px;
            border-radius: 50%;
            background: linear-gradient(135deg, #8b0000, #dc143c);
            border: 3px solid #ffd700;
            color: white;
            font-size: 2rem;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.1s;
            user-select: none;
        }

        .action-btn:active {
            transform: scale(0.9);
            background: linear-gradient(135deg, #dc143c, #ff0000);
        }

        /* شريط الأسلحة */
        .weapons-bar {
            position: absolute;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 15px;
            background: rgba(0, 0, 0, 0.8);
            padding: 15px 25px;
            border-radius: 20px;
            border: 3px solid #ffd700;
            pointer-events: all;
            backdrop-filter: blur(10px);
        }

        .weapon-slot {
            width: 70px;
            height: 70px;
            border-radius: 15px;
            background: rgba(50, 50, 50, 0.7);
            border: 3px solid #666;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
        }

        .weapon-slot.active {
            border-color: #ffd700;
            background: rgba(139, 0, 0, 0.7);
            transform: scale(1.1);
            box-shadow: 0 0 20px #ffd700;
        }

        .weapon-icon {
            font-size: 1.8rem;
            margin-bottom: 5px;
        }

        .weapon-name {
            font-size: 0.8rem;
            color: #ffd700;
        }

        .ammo-count {
            position: absolute;
            bottom: 5px;
            right: 5px;
            background: #dc143c;
            color: white;
            font-size: 0.7rem;
            padding: 2px 6px;
            border-radius: 10px;
            border: 1px solid #ffd700;
        }

        /* تأثيرات الشاشة */
        .screen-effects {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 5;
        }

        .hit-effect {
            position: absolute;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle, rgba(255,0,0,0.3) 0%, transparent 70%);
            opacity: 0;
            transition: opacity 0.3s;
        }

        .heal-effect {
            position: absolute;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle, rgba(0,255,0,0.2) 0%, transparent 70%);
            opacity: 0;
            transition: opacity 0.3s;
        }

        /* تحذير البوس */
        .boss-alert {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, #8b0000, #000);
            padding: 40px 60px;
            border-radius: 20px;
            border: 4px solid #ff0000;
            text-align: center;
            z-index: 20;
            display: none;
            pointer-events: none;
            animation: alertPulse 1s infinite;
            box-shadow: 0 0 100px rgba(255,0,0,0.7);
        }

        @keyframes alertPulse {
            0%, 100% { transform: translate(-50%, -50%) scale(1); }
            50% { transform: translate(-50%, -50%) scale(1.05); }
        }

        /* مؤشرات الضرر */
        .damage-popup {
            position: absolute;
            color: #ff0000;
            font-size: 1.5rem;
            font-weight: bold;
            text-shadow: 0 0 10px #ff0000;
            pointer-events: none;
            z-index: 15;
            animation: floatUp 1s ease-out forwards;
        }

        @keyframes floatUp {
            0% {
                opacity: 1;
                transform: translateY(0);
            }
            100% {
                opacity: 0;
                transform: translateY(-100px);
            }
        }

        /* قائمة الإعدادات */
        .settings-panel {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.95);
            padding: 40px;
            border-radius: 20px;
            border: 4px solid #ffd700;
            display: none;
            z-index: 30;
            pointer-events: all;
            min-width: 300px;
        }

        .settings-title {
            color: #ffd700;
            text-align: center;
            margin-bottom: 30px;
            font-size: 2rem;
        }

        .settings-group {
            margin-bottom: 20px;
        }

        .settings-label {
            display: block;
            margin-bottom: 10px;
            color: #ffd700;
        }

        .volume-slider {
            width: 100%;
            height: 10px;
            -webkit-appearance: none;
            background: #333;
            border-radius: 5px;
            outline: none;
        }

        .volume-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 20px;
            height: 20px;
            background: #ffd700;
            border-radius: 50%;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <!-- شاشة التحميل -->
    <div id="loadingScreen">
        <div class="loader-container">
            <div class="game-title">⚔️ حصار القلعة ⚔️</div>
            <div class="progress-container">
                <div class="progress-bar" id="loaderProgress"></div>
            </div>
            <div class="loading-status" id="loadingStatus">جاري التحميل...</div>
        </div>
    </div>

    <!-- الشاشة الرئيسية -->
    <div id="gameScreen">
        <canvas id="gameCanvas"></canvas>
        
        <!-- واجهة المستخدم -->
        <div class="ui-container">
            <!-- شريط المعلومات العلوي -->
            <div class="top-bar">
                <div class="player-stats">
                    <div class="stat health-stat">
                        <div class="stat-icon">❤️</div>
                        <div class="stat-info">
                            <div>الصحة: <span id="healthValue">100</span></div>
                            <div class="stat-bar">
                                <div id="healthFill" class="stat-fill health-fill" style="width: 100%"></div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="stat stamina-stat">
                        <div class="stat-icon">⚡</div>
                        <div class="stat-info">
                            <div>الطاقة: <span id="staminaValue">100</span></div>
                            <div class="stat-bar">
                                <div id="staminaFill" class="stat-fill stamina-fill" style="width: 100%"></div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="stat gold-stat">
                        <div class="stat-icon">💰</div>
                        <div class="stat-info">
                            <div>الذهب: <span id="goldValue">1000</span></div>
                            <div>المستوى: <span id="levelValue">1</span></div>
                        </div>
                    </div>
                </div>
                
                <div class="wave-info">
                    <div>🌊 الموجة: <span id="waveValue">1</span></div>
                    <div>👹 الوحوش: <span id="enemiesValue">0/20</span></div>
                    <div>🎯 النقاط: <span id="scoreValue">0</span></div>
                </div>
            </div>
            
            <!-- منطقة التحكم -->
            <div class="controls">
                <div class="joystick-area" id="joystickArea">
                    <div class="joystick" id="joystick"></div>
                </div>
                
                <div class="action-buttons">
                    <div class="action-btn" id="jumpBtn" title="قفز">⬆️</div>
                    <div class="action-btn" id="attackBtn" title="هجوم">⚔️</div>
                    <div class="action-btn" id="dashBtn" title="دفعة سريعة">💨</div>
                    <div class="action-btn" id="blockBtn" title="دفاع">🛡️</div>
                </div>
            </div>
            
            <!-- شريط الأسلحة -->
            <div class="weapons-bar">
                <div class="weapon-slot active" data-weapon="sword">
                    <div class="weapon-icon">🗡️</div>
                    <div class="weapon-name">سيف</div>
                </div>
                <div class="weapon-slot" data-weapon="pistol">
                    <div class="weapon-icon">🔫</div>
                    <div class="weapon-name">مسدس</div>
                    <div class="ammo-count" id="pistolAmmo">30</div>
                </div>
                <div class="weapon-slot" data-weapon="shotgun">
                    <div class="weapon-icon">💥</div>
                    <div class="weapon-name">صيد</div>
                    <div class="ammo-count" id="shotgunAmmo">12</div>
                </div>
                <div class="weapon-slot" data-weapon="rifle">
                    <div class="weapon-icon">🎯</div>
                    <div class="weapon-name">قنص</div>
                    <div class="ammo-count" id="rifleAmmo">10</div>
                </div>
                <div class="weapon-slot" data-weapon="rocket">
                    <div class="weapon-icon">🚀</div>
                    <div class="weapon-name">صاروخ</div>
                    <div class="ammo-count" id="rocketAmmo">5</div>
                </div>
            </div>
            
            <!-- تأثيرات الشاشة -->
            <div class="screen-effects">
                <div class="hit-effect" id="hitEffect"></div>
                <div class="heal-effect" id="healEffect"></div>
            </div>
            
            <!-- تحذير البوس -->
            <div class="boss-alert" id="bossAlert">
                <div style="font-size: 2rem; color: #ff0000; margin-bottom: 20px;">👹 تحذير 👹</div>
                <div style="font-size: 1.5rem; color: #ffd700;">وحش عملاق يقترب!</div>
                <div style="margin-top: 10px; color: #fff;">استعد للمعركة النهائية!</div>
            </div>
        </div>
        
        <!-- لوحة الإعدادات -->
        <div class="settings-panel" id="settingsPanel">
            <div class="settings-title">⚙️ الإعدادات</div>
            
            <div class="settings-group">
                <label class="settings-label">🔊 الموسيقى الرئيسية</label>
                <input type="range" min="0" max="100" value="50" class="volume-slider" id="musicVolume">
            </div>
            
            <div class="settings-group">
                <label class="settings-label">🎵 أصوات التأثيرات</label>
                <input type="range" min="0" max="100" value="70" class="volume-slider" id="sfxVolume">
            </div>
            
            <div class="settings-group">
                <label class="settings-label">⚡ جودة الرسومات</label>
                <select id="graphicsQuality" style="width: 100%; padding: 10px; background: #333; color: white; border: 2px solid #ffd700; border-radius: 5px;">
                    <option value="low">منخفضة</option>
                    <option value="medium" selected>متوسطة</option>
                    <option value="high">عالية</option>
                </select>
            </div>
            
            <div style="display: flex; gap: 20px; margin-top: 30px;">
                <button id="saveSettings" style="flex: 1; padding: 15px; background: #ffd700; color: #000; border: none; border-radius: 10px; font-weight: bold; cursor: pointer;">حفظ</button>
                <button id="closeSettings" style="flex: 1; padding: 15px; background: #dc143c; color: white; border: none; border-radius: 10px; font-weight: bold; cursor: pointer;">إغلاق</button>
            </div>
        </div>
    </div>

    <script>
        // ============= متغيرات اللعبة =============
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // تعيين حجم اللعبة
        const GAME_WIDTH = 3840;
        const GAME_HEIGHT = 2160;
        canvas.width = GAME_WIDTH;
        canvas.height = GAME_HEIGHT;
        
        // ============= نظام التحميل =============
        let loadingProgress = 0;
        const loader = document.getElementById('loaderProgress');
        const loadingStatus = document.getElementById('loadingStatus');
        
        const loadingSteps = [
            { progress: 10, text: "تهيئة محرك اللعبة..." },
            { progress: 25, text: "تحميل الرسومات والأصول..." },
            { progress: 40, text: "تهيئة نظام الصوت..." },
            { progress: 55, text: "إنشاء نماذج الأعداء..." },
            { progress: 70, text: "تحميل نظام الأسلحة..." },
            { progress: 85, text: "تهيئة نظام الجسيمات..." },
            { progress: 95, text: "التجهيز النهائي..." },
            { progress: 100, text: "جاهز! ابدأ المعركة!" }
        ];
        
        function updateLoading() {
            let currentStep = 0;
            
            function nextStep() {
                if (currentStep < loadingSteps.length) {
                    const step = loadingSteps[currentStep];
                    loadingProgress = step.progress;
                    loader.style.width = `${loadingProgress}%`;
                    loadingStatus.textContent = step.text;
                    currentStep++;
                    
                    setTimeout(nextStep, 500);
                    
                    if (loadingProgress >= 100) {
                        setTimeout(() => {
                            document.getElementById('loadingScreen').style.opacity = '0';
                            setTimeout(() => {
                                document.getElementById('loadingScreen').style.display = 'none';
                                document.getElementById('gameScreen').style.display = 'block';
                                initGame();
                            }, 500);
                        }, 1000);
                    }
                }
            }
            
            nextStep();
        }
        
        setTimeout(updateLoading, 1000);
        
        // ============= النظام الصوتي المحسن =============
        class AudioManager {
            constructor() {
                this.sounds = new Map();
                this.music = new Map();
                this.masterVolume = 1.0;
                this.musicVolume = 0.5;
                this.sfxVolume = 0.7;
                this.isMuted = false;
                
                this.initSounds();
            }
            
            initSounds() {
                // أنواع الأصوات
                const soundTypes = [
                    'sword_swing', 'pistol_shot', 'shotgun_blast', 
                    'sniper_shot', 'rocket_launch', 'reload',
                    'enemy_hit', 'enemy_death', 'player_hit',
                    'player_heal', 'boss_spawn', 'level_up',
                    'coin_collect', 'jump', 'dash', 'block'
                ];
                
                // إنشاء الأصوات بشكل برمجي
                soundTypes.forEach(type => {
                    this.sounds.set(type, this.createSound(type));
                });
            }
            
            createSound(type) {
                const audioContext = new (window.AudioContext || window.webkitAudioContext)();
                
                let oscillator, gainNode, filter;
                
                switch(type) {
                    case 'sword_swing':
                        oscillator = audioContext.createOscillator();
                        gainNode = audioContext.createGain();
                        oscillator.connect(gainNode);
                        gainNode.connect(audioContext.destination);
                        
                        oscillator.type = 'sawtooth';
                        oscillator.frequency.setValueAtTime(200, audioContext.currentTime);
                        oscillator.frequency.exponentialRampToValueAtTime(50, audioContext.currentTime + 0.2);
                        
                        gainNode.gain.setValueAtTime(this.sfxVolume, audioContext.currentTime);
                        gainNode.gain.exponentialRampToValueAtTime(0.001, audioContext.currentTime + 0.3);
                        
                        oscillator.start();
                        oscillator.stop(audioContext.currentTime + 0.3);
                        break;
                        
                    case 'pistol_shot':
                        oscillator = audioContext.createOscillator();
                        gainNode = audioContext.createGain();
                        filter = audioContext.createBiquadFilter();
                        
                        oscillator.connect(filter);
                        filter.connect(gainNode);
                        gainNode.connect(audioContext.destination);
                        
                        filter.type = 'highpass';
                        filter.frequency.value = 1000;
                        
                        oscillator.type = 'square';
                        oscillator.frequency.setValueAtTime(800, audioContext.currentTime);
                        
                        gainNode.gain.setValueAtTime(this.sfxVolume, audioContext.currentTime);
                        gainNode.gain.exponentialRampToValueAtTime(0.001, audioContext.currentTime + 0.1);
                        
                        oscillator.start();
                        oscillator.stop(audioContext.currentTime + 0.1);
                        break;
                        
                    case 'enemy_hit':
                        oscillator = audioContext.createOscillator();
                        gainNode = audioContext.createGain();
                        
                        oscillator.connect(gainNode);
                        gainNode.connect(audioContext.destination);
                        
                        oscillator.type = 'sine';
                        oscillator.frequency.setValueAtTime(300, audioContext.currentTime);
                        oscillator.frequency.exponentialRampToValueAtTime(100, audioContext.currentTime + 0.15);
                        
                        gainNode.gain.setValueAtTime(this.sfxVolume * 0.8, audioContext.currentTime);
                        gainNode.gain.exponentialRampToValueAtTime(0.001, audioContext.currentTime + 0.2);
                        
                        oscillator.start();
                        oscillator.stop(audioContext.currentTime + 0.2);
                        break;
                        
                    case 'boss_spawn':
                        const osc1 = audioContext.createOscillator();
                        const osc2 = audioContext.createOscillator();
                        gainNode = audioContext.createGain();
                        
                        osc1.connect(gainNode);
                        osc2.connect(gainNode);
                        gainNode.connect(audioContext.destination);
                        
                        osc1.type = 'sawtooth';
                        osc2.type = 'triangle';
                        
                        osc1.frequency.setValueAtTime(50, audioContext.currentTime);
                        osc1.frequency.linearRampToValueAtTime(30, audioContext.currentTime + 2);
                        
                        osc2.frequency.setValueAtTime(100, audioContext.currentTime);
                        osc2.frequency.linearRampToValueAtTime(60, audioContext.currentTime + 2);
                        
                        gainNode.gain.setValueAtTime(this.sfxVolume * 1.5, audioContext.currentTime);
                        gainNode.gain.exponentialRampToValueAtTime(0.001, audioContext.currentTime + 2);
                        
                        osc1.start();
                        osc2.start();
                        osc1.stop(audioContext.currentTime + 2);
                        osc2.stop(audioContext.currentTime + 2);
                        break;
                }
                
                return { audioContext, oscillator, gainNode };
            }
            
            play(soundType) {
                if (this.isMuted) return;
                
                const sound = this.sounds.get(soundType);
                if (sound) {
                    // إعادة إنشاء الصوت عند كل تشغيل
                    this.sounds.set(soundType, this.createSound(soundType));
                }
            }
            
            setVolume(type, value) {
                if (type === 'music') {
                    this.musicVolume = value / 100;
                } else if (type === 'sfx') {
                    this.sfxVolume = value / 100;
                }
            }
            
            toggleMute() {
                this.isMuted = !this.isMuted;
                return this.isMuted;
            }
        }
        
        // ============= نظام الجسيمات المحسن =============
        class ParticleSystem {
            constructor() {
                this.particles = [];
                this.emitters = [];
            }
            
            createEffect(type, x, y, options = {}) {
                const count = options.count || 20;
                const color = options.color || '#ff0000';
                const size = options.size || 5;
                const speed = options.speed || 10;
                const life = options.life || 1.0;
                
                for (let i = 0; i < count; i++) {
                    const angle = Math.random() * Math.PI * 2;
                    const velocity = Math.random() * speed + 2;
                    
                    this.particles.push({
                        x, y,
                        vx: Math.cos(angle) * velocity,
                        vy: Math.sin(angle) * velocity,
                        life,
                        maxLife: life,
                        size: Math.random() * size + size/2,
                        color,
                        type,
                        rotation: Math.random() * Math.PI * 2,
                        rotationSpeed: (Math.random() - 0.5) * 0.1,
                        trail: [],
                        gravity: type === 'blood' ? 0.5 : 0.3
                    });
                }
            }
            
            update() {
                for (let i = this.particles.length - 1; i >= 0; i--) {
                    const p = this.particles[i];
                    
                    p.x += p.vx;
                    p.y += p.vy;
                    p.vy += p.gravity;
                    p.rotation += p.rotationSpeed;
                    p.life -= 0.02;
                    
                    // إضافة أثر
                    p.trail.push({x: p.x, y: p.y, size: p.size});
                    if (p.trail.length > 8) p.trail.shift();
                    
                    if (p.life <= 0) {
                        this.particles.splice(i, 1);
                    }
                }
            }
            
            draw() {
                this.particles.forEach(p => {
                    const alpha = p.life / p.maxLife;
                    
                    // رسم الأثر
                    ctx.save();
                    ctx.globalAlpha = alpha * 0.3;
                    
                    for (let i = 0; i < p.trail.length - 1; i++) {
                        const current = p.trail[i];
                        const next = p.trail[i + 1];
                        
                        ctx.strokeStyle = p.color;
                        ctx.lineWidth = current.size * (i / p.trail.length);
                        ctx.lineCap = 'round';
                        
                        ctx.beginPath();
                        ctx.moveTo(current.x, current.y);
                        ctx.lineTo(next.x, next.y);
                        ctx.stroke();
                    }
                    ctx.restore();
                    
                    // رسم الجسيم
                    ctx.save();
                    ctx.globalAlpha = alpha;
                    ctx.fillStyle = p.color;
                    
                    ctx.translate(p.x, p.y);
                    ctx.rotate(p.rotation);
                    
                    if (p.type === 'magic') {
                        // نجمة سداسية
                        ctx.beginPath();
                        for (let j = 0; j < 6; j++) {
                            const angle = (j * Math.PI) / 3;
                            ctx.lineTo(
                                Math.cos(angle) * p.size,
                                Math.sin(angle) * p.size
                            );
                        }
                        ctx.closePath();
                        ctx.fill();
                    } else if (p.type === 'spark') {
                        // شرارة
                        ctx.beginPath();
                        ctx.moveTo(0, -p.size);
                        ctx.lineTo(p.size, 0);
                        ctx.lineTo(0, p.size);
                        ctx.lineTo(-p.size, 0);
                        ctx.closePath();
                        ctx.fill();
                    } else {
                        // دائرة
                        ctx.beginPath();
                        ctx.arc(0, 0, p.size, 0, Math.PI * 2);
                        ctx.fill();
                        
                        // توهج
                        if (alpha > 0.7) {
                            ctx.globalAlpha = (alpha - 0.7) * 2;
                            ctx.beginPath();
                            ctx.arc(0, 0, p.size * 2, 0, Math.PI * 2);
                            ctx.fill();
                        }
                    }
                    
                    ctx.restore();
                });
            }
        }
        
        // ============= نظام الكاميرا الذكية =============
        class Camera {
            constructor() {
                this.x = 0;
                this.y = 0;
                this.zoom = 1.5;
                this.target = null;
                this.smoothness = 0.1;
                this.shakeIntensity = 0;
                this.shakeDuration = 0;
                this.bounds = {
                    minX: 0,
                    maxX: GAME_WIDTH,
                    minY: 0,
                    maxY: GAME_HEIGHT
                };
            }
            
            follow(target) {
                this.target = target;
            }
            
            update() {
                if (this.target) {
                    // حساب الموضع المستهدف
                    let targetX = this.target.x - (window.innerWidth / 2) / this.zoom;
                    let targetY = this.target.y - (window.innerHeight / 2) / this.zoom;
                    
                    // تطبيق الحدود
                    const marginX = window.innerWidth / this.zoom / 3;
                    const marginY = window.innerHeight / this.zoom / 3;
                    
                    targetX = Math.max(this.bounds.minX + marginX, 
                        Math.min(this.bounds.maxX - marginX, targetX));
                    targetY = Math.max(this.bounds.minY + marginY, 
                        Math.min(this.bounds.maxY - marginY, targetY));
                    
                    // الحركة السلسة
                    this.x += (targetX - this.x) * this.smoothness;
                    this.y += (targetY - this.y) * this.smoothness;
                }
                
                // تأثير الاهتزاز
                if (this.shakeDuration > 0) {
                    this.x += (Math.random() - 0.5) * this.shakeIntensity;
                    this.y += (Math.random() - 0.5) * this.shakeIntensity;
                    this.shakeDuration--;
                    this.shakeIntensity *= 0.9;
                }
            }
            
            shake(intensity, duration) {
                this.shakeIntensity = intensity;
                this.shakeDuration = duration;
            }
            
            apply() {
                ctx.save();
                ctx.translate(window.innerWidth / 2, window.innerHeight / 2);
                ctx.scale(this.zoom, this.zoom);
                ctx.translate(-this.x, -this.y);
            }
            
            reset() {
                ctx.restore();
            }
        }
        
        // ============= اللاعب المحسن =============
        class Player {
            constructor() {
                this.x = GAME_WIDTH / 2;
                this.y = GAME_HEIGHT / 2;
                this.width = 60;
                this.height = 100;
                this.speed = 8;
                this.jumpForce = 20;
                this.dashForce = 30;
                this.vx = 0;
                this.vy = 0;
                this.gravity = 0.8;
                this.isGrounded = false;
                this.facing = 'right';
                this.lastFacing = 'right';
                
                // الإحصائيات
                this.health = 100;
                this.maxHealth = 100;
                this.stamina = 100;
                this.maxStamina = 100;
                this.level = 1;
                this.exp = 0;
                this.gold = 1000;
                this.score = 0;
                this.kills = 0;
                
                // الأسلحة
                this.currentWeapon = 'sword';
                this.weapons = {
                    sword: { 
                        damage: 40, 
                        range: 80, 
                        cooldown: 0, 
                        maxCooldown: 30,
                        ammo: Infinity,
                        color: '#ffd700'
                    },
                    pistol: { 
                        damage: 25, 
                        range: 400, 
                        cooldown: 0, 
                        maxCooldown: 15,
                        ammo: 30,
                        color: '#ffff00'
                    },
                    shotgun: { 
                        damage: 60, 
                        range: 200, 
                        cooldown: 0, 
                        maxCooldown: 60,
                        ammo: 12,
                        color: '#ff4500'
                    },
                    rifle: { 
                        damage: 80, 
                        range: 600, 
                        cooldown: 0, 
                        maxCooldown: 90,
                        ammo: 10,
                        color: '#00ffff'
                    },
                    rocket: { 
                        damage: 150, 
                        range: 500, 
                        cooldown: 0, 
                        maxCooldown: 120,
                        ammo: 5,
                        color: '#ff8c00'
                    }
                };
                
                // الحالة
                this.isAttacking = false;
                this.isBlocking = false;
                this.invincible = 0;
                this.combo = 0;
                this.comboTimer = 0;
                
                // الرسوم المتحركة
                this.animationTimer = 0;
                this.walkCycle = 0;
                this.attackAnimation = 0;
                this.jumpAnimation = 0;
            }
            
            update() {
                // تحديث الحركة
                this.x += this.vx;
                this.y += this.vy;
                
                // الجاذبية
                if (!this.isGrounded) {
                    this.vy += this.gravity;
                }
                
                // حدود اللعبة
                if (this.x < this.width/2) this.x = this.width/2;
                if (this.x > GAME_WIDTH - this.width/2) this.x = GAME_WIDTH - this.width/2;
                if (this.y < 0) this.y = 0;
                if (this.y > GAME_HEIGHT - this.height) {
                    this.y = GAME_HEIGHT - this.height;
                    this.vy = 0;
                    this.isGrounded = true;
                }
                
                // تحديث التوجه
                if (this.vx > 0) this.facing = 'right';
                if (this.vx < 0) this.facing = 'left';
                
                // تحديث الأسلحة
                Object.values(this.weapons).forEach(weapon => {
                    if (weapon.cooldown > 0) weapon.cooldown--;
                });
                
                // تحديث الحالة
                if (this.invincible > 0) this.invincible--;
                if (this.attackAnimation > 0) this.attackAnimation--;
                if (this.comboTimer > 0) this.comboTimer--;
                if (this.jumpAnimation > 0) this.jumpAnimation--;
                
                // استعادة الطاقة
                if (!this.isAttacking && !this.isBlocking) {
                    this.stamina = Math.min(this.maxStamina, this.stamina + 0.5);
                }
                
                // تحديث الرسوم المتحركة
                this.animationTimer++;
                this.walkCycle = Math.sin(this.animationTimer * 0.1) * 5;
                
                // تحديث الواجهة
                this.updateUI();
            }
            
            updateUI() {
                document.getElementById('healthValue').textContent = Math.round(this.health);
                document.getElementById('staminaValue').textContent = Math.round(this.stamina);
                document.getElementById('goldValue').textContent = this.gold;
                document.getElementById('levelValue').textContent = this.level;
                
                const healthPercent = (this.health / this.maxHealth) * 100;
                const staminaPercent = (this.stamina / this.maxStamina) * 100;
                
                document.getElementById('healthFill').style.width = `${healthPercent}%`;
                document.getElementById('staminaFill').style.width = `${staminaPercent}%`;
                
                // تحديث الذخيرة
                document.getElementById('pistolAmmo').textContent = this.weapons.pistol.ammo;
                document.getElementById('shotgunAmmo').textContent = this.weapons.shotgun.ammo;
                document.getElementById('rifleAmmo').textContent = this.weapons.rifle.ammo;
                document.getElementById('rocketAmmo').textContent = this.weapons.rocket.ammo;
            }
            
            move(x, y) {
                this.vx = x * this.speed;
                this.vy = y * this.speed;
                
                // إذا كان يتحرك أفقيًا، تحديث الاتجاه الأخير
                if (x !== 0) {
                    this.lastFacing = x > 0 ? 'right' : 'left';
                }
            }
            
            jump() {
                if (this.isGrounded && this.stamina >= 20) {
                    this.vy = -this.jumpForce;
                    this.isGrounded = false;
                    this.stamina -= 20;
                    this.jumpAnimation = 10;
                    
                    particles.createEffect('spark', this.x, this.y + this.height, {
                        count: 15,
                        color: '#ffff00',
                        size: 4,
                        speed: 8
                    });
                    
                    audioManager.play('jump');
                }
            }
            
            dash() {
                if (this.stamina >= 30) {
                    const dashSpeed = this.dashForce;
                    this.x += (this.facing === 'right' ? 1 : -1) * dashSpeed * 3;
                    this.stamina -= 30;
                    
                    particles.createEffect('spark', this.x, this.y + this.height/2, {
                        count: 20,
                        color: '#00ffff',
                        size: 5,
                        speed: 12
                    });
                    
                    camera.shake(5, 10);
                    audioManager.play('dash');
                }
            }
            
            attack(enemies) {
                const weapon = this.weapons[this.currentWeapon];
                
                if (weapon.cooldown > 0 || weapon.ammo <= 0) return;
                
                weapon.cooldown = weapon.maxCooldown;
                this.isAttacking = true;
                this.attackAnimation = 15;
                
                if (weapon.ammo !== Infinity) {
                    weapon.ammo--;
                }
                
                // تشغيل صوت الهجوم
                switch(this.currentWeapon) {
                    case 'sword': audioManager.play('sword_swing'); break;
                    case 'pistol': audioManager.play('pistol_shot'); break;
                    case 'shotgun': audioManager.play('shotgun_blast'); break;
                    case 'rifle': audioManager.play('sniper_shot'); break;
                    case 'rocket': audioManager.play('rocket_launch'); break;
                }
                
                // اهتزاز الكاميرا حسب السلاح
                camera.shake(
                    this.currentWeapon === 'rocket' ? 8 :
                    this.currentWeapon === 'shotgun' ? 5 : 3,
                    15
                );
                
                // الكومبو
                if (this.comboTimer > 0) {
                    this.combo++;
                } else {
                    this.combo = 1;
                }
                this.comboTimer = 60;
                
                // تأثيرات بصرية
                particles.createEffect('spark', 
                    this.x + (this.facing === 'right' ? this.width : 0),
                    this.y + this.height/2,
                    {
                        count: this.currentWeapon === 'shotgun' ? 25 : 15,
                        color: weapon.color,
                        size: this.currentWeapon === 'rocket' ? 8 : 5,
                        speed: 15
                    }
                );
                
                // تنفيذ الهجوم
                if (this.currentWeapon === 'sword') {
                    this.meleeAttack(enemies, weapon);
                } else {
                    this.rangedAttack(weapon);
                }
            }
            
            meleeAttack(enemies, weapon) {
                const attackX = this.x + (this.facing === 'right' ? this.width : -weapon.range);
                const attackY = this.y + this.height/2 - weapon.range/2;
                
                enemies.forEach(enemy => {
                    if (this.checkCollision(enemy, attackX, attackY, weapon.range, weapon.range)) {
                        const damage = weapon.damage * (1 + this.combo * 0.1);
                        const killed = enemy.takeDamage(damage);
                        
                        if (killed) {
                            this.kills++;
                            this.addExp(enemy.exp);
                            this.gold += enemy.gold;
                            
                            particles.createEffect('blood', enemy.x, enemy.y, {
                                count: 25,
                                color: '#ff0000',
                                size: enemy.type === 'giant' ? 8 : 6,
                                speed: 10
                            });
                            
                            updateKillsUI();
                        }
                    }
                });
            }
            
            rangedAttack(weapon) {
                // إنشاء رصاصة/قذيفة
                const bullet = {
                    x: this.x + (this.facing === 'right' ? this.width : 0),
                    y: this.y + this.height/2,
                    vx: (this.facing === 'right' ? 1 : -1) * 20,
                    vy: 0,
                    damage: weapon.damage,
                    range: weapon.range,
                    color: weapon.color,
                    type: this.currentWeapon,
                    life: 100
                };
                
                bullets.push(bullet);
            }
            
            takeDamage(amount) {
                if (this.invincible > 0 || this.isBlocking) return false;
                
                this.health -= amount;
                this.invincible = 30;
                
                // تأثيرات الضرر
                showHitEffect();
                camera.shake(8, 20);
                showDamagePopup(this.x, this.y - 50, `-${Math.round(amount)}`, '#ff0000');
                
                particles.createEffect('blood', this.x, this.y + this.height/2, {
                    count: 20,
                    color: '#ff0000',
                    size: 6,
                    speed: 10
                });
                
                audioManager.play('player_hit');
                
                if (this.health <= 0) {
                    gameOver();
                    return true;
                }
                
                return false;
            }
            
            heal(amount) {
                const oldHealth = this.health;
                this.health = Math.min(this.maxHealth, this.health + amount);
                const healed = this.health - oldHealth;
                
                if (healed > 0) {
                    particles.createEffect('magic', this.x, this.y + this.height/2, {
                        count: 25,
                        color: '#00ff00',
                        size: 5,
                        speed: 8
                    });
                    
                    showDamagePopup(this.x, this.y - 50, `+${Math.round(healed)}`, '#00ff00');
                    showHealEffect();
                    audioManager.play('player_heal');
                }
            }
            
            addExp(amount) {
                this.exp += amount;
                const expNeeded = this.level * 100;
                
                if (this.exp >= expNeeded) {
                    this.levelUp();
                }
            }
            
            levelUp() {
                this.level++;
                this.exp = 0;
                
                this.maxHealth += 20;
                this.health = this.maxHealth;
                this.maxStamina += 15;
                this.stamina = this.maxStamina;
                
                // مكافأة الارتقاء
                this.gold += 500;
                this.heal(50);
                
                particles.createEffect('magic', this.x, this.y, {
                    count: 50,
                    color: '#9370db',
                    size: 6,
                    speed: 12
                });
                
                showMessage(`🎉 ارتقيت للمستوى ${this.level}!`, '#00ff00');
                audioManager.play('level_up');
            }
            
            checkCollision(enemy, x, y, width, height) {
                return enemy.x < x + width &&
                       enemy.x + enemy.width > x &&
                       enemy.y < y + height &&
                       enemy.y + enemy.height > y;
            }
            
            draw() {
                ctx.save();
                
                // تأثير الرجفة عند الضرر
                if (this.invincible > 0 && Math.floor(this.invincible / 5) % 2 === 0) {
                    ctx.globalAlpha = 0.5;
                }
                
                // تدوير إذا كان يواجه اليسار
                if (this.facing === 'left') {
                    ctx.scale(-1, 1);
                    ctx.translate(-this.x * 2 - this.width, 0);
                }
                
                // تأثير المشي والقفز
                const yOffset = this.walkCycle + (this.jumpAnimation > 0 ? -20 : 0);
                
                // رسم الجسم
                this.drawBody(yOffset);
                
                // رسم السلاح
                this.drawWeapon(yOffset);
                
                ctx.restore();
                
                // رسم معلومات إضافية
                this.drawInfo();
            }
            
            drawBody(yOffset) {
                // الجسم
                const bodyGradient = ctx.createLinearGradient(
                    this.x, this.y + yOffset,
                    this.x, this.y + this.height + yOffset
                );
                bodyGradient.addColorStop(0, '#dc143c');
                bodyGradient.addColorStop(0.5, '#b22222');
                bodyGradient.addColorStop(1, '#8b0000');
                
                ctx.fillStyle = bodyGradient;
                ctx.beginPath();
                ctx.roundRect(
                    this.x, 
                    this.y + yOffset, 
                    this.width, 
                    this.height, 
                    15
                );
                ctx.fill();
                
                // تفاصيل الدروع
                ctx.strokeStyle = '#ffd700';
                ctx.lineWidth = 3;
                ctx.beginPath();
                ctx.roundRect(
                    this.x + 10, 
                    this.y + 20 + yOffset, 
                    this.width - 20, 
                    this.height - 40, 
                    8
                );
                ctx.stroke();
                
                // الرأس
                const headRadius = 20;
                ctx.fillStyle = '#ffb6c1';
                ctx.beginPath();
                ctx.arc(
                    this.x + this.width/2, 
                    this.y - headRadius/2 + yOffset, 
                    headRadius, 
                    0, 
                    Math.PI * 2
                );
                ctx.fill();
                
                // الخوذة
                ctx.strokeStyle = '#ffd700';
                ctx.lineWidth = 4;
                ctx.beginPath();
                ctx.arc(
                    this.x + this.width/2, 
                    this.y - headRadius/2 + yOffset, 
                    headRadius + 5, 
                    0, 
                    Math.PI * 2
                );
                ctx.stroke();
                
                // الوجه
                ctx.fillStyle = '#000';
                ctx.beginPath();
                // العيون
                ctx.arc(this.x + this.width/2 - 8, this.y - 5 + yOffset, 4, 0, Math.PI * 2);
                ctx.arc(this.x + this.width/2 + 8, this.y - 5 + yOffset, 4, 0, Math.PI * 2);
                ctx.fill();
                
                // الفم
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y + 10 + yOffset, 10, 0.2, Math.PI - 0.2);
                ctx.stroke();
            }
            
            drawWeapon(yOffset) {
                const weapon = this.weapons[this.currentWeapon];
                const attackOffset = this.attackAnimation * 2;
                
                ctx.save();
                ctx.translate(
                    this.x + (this.facing === 'right' ? this.width + attackOffset : -attackOffset), 
                    this.y + this.height/2 + yOffset
                );
                
                if (this.facing === 'left') {
                    ctx.scale(-1, 1);
                }
                
                switch(this.currentWeapon) {
                    case 'sword':
                        this.drawSword(weapon);
                        break;
                    case 'pistol':
                    case 'shotgun':
                    case 'rifle':
                    case 'rocket':
                        this.drawGun(weapon);
                        break;
                }
                
                ctx.restore();
            }
            
            drawSword(weapon) {
                // نصل السيف
                const bladeGradient = ctx.createLinearGradient(0, -15, 60, 15);
                bladeGradient.addColorStop(0, '#ffffff');
                bladeGradient.addColorStop(0.3, '#ffd700');
                bladeGradient.addColorStop(0.7, '#c0c0c0');
                bladeGradient.addColorStop(1, '#808080');
                
                ctx.fillStyle = bladeGradient;
                ctx.beginPath();
                ctx.moveTo(0, -12);
                ctx.lineTo(55, -8);
                ctx.lineTo(50, 8);
                ctx.lineTo(0, 12);
                ctx.closePath();
                ctx.fill();
                
                // قبضة السيف
                ctx.fillStyle = '#8b4513';
                ctx.fillRect(-15, -10, 15, 20);
                
                // تفاصيل القبضة
                ctx.fillStyle = '#654321';
                for (let i = 0; i < 3; i++) {
                    ctx.fillRect(-12, -8 + i * 8, 10, 4);
                }
                
                // تأثير التوهج عند الكومبو العالي
                if (this.combo > 5) {
                    ctx.shadowBlur = 20;
                    ctx.shadowColor = weapon.color;
                    ctx.stroke();
                    ctx.shadowBlur = 0;
                }
            }
            
            drawGun(weapon) {
                const length = this.currentWeapon === 'pistol' ? 35 :
                              this.currentWeapon === 'shotgun' ? 45 :
                              this.currentWeapon === 'rifle' ? 55 : 50;
                
                const height = this.currentWeapon === 'pistol' ? 12 :
                              this.currentWeapon === 'shotgun' ? 18 :
                              this.currentWeapon === 'rifle' ? 15 : 25;
                
                // جسم السلاح
                const gunGradient = ctx.createLinearGradient(0, -height/2, length, height/2);
                gunGradient.addColorStop(0, '#2f4f4f');
                gunGradient.addColorStop(0.5, '#696969');
                gunGradient.addColorStop(1, '#a9a9a9');
                
                ctx.fillStyle = gunGradient;
                ctx.fillRect(0, -height/2, length, height);
                
                // تفاصيل السلاح
                ctx.fillStyle = '#000';
                ctx.fillRect(length - 10, -height/2, 10, height);
                
                // فتحة السلاح
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(length, 0, height/3, 0, Math.PI * 2);
                ctx.fill();
            }
            
            drawInfo() {
                // عرض الكومبو
                if (this.combo > 1) {
                    ctx.save();
                    ctx.font = 'bold 30px Arial';
                    ctx.fillStyle = this.combo > 5 ? '#ff0000' : 
                                   this.combo > 3 ? '#ffd700' : '#00ff00';
                    ctx.textAlign = 'center';
                    ctx.fillText(`COMBO x${this.combo}`, this.x, this.y - 80);
                    ctx.restore();
                }
            }
        }
        
        // ============= الأعداء المحسنين =============
        class Enemy {
            constructor(type, x, y) {
                this.type = type;
                this.x = x;
                this.y = y;
                this.health = 100;
                this.maxHealth = 100;
                this.speed = 2;
                this.damage = 10;
                this.color = '#ff0000';
                this.exp = 10;
                this.gold = 5;
                this.width = 50;
                this.height = 70;
                
                // إعدادات خاصة بالنوع
                this.setupEnemy(type);
            }
            
            setupEnemy(type) {
                switch(type) {
                    case 'goblin':
                        this.health = 50;
                        this.maxHealth = 50;
                        this.speed = 3;
                        this.damage = 8;
                        this.color = '#00ff00';
                        this.exp = 5;
                        this.gold = 2;
                        this.width = 40;
                        this.height = 50;
                        break;
                        
                    case 'orc':
                        this.health = 150;
                        this.maxHealth = 150;
                        this.speed = 1.5;
                        this.damage = 20;
                        this.color = '#228b22';
                        this.exp = 20;
                        this.gold = 10;
                        this.width = 60;
                        this.height = 90;
                        break;
                        
                    case 'skeleton':
                        this.health = 80;
                        this.maxHealth = 80;
                        this.speed = 2.5;
                        this.damage = 12;
                        this.color = '#c0c0c0';
                        this.exp = 15;
                        this.gold = 8;
                        this.width = 45;
                        this.height = 65;
                        break;
                        
                    case 'giant':
                        this.health = 500;
                        this.maxHealth = 500;
                        this.speed = 0.8;
                        this.damage = 50;
                        this.color = '#8b0000';
                        this.exp = 100;
                        this.gold = 50;
                        this.width = 120;
                        this.height = 180;
                        break;
                        
                    case 'boss':
                        this.health = 2000;
                        this.maxHealth = 2000;
                        this.speed = 1;
                        this.damage = 80;
                        this.color = '#4b0082';
                        this.exp = 500;
                        this.gold = 200;
                        this.width = 200;
                        this.height = 300;
                        break;
                }
            }
            
            update(player) {
                // تتبع اللاعب
                const dx = player.x - this.x;
                const dy = player.y - this.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance > 0) {
                    this.x += (dx / distance) * this.speed;
                    this.y += (dy / distance) * this.speed;
                }
                
                // الرسوم المتحركة
                this.animationTimer = (this.animationTimer || 0) + 1;
                this.wobble = Math.sin(this.animationTimer * 0.1) * 3;
            }
            
            takeDamage(amount) {
                this.health -= amount;
                
                // تأثيرات الضرر
                particles.createEffect('blood', this.x, this.y, {
                    count: 10,
                    color: this.color,
                    size: 4,
                    speed: 8
                });
                
                showDamagePopup(this.x, this.y - 50, `-${Math.round(amount)}`, '#ff0000');
                audioManager.play('enemy_hit');
                
                if (this.health <= 0) {
                    audioManager.play('enemy_death');
                    return true;
                }
                
                return false;
            }
            
            draw() {
                ctx.save();
                
                // تأثير الرجفة عند الضرر
                if (this.health < this.maxHealth * 0.3) {
                    ctx.globalAlpha = 0.7 + Math.sin(Date.now() * 0.01) * 0.3;
                }
                
                const yOffset = this.wobble || 0;
                
                // رسم الجسم حسب النوع
                switch(this.type) {
                    case 'goblin':
                        this.drawGoblin(yOffset);
                        break;
                    case 'orc':
                        this.drawOrc(yOffset);
                        break;
                    case 'skeleton':
                        this.drawSkeleton(yOffset);
                        break;
                    case 'giant':
                        this.drawGiant(yOffset);
                        break;
                    case 'boss':
                        this.drawBoss(yOffset);
                        break;
                }
                
                // شريط الصحة
                this.drawHealthBar();
                
                ctx.restore();
            }
            
            drawGoblin(yOffset) {
                // الجسم
                ctx.fillStyle = this.color;
                ctx.beginPath();
                ctx.ellipse(this.x, this.y + this.height/2 + yOffset, this.width/2, this.height/2, 0, 0, Math.PI * 2);
                ctx.fill();
                
                // العيون
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x - 10, this.y + 20 + yOffset, 3, 0, Math.PI * 2);
                ctx.arc(this.x + 10, this.y + 20 + yOffset, 3, 0, Math.PI * 2);
                ctx.fill();
                
                // الفم
                ctx.strokeStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x, this.y + 35 + yOffset, 8, 0.1, Math.PI - 0.1);
                ctx.stroke();
                
                // السلاح
                ctx.fillStyle = '#8b4513';
                ctx.fillRect(this.x + 25, this.y + 30 + yOffset, 20, 5);
            }
            
            drawOrc(yOffset) {
                // الجسم
                ctx.fillStyle = this.color;
                ctx.beginPath();
                ctx.roundRect(
                    this.x - this.width/2, 
                    this.y + yOffset, 
                    this.width, 
                    this.height, 
                    10
                );
                ctx.fill();
                
                // العضلات
                ctx.fillStyle = '#32cd32';
                ctx.beginPath();
                // عضلات الذراعين
                ctx.ellipse(this.x - 25, this.y + 40 + yOffset, 8, 12, 0, 0, Math.PI * 2);
                ctx.ellipse(this.x + 25, this.y + 40 + yOffset, 8, 12, 0, 0, Math.PI * 2);
                // عضلات الساقين
                ctx.ellipse(this.x - 15, this.y + 100 + yOffset, 10, 15, 0, 0, Math.PI * 2);
                ctx.ellipse(this.x + 15, this.y + 100 + yOffset, 10, 15, 0, 0, Math.PI * 2);
                ctx.fill();
                
                // الوجه
                ctx.fillStyle = '#228b22';
                ctx.beginPath();
                ctx.arc(this.x, this.y + 25 + yOffset, 15, 0, Math.PI * 2);
                ctx.fill();
                
                // العيون
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x - 8, this.y + 20 + yOffset, 4, 0, Math.PI * 2);
                ctx.arc(this.x + 8, this.y + 20 + yOffset, 4, 0, Math.PI * 2);
                ctx.fill();
                
                // الأنياب
                ctx.fillStyle = '#ffffff';
                ctx.beginPath();
                // ناب علوي أيسر
                ctx.moveTo(this.x - 5, this.y + 35 + yOffset);
                ctx.lineTo(this.x - 10, this.y + 30 + yOffset);
                ctx.lineTo(this.x - 5, this.y + 30 + yOffset);
                // ناب علوي أيمن
                ctx.moveTo(this.x + 5, this.y + 35 + yOffset);
                ctx.lineTo(this.x + 10, this.y + 30 + yOffset);
                ctx.lineTo(this.x + 5, this.y + 30 + yOffset);
                ctx.closePath();
                ctx.fill();
            }
            
            drawSkeleton(yOffset) {
                // الجمجمة
                ctx.fillStyle = '#ffffff';
                ctx.beginPath();
                ctx.arc(this.x, this.y + 20 + yOffset, 15, 0, Math.PI * 2);
                ctx.fill();
                
                // العيون
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x - 5, this.y + 15 + yOffset, 3, 0, Math.PI * 2);
                ctx.arc(this.x + 5, this.y + 15 + yOffset, 3, 0, Math.PI * 2);
                ctx.fill();
                
                // الأنف
                ctx.beginPath();
                ctx.moveTo(this.x, this.y + 20 + yOffset);
                ctx.lineTo(this.x - 3, this.y + 25 + yOffset);
                ctx.lineTo(this.x + 3, this.y + 25 + yOffset);
                ctx.closePath();
                ctx.fill();
                
                // الجسم
                ctx.fillStyle = '#c0c0c0';
                ctx.fillRect(this.x - 10, this.y + 35 + yOffset, 20, 40);
                
                // الذراعين
                ctx.fillRect(this.x - 25, this.y + 40 + yOffset, 15, 5);
                ctx.fillRect(this.x + 10, this.y + 40 + yOffset, 15, 5);
                
                // الساقين
                ctx.fillRect(this.x - 15, this.y + 75 + yOffset, 10, 30);
                ctx.fillRect(this.x + 5, this.y + 75 + yOffset, 10, 30);
                
                // السلاح
                ctx.fillStyle = '#8b4513';
                ctx.fillRect(this.x + 20, this.y + 35 + yOffset, 25, 5);
            }
            
            drawGiant(yOffset) {
                // الجسم
                ctx.fillStyle = this.color;
                ctx.beginPath();
                ctx.roundRect(
                    this.x - this.width/2, 
                    this.y + yOffset, 
                    this.width, 
                    this.height, 
                    20
                );
                ctx.fill();
                
                // العيون
                ctx.fillStyle = '#ffff00';
                ctx.beginPath();
                ctx.arc(this.x - 20, this.y + 40 + yOffset, 10, 0, Math.PI * 2);
                ctx.arc(this.x + 20, this.y + 40 + yOffset, 10, 0, Math.PI * 2);
                ctx.fill();
                
                // بؤبؤ العين
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x - 20, this.y + 40 + yOffset, 4, 0, Math.PI * 2);
                ctx.arc(this.x + 20, this.y + 40 + yOffset, 4, 0, Math.PI * 2);
                ctx.fill();
                
                // الفم
                ctx.strokeStyle = '#000';
                ctx.lineWidth = 5;
                ctx.beginPath();
                ctx.arc(this.x, this.y + 80 + yOffset, 25, 0.1, Math.PI - 0.1);
                ctx.stroke();
                
                // الأسنان
                ctx.fillStyle = '#ffffff';
                for (let i = 0; i < 6; i++) {
                    const toothX = this.x - 20 + i * 8;
                    ctx.fillRect(toothX, this.y + 55 + yOffset, 5, 10);
                }
            }
            
            drawBoss(yOffset) {
                // الجسم الرئيسي
                const bossGradient = ctx.createRadialGradient(
                    this.x, this.y + this.height/2 + yOffset, 0,
                    this.x, this.y + this.height/2 + yOffset, 150
                );
                bossGradient.addColorStop(0, '#4b0082');
                bossGradient.addColorStop(0.5, '#8a2be2');
                bossGradient.addColorStop(1, '#9370db');
                
                ctx.fillStyle = bossGradient;
                ctx.beginPath();
                ctx.ellipse(
                    this.x, 
                    this.y + this.height/2 + yOffset, 
                    this.width/2, 
                    this.height/2, 
                    0, 0, Math.PI * 2
                );
                ctx.fill();
                
                // العيون المتعددة
                ctx.fillStyle = '#ff0000';
                for (let i = 0; i < 6; i++) {
                    const angle = (i * Math.PI * 2) / 6;
                    const eyeX = this.x + Math.cos(angle) * 40;
                    const eyeY = this.y + this.height/2 + Math.sin(angle) * 40 + yOffset;
                    
                    ctx.beginPath();
                    ctx.arc(eyeX, eyeY, 8, 0, Math.PI * 2);
                    ctx.fill();
                    
                    // بؤبؤ العين
                    ctx.fillStyle = '#000';
                    ctx.beginPath();
                    ctx.arc(eyeX, eyeY, 3, 0, Math.PI * 2);
                    ctx.fill();
                    
                    ctx.fillStyle = '#ff0000';
                }
                
                // الفم الكبير
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x, this.y + this.height/2 + yOffset, 30, 0, Math.PI);
                ctx.fill();
                
                // الأنياب
                ctx.fillStyle = '#ffffff';
                for (let i = 0; i < 8; i++) {
                    const toothAngle = (i * Math.PI) / 7;
                    const toothX = this.x + Math.cos(toothAngle) * 30;
                    const toothY = this.y + this.height/2 + Math.sin(toothAngle) * 30 + yOffset;
                    
                    ctx.save();
                    ctx.translate(toothX, toothY);
                    ctx.rotate(toothAngle);
                    ctx.fillRect(-3, -10, 6, 20);
                    ctx.restore();
                }
                
                // تأثير التوهج
                ctx.shadowBlur = 30;
                ctx.shadowColor = '#4b0082';
                ctx.fill();
                ctx.shadowBlur = 0;
            }
            
            drawHealthBar() {
                const barWidth = 60;
                const barHeight = 8;
                const healthPercent = this.health / this.maxHealth;
                
                // خلفية شريط الصحة
                ctx.fillStyle = '#000000';
                ctx.fillRect(
                    this.x - barWidth/2, 
                    this.y - 20, 
                    barWidth, 
                    barHeight
                );
                
                // شريط الصحة
                const healthColor = healthPercent > 0.6 ? '#00ff00' : 
                                  healthPercent > 0.3 ? '#ffff00' : '#ff0000';
                
                ctx.fillStyle = healthColor;
                ctx.fillRect(
                    this.x - barWidth/2, 
                    this.y - 20, 
                    barWidth * healthPercent, 
                    barHeight
                );
                
                // حدود شريط الصحة
                ctx.strokeStyle = '#ffffff';
                ctx.lineWidth = 1;
                ctx.strokeRect(
                    this.x - barWidth/2, 
                    this.y - 20, 
                    barWidth, 
                    barHeight
                );
            }
        }
        
        // ============= نظام الموجات =============
        class WaveSystem {
            constructor() {
                this.currentWave = 1;
                this.enemiesSpawned = 0;
                this.enemiesKilled = 0;
                this.totalEnemies = 20;
                this.spawnTimer = 0;
                this.spawnInterval = 60;
                this.bossSpawned = false;
            }
            
            update() {
                this.spawnTimer++;
                
                if (this.spawnTimer >= this.spawnInterval && 
                    this.enemiesSpawned < this.totalEnemies) {
                    this.spawnEnemy();
                    this.spawnTimer = 0;
                }
                
                // تحقق إذا تم قتل 20 وحش لاستدعاء البوس
                if (this.enemiesKilled >= 20 && !this.bossSpawned) {
                    this.spawnBoss();
                    this.bossSpawned = true;
                }
                
                this.updateUI();
            }
            
            spawnEnemy() {
                const enemyTypes = ['goblin', 'orc', 'skeleton', 'giant'];
                const type = enemyTypes[Math.floor(Math.random() * enemyTypes.length)];
                
                // إحداثيات عشوائية حول اللاعب
                const angle = Math.random() * Math.PI * 2;
                const distance = 400 + Math.random() * 200;
                const x = player.x + Math.cos(angle) * distance;
                const y = player.y + Math.sin(angle) * distance;
                
                const enemy = new Enemy(type, x, y);
                enemies.push(enemy);
                this.enemiesSpawned++;
            }
            
            spawnBoss() {
                // عرض تحذير البوس
                const bossAlert = document.getElementById('bossAlert');
                bossAlert.style.display = 'block';
                
                // تشغيل صوت البوس
                audioManager.play('boss_spawn');
                
                // إضافة تأثيرات
                camera.shake(15, 60);
                
                setTimeout(() => {
                    bossAlert.style.display = 'none';
                    
                    // إنشاء البوس في وسط الشاشة
                    const boss = new Enemy('boss', GAME_WIDTH / 2, GAME_HEIGHT / 2);
                    enemies.push(boss);
                    
                    // تأثيرات ظهور البوس
                    for (let i = 0; i < 100; i++) {
                        setTimeout(() => {
                            particles.createEffect('magic', 
                                boss.x + (Math.random() - 0.5) * 200,
                                boss.y + (Math.random() - 0.5) * 200,
                                {
                                    count: 5,
                                    color: '#4b0082',
                                    size: 8,
                                    speed: 10
                                }
                            );
                        }, i * 20);
                    }
                }, 3000);
            }
            
            onEnemyKilled() {
                this.enemiesKilled++;
                
                if (this.enemiesKilled >= this.totalEnemies && !this.bossSpawned) {
                    this.nextWave();
                }
            }
            
            nextWave() {
                this.currentWave++;
                this.enemiesSpawned = 0;
                this.enemiesKilled = 0;
                this.totalEnemies = Math.floor(20 * (1 + this.currentWave * 0.3));
                this.spawnInterval = Math.max(30, 60 - this.currentWave * 5);
                this.bossSpawned = false;
                
                showMessage(`🌊 بداية الموجة ${this.currentWave}!`, '#00ffff');
                
                // مكافأة الموجة
                player.gold += this.currentWave * 100;
                player.heal(30);
            }
            
            updateUI() {
                document.getElementById('waveValue').textContent = this.currentWave;
                document.getElementById('enemiesValue').textContent = 
                    `${this.enemiesKilled}/${this.totalEnemies}`;
                document.getElementById('scoreValue').textContent = player.score;
            }
        }
        
        // ============= متغيرات اللعبة العامة =============
        let player;
        let enemies = [];
        let bullets = [];
        let particles;
        let camera;
        let waveSystem;
        let audioManager;
        let gameRunning = true;
        
        // متغيرات التحكم
        let joystickActive = false;
        let joystickX = 0;
        let joystickY = 0;
        
        // ============= وظائف المساعدة =============
        function showHitEffect() {
            const effect = document.getElementById('hitEffect');
            effect.style.opacity = '1';
            setTimeout(() => {
                effect.style.opacity = '0';
            }, 300);
        }
        
        function showHealEffect() {
            const effect = document.getElementById('healEffect');
            effect.style.opacity = '1';
            setTimeout(() => {
                effect.style.opacity = '0';
            }, 300);
        }
        
        function showDamagePopup(x, y, text, color) {
            const popup = document.createElement('div');
            popup.className = 'damage-popup';
            popup.textContent = text;
            popup.style.color = color;
            popup.style.left = `${x}px`;
            popup.style.top = `${y}px`;
            
            document.querySelector('.ui-container').appendChild(popup);
            
            setTimeout(() => {
                popup.remove();
            }, 1000);
        }
        
        function showMessage(text, color) {
            showDamagePopup(player.x, player.y - 100, text, color);
        }
        
        function updateKillsUI() {
            player.score += 100;
            waveSystem.updateUI();
        }
        
        function gameOver() {
            gameRunning = false;
            
            showMessage('💀 لقد هزمت! 💀', '#ff0000');
            
            setTimeout(() => {
                if (confirm(`انتهت اللعبة!\n\nالنتائج:\n🎯 النقاط: ${player.score}\n👹 الوحوش: ${player.kills}\n💰 الذهب: ${player.gold}\n\nهل تريد إعادة المحاولة؟`)) {
                    location.reload();
                }
            }, 1000);
        }
        
        // ============= تهيئة اللعبة =============
        function initGame() {
            // إنشاء الأنظمة
            audioManager = new AudioManager();
            particles = new ParticleSystem();
            camera = new Camera();
            waveSystem = new WaveSystem();
            
            // إنشاء اللاعب
            player = new Player();
            camera.follow(player);
            
            // إعداد التحكم
            setupControls();
            
            // إعداد الأسلحة
            setupWeapons();
            
            // إعداد الإعدادات
            setupSettings();
            
            // بدء دورة اللعبة
            gameLoop();
            
            // بدء الموسيقى الخلفية
            setTimeout(() => {
                // هنا يمكن إضافة موسيقى خلفية
            }, 1000);
        }
        
        function setupControls() {
            const joystick = document.getElementById('joystick');
            const joystickArea = document.getElementById('joystickArea');
            const jumpBtn = document.getElementById('jumpBtn');
            const attackBtn = document.getElementById('attackBtn');
            const dashBtn = document.getElementById('dashBtn');
            const blockBtn = document.getElementById('blockBtn');
            
            // التحكم بالجويستيك
            let joystickRect = joystickArea.getBoundingClientRect();
            let joystickCenterX = joystickRect.width / 2;
            let joystickCenterY = joystickRect.height / 2;
            
            function updateJoystickPosition(clientX, clientY) {
                const rect = joystickArea.getBoundingClientRect();
                const x = clientX - rect.left - joystickCenterX;
                const y = clientY - rect.top - joystickCenterY;
                
                // حساب المسافة من المركز
                const distance = Math.sqrt(x * x + y * y);
                const maxDistance = joystickRect.width / 2 - joystick.offsetWidth / 2;
                
                // تحديد القيمة
                let moveX = 0;
                let moveY = 0;
                
                if (distance > 10) { // عتبة الموت
                    const angle = Math.atan2(y, x);
                    const limitedDistance = Math.min(distance, maxDistance);
                    
                    // تحديث موضع الجويستيك
                    joystick.style.transform = `translate(${Math.cos(angle) * limitedDistance}px, ${Math.sin(angle) * limitedDistance}px)`;
                    
                    // حساب حركة اللاعب
                    moveX = Math.cos(angle) * (limitedDistance / maxDistance);
                    moveY = Math.sin(angle) * (limitedDistance / maxDistance);
                } else {
                    joystick.style.transform = 'translate(0, 0)';
                }
                
                // تحديث حركة اللاعب
                player.move(moveX, moveY);
            }
            
            // أحداث اللمس للجويستيك
            joystickArea.addEventListener('touchstart', (e) => {
                e.preventDefault();
                joystickActive = true;
                const touch = e.touches[0];
                updateJoystickPosition(touch.clientX, touch.clientY);
            });
            
            joystickArea.addEventListener('touchmove', (e) => {
                if (!joystickActive) return;
                e.preventDefault();
                const touch = e.touches[0];
                updateJoystickPosition(touch.clientX, touch.clientY);
            });
            
            joystickArea.addEventListener('touchend', () => {
                joystickActive = false;
                joystick.style.transform = 'translate(0, 0)';
                player.move(0, 0);
            });
            
            // أحداث الماوس للجويستيك (للتطوير)
            joystickArea.addEventListener('mousedown', (e) => {
                joystickActive = true;
                updateJoystickPosition(e.clientX, e.clientY);
            });
            
            document.addEventListener('mousemove', (e) => {
                if (!joystickActive) return;
                updateJoystickPosition(e.clientX, e.clientY);
            });
            
            document.addEventListener('mouseup', () => {
                joystickActive = false;
                joystick.style.transform = 'translate(0, 0)';
                player.move(0, 0);
            });
            
            // تحديث حجم الجويستيك عند تغيير حجم النافذة
            window.addEventListener('resize', () => {
                joystickRect = joystickArea.getBoundingClientRect();
                joystickCenterX = joystickRect.width / 2;
                joystickCenterY = joystickRect.height / 2;
            });
            
            // أزرار الإجراءات
            jumpBtn.addEventListener('touchstart', (e) => {
                e.preventDefault();
                player.jump();
            });
            
            attackBtn.addEventListener('touchstart', (e) => {
                e.preventDefault();
                player.attack(enemies);
            });
            
            dashBtn.addEventListener('touchstart', (e) => {
                e.preventDefault();
                player.dash();
            });
            
            blockBtn.addEventListener('touchstart', (e) => {
                e.preventDefault();
                player.isBlocking = true;
            });
            
            blockBtn.addEventListener('touchend', (e) => {
                e.preventDefault();
                player.isBlocking = false;
            });
            
            // التحكم بالواجهة
            const pauseBtn = document.createElement('div');
            pauseBtn.className = 'action-btn';
            pauseBtn.textContent = '⏸️';
            pauseBtn.title = 'إيقاف';
            pauseBtn.style.position = 'absolute';
            pauseBtn.style.top = '20px';
            pauseBtn.style.right = '20px';
            pauseBtn.style.pointerEvents = 'all';
            
            pauseBtn.addEventListener('click', () => {
                gameRunning = !gameRunning;
                pauseBtn.textContent = gameRunning ? '⏸️' : '▶️';
            });
            
            document.querySelector('.ui-container').appendChild(pauseBtn);
        }
        
        function setupWeapons() {
            const weaponSlots = document.querySelectorAll('.weapon-slot');
            
            weaponSlots.forEach(slot => {
                slot.addEventListener('click', () => {
                    // إزالة النشاط من جميع الأسلحة
                    weaponSlots.forEach(s => s.classList.remove('active'));
                    
                    // تفعيل السلاح المحدد
                    slot.classList.add('active');
                    player.currentWeapon = slot.dataset.weapon;
                });
                
                // إضافة أحداث اللمس للأجهزة المحمولة
                slot.addEventListener('touchstart', (e) => {
                    e.preventDefault();
                    weaponSlots.forEach(s => s.classList.remove('active'));
                    slot.classList.add('active');
                    player.currentWeapon = slot.dataset.weapon;
                });
            });
        }
        
        function setupSettings() {
            const settingsPanel = document.getElementById('settingsPanel');
            const musicVolume = document.getElementById('musicVolume');
            const sfxVolume = document.getElementById('sfxVolume');
            const graphicsQuality = document.getElementById('graphicsQuality');
            const saveSettings = document.getElementById('saveSettings');
            const closeSettings = document.getElementById('closeSettings');
            
            // زر الإعدادات
            const settingsBtn = document.createElement('div');
            settingsBtn.className = 'action-btn';
            settingsBtn.textContent = '⚙️';
            settingsBtn.title = 'إعدادات';
            settingsBtn.style.position = 'absolute';
            settingsBtn.style.top = '20px';
            settingsBtn.style.right = '100px';
            settingsBtn.style.pointerEvents = 'all';
            
            settingsBtn.addEventListener('click', () => {
                settingsPanel.style.display = 'block';
                gameRunning = false;
            });
            
            document.querySelector('.ui-container').appendChild(settingsBtn);
            
            // حفظ الإعدادات
            saveSettings.addEventListener('click', () => {
                audioManager.setVolume('music', musicVolume.value);
                audioManager.setVolume('sfx', sfxVolume.value);
                
                // تطبيق جودة الرسومات
                const quality = graphicsQuality.value;
                switch(quality) {
                    case 'low':
                        canvas.style.imageRendering = 'pixelated';
                        break;
                    case 'medium':
                        canvas.style.imageRendering = 'auto';
                        break;
                    case 'high':
                        canvas.style.imageRendering = 'auto';
                        // هنا يمكن إضافة تأثيرات إضافية للجودة العالية
                        break;
                }
                
                settingsPanel.style.display = 'none';
                gameRunning = true;
            });
            
            // إغلاق الإعدادات
            closeSettings.addEventListener('click', () => {
                settingsPanel.style.display = 'none';
                gameRunning = true;
            });
        }
        
        // ============= دورة اللعبة الرئيسية =============
        function gameLoop() {
            if (!gameRunning) {
                requestAnimationFrame(gameLoop);
                return;
            }
            
            // تحديث الأنظمة
            player.update();
            camera.update();
            waveSystem.update();
            particles.update();
            
            // تحديث الأعداء
            for (let i = enemies.length - 1; i >= 0; i--) {
                const enemy = enemies[i];
                enemy.update(player);
                
                // تحقق من اصطدام العدو باللاعب
                if (Math.abs(enemy.x - player.x) < enemy.width/2 + player.width/2 &&
                    Math.abs(enemy.y - player.y) < enemy.height/2 + player.height/2) {
                    player.takeDamage(enemy.damage);
                }
            }
            
            // تحديث الرصاصات
            for (let i = bullets.length - 1; i >= 0; i--) {
                const bullet = bullets[i];
                bullet.x += bullet.vx;
                bullet.y += bullet.vy;
                bullet.life--;
                
                // تحقق من اصطدام الرصاصة بالأعداء
                for (let j = enemies.length - 1; j >= 0; j--) {
                    const enemy = enemies[j];
                    
                    if (Math.abs(bullet.x - enemy.x) < enemy.width/2 &&
                        Math.abs(bullet.y - enemy.y) < enemy.height/2) {
                        
                        const killed = enemy.takeDamage(bullet.damage);
                        
                        if (killed) {
                            player.kills++;
                            player.addExp(enemy.exp);
                            player.gold += enemy.gold;
                            enemies.splice(j, 1);
                            waveSystem.onEnemyKilled();
                            
                            particles.createEffect('blood', enemy.x, enemy.y, {
                                count: 30,
                                color: enemy.color,
                                size: bullet.type === 'rocket' ? 10 : 6,
                                speed: 15
                            });
                            
                            if (bullet.type === 'rocket') {
                                camera.shake(10, 20);
                                
                                // ضرر انفجار الصاروخ
                                enemies.forEach(e => {
                                    const dx = e.x - enemy.x;
                                    const dy = e.y - enemy.y;
                                    const distance = Math.sqrt(dx * dx + dy * dy);
                                    
                                    if (distance < 150) {
                                        e.takeDamage(bullet.damage * 0.5);
                                    }
                                });
                            }
                        }
                        
                        bullets.splice(i, 1);
                        break;
                    }
                }
                
                // إزالة الرصاصة إذا تجاوزت المدى أو عمرها
                if (bullet.life <= 0 || 
                    bullet.x < 0 || 
                    bullet.x > GAME_WIDTH || 
                    bullet.y < 0 || 
                    bullet.y > GAME_HEIGHT) {
                    bullets.splice(i, 1);
                }
            }
            
            // الرسم
            render();
            
            // الاستمرار في الدورة
            requestAnimationFrame(gameLoop);
        }
        
        function render() {
            // مسح الشاشة
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // تطبيق الكاميرا
            camera.apply();
            
            // رسم الخلفية
            drawBackground();
            
            // رسم الجسيمات
            particles.draw();
            
            // رسم الرصاصات
            drawBullets();
            
            // رسم الأعداء
            enemies.forEach(enemy => enemy.draw());
            
            // رسم اللاعب
            player.draw();
            
            // إعادة ضبط الكاميرا
            camera.reset();
        }
        
        function drawBackground() {
            // خلفية متدرجة
            const gradient = ctx.createLinearGradient(0, 0, 0, GAME_HEIGHT);
            gradient.addColorStop(0, '#0a0a2a');
            gradient.addColorStop(0.5, '#1a1a40');
            gradient.addColorStop(1, '#2a2a5a');
            
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, GAME_WIDTH, GAME_HEIGHT);
            
            // النجوم
            ctx.fillStyle = '#ffffff';
            for (let i = 0; i < 100; i++) {
                const x = (Math.sin(i * 7) * GAME_WIDTH + Date.now() * 0.01) % GAME_WIDTH;
                const y = (Math.cos(i * 11) * GAME_HEIGHT + Date.now() * 0.008) % GAME_HEIGHT;
                const size = Math.sin(i * 13 + Date.now() * 0.002) * 2 + 1;
                
                ctx.globalAlpha = Math.sin(Date.now() * 0.001 + i) * 0.5 + 0.5;
                ctx.beginPath();
                ctx.arc(x, y, size, 0, Math.PI * 2);
                ctx.fill();
            }
            ctx.globalAlpha = 1;
            
            // الأرض
            ctx.fillStyle = '#2d5016';
            ctx.fillRect(0, GAME_HEIGHT - 200, GAME_WIDTH, 200);
            
            // العشب
            ctx.fillStyle = '#3a7d1e';
            for (let i = 0; i < 50; i++) {
                const x = (i * 80) % GAME_WIDTH;
                const height = 20 + Math.sin(i * 0.5) * 10;
                
                ctx.beginPath();
                ctx.moveTo(x, GAME_HEIGHT - 200);
                ctx.lineTo(x + 40, GAME_HEIGHT - 200 - height);
                ctx.lineTo(x + 80, GAME_HEIGHT - 200);
                ctx.closePath();
                ctx.fill();
            }
            
            // القلعة في الخلفية
            ctx.fillStyle = '#696969';
            // الجدران
            ctx.fillRect(GAME_WIDTH/2 - 300, GAME_HEIGHT - 600, 600, 400);
            // الأبراج
            ctx.fillRect(GAME_WIDTH/2 - 350, GAME_HEIGHT - 700, 100, 300);
            ctx.fillRect(GAME_WIDTH/2 + 250, GAME_HEIGHT - 700, 100, 300);
            // العلم
            ctx.fillStyle = '#ff0000';
            ctx.fillRect(GAME_WIDTH/2 + 270, GAME_HEIGHT - 750, 60, 40);
        }
        
        function drawBullets() {
            bullets.forEach(bullet => {
                ctx.save();
                
                if (bullet.type === 'rocket') {
                    // رسم الصاروخ
                    const rocketGradient = ctx.createLinearGradient(
                        bullet.x - 15, bullet.y,
                        bullet.x + 15, bullet.y
                    );
                    rocketGradient.addColorStop(0, '#ff8c00');
                    rocketGradient.addColorStop(0.5, '#ff4500');
                    rocketGradient.addColorStop(1, '#8b0000');
                    
                    ctx.fillStyle = rocketGradient;
                    ctx.beginPath();
                    ctx.moveTo(bullet.x - 15, bullet.y);
                    ctx.lineTo(bullet.x + 15, bullet.y - 8);
                    ctx.lineTo(bullet.x + 15, bullet.y + 8);
                    ctx.closePath();
                    ctx.fill();
                    
                    // نيران العادم
                    const flameGradient = ctx.createRadialGradient(
                        bullet.x - 20, bullet.y, 0,
                        bullet.x - 30, bullet.y, 15
                    );
                    flameGradient.addColorStop(0, '#ffff00');
                    flameGradient.addColorStop(0.5, '#ff4500');
                    flameGradient.addColorStop(1, 'transparent');
                    
                    ctx.fillStyle = flameGradient;
                    ctx.beginPath();
                    ctx.arc(bullet.x - 20, bullet.y, 15, 0, Math.PI * 2);
                    ctx.fill();
                } else {
                    // رسم الرصاصات العادية
                    ctx.fillStyle = bullet.color;
                    ctx.beginPath();
                    ctx.arc(bullet.x, bullet.y, 4, 0, Math.PI * 2);
                    ctx.fill();
                    
                    // توهج خفيف
                    ctx.globalAlpha = 0.3;
                    ctx.beginPath();
                    ctx.arc(bullet.x, bullet.y, 8, 0, Math.PI * 2);
                    ctx.fill();
                }
                
                ctx.restore();
            });
        }
        
        // ============= تشغيل اللعبة =============
        window.addEventListener('load', () => {
            // إضافة دعم roundRect إذا لم يكن موجودًا
            if (!CanvasRenderingContext2D.prototype.roundRect) {
                CanvasRenderingContext2D.prototype.roundRect = function(x, y, w, h, r) {
                    if (w < 2 * r) r = w / 2;
                    if (h < 2 * r) r = h / 2;
                    this.beginPath();
                    this.moveTo(x + r, y);
                    this.arcTo(x + w, y, x + w, y + h, r);
                    this.arcTo(x + w, y + h, x, y + h, r);
                    this.arcTo(x, y + h, x, y, r);
                    this.arcTo(x, y, x + w, y, r);
                    this.closePath();
                    return this;
                }
            }
            
            // تعديل حجم الكانفاس مع النافذة
            function resizeCanvas() {
                const gameScreen = document.getElementById('gameScreen');
                canvas.style.width = '100%';
                canvas.style.height = '100%';
            }
            
            window.addEventListener('resize', resizeCanvas);
            resizeCanvas();
        });
    </script>
</body>
</html>

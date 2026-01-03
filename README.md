<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حصار القلعة - النسخة النهائية</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            touch-action: manipulation;
            -webkit-tap-highlight-color: transparent;
            user-select: none;
            image-rendering: crisp-edges;
            image-rendering: pixelated;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            background: #000;
            color: white;
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
            background: linear-gradient(135deg, #0a0a2a 0%, #1a1a3a 100%);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .loader-container {
            width: 300px;
            background: rgba(0, 0, 0, 0.8);
            padding: 30px;
            border-radius: 20px;
            border: 3px solid #ffd700;
            text-align: center;
        }

        .loader-title {
            color: #ffd700;
            font-size: 24px;
            margin-bottom: 20px;
        }

        .loader-bar {
            width: 100%;
            height: 20px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            overflow: hidden;
            margin-bottom: 10px;
        }

        .loader-fill {
            height: 100%;
            background: linear-gradient(90deg, #dc143c, #ffd700);
            width: 0%;
            transition: width 0.3s;
            border-radius: 10px;
        }

        .loading-details {
            color: #aaa;
            font-size: 14px;
            margin-top: 10px;
        }

        /* إحصائيات التحميل */
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
            margin-top: 20px;
            text-align: left;
        }

        .stat-item {
            background: rgba(255, 215, 0, 0.1);
            padding: 10px;
            border-radius: 10px;
            border: 1px solid rgba(255, 215, 0, 0.3);
        }

        .stat-value {
            color: #ffd700;
            font-size: 18px;
            font-weight: bold;
        }

        /* الشاشة الرئيسية */
        #gameScreen {
            width: 100vw;
            height: 100vh;
            position: relative;
            overflow: hidden;
        }

        /* الكانفاس الرئيسي - بحجم أكبر */
        #gameCanvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 1920px;
            height: 1080px;
            z-index: 1;
            image-rendering: -webkit-optimize-contrast;
            image-rendering: crisp-edges;
        }

        /* طبقة واجهة المستخدم */
        .ui-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: 2;
            pointer-events: none;
            overflow: hidden;
        }

        /* شريط المعلومات العلوي - مصغر */
        .top-hud {
            position: absolute;
            top: 15px;
            left: 15px;
            right: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.95),
                rgba(20, 20, 40, 0.95));
            padding: 12px 20px;
            border-radius: 20px;
            border: 3px solid #ffd700;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.8);
            pointer-events: all;
            backdrop-filter: blur(15px);
            transform: scale(0.95);
            transform-origin: top center;
        }

        .health-container {
            display: flex;
            align-items: center;
            gap: 15px;
            min-width: 200px;
        }

        .health-bar {
            flex: 1;
            height: 16px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 8px;
            overflow: hidden;
            border: 2px solid rgba(255, 255, 255, 0.2);
        }

        .health-fill {
            height: 100%;
            background: linear-gradient(90deg, 
                #ff0000, #ff6b6b, #ff0000);
            background-size: 200% 100%;
            animation: gradientFlow 3s infinite linear;
            border-radius: 8px;
            transition: width 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .health-text {
            color: #ffd700;
            font-size: 18px;
            font-weight: bold;
            min-width: 80px;
            text-align: center;
        }

        .resources-container {
            display: flex;
            gap: 25px;
            align-items: center;
        }

        .resource-item {
            display: flex;
            align-items: center;
            gap: 8px;
            background: linear-gradient(135deg, 
                rgba(255, 215, 0, 0.15),
                rgba(255, 215, 0, 0.05));
            padding: 8px 15px;
            border-radius: 12px;
            border: 2px solid rgba(255, 215, 0, 0.3);
            font-size: 18px;
            font-weight: bold;
        }

        .wave-container {
            background: linear-gradient(135deg, 
                rgba(139, 0, 0, 0.9),
                rgba(220, 20, 60, 0.9));
            padding: 8px 20px;
            border-radius: 12px;
            border: 2px solid #ffd700;
            text-align: center;
            min-width: 180px;
        }

        .wave-text {
            font-size: 18px;
            font-weight: bold;
            color: #ffd700;
        }

        /* منطقة التحكم المحسنة */
        .control-area {
            position: absolute;
            bottom: 150px;
            left: 50%;
            transform: translateX(-50%) scale(1.2);
            width: 400px;
            height: 400px;
            z-index: 3;
            pointer-events: none;
        }

        /* التحكم الدائري المحسن */
        .control-circle {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 350px;
            height: 350px;
            border-radius: 50%;
            background: radial-gradient(circle, 
                rgba(0, 0, 0, 0.6) 0%,
                rgba(0, 0, 0, 0.3) 50%,
                transparent 70%);
            border: 3px solid rgba(255, 215, 0, 0.4);
            pointer-events: all;
            backdrop-filter: blur(10px);
            box-shadow: 
                0 0 60px rgba(255, 215, 0, 0.3),
                inset 0 0 30px rgba(255, 255, 255, 0.1);
        }

        /* أزرار التحكم المحسنة */
        .control-button {
            position: absolute;
            width: 75px;
            height: 75px;
            border-radius: 50%;
            background: linear-gradient(145deg, 
                rgba(139, 0, 0, 0.95),
                rgba(220, 20, 60, 0.95));
            border: 4px solid #ffd700;
            color: white;
            font-size: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            user-select: none;
            box-shadow: 
                0 10px 30px rgba(0, 0, 0, 0.6),
                inset 0 2px 15px rgba(255, 255, 255, 0.2);
            transition: all 0.15s cubic-bezier(0.4, 0, 0.2, 1);
            pointer-events: all;
            z-index: 4;
        }

        .control-button:active {
            transform: scale(0.85);
            box-shadow: 
                0 5px 15px rgba(0, 0, 0, 0.4),
                inset 0 2px 10px rgba(0, 0, 0, 0.3);
        }

        /* أزرار الحركة */
        .move-btn {
            background: linear-gradient(145deg, 
                rgba(65, 105, 225, 0.95),
                rgba(100, 149, 237, 0.95));
            border-color: #4169e1;
        }

        /* تحديد مواقع الأزرار */
        .move-up { top: 20px; left: 50%; transform: translateX(-50%); }
        .move-left { top: 50%; left: 20px; transform: translateY(-50%); }
        .move-right { top: 50%; right: 20px; transform: translateY(-50%); }
        .move-down { bottom: 20px; left: 50%; transform: translateX(-50%); }
        
        .jump-btn { top: 70px; left: 70px; }
        .dash-btn { top: 70px; right: 70px; }
        .attack-btn { bottom: 70px; right: 70px; background: linear-gradient(145deg, #ff4500, #ff8c00); }
        .block-btn { bottom: 70px; left: 70px; background: linear-gradient(145deg, #4169e1, #6495ed); }

        /* شريط الأسلحة - مصغر */
        .weapon-bar {
            position: absolute;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%) scale(1.1);
            display: flex;
            gap: 12px;
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.9),
                rgba(20, 20, 40, 0.9));
            padding: 15px 25px;
            border-radius: 25px;
            border: 3px solid #dc143c;
            box-shadow: 0 10px 40px rgba(220, 20, 60, 0.4);
            pointer-events: all;
            z-index: 3;
            backdrop-filter: blur(15px);
        }

        .weapon-slot {
            width: 65px;
            height: 65px;
            border-radius: 15px;
            background: linear-gradient(135deg, 
                rgba(50, 50, 50, 0.9),
                rgba(30, 30, 30, 0.9));
            border: 3px solid #666;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            font-size: 32px;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;
        }

        .weapon-slot:hover {
            transform: translateY(-5px) scale(1.1);
            box-shadow: 0 10px 25px rgba(255, 215, 0, 0.4);
        }

        .weapon-slot.active {
            border-color: #ffd700;
            background: linear-gradient(135deg, 
                rgba(139, 0, 0, 0.8),
                rgba(220, 20, 60, 0.8));
            box-shadow: 
                0 0 30px #ffd700,
                inset 0 0 20px rgba(255, 215, 0, 0.4);
            transform: scale(1.15);
        }

        .weapon-name {
            font-size: 10px;
            margin-top: 3px;
            color: #aaa;
            font-weight: bold;
        }

        .ammo-count {
            position: absolute;
            bottom: 3px;
            right: 3px;
            background: #dc143c;
            color: white;
            font-size: 11px;
            padding: 2px 6px;
            border-radius: 10px;
            font-weight: bold;
            min-width: 22px;
            text-align: center;
            border: 1px solid #ffd700;
        }

        /* الأزرار الجانبية */
        .side-buttons {
            position: absolute;
            top: 120px;
            right: 20px;
            display: flex;
            flex-direction: column;
            gap: 15px;
            z-index: 3;
            pointer-events: all;
        }

        .side-button {
            width: 70px;
            height: 70px;
            background: linear-gradient(135deg, #8b0000, #dc143c);
            border: 3px solid #ffd700;
            border-radius: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 32px;
            cursor: pointer;
            box-shadow: 0 8px 25px rgba(139, 0, 0, 0.5);
            transition: all 0.3s;
        }

        .side-button:hover {
            transform: rotate(15deg) scale(1.1);
            box-shadow: 0 12px 30px rgba(255, 215, 0, 0.5);
        }

        /* النوافذ المنبثقة */
        .popup-window {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 400px;
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.95),
                rgba(20, 20, 40, 0.95));
            border-radius: 25px;
            border: 4px solid #ffd700;
            padding: 25px;
            box-shadow: 0 25px 60px rgba(0, 0, 0, 0.9);
            backdrop-filter: blur(20px);
            display: none;
            z-index: 100;
            pointer-events: all;
        }

        /* مؤشرات الضرر */
        .damage-indicator {
            position: absolute;
            font-size: 28px;
            font-weight: bold;
            text-shadow: 
                0 0 15px currentColor,
                0 0 30px currentColor;
            pointer-events: none;
            z-index: 10;
            animation: floatDamage 1.2s ease-out forwards;
        }

        @keyframes floatDamage {
            0% {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
            100% {
                opacity: 0;
                transform: translateY(-60px) scale(1.3);
            }
        }

        @keyframes gradientFlow {
            0% { background-position: 200% 0; }
            100% { background-position: -200% 0; }
        }

        /* تأثيرات الشاشة */
        .screen-effect {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 5;
        }

        .hit-flash {
            background: radial-gradient(circle at var(--hit-x, 50%) var(--hit-y, 50%), 
                rgba(255, 0, 0, 0.4) 0%,
                transparent 70%);
            opacity: 0;
            transition: opacity 0.3s;
        }

        .heal-flash {
            background: radial-gradient(circle at var(--heal-x, 50%) var(--heal-y, 50%), 
                rgba(0, 255, 0, 0.3) 0%,
                transparent 70%);
            opacity: 0;
            transition: opacity 0.3s;
        }

        /* التكيف مع الشاشات */
        @media (max-width: 768px) {
            .control-area {
                transform: translateX(-50%) scale(1);
                bottom: 120px;
            }
            
            .control-circle {
                width: 300px;
                height: 300px;
            }
            
            .control-button {
                width: 65px;
                height: 65px;
                font-size: 26px;
            }
            
            .weapon-bar {
                transform: translateX(-50%) scale(0.9);
                bottom: 20px;
            }
            
            .top-hud {
                transform: scale(0.85);
            }
        }
    </style>
</head>
<body>
    <!-- شاشة التحميل -->
    <div id="loadingScreen">
        <div class="loader-container">
            <div class="loader-title">🚀 تحميل عالم القلعة الأسطوري</div>
            <div class="loader-bar">
                <div class="loader-fill" id="loaderFill"></div>
            </div>
            <div class="loading-details" id="loadingText">تهيئة النظام...</div>
            
            <div class="stats-grid">
                <div class="stat-item">
                    <div>🎨 الجرافيكس:</div>
                    <div class="stat-value" id="graphicsStat">0%</div>
                </div>
                <div class="stat-item">
                    <div>🎮 التحكم:</div>
                    <div class="stat-value" id="controlsStat">0%</div>
                </div>
                <div class="stat-item">
                    <div>🎯 الأسلحة:</div>
                    <div class="stat-value" id="weaponsStat">0%</div>
                </div>
                <div class="stat-item">
                    <div>👾 الأعداء:</div>
                    <div class="stat-value" id="enemiesStat">0%</div>
                </div>
            </div>
        </div>
    </div>

    <!-- الشاشة الرئيسية -->
    <div id="gameScreen">
        <canvas id="gameCanvas"></canvas>
        
        <div class="ui-layer">
            <!-- شريط المعلومات العلوي -->
            <div class="top-hud">
                <div class="health-container">
                    <div class="health-text">❤️ <span id="healthValue">100</span>/100</div>
                    <div class="health-bar">
                        <div id="healthFill" class="health-fill" style="width: 100%"></div>
                    </div>
                </div>
                
                <div class="resources-container">
                    <div class="resource-item">
                        <span>💰</span>
                        <span id="goldValue">1000</span>
                    </div>
                    <div class="resource-item">
                        <span>⭐</span>
                        <span id="scoreValue">0</span>
                    </div>
                    <div class="resource-item">
                        <span>🎯</span>
                        <span id="levelValue">1</span>
                    </div>
                </div>
                
                <div class="wave-container">
                    <div class="wave-text">🌊 <span id="waveValue">1</span> | 👾 <span id="enemiesLeft">10</span></div>
                </div>
            </div>
            
            <!-- منطقة التحكم -->
            <div class="control-area">
                <div class="control-circle"></div>
                
                <!-- أزرار الحركة -->
                <div class="control-button move-btn move-up" data-action="moveUp">
                    ↑
                </div>
                <div class="control-button move-btn move-left" data-action="moveLeft">
                    ←
                </div>
                <div class="control-button move-btn move-right" data-action="moveRight">
                    →
                </div>
                <div class="control-button move-btn move-down" data-action="moveDown">
                    ↓
                </div>
                
                <!-- أزرار الإجراءات -->
                <div class="control-button jump-btn" data-action="jump">
                    ⬆️
                </div>
                <div class="control-button dash-btn" data-action="dash">
                    💨
                </div>
                <div class="control-button attack-btn" data-action="attack">
                    ⚔️
                </div>
                <div class="control-button block-btn" data-action="block">
                    🛡️
                </div>
            </div>
            
            <!-- شريط الأسلحة -->
            <div class="weapon-bar">
                <!-- 10 أسلحة -->
                <div class="weapon-slot active" data-weapon="sword">
                    🗡️
                    <div class="weapon-name">سيف</div>
                </div>
                <div class="weapon-slot" data-weapon="pistol">
                    🔫
                    <div class="weapon-name">مسدس</div>
                    <div class="ammo-count" id="pistolAmmo">30</div>
                </div>
                <div class="weapon-slot" data-weapon="shotgun">
                    💥
                    <div class="weapon-name">صيد</div>
                    <div class="ammo-count" id="shotgunAmmo">12</div>
                </div>
                <div class="weapon-slot" data-weapon="rifle">
                    🎯
                    <div class="weapon-name">قنص</div>
                    <div class="ammo-count" id="rifleAmmo">10</div>
                </div>
                <div class="weapon-slot" data-weapon="minigun">
                    🔥
                    <div class="weapon-name">رشاش</div>
                    <div class="ammo-count" id="minigunAmmo">200</div>
                </div>
                <div class="weapon-slot" data-weapon="rocket">
                    🚀
                    <div class="weapon-name">صاروخ</div>
                    <div class="ammo-count" id="rocketAmmo">5</div>
                </div>
            </div>
            
            <!-- الأزرار الجانبية -->
            <div class="side-buttons">
                <div class="side-button" id="shopButton" title="المتجر">
                    🏪
                </div>
                <div class="side-button" id="skillsButton" title="المهارات">
                    🎯
                </div>
                <div class="side-button" id="inventoryButton" title="المخزون">
                    🎒
                </div>
            </div>
            
            <!-- تأثيرات الشاشة -->
            <div class="screen-effect">
                <div class="hit-flash" id="hitFlash"></div>
                <div class="heal-flash" id="healFlash"></div>
            </div>
        </div>
        
        <!-- نوافذ -->
        <div class="popup-window" id="shopWindow">
            <h3 style="color: #ffd700; text-align: center; margin-bottom: 20px;">🏪 متجر المحارب</h3>
            <div style="max-height: 400px; overflow-y: auto;">
                <!-- سيتم ملؤها ديناميكياً -->
            </div>
        </div>
    </div>

    <!-- نظام الصوت -->
    <audio id="bgMusic" loop style="display: none;"></audio>

    <script>
        // ============= نظام التحميل الذكي =============
        const loadingScreen = document.getElementById('loadingScreen');
        const gameScreen = document.getElementById('gameScreen');
        const loaderFill = document.getElementById('loaderFill');
        const loadingText = document.getElementById('loadingText');
        
        const graphicsStat = document.getElementById('graphicsStat');
        const controlsStat = document.getElementById('controlsStat');
        const weaponsStat = document.getElementById('weaponsStat');
        const enemiesStat = document.getElementById('enemiesStat');
        
        let totalAssets = 100;
        let loadedAssets = 0;
        let gameLoaded = false;
        
        function updateLoading(progress, text, stats) {
            loaderFill.style.width = `${progress}%`;
            loadingText.textContent = text;
            
            if (stats) {
                if (stats.graphics !== undefined) graphicsStat.textContent = `${stats.graphics}%`;
                if (stats.controls !== undefined) controlsStat.textContent = `${stats.controls}%`;
                if (stats.weapons !== undefined) weaponsStat.textContent = `${stats.weapons}%`;
                if (stats.enemies !== undefined) enemiesStat.textContent = `${stats.enemies}%`;
            }
            
            if (progress >= 100 && !gameLoaded) {
                gameLoaded = true;
                setTimeout(() => {
                    loadingScreen.style.opacity = '0';
                    setTimeout(() => {
                        loadingScreen.style.display = 'none';
                        gameScreen.style.display = 'block';
                        initGame();
                    }, 500);
                }, 1000);
            }
        }
        
        function simulateLoading() {
            const steps = [
                {time: 500, progress: 10, text: "🚀 تشغيل محرك الرسوميات...", stats: {graphics: 10}},
                {time: 1000, progress: 25, text: "🎨 تحميل النماذج ثلاثية الأبعاد...", stats: {graphics: 40}},
                {time: 1500, progress: 40, text: "⚙️ تهيئة أنظمة الجسيمات...", stats: {graphics: 65}},
                {time: 2000, progress: 55, text: "🎮 إعداد نظام التحكم...", stats: {controls: 70, graphics: 80}},
                {time: 2500, progress: 65, text: "🔫 تحميل أصول الأسلحة...", stats: {weapons: 40, graphics: 90}},
                {time: 3000, progress: 75, text: "👾 توليد نماذج الأعداء...", stats: {enemies: 50, weapons: 70}},
                {time: 3500, progress: 85, text: "🎵 تهيئة النظام الصوتي...", stats: {controls: 90, enemies: 80}},
                {time: 4000, progress: 95, text: "✨ تطبيق التأثيرات البصرية...", stats: {graphics: 100, weapons: 100}},
                {time: 4500, progress: 100, text: "✅ جاهز للمعركة!", stats: {controls: 100, enemies: 100}}
            ];
            
            steps.forEach(step => {
                setTimeout(() => {
                    updateLoading(step.progress, step.text, step.stats);
                }, step.time);
            });
        }
        
        // بدء التحميل
        setTimeout(simulateLoading, 500);
        
        // ============= النظام الرئيسي =============
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // تحسين جودة الرسم
        canvas.width = 1920;
        canvas.height = 1080;
        
        // تمكين الأنظمة المتقدمة
        ctx.imageSmoothingEnabled = false;
        ctx.webkitImageSmoothingEnabled = false;
        ctx.mozImageSmoothingEnabled = false;
        
        // ============= نظام الكاميرا الذكية =============
        class SmartCamera {
            constructor() {
                this.x = 0;
                this.y = 0;
                this.zoom = 2; // تكبير عالي للرؤية الواضحة
                this.target = null;
                this.smoothness = 0.08; // سلاسة عالية
                this.shake = 0;
                this.bounds = {
                    minX: -500,
                    maxX: 2500,
                    minY: -300,
                    maxY: 1200
                };
                this.effects = [];
                this.offsetX = 0;
                this.offsetY = 0;
            }
            
            follow(target) {
                this.target = target;
            }
            
            update() {
                if (!this.target) return;
                
                // حساب الموضع المستهدف مع إزاحة لجعل اللاعب في وسط الشاشة
                const targetX = this.target.x - (window.innerWidth / 2) / this.zoom + this.offsetX;
                const targetY = this.target.y - (window.innerHeight / 2) / this.zoom + this.offsetY;
                
                // تطبيق الحدود
                const boundedX = Math.max(this.bounds.minX, Math.min(this.bounds.maxX, targetX));
                const boundedY = Math.max(this.bounds.minY, Math.min(this.bounds.maxY, targetY));
                
                // الحركة السلسة
                this.x += (boundedX - this.x) * this.smoothness;
                this.y += (boundedY - this.y) * this.smoothness;
                
                // تأثير الاهتزاز
                if (this.shake > 0) {
                    this.x += (Math.random() - 0.5) * this.shake;
                    this.y += (Math.random() - 0.5) * this.shake;
                    this.shake *= 0.85;
                    if (this.shake < 0.1) this.shake = 0;
                }
                
                // تحديث التأثيرات
                this.updateEffects();
            }
            
            updateEffects() {
                this.effects = this.effects.filter(effect => {
                    effect.time--;
                    return effect.time > 0;
                });
            }
            
            apply() {
                ctx.save();
                
                // تطبيق التكبير والتحويل
                ctx.scale(this.zoom, this.zoom);
                ctx.translate(-this.x, -this.y);
                
                // تطبيق التأثيرات
                this.effects.forEach(effect => {
                    if (effect.type === 'blur') {
                        ctx.filter = `blur(${effect.amount}px)`;
                    }
                });
            }
            
            reset() {
                ctx.restore();
                ctx.filter = 'none';
            }
            
            addShake(intensity) {
                this.shake = Math.max(this.shake, intensity);
            }
            
            addEffect(type, amount, duration) {
                this.effects.push({ type, amount, time: duration });
            }
            
            screenToWorld(x, y) {
                return {
                    x: (x / this.zoom) + this.x,
                    y: (y / this.zoom) + this.y
                };
            }
            
            worldToScreen(x, y) {
                return {
                    x: (x - this.x) * this.zoom,
                    y: (y - this.y) * this.zoom
                };
            }
            
            centerOn(x, y) {
                this.x = x - (window.innerWidth / 2) / this.zoom;
                this.y = y - (window.innerHeight / 2) / this.zoom;
            }
        }
        
        // ============= نظام الجسيمات فائقة الجودة =============
        class UltraParticleSystem {
            constructor() {
                this.particles = [];
                this.emitters = [];
            }
            
            createEffect(type, x, y, options = {}) {
                const config = {
                    count: options.count || 20,
                    color: options.color || '#ff0000',
                    size: options.size || 6,
                    speed: options.speed || 8,
                    life: options.life || 1.5,
                    gravity: options.gravity || 0.3,
                    spread: options.spread || 1
                };
                
                for (let i = 0; i < config.count; i++) {
                    const angle = Math.random() * Math.PI * 2;
                    const velocity = Math.random() * config.speed * config.spread;
                    
                    const particle = {
                        x, y,
                        vx: Math.cos(angle) * velocity,
                        vy: Math.sin(angle) * velocity - 2,
                        life: config.life,
                        maxLife: config.life,
                        size: Math.random() * config.size + config.size/2,
                        color: config.color,
                        type,
                        rotation: Math.random() * Math.PI * 2,
                        rotationSpeed: (Math.random() - 0.5) * 0.2,
                        gravity: config.gravity,
                        trail: [],
                        glow: Math.random() > 0.5
                    };
                    
                    if (type === 'blood') {
                        particle.color = '#ff0000';
                        particle.gravity = 0.4;
                        particle.size *= 1.2;
                    } else if (type === 'spark') {
                        particle.color = config.color || '#ffff00';
                        particle.life *= 0.7;
                        particle.glow = true;
                    } else if (type === 'magic') {
                        particle.color = config.color || '#9370db';
                        particle.size *= 1.5;
                        particle.rotationSpeed *= 2;
                        particle.glow = true;
                    }
                    
                    this.particles.push(particle);
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
                    if (p.trail.length > 1) {
                        ctx.save();
                        ctx.globalAlpha = alpha * 0.3;
                        
                        for (let i = 0; i < p.trail.length - 1; i++) {
                            const current = p.trail[i];
                            const next = p.trail[i + 1];
                            const trailAlpha = i / p.trail.length;
                            
                            const gradient = ctx.createLinearGradient(
                                current.x, current.y,
                                next.x, next.y
                            );
                            gradient.addColorStop(0, `${p.color}${Math.floor(trailAlpha * 255).toString(16).padStart(2, '0')}`);
                            gradient.addColorStop(1, `${p.color}00`);
                            
                            ctx.strokeStyle = gradient;
                            ctx.lineWidth = current.size * trailAlpha;
                            ctx.lineCap = 'round';
                            
                            ctx.beginPath();
                            ctx.moveTo(current.x, current.y);
                            ctx.lineTo(next.x, next.y);
                            ctx.stroke();
                        }
                        
                        ctx.restore();
                    }
                    
                    // رسم الجسيم
                    ctx.save();
                    ctx.globalAlpha = alpha;
                    
                    if (p.glow) {
                        // تأثير التوهج
                        const gradient = ctx.createRadialGradient(
                            p.x, p.y, 0,
                            p.x, p.y, p.size * 2
                        );
                        gradient.addColorStop(0, p.color);
                        gradient.addColorStop(1, 'transparent');
                        
                        ctx.fillStyle = gradient;
                        ctx.beginPath();
                        ctx.arc(p.x, p.y, p.size * 2, 0, Math.PI * 2);
                        ctx.fill();
                    }
                    
                    // الجسم الرئيسي
                    ctx.fillStyle = p.color;
                    ctx.translate(p.x, p.y);
                    ctx.rotate(p.rotation);
                    
                    if (p.type === 'magic') {
                        // نجمة سداسية
                        ctx.beginPath();
                        for (let i = 0; i < 6; i++) {
                            const angle = (i * Math.PI) / 3;
                            ctx.lineTo(
                                Math.cos(angle) * p.size,
                                Math.sin(angle) * p.size
                            );
                        }
                        ctx.closePath();
                        ctx.fill();
                    } else {
                        // دائرة
                        ctx.beginPath();
                        ctx.arc(0, 0, p.size, 0, Math.PI * 2);
                        ctx.fill();
                    }
                    
                    ctx.restore();
                });
            }
        }
        
        // ============= اللاعب - جرافيكس فائقة الجودة =============
        class UltraPlayer {
            constructor() {
                // الموضع والقياسات
                this.x = canvas.width / 2;
                this.y = canvas.height - 300;
                this.width = 35;
                this.height = 70;
                
                // الحركة
                this.speed = 8;
                this.jumpPower = 22;
                this.dashPower = 30;
                this.velocityX = 0;
                this.velocityY = 0;
                this.gravity = 0.8;
                this.isJumping = false;
                this.isDashing = false;
                this.dashCooldown = 0;
                this.onGround = true;
                
                // الإحصائيات
                this.health = 100;
                this.maxHealth = 100;
                this.armor = 50;
                this.maxArmor = 50;
                this.stamina = 100;
                this.maxStamina = 100;
                this.level = 1;
                this.exp = 0;
                this.maxExp = 100;
                
                // الأسلحة
                this.weapon = 'sword';
                this.weapons = {
                    sword: { 
                        name: "سيف فولاذي", 
                        damage: 35, 
                        range: 65, 
                        cooldown: 400, 
                        unlocked: true,
                        ammo: Infinity,
                        color: '#ffd700'
                    },
                    pistol: { 
                        name: "مسدس عالي الدقة", 
                        damage: 30, 
                        range: 350, 
                        cooldown: 300, 
                        unlocked: true,
                        ammo: 30,
                        color: '#ffff00'
                    },
                    shotgun: { 
                        name: "بندقية الصيد", 
                        damage: 45, 
                        range: 180, 
                        cooldown: 800, 
                        unlocked: true,
                        ammo: 12,
                        color: '#ff4500'
                    },
                    rifle: { 
                        name: "بندقية القنص", 
                        damage: 70, 
                        range: 600, 
                        cooldown: 1000, 
                        unlocked: true,
                        ammo: 10,
                        color: '#00ffff'
                    },
                    minigun: { 
                        name: "الرشاش السريع", 
                        damage: 20, 
                        range: 250, 
                        cooldown: 100, 
                        unlocked: true,
                        ammo: 200,
                        color: '#ff0000'
                    },
                    rocket: { 
                        name: "قاذفة الصواريخ", 
                        damage: 120, 
                        range: 450, 
                        cooldown: 1500, 
                        unlocked: true,
                        ammo: 5,
                        color: '#ff8c00'
                    }
                };
                
                // الحالة
                this.isBlocking = false;
                this.invincible = 0;
                this.combo = 0;
                this.lastComboTime = 0;
                this.lastAttack = 0;
                this.attackAnimation = 0;
                this.walkAnimation = 0;
                
                // الموارد
                this.gold = 1000;
                this.score = 0;
                this.kills = 0;
                
                // الرسوم المتحركة
                this.legAngle = 0;
                this.armAngle = 0;
                this.bobOffset = 0;
            }
            
            update() {
                // الحركة الأساسية
                this.velocityX = 0;
                this.velocityY = 0;
                
                if (keys.moveLeft) this.velocityX = -this.speed;
                if (keys.moveRight) this.velocityX = this.speed;
                if (keys.moveUp) this.velocityY = -this.speed;
                if (keys.moveDown) this.velocityY = this.speed;
                
                // القفز
                if (keys.jump && !this.isJumping && this.stamina >= 20) {
                    this.velocityY = -this.jumpPower;
                    this.isJumping = true;
                    this.onGround = false;
                    this.stamina -= 20;
                    particles.createEffect('spark', this.x, this.y + this.height, '#ffff00', {
                        count: 15,
                        size: 4,
                        speed: 6
                    });
                }
                
                // الدفعة السريعة
                if (keys.dash && this.dashCooldown <= 0 && this.stamina >= 30) {
                    this.isDashing = true;
                    this.dashCooldown = 60;
                    this.stamina -= 30;
                    
                    const dashMultiplier = 6;
                    this.x += this.velocityX * dashMultiplier;
                    this.y += this.velocityY * dashMultiplier * 0.7;
                    
                    particles.createEffect('spark', this.x, this.y, '#00ffff', {
                        count: 25,
                        size: 5,
                        speed: 10
                    });
                }
                
                // الجاذبية والحركة
                if (!this.onGround) {
                    this.velocityY += this.gravity;
                }
                
                this.x += this.velocityX;
                this.y += this.velocityY;
                
                // حدود الأرض
                const groundLevel = canvas.height - 200;
                if (this.y > groundLevel - this.height) {
                    this.y = groundLevel - this.height;
                    this.velocityY = 0;
                    this.isJumping = false;
                    this.onGround = true;
                } else {
                    this.onGround = false;
                }
                
                // تحديث التوقيتات
                if (this.dashCooldown > 0) this.dashCooldown--;
                if (this.invincible > 0) this.invincible--;
                if (this.attackAnimation > 0) this.attackAnimation--;
                
                // استعادة الطاقة
                if (!this.isJumping && !this.isDashing) {
                    this.stamina = Math.min(this.maxStamina, this.stamina + 1);
                }
                
                // الدفاع
                this.isBlocking = keys.block && this.stamina >= 2;
                if (this.isBlocking) {
                    this.stamina -= 2;
                }
                
                // تحديث الرسوم المتحركة
                this.updateAnimations();
                
                // تحديث الواجهة
                this.updateUI();
            }
            
            updateAnimations() {
                // حركة المشي
                if (Math.abs(this.velocityX) > 0 || Math.abs(this.velocityY) > 0) {
                    this.walkAnimation += 0.2;
                    this.bobOffset = Math.sin(this.walkAnimation) * 3;
                    this.legAngle = Math.sin(this.walkAnimation * 2) * 0.3;
                } else {
                    this.bobOffset = 0;
                    this.legAngle = 0;
                }
                
                // حركة الذراع
                if (this.attackAnimation > 0) {
                    this.armAngle = Math.sin(this.attackAnimation * 0.5) * 0.5;
                } else {
                    this.armAngle = 0;
                }
            }
            
            attack(enemies) {
                const now = Date.now();
                const weaponData = this.weapons[this.weapon];
                
                if (!weaponData.unlocked) return;
                if (now - this.lastAttack < weaponData.cooldown) return;
                if (weaponData.ammo <= 0 && weaponData.ammo !== Infinity) return;
                
                this.lastAttack = now;
                this.attackAnimation = 15;
                
                // استخدام الذخيرة
                if (weaponData.ammo !== Infinity) {
                    weaponData.ammo--;
                    updateAmmoUI();
                }
                
                // الكومبو
                if (now - this.lastComboTime < 2000) {
                    this.combo++;
                } else {
                    this.combo = 1;
                }
                this.lastComboTime = now;
                
                // تنفيذ الهجوم
                let damage = weaponData.damage;
                if (this.combo > 3) {
                    damage *= (1 + (this.combo - 3) * 0.1);
                }
                
                switch(this.weapon) {
                    case 'sword':
                        this.swingSword(enemies, damage, weaponData);
                        break;
                    default:
                        this.shootWeapon(damage, weaponData);
                        break;
                }
                
                // اهتزاز الكاميرا
                camera.addShake(this.weapon === 'rocket' ? 8 : 
                               this.weapon === 'shotgun' ? 5 : 2);
            }
            
            swingSword(enemies, damage, weaponData) {
                const swingRadius = weaponData.range;
                const swingAngle = this.attackAnimation * 0.2;
                
                // تأثيرات بصرية
                particles.createEffect('spark', 
                    this.x + Math.cos(swingAngle) * swingRadius,
                    this.y + Math.sin(swingAngle) * swingRadius,
                    weaponData.color,
                    { count: 10, size: 4, speed: 6 }
                );
                
                // البحث عن أعداء في نطاق السيف
                enemies.forEach(enemy => {
                    const dx = enemy.x - this.x;
                    const dy = enemy.y - this.y;
                    const distance = Math.sqrt(dx * dx + dy * dy);
                    
                    if (distance < swingRadius) {
                        const angleToEnemy = Math.atan2(dy, dx);
                        const angleDiff = Math.abs(angleToEnemy - swingAngle);
                        
                        if (angleDiff < 0.5) { // 30 درجة
                            const killed = enemy.takeDamage(damage, this);
                            if (killed) {
                                this.combo += 2;
                                particles.createEffect('blood', enemy.x, enemy.y, '#ff0000', {
                                    count: 20,
                                    size: 6,
                                    speed: 8
                                });
                            }
                        }
                    }
                });
            }
            
            shootWeapon(damage, weaponData) {
                const bulletCount = this.weapon === 'shotgun' ? 8 : 
                                  this.weapon === 'minigun' ? 3 : 1;
                const spread = this.weapon === 'shotgun' ? 0.25 : 
                              this.weapon === 'minigun' ? 0.15 : 0.05;
                
                for (let i = 0; i < bulletCount; i++) {
                    const bullet = new UltraBullet(
                        this.x + this.width,
                        this.y + this.height / 2,
                        20 + (this.weapon === 'rifle' ? 15 : 0),
                        damage / (this.weapon === 'shotgun' ? 2.5 : 1),
                        weaponData.color,
                        this.weapon,
                        spread
                    );
                    bullets.push(bullet);
                }
                
                // تأثيرات الإطلاق
                particles.createEffect('spark', 
                    this.x + this.width, 
                    this.y + this.height / 2, 
                    weaponData.color,
                    { 
                        count: this.weapon === 'shotgun' ? 20 : 12,
                        size: this.weapon === 'rocket' ? 8 : 4,
                        speed: 10
                    }
                );
            }
            
            takeDamage(amount, source) {
                if (this.invincible > 0) return false;
                
                let actualDamage = amount;
                
                if (this.isBlocking) {
                    actualDamage *= 0.3;
                    particles.createEffect('spark', this.x, this.y, '#4169e1', {
                        count: 8,
                        size: 3,
                        speed: 5
                    });
                }
                
                if (this.armor > 0) {
                    const armorDamage = Math.min(actualDamage, this.armor);
                    this.armor -= armorDamage;
                    actualDamage -= armorDamage;
                }
                
                this.health -= actualDamage;
                
                // تأثيرات الضرر
                showHitEffect(this.x + this.width/2, this.y + this.height/2);
                camera.addShake(4);
                createDamageIndicator(this.x, this.y - 30, `-${Math.round(actualDamage)}`, '#ff0000');
                
                particles.createEffect('blood', 
                    this.x + this.width/2, 
                    this.y + this.height/2, 
                    '#ff0000',
                    { count: 12, size: 5, speed: 6 }
                );
                
                if (this.health <= 0) {
                    gameOver();
                    return true;
                }
                
                this.invincible = 20;
                return false;
            }
            
            heal(amount) {
                const oldHealth = this.health;
                this.health = Math.min(this.maxHealth, this.health + amount);
                const healed = this.health - oldHealth;
                
                if (healed > 0) {
                    particles.createEffect('magic', 
                        this.x + this.width/2, 
                        this.y + this.height/2, 
                        '#00ff00',
                        { count: 15, size: 4, speed: 4 }
                    );
                    
                    createDamageIndicator(this.x, this.y - 30, `+${Math.round(healed)}`, '#00ff00');
                    showHealEffect(this.x + this.width/2, this.y + this.height/2);
                }
            }
            
            updateUI() {
                // تحديث القيم
                document.getElementById('healthValue').textContent = Math.round(this.health);
                document.getElementById('goldValue').textContent = this.gold;
                document.getElementById('scoreValue').textContent = this.score;
                document.getElementById('levelValue').textContent = this.level;
                
                // تحديث شريط الصحة
                const healthPercent = (this.health / this.maxHealth) * 100;
                document.getElementById('healthFill').style.width = `${healthPercent}%`;
                
                // تحديث لون النص بناءً على الصحة
                const healthText = document.querySelector('.health-text');
                if (healthPercent < 30) {
                    healthText.style.color = '#ff0000';
                    healthText.style.animation = 'pulse 0.5s infinite';
                } else if (healthPercent < 60) {
                    healthText.style.color = '#ffff00';
                    healthText.style.animation = 'none';
                } else {
                    healthText.style.color = '#ffd700';
                    healthText.style.animation = 'none';
                }
            }
            
            draw() {
                ctx.save();
                
                // تأثير الرجفة عند الضرر
                if (this.invincible > 0 && this.invincible % 4 < 2) {
                    ctx.globalAlpha = 0.6;
                }
                
                // تطبيق حركة التأرجح
                ctx.translate(0, this.bobOffset);
                
                // رسم الساقين
                this.drawLegs();
                
                // جسم المحارب
                this.drawBody();
                
                // الرأس
                this.drawHead();
                
                // السلاح
                this.drawWeapon();
                
                // تأثيرات خاصة
                this.drawEffects();
                
                ctx.restore();
                
                // رسم الكومبو والمعلومات
                this.drawHUD();
            }
            
            drawLegs() {
                // الساق اليسرى
                ctx.save();
                ctx.translate(this.x + this.width * 0.3, this.y + this.height);
                ctx.rotate(this.legAngle);
                
                ctx.fillStyle = '#8b4513';
                ctx.fillRect(-8, 0, 16, 45);
                
                // القدم
                ctx.fillStyle = '#654321';
                ctx.fillRect(-12, 40, 24, 10);
                
                ctx.restore();
                
                // الساق اليمنى
                ctx.save();
                ctx.translate(this.x + this.width * 0.7, this.y + this.height);
                ctx.rotate(-this.legAngle);
                
                ctx.fillStyle = '#8b4513';
                ctx.fillRect(-8, 0, 16, 45);
                
                // القدم
                ctx.fillStyle = '#654321';
                ctx.fillRect(-12, 40, 24, 10);
                
                ctx.restore();
            }
            
            drawBody() {
                // تدرج اللون للجسم
                const gradient = ctx.createLinearGradient(
                    this.x, this.y,
                    this.x, this.y + this.height
                );
                gradient.addColorStop(0, '#dc143c');
                gradient.addColorStop(0.4, '#b22222');
                gradient.addColorStop(0.7, '#8b0000');
                gradient.addColorStop(1, '#660000');
                
                ctx.fillStyle = gradient;
                
                // الجسم الرئيسي
                ctx.beginPath();
                ctx.roundRect(this.x, this.y, this.width, this.height, 10);
                ctx.fill();
                
                // تفاصيل الدروع
                ctx.strokeStyle = '#ffd700';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.roundRect(this.x + 5, this.y + 10, this.width - 10, this.height - 20, 5);
                ctx.stroke();
            }
            
            drawHead() {
                // الرأس
                ctx.fillStyle = '#ffb6c1';
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y - 12, 18, 0, Math.PI * 2);
                ctx.fill();
                
                // العيون
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x + this.width/2 - 7, this.y - 14, 3, 0, Math.PI * 2);
                ctx.arc(this.x + this.width/2 + 7, this.y - 14, 3, 0, Math.PI * 2);
                ctx.fill();
                
                // الفم
                ctx.strokeStyle = '#000';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y - 3, 8, 0.2, Math.PI - 0.2);
                ctx.stroke();
                
                // الخوذة
                ctx.strokeStyle = '#ffd700';
                ctx.lineWidth = 3;
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y - 12, 22, 0, Math.PI * 2);
                ctx.stroke();
            }
            
            drawWeapon() {
                const weaponData = this.weapons[this.weapon];
                ctx.save();
                ctx.translate(this.x + this.width, this.y + this.height * 0.4);
                ctx.rotate(this.armAngle);
                
                switch(this.weapon) {
                    case 'sword':
                        this.drawSword(weaponData);
                        break;
                    default:
                        this.drawGun(weaponData);
                        break;
                }
                
                ctx.restore();
            }
            
            drawSword(weaponData) {
                // نصل السيف
                const swordGradient = ctx.createLinearGradient(0, 0, 50, 0);
                swordGradient.addColorStop(0, '#ffffff');
                swordGradient.addColorStop(0.3, '#ffd700');
                swordGradient.addColorStop(0.7, '#c0c0c0');
                swordGradient.addColorStop(1, '#808080');
                
                ctx.fillStyle = swordGradient;
                ctx.beginPath();
                ctx.moveTo(0, -5);
                ctx.lineTo(45, -10);
                ctx.lineTo(40, 5);
                ctx.lineTo(0, 10);
                ctx.closePath();
                ctx.fill();
                
                // قبضة السيف
                ctx.fillStyle = '#8b4513';
                ctx.fillRect(-10, -8, 10, 16);
                
                // تأثير التوهج إذا كان في كومبو عالي
                if (this.combo > 5) {
                    ctx.strokeStyle = '#ff0000';
                    ctx.lineWidth = 2;
                    ctx.shadowBlur = 15;
                    ctx.shadowColor = '#ff0000';
                    ctx.beginPath();
                    ctx.moveTo(0, -3);
                    ctx.lineTo(50, -8);
                    ctx.stroke();
                    ctx.shadowBlur = 0;
                }
            }
            
            drawGun(weaponData) {
                // جسم السلاح
                ctx.fillStyle = '#2f4f4f';
                ctx.fillRect(0, -8, 30, 16);
                
                // السبطانة
                ctx.fillStyle = '#696969';
                ctx.fillRect(25, -6, 15, 12);
                
                // تفاصيل
                ctx.fillStyle = weaponData.color;
                ctx.fillRect(5, -4, 15, 8);
                
                // تأثير الإطلاق
                if (this.attackAnimation > 0) {
                    ctx.fillStyle = '#ffff00';
                    ctx.beginPath();
                    ctx.arc(40, 0, 5 + this.attackAnimation, 0, Math.PI * 2);
                    ctx.fill();
                }
            }
            
            drawEffects() {
                // تأثير الدفاع
                if (this.isBlocking) {
                    ctx.strokeStyle = '#4169e1';
                    ctx.lineWidth = 4;
                    ctx.globalAlpha = 0.4;
                    ctx.beginPath();
                    ctx.arc(this.x + this.width/2, this.y + this.height/2, 50, 0, Math.PI * 2);
                    ctx.stroke();
                }
                
                // تأثير الدفعة السريعة
                if (this.isDashing) {
                    ctx.strokeStyle = '#00ffff';
                    ctx.lineWidth = 3;
                    ctx.globalAlpha = 0.3;
                    ctx.beginPath();
                    ctx.arc(this.x + this.width/2, this.y + this.height/2, 65, 0, Math.PI * 2);
                    ctx.stroke();
                }
                
                ctx.globalAlpha = 1;
            }
            
            drawHUD() {
                // الكومبو
                if (this.combo > 1) {
                    ctx.save();
                    const screenPos = camera.worldToScreen(this.x + this.width/2, this.y - 50);
                    
                    ctx.font = 'bold 32px Arial';
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    
                    // تدرج اللون للكومبو
                    const comboGradient = ctx.createLinearGradient(
                        screenPos.x - 50, screenPos.y,
                        screenPos.x + 50, screenPos.y
                    );
                    comboGradient.addColorStop(0, '#ff0000');
                    comboGradient.addColorStop(0.5, '#ffd700');
                    comboGradient.addColorStop(1, '#ff0000');
                    
                    ctx.fillStyle = comboGradient;
                    ctx.shadowBlur = 10;
                    ctx.shadowColor = '#ffd700';
                    
                    const comboText = `COMBO x${this.combo}`;
                    ctx.fillText(comboText, screenPos.x, screenPos.y);
                    
                    // تأثير النبض للكومبو العالي
                    if (this.combo > 10) {
                        const pulse = Math.sin(Date.now() * 0.01) * 0.1 + 1;
                        ctx.font = `bold ${32 * pulse}px Arial`;
                        ctx.fillText(comboText, screenPos.x, screenPos.y);
                    }
                    
                    ctx.restore();
                }
            }
        }
        
        // ============= الرصاصات فائقة الجودة =============
        class UltraBullet {
            constructor(x, y, speed, damage, color, type, spread = 0) {
                this.x = x;
                this.y = y;
                this.speed = speed;
                this.damage = damage;
                this.color = color;
                this.type = type;
                this.spread = spread;
                
                this.vx = speed * (1 + (Math.random() - 0.5) * spread);
                this.vy = (Math.random() - 0.5) * speed * spread;
                
                this.size = type === 'rocket' ? 8 : 
                           type === 'shotgun' ? 4 : 6;
                
                this.trail = [];
                this.lifetime = 120;
                this.age = 0;
                this.rotation = 0;
                this.rotationSpeed = (Math.random() - 0.5) * 0.1;
                
                // خصائص خاصة حسب النوع
                if (type === 'rocket') {
                    this.size = 10;
                    this.gravity = 0.05;
                    this.smoke = true;
                } else if (type === 'rifle') {
                    this.size = 8;
                    this.penetration = 2;
                } else {
                    this.gravity = 0.02;
                    this.smoke = false;
                    this.penetration = 1;
                }
            }
            
            update() {
                this.x += this.vx;
                this.y += this.vy;
                this.vy += this.gravity || 0;
                this.rotation += this.rotationSpeed;
                this.age++;
                
                // إضافة أثر
                this.trail.push({
                    x: this.x,
                    y: this.y,
                    size: this.size * (1 - this.age / this.lifetime)
                });
                
                if (this.trail.length > 15) this.trail.shift();
                
                // دخان للصواريخ
                if (this.smoke && this.age % 3 === 0) {
                    particles.createEffect('spark', this.x, this.y, '#888888', {
                        count: 2,
                        size: 3,
                        speed: 2,
                        life: 0.8
                    });
                }
                
                return this.age >= this.lifetime;
            }
            
            draw() {
                // رسم الأثر
                if (this.trail.length > 1) {
                    ctx.save();
                    ctx.globalAlpha = 0.4;
                    
                    for (let i = 0; i < this.trail.length - 1; i++) {
                        const current = this.trail[i];
                        const next = this.trail[i + 1];
                        const alpha = i / this.trail.length;
                        
                        const gradient = ctx.createLinearGradient(
                            current.x, current.y,
                            next.x, next.y
                        );
                        gradient.addColorStop(0, `${this.color}${Math.floor(alpha * 255).toString(16).padStart(2, '0')}`);
                        gradient.addColorStop(1, `${this.color}00`);
                        
                        ctx.strokeStyle = gradient;
                        ctx.lineWidth = current.size;
                        ctx.lineCap = 'round';
                        
                        ctx.beginPath();
                        ctx.moveTo(current.x, current.y);
                        ctx.lineTo(next.x, next.y);
                        ctx.stroke();
                    }
                    
                    ctx.restore();
                }
                
                // رسم الرصاصة
                ctx.save();
                ctx.translate(this.x, this.y);
                ctx.rotate(this.rotation);
                
                switch(this.type) {
                    case 'rocket':
                        this.drawRocket();
                        break;
                    case 'rifle':
                        this.drawSniperBullet();
                        break;
                    default:
                        this.drawStandardBullet();
                        break;
                }
                
                ctx.restore();
            }
            
            drawRocket() {
                // جسم الصاروخ
                const bodyGradient = ctx.createLinearGradient(-this.size/2, 0, this.size/2, 0);
                bodyGradient.addColorStop(0, '#ff8c00');
                bodyGradient.addColorStop(0.3, '#ff4500');
                bodyGradient.addColorStop(0.7, '#8b0000');
                bodyGradient.addColorStop(1, '#660000');
                
                ctx.fillStyle = bodyGradient;
                ctx.beginPath();
                ctx.ellipse(0, 0, this.size/2, this.size, 0, 0, Math.PI * 2);
                ctx.fill();
                
                // رأس الصاروخ
                ctx.fillStyle = '#ffd700';
                ctx.beginPath();
                ctx.arc(this.size/2, 0, this.size/3, 0, Math.PI * 2);
                ctx.fill();
                
                // زعانف
                ctx.fillStyle = '#ff4500';
                for (let i = 0; i < 3; i++) {
                    const angle = (i * Math.PI * 2) / 3;
                    ctx.save();
                    ctx.rotate(angle);
                    ctx.beginPath();
                    ctx.moveTo(-this.size/2, 0);
                    ctx.lineTo(-this.size, -this.size/3);
                    ctx.lineTo(-this.size, this.size/3);
                    ctx.closePath();
                    ctx.fill();
                    ctx.restore();
                }
                
                // اللهب
                const flameSize = this.size * 1.5;
                const flameGradient = ctx.createRadialGradient(
                    -this.size/2, 0, 0,
                    -this.size/2, 0, flameSize
                );
                flameGradient.addColorStop(0, '#ffff00');
                flameGradient.addColorStop(0.5, '#ff4500');
                flameGradient.addColorStop(1, 'transparent');
                
                ctx.fillStyle = flameGradient;
                ctx.beginPath();
                ctx.arc(-this.size/2, 0, flameSize, 0, Math.PI * 2);
                ctx.fill();
            }
            
            drawSniperBullet() {
                // نواة الرصاصة
                const coreGradient = ctx.createRadialGradient(0, 0, 0, 0, 0, this.size);
                coreGradient.addColorStop(0, '#ffffff');
                coreGradient.addColorStop(0.7, '#00ffff');
                coreGradient.addColorStop(1, '#008b8b');
                
                ctx.fillStyle = coreGradient;
                ctx.beginPath();
                ctx.arc(0, 0, this.size, 0, Math.PI * 2);
                ctx.fill();
                
                // قلب الرصاصة
                ctx.fillStyle = '#ffffff';
                ctx.beginPath();
                ctx.arc(0, 0, this.size/3, 0, Math.PI * 2);
                ctx.fill();
                
                // حلقة خارجية
                ctx.strokeStyle = '#00ffff';
                ctx.lineWidth = 1;
                ctx.beginPath();
                ctx.arc(0, 0, this.size * 1.2, 0, Math.PI * 2);
                ctx.stroke();
            }
            
            drawStandardBullet() {
                // الجسم الرئيسي
                const bulletGradient = ctx.createLinearGradient(
                    -this.size/2, -this.size/2,
                    this.size/2, this.size/2
                );
                bulletGradient.addColorStop(0, this.color);
                bulletGradient.addColorStop(1, '#ffffff');
                
                ctx.fillStyle = bulletGradient;
                ctx.beginPath();
                ctx.ellipse(0, 0, this.size/2, this.size, 0, 0, Math.PI * 2);
                ctx.fill();
                
                // طرف الرصاصة
                ctx.fillStyle = '#ffffff';
                ctx.beginPath();
                ctx.arc(this.size/2, 0, this.size/3, 0, Math.PI * 2);
                ctx.fill();
            }
        }
        
        // ============= الأعداء فائقة الجودة =============
        class UltraEnemy {
            constructor(wave, type = 'normal') {
                this.type = type;
                
                // القياسات حسب النوع
                switch(type) {
                    case 'boss':
                        this.width = 80;
                        this.height = 100;
                        this.speed = 1.2 + wave * 0.2;
                        this.health = 200 + wave * 50;
                        this.maxHealth = 200 + wave * 50;
                        this.damage = 20 + wave * 5;
                        this.reward = 300 + wave * 100;
                        this.color = '#ff8c00';
                        break;
                    case 'elite':
                        this.width = 60;
                        this.height = 80;
                        this.speed = 2 + wave * 0.3;
                        this.health = 100 + wave * 30;
                        this.maxHealth = 100 + wave * 30;
                        this.damage = 15 + wave * 4;
                        this.reward = 150 + wave * 50;
                        this.color = '#ffd700';
                        break;
                    default:
                        this.width = 45;
                        this.height = 65;
                        this.speed = 2.5 + wave * 0.4;
                        this.health = 50 + wave * 20;
                        this.maxHealth = 50 + wave * 20;
                        this.damage = 10 + wave * 3;
                        this.reward = 80 + wave * 30;
                        this.color = '#ffff00';
                        break;
                }
                
                // الموضع
                this.x = -100 - Math.random() * 200;
                this.y = canvas.height - 200 - this.height + Math.random() * 50;
                
                // الحركة
                this.velocityX = 0;
                this.velocityY = 0;
                this.onGround = true;
                
                // الرسوم المتحركة
                this.walkAnimation = Math.random() * Math.PI * 2;
                this.legAngle = 0;
                this.hitAnimation = 0;
                this.attackCooldown = 0;
                
                // التحديات
                this.attackRange = this.type === 'boss' ? 70 : 50;
                this.attackSpeed = 60 - wave * 2;
            }
            
            update(player) {
                // الحركة نحو اللاعب
                const dx = player.x - this.x;
                const dy = player.y - this.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance > 0) {
                    this.velocityX = (dx / distance) * this.speed;
                    this.velocityY = (dy / distance) * this.speed * 0.3;
                }
                
                // تحديث الموضع
                this.x += this.velocityX;
                this.y += this.velocityY;
                
                // حدود الأرض
                const groundLevel = canvas.height - 200;
                if (this.y > groundLevel - this.height) {
                    this.y = groundLevel - this.height;
                    this.onGround = true;
                } else {
                    this.onGround = false;
                    this.velocityY += 0.5; // جاذبية
                }
                
                // تحديث الرسوم المتحركة
                this.updateAnimation();
                
                // تحديث الهجوم
                if (this.attackCooldown > 0) this.attackCooldown--;
                
                // الهجوم على اللاعب
                if (distance < this.attackRange && this.attackCooldown <= 0) {
                    this.attackCooldown = this.attackSpeed;
                    return player.takeDamage(this.damage, this);
                }
                
                return false;
            }
            
            updateAnimation() {
                // حركة المشي
                if (Math.abs(this.velocityX) > 0.1) {
                    this.walkAnimation += 0.3;
                    this.legAngle = Math.sin(this.walkAnimation) * 0.4;
                } else {
                    this.legAngle = 0;
                }
                
                // تأثير الضرر
                if (this.hitAnimation > 0) {
                    this.hitAnimation--;
                }
            }
            
            takeDamage(amount, source) {
                this.health -= amount;
                this.hitAnimation = 10;
                
                // تأثيرات بصرية
                const effectColor = this.type === 'boss' ? '#8b0000' : 
                                  this.type === 'elite' ? '#ff4500' : '#ff0000';
                
                particles.createEffect('blood', 
                    this.x + this.width/2, 
                    this.y + this.height/2, 
                    effectColor,
                    { 
                        count: 15 + amount / 5,
                        size: this.type === 'boss' ? 8 : 5,
                        speed: 6 + amount / 10
                    }
                );
                
                // الارتداد
                const dx = this.x - source.x;
                const dy = this.y - source.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance > 0) {
                    const force = Math.min(15, amount / 5);
                    this.x += (dx / distance) * force;
                    this.y += (dy / distance) * force * 0.5;
                }
                
                // مؤشر الضرر
                createDamageIndicator(
                    this.x + this.width/2, 
                    this.y - 20, 
                    `-${Math.round(amount)}`, 
                    effectColor
                );
                
                return this.health <= 0;
            }
            
            draw() {
                ctx.save();
                
                // تأثير الضرر
                if (this.hitAnimation > 0) {
                    ctx.globalAlpha = 0.7;
                }
                
                // رسم الساقين
                this.drawLegs();
                
                // رسم الجسم
                this.drawBody();
                
                // رسم الرأس
                this.drawHead();
                
                // رسم شريط الصحة
                this.drawHealthBar();
                
                // تأثيرات خاصة للبوس
                if (this.type === 'boss') {
                    this.drawBossEffects();
                }
                
                ctx.restore();
            }
            
            drawLegs() {
                const legWidth = 12;
                const legHeight = 40;
                
                // الساق اليسرى
                ctx.save();
                ctx.translate(this.x + this.width * 0.3, this.y + this.height);
                ctx.rotate(this.legAngle);
                
                ctx.fillStyle = '#8b4513';
                ctx.fillRect(-legWidth/2, 0, legWidth, legHeight);
                
                // القدم
                ctx.fillStyle = '#654321';
                ctx.fillRect(-legWidth, legHeight - 5, legWidth * 2, 10);
                
                ctx.restore();
                
                // الساق اليمنى
                ctx.save();
                ctx.translate(this.x + this.width * 0.7, this.y + this.height);
                ctx.rotate(-this.legAngle);
                
                ctx.fillStyle = '#8b4513';
                ctx.fillRect(-legWidth/2, 0, legWidth, legHeight);
                
                // القدم
                ctx.fillStyle = '#654321';
                ctx.fillRect(-legWidth, legHeight - 5, legWidth * 2, 10);
                
                ctx.restore();
            }
            
            drawBody() {
                // تدرج اللون للجسم
                const bodyGradient = ctx.createLinearGradient(
                    this.x, this.y,
                    this.x, this.y + this.height
                );
                
                if (this.type === 'boss') {
                    bodyGradient.addColorStop(0, '#ff8c00');
                    bodyGradient.addColorStop(0.4, '#ff4500');
                    bodyGradient.addColorStop(0.7, '#8b0000');
                    bodyGradient.addColorStop(1, '#660000');
                } else if (this.type === 'elite') {
                    bodyGradient.addColorStop(0, '#ffd700');
                    bodyGradient.addColorStop(0.4, '#daa520');
                    bodyGradient.addColorStop(0.7, '#b8860b');
                    bodyGradient.addColorStop(1, '#8b4513');
                } else {
                    bodyGradient.addColorStop(0, '#ffff00');
                    bodyGradient.addColorStop(0.4, '#ffd700');
                    bodyGradient.addColorStop(0.7, '#b8860b');
                    bodyGradient.addColorStop(1, '#8b4513');
                }
                
                ctx.fillStyle = bodyGradient;
                
                // الجسم الرئيسي
                ctx.beginPath();
                ctx.roundRect(this.x, this.y, this.width, this.height, 8);
                ctx.fill();
                
                // تفاصيل الدروع
                ctx.strokeStyle = this.type === 'boss' ? '#ffd700' : '#ffffff';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.roundRect(this.x + 5, this.y + 10, this.width - 10, this.height - 20, 4);
                ctx.stroke();
            }
            
            drawHead() {
                const headRadius = this.type === 'boss' ? 25 : 
                                 this.type === 'elite' ? 20 : 15;
                
                // الرأس
                ctx.fillStyle = this.type === 'boss' ? '#ff4500' : 
                               this.type === 'elite' ? '#daa520' : '#f0e68c';
                ctx.beginPath();
                ctx.arc(
                    this.x + this.width/2, 
                    this.y - headRadius/2, 
                    headRadius, 
                    0, 
                    Math.PI * 2
                );
                ctx.fill();
                
                // العيون
                ctx.fillStyle = '#000';
                const eyeSpacing = this.type === 'boss' ? 15 : 10;
                ctx.beginPath();
                ctx.arc(
                    this.x + this.width/2 - eyeSpacing, 
                    this.y - headRadius/2 - 3, 
                    4, 
                    0, 
                    Math.PI * 2
                );
                ctx.arc(
                    this.x + this.width/2 + eyeSpacing, 
                    this.y - headRadius/2 - 3, 
                    4, 
                    0, 
                    Math.PI * 2
                );
                ctx.fill();
                
                // الفم
                ctx.strokeStyle = '#000';
                ctx.lineWidth = 3;
                ctx.beginPath();
                const mouthRadius = this.type === 'boss' ? 15 : 10;
                ctx.arc(
                    this.x + this.width/2, 
                    this.y - headRadius/2 + 10, 
                    mouthRadius, 
                    0.2, 
                    Math.PI - 0.2
                );
                ctx.stroke();
                
                // قرون للبوس
                if (this.type === 'boss') {
                    ctx.fillStyle = '#ffd700';
                    for (let i = 0; i < 2; i++) {
                        const side = i === 0 ? -1 : 1;
                        ctx.beginPath();
                        ctx.moveTo(this.x + this.width/2 + side * 20, this.y - headRadius/2 - 10);
                        ctx.lineTo(this.x + this.width/2 + side * 35, this.y - headRadius/2 - 25);
                        ctx.lineTo(this.x + this.width/2 + side * 30, this.y - headRadius/2 - 15);
                        ctx.closePath();
                        ctx.fill();
                    }
                }
            }
            
            drawHealthBar() {
                const barWidth = this.width;
                const barHeight = 8;
                const barY = this.y - 15;
                
                // خلفية الشريط
                ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
                ctx.fillRect(this.x, barY, barWidth, barHeight);
                
                // شريط الصحة
                const healthPercent = this.health / this.maxHealth;
                if (healthPercent > 0) {
                    let barColor;
                    if (healthPercent > 0.6) {
                        barColor = '#00ff00';
                    } else if (healthPercent > 0.3) {
                        barColor = '#ffff00';
                    } else {
                        barColor = '#ff0000';
                    }
                    
                    ctx.fillStyle = barColor;
                    ctx.fillRect(this.x, barY, barWidth * healthPercent, barHeight);
                    
                    // تفاصيل إضافية للبوس
                    if (this.type === 'boss') {
                        ctx.strokeStyle = '#ffd700';
                        ctx.lineWidth = 2;
                        ctx.strokeRect(this.x - 1, barY - 1, barWidth + 2, barHeight + 2);
                        
                        // نص نسبة الصحة للبوس
                        ctx.fillStyle = '#ffffff';
                        ctx.font = 'bold 12px Arial';
                        ctx.textAlign = 'center';
                        ctx.fillText(
                            `${Math.round(healthPercent * 100)}%`,
                            this.x + barWidth/2,
                            barY + barHeight/2 + 4
                        );
                    }
                }
            }
            
            drawBossEffects() {
                // هالة حول البوس
                ctx.strokeStyle = '#ff4500';
                ctx.lineWidth = 3;
                ctx.globalAlpha = 0.3;
                ctx.beginPath();
                ctx.arc(
                    this.x + this.width/2,
                    this.y + this.height/2,
                    this.width * 0.8,
                    0,
                    Math.PI * 2
                );
                ctx.stroke();
                
                // تأثير النبض
                const pulse = Math.sin(Date.now() * 0.005) * 5;
                ctx.lineWidth = 2;
                ctx.globalAlpha = 0.2;
                ctx.beginPath();
                ctx.arc(
                    this.x + this.width/2,
                    this.y + this.height/2,
                    this.width * 0.8 + pulse,
                    0,
                    Math.PI * 2
                );
                ctx.stroke();
                
                ctx.globalAlpha = 1;
            }
        }
        
        // ============= نظام الموجات الذكي =============
        class SmartWaveSystem {
            constructor() {
                this.wave = 1;
                this.enemiesPerWave = 6;
                this.enemiesSpawned = 0;
                this.enemiesDefeated = 0;
                this.spawnTimer = 0;
                this.spawnDelay = 100;
                this.waveCooldown = 0;
                this.bossEvery = 5;
                this.eliteChance = 0.2;
            }
            
            update() {
                if (this.waveCooldown > 0) {
                    this.waveCooldown--;
                    
                    if (this.waveCooldown === 60) {
                        showMessage(`🌊 الموجة ${this.wave} تبدأ!`, '#ffd700');
                    }
                    
                    return;
                }
                
                this.spawnTimer++;
                
                if (this.spawnTimer >= this.spawnDelay && 
                    this.enemiesSpawned < this.enemiesPerWave) {
                    
                    this.enemiesSpawned++;
                    
                    // تحديد نوع العدو
                    let enemyType = 'normal';
                    const rand = Math.random();
                    
                    if (this.wave % this.bossEvery === 0 && this.enemiesSpawned === 1) {
                        enemyType = 'boss';
                    } else if (rand < this.eliteChance + this.wave * 0.01) {
                        enemyType = 'elite';
                    }
                    
                    enemies.push(new UltraEnemy(this.wave, enemyType));
                    this.spawnTimer = 0;
                    
                    updateEnemiesUI();
                }
                
                if (this.enemiesDefeated >= this.enemiesPerWave) {
                    this.nextWave();
                }
            }
            
            nextWave() {
                this.wave++;
                this.enemiesPerWave = 6 + Math.floor(this.wave * 1.2);
                this.enemiesSpawned = 0;
                this.enemiesDefeated = 0;
                this.waveCooldown = 180;
                this.eliteChance = Math.min(0.4, 0.2 + this.wave * 0.02);
                
                // مكافآت الموجة
                player.gold += 200 * this.wave;
                player.score += 800 * this.wave;
                player.heal(25 + this.wave * 5);
                
                // تحديث الواجهة
                document.getElementById('waveValue').textContent = this.wave;
                document.getElementById('enemiesLeft').textContent = this.enemiesPerWave;
                
                // رسالة نجاح
                showMessage(
                    `🎉 اكتملت الموجة ${this.wave-1}! +${200 * this.wave} ذهب`,
                    '#00ff00',
                    3000
                );
            }
        }
        
        // ============= دوال المساعدة =============
        function updateAmmoUI() {
            // تحديث عداد الذخيرة
            document.getElementById('pistolAmmo').textContent = player.weapons.pistol.ammo;
            document.getElementById('shotgunAmmo').textContent = player.weapons.shotgun.ammo;
            document.getElementById('rifleAmmo').textContent = player.weapons.rifle.ammo;
            document.getElementById('minigunAmmo').textContent = player.weapons.minigun.ammo;
            document.getElementById('rocketAmmo').textContent = player.weapons.rocket.ammo;
        }
        
        function updateEnemiesUI() {
            const remaining = waveSystem.enemiesPerWave - waveSystem.enemiesDefeated;
            document.getElementById('enemiesLeft').textContent = remaining;
        }
        
        function showHitEffect(x, y) {
            const hitFlash = document.getElementById('hitFlash');
            const screenPos = camera.worldToScreen(x, y);
            
            hitFlash.style.setProperty('--hit-x', `${screenPos.x}px`);
            hitFlash.style.setProperty('--hit-y', `${screenPos.y}px`);
            hitFlash.style.opacity = '0.8';
            
            setTimeout(() => {
                hitFlash.style.opacity = '0';
            }, 300);
        }
        
        function showHealEffect(x, y) {
            const healFlash = document.getElementById('healFlash');
            const screenPos = camera.worldToScreen(x, y);
            
            healFlash.style.setProperty('--heal-x', `${screenPos.x}px`);
            healFlash.style.setProperty('--heal-y', `${screenPos.y}px`);
            healFlash.style.opacity = '0.6';
            
            setTimeout(() => {
                healFlash.style.opacity = '0';
            }, 300);
        }
        
        function createDamageIndicator(x, y, text, color) {
            const indicator = document.createElement('div');
            indicator.className = 'damage-indicator';
            indicator.textContent = text;
            indicator.style.color = color;
            
            const screenPos = camera.worldToScreen(x, y);
            indicator.style.left = `${screenPos.x}px`;
            indicator.style.top = `${screenPos.y}px`;
            
            document.querySelector('.ui-layer').appendChild(indicator);
            
            setTimeout(() => {
                indicator.remove();
            }, 1200);
        }
        
        function showMessage(text, color = '#ffffff', duration = 2000) {
            createDamageIndicator(
                player.x + player.width/2,
                player.y - 100,
                text,
                color
            );
        }
        
        // ============= التحكم =============
        let keys = {
            moveLeft: false,
            moveRight: false,
            moveUp: false,
            moveDown: false,
            jump: false,
            dash: false,
            block: false
        };
        
        function setupControls() {
            // أزرار التحكم
            document.querySelectorAll('.control-button').forEach(btn => {
                const action = btn.dataset.action;
                
                // اللمس
                btn.addEventListener('touchstart', (e) => {
                    e.preventDefault();
                    if (action === 'attack') {
                        player.attack(enemies);
                    } else {
                        keys[action] = true;
                    }
                });
                
                btn.addEventListener('touchend', (e) => {
                    e.preventDefault();
                    if (action !== 'attack' && action !== 'jump') {
                        keys[action] = false;
                    }
                });
                
                btn.addEventListener('touchcancel', (e) => {
                    e.preventDefault();
                    keys[action] = false;
                });
                
                // الماوس
                btn.addEventListener('mousedown', () => {
                    if (action === 'attack') {
                        player.attack(enemies);
                    } else {
                        keys[action] = true;
                    }
                });
                
                btn.addEventListener('mouseup', () => {
                    if (action !== 'attack' && action !== 'jump') {
                        keys[action] = false;
                    }
                });
                
                btn.addEventListener('mouseleave', () => {
                    keys[action] = false;
                });
            });
            
            // اختيار الأسلحة
            document.querySelectorAll('.weapon-slot').forEach(slot => {
                slot.addEventListener('click', () => {
                    const weapon = slot.dataset.weapon;
                    if (player.weapons[weapon].unlocked) {
                        // إلغاء تفعيل الجميع
                        document.querySelectorAll('.weapon-slot').forEach(s => {
                            s.classList.remove('active');
                        });
                        
                        // تفعيل السلاح المحدد
                        slot.classList.add('active');
                        player.weapon = weapon;
                        
                        showMessage(`✅ ${player.weapons[weapon].name}`, '#ffd700');
                    }
                });
            });
            
            // الأزرار الجانبية
            document.getElementById('shopButton').addEventListener('click', () => {
                const shop = document.getElementById('shopWindow');
                shop.style.display = shop.style.display === 'block' ? 'none' : 'block';
            });
            
            // لوحة المفاتيح للاختبار
            document.addEventListener('keydown', (e) => {
                switch(e.key.toLowerCase()) {
                    case 'arrowleft': case 'a': keys.moveLeft = true; break;
                    case 'arrowright': case 'd': keys.moveRight = true; break;
                    case 'arrowup': case 'w': keys.moveUp = true; break;
                    case 'arrowdown': case 's': keys.moveDown = true; break;
                    case ' ': keys.jump = true; break;
                    case 'shift': keys.block = true; break;
                    case 'control': keys.dash = true; break;
                    case 'x': case 'f': player.attack(enemies); break;
                    case '1': case '2': case '3': case '4': case '5': case '6':
                        const weapons = ['sword', 'pistol', 'shotgun', 'rifle', 'minigun', 'rocket'];
                        const index = parseInt(e.key) - 1;
                        if (weapons[index] && player.weapons[weapons[index]].unlocked) {
                            player.weapon = weapons[index];
                            updateWeaponSelection();
                        }
                        break;
                }
            });
            
            document.addEventListener('keyup', (e) => {
                switch(e.key.toLowerCase()) {
                    case 'arrowleft': case 'a': keys.moveLeft = false; break;
                    case 'arrowright': case 'd': keys.moveRight = false; break;
                    case 'arrowup': case 'w': keys.moveUp = false; break;
                    case 'arrowdown': case 's': keys.moveDown = false; break;
                    case ' ': keys.jump = false; break;
                    case 'shift': keys.block = false; break;
                    case 'control': keys.dash = false; break;
                }
            });
            
            function updateWeaponSelection() {
                document.querySelectorAll('.weapon-slot').forEach(slot => {
                    slot.classList.remove('active');
                    if (slot.dataset.weapon === player.weapon) {
                        slot.classList.add('active');
                    }
                });
            }
        }
        
        // ============= نهاية اللعبة =============
        function gameOver() {
            gameRunning = false;
            
            setTimeout(() => {
                const stats = `
                    ⭐ النقاط: ${player.score}
                    🌊 الموجة: ${waveSystem.wave}
                    💰 الذهب: ${player.gold}
                    🎯 المستوى: ${player.level}
                    🗡️ الكومبو الأعلى: x${player.combo}
                    👾 الأعداء المهزومين: ${player.kills}
                `;
                
                if (confirm(`💀 انتهت اللعبة!\n\n${stats}\n\nهل تريد إعادة اللعبة؟`)) {
                    location.reload();
                }
            }, 1000);
        }
        
        // ============= رسم الخلفية =============
        function drawBackground() {
            // السماء
            const skyGradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            skyGradient.addColorStop(0, '#0f0c29');
            skyGradient.addColorStop(0.3, '#302b63');
            skyGradient.addColorStop(0.7, '#24243e');
            skyGradient.addColorStop(1, '#1a1a2e');
            
            ctx.fillStyle = skyGradient;
            ctx.fillRect(camera.x - 500, camera.y - 300, 
                        canvas.width + 1000, canvas.height + 600);
            
            // النجوم
            ctx.fillStyle = '#ffffff';
            for (let i = 0; i < 200; i++) {
                const starX = (camera.x + i * 97) % (canvas.width * 2);
                const starY = (camera.y + i * 137) % canvas.height;
                const size = Math.sin(Date.now() * 0.0005 + i) * 0.8 + 1.2;
                const alpha = Math.sin(Date.now() * 0.001 + i) * 0.3 + 0.7;
                
                ctx.globalAlpha = alpha;
                ctx.beginPath();
                ctx.arc(starX, starY, size, 0, Math.PI * 2);
                ctx.fill();
            }
            ctx.globalAlpha = 1;
            
            // القمر
            ctx.fillStyle = '#f0f0f0';
            ctx.beginPath();
            ctx.arc(camera.x + canvas.width * 0.8, camera.y + 100, 40, 0, Math.PI * 2);
            ctx.fill();
            
            // تفاصيل القمر
            ctx.fillStyle = '#d0d0d0';
            ctx.beginPath();
            ctx.arc(camera.x + canvas.width * 0.75, camera.y + 90, 8, 0, Math.PI * 2);
            ctx.arc(camera.x + canvas.width * 0.85, camera.y + 120, 12, 0, Math.PI * 2);
            ctx.arc(camera.x + canvas.width * 0.78, camera.y + 130, 6, 0, Math.PI * 2);
            ctx.fill();
            
            // الأرض
            const groundY = canvas.height - 200;
            const groundGradient = ctx.createLinearGradient(
                0, groundY,
                0, groundY + 200
            );
            groundGradient.addColorStop(0, '#228b22');
            groundGradient.addColorStop(0.5, '#1c7a1c');
            groundGradient.addColorStop(1, '#145214');
            
            ctx.fillStyle = groundGradient;
            ctx.fillRect(camera.x - 500, camera.y + groundY, 
                        canvas.width + 1000, 200);
            
            // العشب
            ctx.fillStyle = '#32cd32';
            for (let i = 0; i < 40; i++) {
                const x = camera.x + (i * 50) % (canvas.width + 1000);
                const height = 15 + Math.sin(i * 0.5) * 5;
                ctx.fillRect(x, camera.y + groundY, 40, height);
            }
            
            // التلال في الخلفية
            ctx.fillStyle = '#2e8b57';
            for (let i = 0; i < 3; i++) {
                const hillX = camera.x + i * 400;
                const hillY = groundY - 100 - i * 30;
                const hillWidth = 300 + i * 100;
                
                ctx.beginPath();
                ctx.arc(hillX + hillWidth/2, hillY, hillWidth/2, 0, Math.PI);
                ctx.fill();
            }
            
            // القلعة في الخلفية
            this.drawCastle();
        }
        
        function drawCastle() {
            const castleX = camera.x + canvas.width * 0.6;
            const castleY = canvas.height - 400;
            
            // القاعدة
            ctx.fillStyle = '#8b0000';
            ctx.fillRect(castleX, castleY, 200, 200);
            
            // الجدران
            ctx.fillStyle = '#b22222';
            for (let i = 0; i < 5; i++) {
                const wallX = castleX + i * 40;
                ctx.fillRect(wallX, castleY - 30, 20, 30);
            }
            
            // الأبراج
            ctx.fillStyle = '#dc143c';
            ctx.fillRect(castleX - 20, castleY - 80, 40, 80);
            ctx.fillRect(castleX + 180, castleY - 80, 40, 80);
            
            // الأعلام
            ctx.fillStyle = '#ffd700';
            for (let i = 0; i < 2; i++) {
                const flagX = castleX + (i * 200);
                ctx.beginPath();
                ctx.moveTo(flagX, castleY - 80);
                ctx.lineTo(flagX - 20, castleY - 120);
                ctx.lineTo(flagX, castleY - 100);
                ctx.closePath();
                ctx.fill();
            }
            
            // النوافذ
            ctx.fillStyle = '#ffff00';
            for (let i = 0; i < 3; i++) {
                for (let j = 0; j < 2; j++) {
                    const windowX = castleX + 30 + i * 50;
                    const windowY = castleY + 30 + j * 60;
                    ctx.fillRect(windowX, windowY, 15, 25);
                }
            }
            
            // البوابة
            ctx.fillStyle = '#8b4513';
            ctx.fillRect(castleX + 80, castleY + 100, 40, 80);
            
            // تفاصيل البوابة
            ctx.fillStyle = '#ffd700';
            ctx.beginPath();
            ctx.arc(castleX + 100, castleY + 140, 5, 0, Math.PI * 2);
            ctx.fill();
        }
        
        // ============= المتغيرات الرئيسية =============
        let player, enemies = [], bullets = [], particles, camera, waveSystem;
        let gameRunning = true;
        
        // ============= تهيئة اللعبة =============
        function initGame() {
            // إنشاء الأنظمة
            particles = new UltraParticleSystem();
            camera = new SmartCamera();
            waveSystem = new SmartWaveSystem();
            
            // إنشاء اللاعب
            player = new UltraPlayer();
            camera.follow(player);
            camera.centerOn(player.x, player.y);
            
            // تهيئة المصفوفات
            enemies = [];
            bullets = [];
            
            // إعداد التحكم
            setupControls();
            
            // تحديث الواجهة
            updateAmmoUI();
            updateEnemiesUI();
            
            // رسالة ترحيب
            setTimeout(() => {
                showMessage('🎮 حرك الأزرار للتحكم! ⚔️ اضغط للهجوم', '#ffd700', 3000);
            }, 1000);
            
            // بدء حلقة اللعبة
            gameLoop();
        }
        
        // ============= حلقة اللعبة الرئيسية =============
        function gameLoop() {
            if (!gameRunning || !gameLoaded) return;
            
            // تحديث الكاميرا
            camera.update();
            
            // تطبيق تحويلات الكاميرا
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            camera.apply();
            
            // رسم الخلفية
            drawBackground();
            
            // تحديث الأنظمة
            particles.update();
            player.update();
            waveSystem.update();
            
            // تحديث الأعداء
            for (let i = enemies.length - 1; i >= 0; i--) {
                const enemy = enemies[i];
                const hitPlayer = enemy.update(player);
                
                enemy.draw();
                
                if (enemy.health <= 0) {
                    // مكافآت القتل
                    player.gold += enemy.reward;
                    player.score += enemy.reward * 2;
                    player.kills++;
                    waveSystem.enemiesDefeated++;
                    
                    // تأثيرات القتل
                    particles.createEffect('blood', enemy.x, enemy.y, 
                        enemy.type === 'boss' ? '#8b0000' : '#ff0000',
                        { 
                            count: 25,
                            size: enemy.type === 'boss' ? 10 : 6,
                            speed: 8
                        }
                    );
                    
                    enemies.splice(i, 1);
                    updateEnemiesUI();
                }
            }
            
            // تحديث الرصاصات
            for (let i = bullets.length - 1; i >= 0; i--) {
                const bullet = bullets[i];
                const expired = bullet.update();
                
                if (expired) {
                    bullets.splice(i, 1);
                    continue;
                }
                
                // حدود الشاشة
                if (bullet.x < camera.x - 500 || 
                    bullet.x > camera.x + canvas.width + 500 ||
                    bullet.y < camera.y - 500 || 
                    bullet.y > camera.y + canvas.height + 500) {
                    bullets.splice(i, 1);
                    continue;
                }
                
                // الاصطدام بالأعداء
                let hit = false;
                for (let j = enemies.length - 1; j >= 0; j--) {
                    const enemy = enemies[j];
                    
                    const dx = bullet.x - (enemy.x + enemy.width/2);
                    const dy = bullet.y - (enemy.y + enemy.height/2);
                    const distance = Math.sqrt(dx * dx + dy * dy);
                    const hitRadius = enemy.width/2 + bullet.size;
                    
                    if (distance < hitRadius) {
                        if (enemy.takeDamage(bullet.damage, player)) {
                            // قتل العدو
                            player.gold += enemy.reward;
                            player.score += enemy.reward * 2;
                            player.kills++;
                            waveSystem.enemiesDefeated++;
                            
                            particles.createEffect('blood', enemy.x, enemy.y, 
                                enemy.type === 'boss' ? '#8b0000' : '#ff0000',
                                { count: 30, size: 8, speed: 10 }
                            );
                            
                            enemies.splice(j, 1);
                            updateEnemiesUI();
                        }
                        
                        // تأثيرات الاصطدام
                        if (bullet.type === 'rocket') {
                            // انفجار الصاروخ
                            particles.createEffect('spark', bullet.x, bullet.y, '#ff4500', {
                                count: 40,
                                size: 8,
                                speed: 15,
                                spread: 2
                            });
                            camera.addShake(6);
                        } else {
                            particles.createEffect('spark', bullet.x, bullet.y, bullet.color, {
                                count: 12,
                                size: 4,
                                speed: 6
                            });
                        }
                        
                        bullets.splice(i, 1);
                        hit = true;
                        break;
                    }
                }
                
                if (!hit) {
                    bullet.draw();
                }
            }
            
            // رسم اللاعب
            player.draw();
            
            // رسم الجسيمات
            particles.draw();
            
            // إعادة ضبط الكاميرا
            camera.reset();
            
            // الاستمرار في الحلقة
            requestAnimationFrame(gameLoop);
        }
        
        // ============= بدء النظام =============
        window.addEventListener('resize', () => {
            // إعادة حساب موضع الكاميرا
            if (camera && player) {
                camera.centerOn(player.x, player.y);
            }
        });
    </script>
</body>
</html>

<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حصار القلعة - المحارب الأسطوري الإصدار المتطور</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            touch-action: manipulation;
            -webkit-tap-highlight-color: transparent;
            user-select: none;
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
        
        .loader {
            width: 200px;
            height: 4px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 2px;
            overflow: hidden;
            margin: 30px 0;
        }
        
        .loader-fill {
            height: 100%;
            background: linear-gradient(90deg, #dc143c, #ffd700);
            width: 0%;
            transition: width 0.3s;
            border-radius: 2px;
        }
        
        /* الشاشة الرئيسية للعبة */
        #gameScreen {
            width: 100vw;
            height: 100vh;
            position: relative;
            background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e);
            overflow: hidden;
            display: none;
        }
        
        /* طبقة الكانفاس */
        #gameCanvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
        }
        
        /* طبقة واجهة المستخدم */
        .ui-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 2;
            pointer-events: none;
        }
        
        /* شريط المعلومات العلوي المحسن */
        .top-bar {
            position: absolute;
            top: 10px;
            left: 10px;
            right: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: linear-gradient(135deg, rgba(0, 0, 0, 0.9), rgba(20, 20, 40, 0.9));
            padding: 12px 20px;
            border-radius: 20px;
            border: 3px solid #ffd700;
            box-shadow: 0 5px 20px rgba(255, 215, 0, 0.2);
            pointer-events: all;
            backdrop-filter: blur(10px);
        }
        
        .player-info {
            display: flex;
            align-items: center;
            gap: 20px;
        }
        
        .stat-container {
            display: flex;
            flex-direction: column;
            gap: 5px;
            min-width: 180px;
        }
        
        .stat-label {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 14px;
            color: #ffd700;
        }
        
        .stat-bar-container {
            width: 100%;
            height: 12px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 6px;
            overflow: hidden;
            border: 1px solid rgba(255, 255, 255, 0.2);
            position: relative;
        }
        
        .stat-bar {
            height: 100%;
            border-radius: 6px;
            transition: width 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;
        }
        
        .stat-bar::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(
                90deg,
                transparent,
                rgba(255, 255, 255, 0.3),
                transparent
            );
            animation: shimmer 2s infinite;
        }
        
        .health-bar { 
            background: linear-gradient(90deg, #ff0000, #ff6b6b, #ff0000);
            background-size: 200% 100%;
            animation: gradientMove 3s infinite linear;
        }
        
        .armor-bar { 
            background: linear-gradient(90deg, #4169e1, #6495ed, #4169e1);
            background-size: 200% 100%;
            animation: gradientMove 3s infinite linear;
        }
        
        .stamina-bar { 
            background: linear-gradient(90deg, #32cd32, #7cfc00, #32cd32);
            background-size: 200% 100%;
            animation: gradientMove 3s infinite linear;
        }
        
        .stat-text {
            font-size: 12px;
            text-align: center;
            margin-top: 2px;
            color: #aaa;
        }
        
        /* موارد اللاعب */
        .resources {
            display: flex;
            gap: 20px;
        }
        
        .resource-item {
            display: flex;
            align-items: center;
            gap: 8px;
            background: rgba(255, 215, 0, 0.1);
            padding: 8px 15px;
            border-radius: 10px;
            border: 1px solid rgba(255, 215, 0, 0.3);
        }
        
        /* معلومات الموجة */
        .wave-info {
            background: linear-gradient(135deg, rgba(139, 0, 0, 0.8), rgba(220, 20, 60, 0.8));
            padding: 10px 20px;
            border-radius: 12px;
            border: 2px solid #ffd700;
            text-align: center;
            min-width: 200px;
        }
        
        /* شريط التقدم */
        .progress-bar {
            width: 100%;
            height: 6px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 3px;
            margin-top: 5px;
            overflow: hidden;
        }
        
        .progress-fill {
            height: 100%;
            background: linear-gradient(90deg, #00ffff, #0080ff);
            border-radius: 3px;
            transition: width 0.5s ease-out;
        }
        
        /* منطقة التحكم المحسنة */
        .control-area {
            position: absolute;
            bottom: 180px;
            left: 50%;
            transform: translateX(-50%);
            width: 500px;
            height: 500px;
            z-index: 3;
            pointer-events: none;
        }
        
        /* التحكم الدائري المحسن */
        .circular-controls {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 400px;
            height: 400px;
            border-radius: 50%;
            background: radial-gradient(circle, rgba(0,0,0,0.5) 0%, rgba(0,0,0,0) 70%);
            border: 2px solid rgba(255, 215, 0, 0.3);
            pointer-events: all;
            backdrop-filter: blur(5px);
        }
        
        /* زر التحكم المحسن */
        .control-button {
            position: absolute;
            width: 80px;
            height: 80px;
            border-radius: 50%;
            background: linear-gradient(145deg, 
                rgba(139, 0, 0, 0.95),
                rgba(220, 20, 60, 0.95));
            border: 3px solid #ffd700;
            color: white;
            font-size: 28px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            user-select: none;
            box-shadow: 
                0 8px 25px rgba(0, 0, 0, 0.5),
                inset 0 2px 10px rgba(255, 255, 255, 0.1);
            transition: all 0.15s cubic-bezier(0.4, 0, 0.2, 1);
            pointer-events: all;
            z-index: 4;
        }
        
        .control-button:hover {
            transform: scale(1.1);
            box-shadow: 
                0 12px 30px rgba(255, 215, 0, 0.3),
                inset 0 2px 15px rgba(255, 255, 255, 0.2);
        }
        
        .control-button:active {
            transform: scale(0.9);
            box-shadow: 
                0 4px 15px rgba(0, 0, 0, 0.3),
                inset 0 2px 8px rgba(0, 0, 0, 0.2);
        }
        
        /* أزرار الحركة */
        .move-btn {
            background: linear-gradient(145deg, 
                rgba(65, 105, 225, 0.95),
                rgba(100, 149, 237, 0.95));
            border-color: #4169e1;
        }
        
        .action-btn {
            background: linear-gradient(145deg, 
                rgba(255, 69, 0, 0.95),
                rgba(255, 140, 0, 0.95));
            border-color: #ff4500;
        }
        
        /* تحديد مواقع الأزرار */
        .move-up { top: 20px; left: 50%; transform: translateX(-50%); }
        .move-left { top: 50%; left: 20px; transform: translateY(-50%); }
        .move-right { top: 50%; right: 20px; transform: translateY(-50%); }
        .move-down { bottom: 20px; left: 50%; transform: translateX(-50%); }
        
        .jump-btn { top: 80px; left: 80px; }
        .dash-btn { top: 80px; right: 80px; }
        .attack-btn { bottom: 80px; right: 80px; }
        .block-btn { bottom: 80px; left: 80px; }
        
        /* اختيار الأسلحة المحسن */
        .weapon-selector {
            position: absolute;
            bottom: 40px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 15px;
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.85),
                rgba(20, 20, 40, 0.85));
            padding: 20px;
            border-radius: 25px;
            border: 3px solid #dc143c;
            box-shadow: 0 10px 30px rgba(220, 20, 60, 0.3);
            pointer-events: all;
            z-index: 3;
            backdrop-filter: blur(10px);
        }
        
        .weapon-slot {
            width: 70px;
            height: 70px;
            border-radius: 15px;
            background: linear-gradient(135deg, 
                rgba(50, 50, 50, 0.9),
                rgba(30, 30, 30, 0.9));
            border: 2px solid #666;
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
        
        .weapon-slot::before {
            content: '';
            position: absolute;
            top: 0;
            left: -100%;
            width: 100%;
            height: 100%;
            background: linear-gradient(
                90deg,
                transparent,
                rgba(255, 255, 255, 0.2),
                transparent
            );
            transition: left 0.5s;
        }
        
        .weapon-slot:hover::before {
            left: 100%;
        }
        
        .weapon-slot:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 20px rgba(255, 215, 0, 0.3);
        }
        
        .weapon-slot.active {
            border-color: #ffd700;
            background: linear-gradient(135deg, 
                rgba(139, 0, 0, 0.7),
                rgba(220, 20, 60, 0.7));
            box-shadow: 
                0 0 25px #ffd700,
                inset 0 0 15px rgba(255, 215, 0, 0.3);
            transform: scale(1.15);
        }
        
        .weapon-slot.locked {
            opacity: 0.5;
            cursor: not-allowed;
            filter: grayscale(1);
        }
        
        .weapon-name {
            font-size: 10px;
            margin-top: 5px;
            color: #aaa;
        }
        
        .ammo-count {
            position: absolute;
            bottom: 5px;
            right: 5px;
            background: #dc143c;
            color: white;
            font-size: 11px;
            padding: 2px 6px;
            border-radius: 10px;
            font-weight: bold;
            min-width: 20px;
            text-align: center;
        }
        
        /* المتجر المتطور */
        .shop-container {
            position: absolute;
            top: 90px;
            right: 20px;
            display: flex;
            flex-direction: column;
            gap: 10px;
            z-index: 3;
            pointer-events: all;
        }
        
        .shop-toggle {
            width: 70px;
            height: 70px;
            background: linear-gradient(135deg, #8b0000, #dc143c);
            border: 3px solid #ffd700;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 32px;
            cursor: pointer;
            box-shadow: 0 8px 25px rgba(139, 0, 0, 0.5);
            transition: all 0.3s;
        }
        
        .shop-toggle:hover {
            transform: rotate(15deg) scale(1.1);
            box-shadow: 0 12px 30px rgba(255, 215, 0, 0.4);
        }
        
        .shop-panel {
            width: 350px;
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.95),
                rgba(20, 20, 40, 0.95));
            border-radius: 20px;
            border: 3px solid #ffd700;
            padding: 20px;
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(15px);
            display: none;
        }
        
        .shop-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 10px;
            border-bottom: 2px solid #ffd700;
        }
        
        .shop-categories {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }
        
        .category-btn {
            flex: 1;
            padding: 8px;
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid #666;
            border-radius: 8px;
            color: white;
            cursor: pointer;
            transition: all 0.2s;
            text-align: center;
        }
        
        .category-btn.active {
            background: #dc143c;
            border-color: #ffd700;
        }
        
        .shop-items {
            max-height: 400px;
            overflow-y: auto;
            padding-right: 5px;
        }
        
        .shop-item {
            background: linear-gradient(135deg, 
                rgba(50, 50, 50, 0.7),
                rgba(30, 30, 30, 0.7));
            border: 2px solid #444;
            padding: 15px;
            margin: 10px 0;
            border-radius: 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        
        .shop-item:hover {
            transform: translateX(-5px);
            border-color: #ffd700;
            box-shadow: 0 5px 15px rgba(255, 215, 0, 0.2);
        }
        
        .shop-item.disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none !important;
        }
        
        .item-info {
            display: flex;
            flex-direction: column;
            gap: 5px;
        }
        
        .item-name {
            font-size: 16px;
            color: #ffd700;
        }
        
        .item-desc {
            font-size: 12px;
            color: #aaa;
            max-width: 200px;
        }
        
        .item-price {
            color: #ffd700;
            font-weight: bold;
            font-size: 18px;
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        /* نافذة المهارات */
        .skills-panel {
            position: absolute;
            top: 90px;
            left: 20px;
            width: 300px;
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.95),
                rgba(20, 20, 40, 0.95));
            border-radius: 20px;
            border: 3px solid #4169e1;
            padding: 20px;
            display: none;
            pointer-events: all;
            backdrop-filter: blur(15px);
        }
        
        .skill-tree {
            margin-top: 15px;
        }
        
        .skill-node {
            background: rgba(65, 105, 225, 0.2);
            border: 2px solid #4169e1;
            padding: 10px;
            margin: 10px 0;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.2s;
        }
        
        .skill-node:hover {
            background: rgba(65, 105, 225, 0.4);
            transform: translateX(5px);
        }
        
        /* تأثيرات الجرافيكس */
        .damage-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: radial-gradient(
                circle at var(--x, 50%) var(--y, 50%),
                rgba(255, 0, 0, 0.4) 0%,
                transparent 60%
            );
            pointer-events: none;
            opacity: 0;
            z-index: 10;
            transition: opacity 0.3s;
        }
        
        .hit-indicator {
            position: absolute;
            font-size: 24px;
            font-weight: bold;
            color: #ff0000;
            text-shadow: 0 0 10px #ff0000;
            pointer-events: none;
            z-index: 11;
            animation: floatUp 1s ease-out forwards;
        }
        
        /* رسائل التلميح */
        .hint-message {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.9),
                rgba(20, 20, 40, 0.9));
            color: #ffd700;
            padding: 25px 40px;
            border-radius: 20px;
            border: 3px solid #ffd700;
            text-align: center;
            font-size: 22px;
            z-index: 100;
            display: none;
            box-shadow: 0 20px 50px rgba(0, 0, 0, 0.8);
            backdrop-filter: blur(10px);
            min-width: 300px;
        }
        
        /* شاشة الإنجازات */
        .achievement-popup {
            position: fixed;
            top: 20px;
            right: 20px;
            background: linear-gradient(135deg, #8b0000, #dc143c);
            border: 3px solid #ffd700;
            padding: 15px 20px;
            border-radius: 15px;
            display: none;
            align-items: center;
            gap: 15px;
            z-index: 1000;
            animation: slideInRight 0.5s ease-out;
        }
        
        /* الحركات */
        @keyframes shimmer {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }
        
        @keyframes gradientMove {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }
        
        @keyframes floatUp {
            0% {
                opacity: 1;
                transform: translateY(0);
            }
            100% {
                opacity: 0;
                transform: translateY(-50px);
            }
        }
        
        @keyframes slideInRight {
            from {
                transform: translateX(100%);
                opacity: 0;
            }
            to {
                transform: translateX(0);
                opacity: 1;
            }
        }
        
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }
        
        @keyframes glow {
            0%, 100% { box-shadow: 0 0 20px #ffd700; }
            50% { box-shadow: 0 0 40px #ffd700; }
        }
        
        /* التكيف مع الشاشات الصغيرة */
        @media (max-width: 768px) {
            .control-area {
                width: 400px;
                height: 400px;
                bottom: 150px;
            }
            
            .circular-controls {
                width: 320px;
                height: 320px;
            }
            
            .control-button {
                width: 65px;
                height: 65px;
                font-size: 24px;
            }
            
            .top-bar {
                flex-direction: column;
                gap: 10px;
                padding: 10px;
            }
            
            .weapon-selector {
                padding: 15px;
                bottom: 20px;
            }
            
            .weapon-slot {
                width: 60px;
                height: 60px;
                font-size: 28px;
            }
            
            .shop-panel {
                width: 300px;
                right: 10px;
            }
        }
        
        @media (max-width: 480px) {
            .control-area {
                width: 350px;
                height: 350px;
            }
            
            .circular-controls {
                width: 280px;
                height: 280px;
            }
            
            .control-button {
                width: 55px;
                height: 55px;
                font-size: 20px;
            }
            
            .weapon-selector {
                gap: 10px;
                padding: 10px;
            }
            
            .weapon-slot {
                width: 50px;
                height: 50px;
                font-size: 24px;
            }
        }
    </style>
</head>
<body>
    <!-- شاشة التحميل -->
    <div id="loadingScreen">
        <h1 style="color: #ffd700; font-size: 3em; margin-bottom: 20px;">حصار القلعة</h1>
        <p style="color: #aaa; margin-bottom: 30px; text-align: center; max-width: 80%;">
            تحميل عالم المحارب الأسطوري...
        </p>
        <div class="loader">
            <div class="loader-fill" id="loaderFill"></div>
        </div>
        <p id="loadingText" style="margin-top: 20px; color: #ffd700;">جاري تهيئة الجرافيكس...</p>
    </div>
    
    <!-- الشاشة الرئيسية -->
    <div id="gameScreen">
        <canvas id="gameCanvas"></canvas>
        
        <div class="ui-layer">
            <!-- شريط المعلومات العلوي -->
            <div class="top-bar">
                <div class="player-info">
                    <div class="stat-container">
                        <div class="stat-label">
                            <span>❤️</span>
                            <span>الصحة</span>
                        </div>
                        <div class="stat-bar-container">
                            <div id="healthBar" class="stat-bar health-bar" style="width: 100%"></div>
                        </div>
                        <div id="healthText" class="stat-text">100/100</div>
                    </div>
                    
                    <div class="stat-container">
                        <div class="stat-label">
                            <span>🛡️</span>
                            <span>الدرع</span>
                        </div>
                        <div class="stat-bar-container">
                            <div id="armorBar" class="stat-bar armor-bar" style="width: 100%"></div>
                        </div>
                        <div id="armorText" class="stat-text">50/50</div>
                    </div>
                    
                    <div class="stat-container">
                        <div class="stat-label">
                            <span>⚡</span>
                            <span>الطاقة</span>
                        </div>
                        <div class="stat-bar-container">
                            <div id="staminaBar" class="stat-bar stamina-bar" style="width: 100%"></div>
                        </div>
                        <div id="staminaText" class="stat-text">100/100</div>
                    </div>
                </div>
                
                <div class="resources">
                    <div class="resource-item">
                        <span>💰</span>
                        <span id="goldText">1000</span>
                    </div>
                    <div class="resource-item">
                        <span>⭐</span>
                        <span id="scoreText">0</span>
                    </div>
                    <div class="resource-item">
                        <span>💎</span>
                        <span id="gemText">0</span>
                    </div>
                </div>
                
                <div class="wave-info">
                    <div style="font-size: 18px; font-weight: bold;">
                        🌊 الموجة: <span id="waveText">1</span>
                    </div>
                    <div style="font-size: 14px; margin-top: 5px;">
                        👾 متبقية: <span id="enemiesText">10</span>
                    </div>
                    <div class="progress-bar">
                        <div id="waveProgress" class="progress-fill" style="width: 0%"></div>
                    </div>
                </div>
            </div>
            
            <!-- منطقة التحكم الدائري -->
            <div class="control-area">
                <div class="circular-controls"></div>
                
                <!-- أزرار الحركة -->
                <div class="control-button move-btn move-up" data-action="moveUp" title="تحرك للأعلى">
                    ↑
                </div>
                <div class="control-button move-btn move-left" data-action="moveLeft" title="تحرك لليسار">
                    ←
                </div>
                <div class="control-button move-btn move-right" data-action="moveRight" title="تحرك لليمين">
                    →
                </div>
                <div class="control-button move-btn move-down" data-action="moveDown" title="تحرك للأسفل">
                    ↓
                </div>
                
                <!-- أزرار الإجراءات -->
                <div class="control-button action-btn jump-btn" data-action="jump" title="القفز">
                    ⬆️
                </div>
                <div class="control-button action-btn dash-btn" data-action="dash" title="الدفعة السريعة">
                    💨
                </div>
                <div class="control-button action-btn attack-btn" data-action="attack" title="الهجوم">
                    ⚔️
                </div>
                <div class="control-button action-btn block-btn" data-action="block" title="الدفاع">
                    🛡️
                </div>
            </div>
            
            <!-- اختيار الأسلحة -->
            <div class="weapon-selector">
                <!-- 10 أسلحة مختلفة -->
                <div class="weapon-slot active" data-weapon="sword" title="السيف الأساسي">
                    🗡️
                    <div class="weapon-name">سيف</div>
                </div>
                <div class="weapon-slot" data-weapon="dagger" title="خنجر سريع">
                    🔪
                    <div class="weapon-name">خنجر</div>
                </div>
                <div class="weapon-slot" data-weapon="pistol" title="المسدس">
                    🔫
                    <div class="weapon-name">مسدس</div>
                    <div class="ammo-count" id="pistolAmmo">30</div>
                </div>
                <div class="weapon-slot locked" data-weapon="shotgun" title="بندقية الصيد">
                    💥
                    <div class="weapon-name">صيد</div>
                </div>
                <div class="weapon-slot locked" data-weapon="rifle" title="بندقية القنص">
                    🎯
                    <div class="weapon-name">قنص</div>
                </div>
                <div class="weapon-slot locked" data-weapon="minigun" title="رشاش سريع">
                    🔥
                    <div class="weapon-name">رشاش</div>
                </div>
                <div class="weapon-slot locked" data-weapon="rocket" title="قاذفة صواريخ">
                    🚀
                    <div class="weapon-name">صواريخ</div>
                </div>
                <div class="weapon-slot locked" data-weapon="flamethrower" title="قاذفة لهب">
                    🔥
                    <div class="weapon-name">لهب</div>
                </div>
                <div class="weapon-slot locked" data-weapon="laser" title="بندقية ليزر">
                    ✨
                    <div class="weapon-name">ليزر</div>
                </div>
                <div class="weapon-slot locked" data-weapon="plasma" title="بندقية بلازما">
                    ⚡
                    <div class="weapon-name">بلازما</div>
                </div>
            </div>
            
            <!-- المتجر والمهارات -->
            <div class="shop-container">
                <div class="shop-toggle" id="shopToggle" title="فتح المتجر">
                    🏪
                </div>
                <div class="shop-panel" id="shopPanel">
                    <div class="shop-header">
                        <h3 style="color: #ffd700;">🏪 متجر المحارب</h3>
                        <div style="color: #ffd700; font-size: 20px;">
                            💰 <span id="shopGold">1000</span>
                        </div>
                    </div>
                    
                    <div class="shop-categories">
                        <div class="category-btn active" data-category="weapons">أسلحة</div>
                        <div class="category-btn" data-category="upgrades">ترقيات</div>
                        <div class="category-btn" data-category="consumables">مستهلكات</div>
                        <div class="category-btn" data-category="special">خاص</div>
                    </div>
                    
                    <div class="shop-items">
                        <!-- سيتم ملء العناصر ديناميكياً -->
                    </div>
                </div>
            </div>
            
            <!-- نافذة المهارات -->
            <div class="skills-panel" id="skillsPanel">
                <h3 style="color: #4169e1; margin-bottom: 15px;">🎯 شجرة المهارات</h3>
                <div class="skill-tree">
                    <div class="skill-node" data-skill="doubleJump">
                        <div style="font-weight: bold; color: #4169e1;">القفز المزدوج</div>
                        <div style="font-size: 12px; color: #aaa;">القفز مرتين في الهواء</div>
                    </div>
                    <div class="skill-node" data-skill="criticalHit">
                        <div style="font-weight: bold; color: #4169e1;">ضربة حرجة</div>
                        <div style="font-size: 12px; color: #aaa;">+20% فرصة ضربة حرجة</div>
                    </div>
                    <div class="skill-node" data-skill="lifeSteal">
                        <div style="font-weight: bold; color: #4169e1;">امتصاص الحياة</div>
                        <div style="font-size: 12px; color: #aaa;">استعادة 10% من الضرر</div>
                    </div>
                </div>
            </div>
            
            <!-- تأثيرات الجرافيكس -->
            <div class="damage-overlay" id="damageOverlay"></div>
            
            <!-- رسائل التلميح -->
            <div class="hint-message" id="hintMessage">
                🎮 اضغط على الأزرار حولك للتحكم!
            </div>
            
            <!-- شاشة الإنجازات -->
            <div class="achievement-popup" id="achievementPopup">
                <div style="font-size: 32px;">🏆</div>
                <div>
                    <div style="font-weight: bold; color: #ffd700;" id="achievementTitle"></div>
                    <div style="font-size: 12px; color: #ffd700;" id="achievementDesc"></div>
                </div>
            </div>
        </div>
    </div>

    <!-- النظام الصوتي -->
    <audio id="bgMusic" loop preload="auto" style="display: none;"></audio>
    <audio id="swordSound" preload="auto" style="display: none;"></audio>
    <audio id="gunSound" preload="auto" style="display: none;"></audio>
    <audio id="hitSound" preload="auto" style="display: none;"></audio>
    <audio id="explosionSound" preload="auto" style="display: none;"></audio>
    <audio id="healSound" preload="auto" style="display: none;"></audio>
    <audio id="coinSound" preload="auto" style="display: none;"></audio>
    <audio id="levelUpSound" preload="auto" style="display: none;"></audio>

    <script>
        // ============= تهيئة اللعبة =============
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const gameScreen = document.getElementById('gameScreen');
        const loadingScreen = document.getElementById('loadingScreen');
        const loaderFill = document.getElementById('loaderFill');
        const loadingText = document.getElementById('loadingText');
        
        // متغيرات النظام
        let gameLoaded = false;
        let loadingProgress = 0;
        let totalAssets = 10;
        let loadedAssets = 0;
        
        // تحجيم الكانفاس
        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            if (camera) camera.updateBounds();
            if (player) player.updateControlPosition();
        }
        
        // تحديث شاشة التحميل
        function updateLoading(progress, text) {
            loadingProgress = progress;
            loaderFill.style.width = `${progress}%`;
            loadingText.textContent = text;
            
            if (progress >= 100 && !gameLoaded) {
                setTimeout(() => {
                    loadingScreen.style.opacity = '0';
                    setTimeout(() => {
                        loadingScreen.style.display = 'none';
                        gameScreen.style.display = 'block';
                        gameLoaded = true;
                        initGame();
                    }, 500);
                }, 500);
            }
        }
        
        // محاكاة تحميل الأصول
        function simulateLoading() {
            const loadingSteps = [
                {progress: 10, text: "جاري تهيئة المحرك الرسومي..."},
                {progress: 25, text: "تحميل النماذج ثلاثية الأبعاد..."},
                {progress: 40, text: "تحميل أنظمة الجسيمات..."},
                {progress: 55, text: "تهيئة النظام الصوتي..."},
                {progress: 70, text: "تحميل أصول الأسلحة..."},
                {progress: 85, text: "تهيئة الذكاء الاصطناعي..."},
                {progress: 95, text: "إعداد عالم اللعبة..."},
                {progress: 100, text: "جاهز للعب!"}
            ];
            
            loadingSteps.forEach((step, index) => {
                setTimeout(() => {
                    updateLoading(step.progress, step.text);
                }, index * 500);
            });
        }
        
        // بدء تحميل اللعبة
        setTimeout(simulateLoading, 1000);
        
        // ============= نظام الجسيمات المتقدم =============
        class AdvancedParticleSystem {
            constructor() {
                this.particles = [];
                this.emitters = [];
            }
            
            createEffect(type, x, y, options = {}) {
                const {
                    color = '#ff0000',
                    count = 15,
                    size = 4,
                    lifetime = 1,
                    velocity = 8,
                    gravity = 0.2
                } = options;
                
                for (let i = 0; i < count; i++) {
                    const particle = {
                        x, y,
                        vx: (Math.random() - 0.5) * velocity * 2,
                        vy: (Math.random() - 0.5) * velocity - 3,
                        life: lifetime,
                        maxLife: lifetime,
                        color,
                        size: Math.random() * size + size/2,
                        type,
                        rotation: Math.random() * Math.PI * 2,
                        rotationSpeed: (Math.random() - 0.5) * 0.1,
                        gravity,
                        trail: []
                    };
                    
                    if (type === 'blood') {
                        particle.color = '#ff0000';
                        particle.vy -= 2;
                        particle.gravity = 0.3;
                    } else if (type === 'spark') {
                        particle.color = color;
                        particle.vx *= 0.5;
                        particle.vy *= 0.5;
                        particle.life *= 0.5;
                    } else if (type === 'heal') {
                        particle.color = '#00ff00';
                        particle.vy = -Math.random() * 4 - 2;
                    } else if (type === 'magic') {
                        particle.color = '#9370db';
                        particle.size *= 1.5;
                        particle.rotationSpeed *= 2;
                    }
                    
                    this.particles.push(particle);
                }
            }
            
            createTrail(x, y, color, size = 2) {
                for (let i = 0; i < 2; i++) {
                    this.particles.push({
                        x: x + (Math.random() - 0.5) * 10,
                        y: y + (Math.random() - 0.5) * 10,
                        vx: (Math.random() - 0.5) * 2,
                        vy: (Math.random() - 0.5) * 2,
                        life: 0.3,
                        maxLife: 0.3,
                        color,
                        size: Math.random() * size + 1,
                        type: 'trail',
                        rotation: 0,
                        rotationSpeed: 0,
                        gravity: 0,
                        trail: []
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
                    
                    // إضافة إلى الأثر
                    p.trail.push({x: p.x, y: p.y});
                    if (p.trail.length > 5) p.trail.shift();
                    
                    if (p.life <= 0) {
                        this.particles.splice(i, 1);
                    }
                }
            }
            
            draw() {
                this.particles.forEach(p => {
                    const alpha = p.life / p.maxLife;
                    ctx.save();
                    ctx.globalAlpha = alpha;
                    
                    if (p.type === 'magic') {
                        // جسيمات سحرية متوهجة
                        ctx.fillStyle = p.color;
                        ctx.translate(p.x, p.y);
                        ctx.rotate(p.rotation);
                        
                        // نجمة سداسية
                        ctx.beginPath();
                        for (let i = 0; i < 6; i++) {
                            const angle = (i * Math.PI) / 3;
                            const radius = p.size;
                            ctx.lineTo(
                                Math.cos(angle) * radius,
                                Math.sin(angle) * radius
                            );
                        }
                        ctx.closePath();
                        ctx.fill();
                        
                        // هالة
                        ctx.globalAlpha = alpha * 0.3;
                        ctx.beginPath();
                        ctx.arc(0, 0, p.size * 2, 0, Math.PI * 2);
                        ctx.fill();
                    } else if (p.type === 'spark') {
                        // شرارة متوهجة
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
                    } else {
                        // جسيمات عادية مع أثر
                        if (p.trail.length > 1) {
                            ctx.strokeStyle = p.color;
                            ctx.lineWidth = p.size;
                            ctx.lineCap = 'round';
                            ctx.globalAlpha = alpha * 0.3;
                            
                            ctx.beginPath();
                            ctx.moveTo(p.trail[0].x, p.trail[0].y);
                            for (let j = 1; j < p.trail.length; j++) {
                                ctx.lineTo(p.trail[j].x, p.trail[j].y);
                            }
                            ctx.stroke();
                        }
                        
                        ctx.globalAlpha = alpha;
                        ctx.fillStyle = p.color;
                        ctx.beginPath();
                        ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                        ctx.fill();
                    }
                    
                    ctx.restore();
                });
            }
        }
        
        // ============= نظام الكاميرا المتقدم =============
        class AdvancedCamera {
            constructor() {
                this.x = 0;
                this.y = 0;
                this.target = null;
                this.shake = 0;
                this.zoom = 1;
                this.rotation = 0;
                this.bounds = {
                    left: 0,
                    right: canvas.width,
                    top: 0,
                    bottom: canvas.height
                };
                this.smoothing = 0.08;
                this.effects = [];
            }
            
            follow(target) {
                this.target = target;
            }
            
            updateBounds() {
                this.bounds.right = canvas.width;
                this.bounds.bottom = canvas.height;
            }
            
            update() {
                if (this.target) {
                    // تتبع سلس مع حدود
                    const targetX = Math.max(
                        this.bounds.left + canvas.width / 2,
                        Math.min(this.bounds.right - canvas.width / 2, this.target.x)
                    );
                    const targetY = Math.max(
                        this.bounds.top + canvas.height / 2,
                        Math.min(this.bounds.bottom - canvas.height / 2, this.target.y)
                    );
                    
                    this.x += (targetX - canvas.width / 2 - this.x) * this.smoothing;
                    this.y += (targetY - canvas.height / 2 - this.y) * this.smoothing;
                }
                
                // تأثيرات الاهتزاز
                if (this.shake > 0) {
                    this.x += (Math.random() - 0.5) * this.shake * 2;
                    this.y += (Math.random() - 0.5) * this.shake * 2;
                    this.shake *= 0.85;
                    if (this.shake < 0.1) this.shake = 0;
                }
                
                // تحديث التأثيرات
                this.effects = this.effects.filter(effect => {
                    effect.duration--;
                    return effect.duration > 0;
                });
            }
            
            apply() {
                ctx.save();
                ctx.translate(canvas.width / 2, canvas.height / 2);
                ctx.scale(this.zoom, this.zoom);
                ctx.rotate(this.rotation);
                ctx.translate(-this.x, -this.y);
                
                // تطبيق تأثيرات ما بعد المعالجة
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
            
            addShake(amount) {
                this.shake = Math.max(this.shake, amount);
            }
            
            addEffect(type, amount, duration) {
                this.effects.push({ type, amount, duration });
            }
            
            zoomTo(target, duration = 60) {
                const startZoom = this.zoom;
                const zoomDiff = target - startZoom;
                let frames = 0;
                
                const zoomStep = () => {
                    frames++;
                    const progress = frames / duration;
                    this.zoom = startZoom + zoomDiff * Math.sin(progress * Math.PI / 2);
                    
                    if (frames < duration) {
                        requestAnimationFrame(zoomStep);
                    }
                };
                zoomStep();
            }
        }
        
        // ============= نظام الرسوم المتحركة للأرجل =============
        class LegAnimationSystem {
            constructor() {
                this.legs = [];
                this.animations = {};
            }
            
            createLegsForEntity(entity, count = 2) {
                for (let i = 0; i < count; i++) {
                    this.legs.push({
                        entity,
                        index: i,
                        angle: 0,
                        length: entity.height * 0.6,
                        thickness: entity.width * 0.15,
                        offsetX: (i - 0.5) * entity.width * 0.4,
                        swing: 0,
                        swingSpeed: 0.1 + Math.random() * 0.1,
                        groundContact: true
                    });
                }
            }
            
            update(entity, velocityX, velocityY, onGround) {
                const entityLegs = this.legs.filter(l => l.entity === entity);
                
                entityLegs.forEach((leg, index) => {
                    const speed = Math.abs(velocityX) + Math.abs(velocityY);
                    
                    if (onGround && speed > 0.5) {
                        leg.swing += leg.swingSpeed * speed;
                        leg.angle = Math.sin(leg.swing) * (Math.PI / 6) * (index % 2 === 0 ? 1 : -1);
                    } else {
                        leg.angle = 0;
                    }
                    
                    leg.groundContact = onGround;
                });
            }
            
            draw(entity) {
                const entityLegs = this.legs.filter(l => l.entity === entity);
                
                entityLegs.forEach(leg => {
                    const x = entity.x + leg.offsetX;
                    const y = entity.y + entity.height;
                    
                    ctx.save();
                    ctx.translate(x, y);
                    ctx.rotate(leg.angle);
                    
                    // رسم الساق
                    ctx.fillStyle = entity.color || '#8b4513';
                    ctx.fillRect(
                        -leg.thickness / 2,
                        0,
                        leg.thickness,
                        leg.length
                    );
                    
                    // القدم إذا كانت على الأرض
                    if (leg.groundContact) {
                        ctx.fillStyle = '#654321';
                        ctx.fillRect(
                            -leg.thickness * 1.2 / 2,
                            leg.length - leg.thickness / 2,
                            leg.thickness * 1.2,
                            leg.thickness
                        );
                    }
                    
                    ctx.restore();
                });
            }
        }
        
        // ============= اللاعب المحسن برسومات الأرجل =============
        class EnhancedPlayer {
            constructor() {
                this.x = canvas.width / 2;
                this.y = canvas.height - 200;
                this.width = 50;
                this.height = 80;
                this.speed = 10;
                this.jumpPower = 20;
                this.dashPower = 25;
                this.velocityX = 0;
                this.velocityY = 0;
                this.gravity = 0.8;
                this.isJumping = false;
                this.isDashing = false;
                this.dashCooldown = 0;
                this.onGround = false;
                
                // نظام الرسوم المتحركة للأرجل
                this.legSystem = new LegAnimationSystem();
                this.legSystem.createLegsForEntity(this, 2);
                
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
                
                // الأسلحة المتقدمة (10 أسلحة)
                this.weapon = 'sword';
                this.weapons = {
                    sword: { 
                        name: "سيف فولاذي", 
                        damage: 30, 
                        range: 60, 
                        cooldown: 400, 
                        unlocked: true,
                        ammo: Infinity,
                        color: '#ffd700',
                        effect: 'slash'
                    },
                    dagger: { 
                        name: "خنجر سريع", 
                        damage: 20, 
                        range: 40, 
                        cooldown: 200, 
                        unlocked: true,
                        ammo: Infinity,
                        color: '#c0c0c0',
                        effect: 'stab'
                    },
                    pistol: { 
                        name: "مسدس عادي", 
                        damage: 25, 
                        range: 300, 
                        cooldown: 300, 
                        unlocked: true,
                        ammo: 30,
                        color: '#ffff00',
                        effect: 'bullet'
                    },
                    shotgun: { 
                        name: "بندقية صيد", 
                        damage: 40, 
                        range: 150, 
                        cooldown: 800, 
                        unlocked: false,
                        ammo: 12,
                        color: '#ff4500',
                        effect: 'shotgun'
                    },
                    rifle: { 
                        name: "بندقية قنص", 
                        damage: 60, 
                        range: 500, 
                        cooldown: 1000, 
                        unlocked: false,
                        ammo: 10,
                        color: '#00ffff',
                        effect: 'sniper'
                    },
                    minigun: { 
                        name: "رشاش سريع", 
                        damage: 15, 
                        range: 200, 
                        cooldown: 100, 
                        unlocked: false,
                        ammo: 200,
                        color: '#ff0000',
                        effect: 'minigun'
                    },
                    rocket: { 
                        name: "قاذفة صواريخ", 
                        damage: 100, 
                        range: 400, 
                        cooldown: 1500, 
                        unlocked: false,
                        ammo: 5,
                        color: '#ff8c00',
                        effect: 'rocket'
                    },
                    flamethrower: { 
                        name: "قاذفة لهب", 
                        damage: 10, 
                        range: 100, 
                        cooldown: 50, 
                        unlocked: false,
                        ammo: 100,
                        color: '#ff4500',
                        effect: 'flame'
                    },
                    laser: { 
                        name: "بندقية ليزر", 
                        damage: 45, 
                        range: 350, 
                        cooldown: 500, 
                        unlocked: false,
                        ammo: 30,
                        color: '#9370db',
                        effect: 'laser'
                    },
                    plasma: { 
                        name: "بندقية بلازما", 
                        damage: 75, 
                        range: 250, 
                        cooldown: 600, 
                        unlocked: false,
                        ammo: 20,
                        color: '#00ff00',
                        effect: 'plasma'
                    }
                };
                
                // المهارات
                this.skills = {
                    doubleJump: false,
                    criticalHit: false,
                    lifeSteal: false,
                    dashAttack: false,
                    bulletTime: false
                };
                
                // الموارد
                this.gold = 1000;
                this.gems = 0;
                this.score = 0;
                this.kills = 0;
                
                // الحالة
                this.isBlocking = false;
                this.invincible = 0;
                this.combo = 0;
                this.lastComboTime = 0;
                this.lastAttack = 0;
                this.attackAnimation = 0;
            }
            
            update() {
                // الحركة
                let moveX = 0;
                let moveY = 0;
                
                if (keys.moveLeft && this.x > 100) moveX -= this.speed;
                if (keys.moveRight && this.x < canvas.width - 100) moveX += this.speed;
                if (keys.moveUp && this.y > 100) moveY -= this.speed;
                if (keys.moveDown && this.y < canvas.height - 150) moveY += this.speed;
                
                this.velocityX = moveX;
                this.velocityY = moveY;
                
                // القفز
                if (keys.jump && !this.isJumping && this.stamina >= 20) {
                    this.velocityY = -this.jumpPower;
                    this.isJumping = true;
                    this.onGround = false;
                    this.stamina -= 20;
                    particles.createEffect('spark', this.x, this.y + this.height, '#ffff00', 10);
                }
                
                // الدفعة السريعة
                if (keys.dash && this.dashCooldown <= 0 && this.stamina >= 30) {
                    this.isDashing = true;
                    this.dashCooldown = 60;
                    this.stamina -= 30;
                    
                    const dashMultiplier = 5;
                    this.x += moveX * this.dashPower * dashMultiplier;
                    this.y += moveY * this.dashPower * 3;
                    
                    particles.createEffect('spark', this.x, this.y, '#00ffff', 20);
                }
                
                // الجاذبية والحركة
                if (!this.onGround) {
                    this.velocityY += this.gravity;
                }
                
                this.x += this.velocityX;
                this.y += this.velocityY;
                
                // التحقق من الأرض
                const groundLevel = canvas.height - 150;
                if (this.y > groundLevel - this.height) {
                    this.y = groundLevel - this.height;
                    this.velocityY = 0;
                    this.isJumping = false;
                    this.onGround = true;
                } else {
                    this.onGround = false;
                }
                
                // تحديث توقيتات
                if (this.dashCooldown > 0) this.dashCooldown--;
                if (this.invincible > 0) this.invincible--;
                if (this.attackAnimation > 0) this.attackAnimation--;
                
                // استعادة الطاقة
                if (!this.isJumping && !this.isDashing) {
                    this.stamina = Math.min(this.maxStamina, this.stamina + 0.8);
                }
                
                // الدفاع
                this.isBlocking = keys.block && this.stamina >= 2;
                if (this.isBlocking) {
                    this.stamina -= 2;
                }
                
                // تحديث رسوميات الأرجل
                this.legSystem.update(this, this.velocityX, this.velocityY, this.onGround);
                
                // تحديث الواجهة
                this.updateUI();
            }
            
            attack(enemies) {
                const now = Date.now();
                const weaponData = this.weapons[this.weapon];
                
                if (!weaponData.unlocked) {
                    showHint(`🔒 هذا السلاح غير متاح!`);
                    return;
                }
                
                if (now - this.lastAttack < weaponData.cooldown) return;
                if (weaponData.ammo <= 0 && weaponData.ammo !== Infinity) {
                    showHint(`💥 نفذت الذخيرة!`);
                    return;
                }
                
                this.lastAttack = now;
                this.attackAnimation = 10;
                
                // استخدام الذخيرة
                if (weaponData.ammo !== Infinity) {
                    weaponData.ammo--;
                    updateAmmoUI();
                }
                
                // تشغيل الصوت
                playSound(this.weapon);
                
                // الكومبو
                if (now - this.lastComboTime < 2000) {
                    this.combo++;
                } else {
                    this.combo = 1;
                }
                this.lastComboTime = now;
                
                // حساب الضرر
                let damage = weaponData.damage;
                if (this.skills.criticalHit && Math.random() < 0.2) {
                    damage *= 2;
                    createHitIndicator(this.x, this.y, 'CRITICAL!', '#ff0000');
                }
                
                // امتصاص الحياة
                if (this.skills.lifeSteal) {
                    this.heal(damage * 0.1);
                }
                
                // تنفيذ الهجوم حسب نوع السلاح
                switch(this.weapon) {
                    case 'sword':
                    case 'dagger':
                        this.meleeAttack(enemies, damage, weaponData);
                        break;
                    default:
                        this.rangedAttack(damage, weaponData);
                        break;
                }
                
                camera.addShake(weaponData.name.includes('صاروخ') ? 8 : 2);
            }
            
            meleeAttack(enemies, damage, weaponData) {
                particles.createEffect('spark', 
                    this.x + this.width, 
                    this.y + 40, 
                    weaponData.color, 
                    15
                );
                
                enemies.forEach(enemy => {
                    const distX = Math.abs(enemy.x - this.x);
                    const distY = Math.abs(enemy.y - this.y);
                    
                    if (distX < weaponData.range && distY < 50) {
                        const killed = enemy.takeDamage(damage, this);
                        if (killed) {
                            this.combo += 2;
                            particles.createEffect('blood', enemy.x, enemy.y, '#ff0000', 25);
                        }
                    }
                });
            }
            
            rangedAttack(damage, weaponData) {
                const bulletCount = weaponData.effect === 'shotgun' ? 8 : 
                                  weaponData.effect === 'minigun' ? 3 : 1;
                const spread = weaponData.effect === 'shotgun' ? 0.3 : 
                              weaponData.effect === 'minigun' ? 0.15 : 0.05;
                
                for (let i = 0; i < bulletCount; i++) {
                    const bullet = new AdvancedBullet(
                        this.x + this.width,
                        this.y + this.height / 2,
                        15 + (weaponData.effect === 'sniper' ? 10 : 0),
                        damage / (weaponData.effect === 'shotgun' ? 2 : 1),
                        weaponData.color,
                        weaponData.effect,
                        spread
                    );
                    bullets.push(bullet);
                }
                
                particles.createEffect('spark', 
                    this.x + this.width, 
                    this.y + 40, 
                    weaponData.color, 
                    weaponData.effect === 'shotgun' ? 20 : 10
                );
            }
            
            takeDamage(amount, source) {
                if (this.invincible > 0) return false;
                
                let actualDamage = amount;
                
                if (this.isBlocking) {
                    actualDamage *= 0.3;
                    particles.createEffect('spark', this.x, this.y, '#4169e1', 8);
                }
                
                if (this.armor > 0) {
                    const armorDamage = Math.min(actualDamage, this.armor);
                    this.armor -= armorDamage;
                    actualDamage -= armorDamage;
                }
                
                this.health -= actualDamage;
                
                // تأثيرات الضرر
                showDamageEffect(this.x, this.y);
                camera.addShake(3);
                particles.createEffect('blood', 
                    this.x + this.width/2, 
                    this.y + this.height/2, 
                    '#ff0000', 
                    10
                );
                
                createHitIndicator(this.x, this.y, `-${Math.round(actualDamage)}`, '#ff0000');
                
                if (this.health <= 0) {
                    gameOver();
                    return true;
                }
                
                this.invincible = 15;
                return false;
            }
            
            heal(amount) {
                const oldHealth = this.health;
                this.health = Math.min(this.maxHealth, this.health + amount);
                const healed = this.health - oldHealth;
                
                if (healed > 0) {
                    particles.createEffect('heal', 
                        this.x + this.width/2, 
                        this.y + this.height/2, 
                        '#00ff00', 
                        15
                    );
                    createHitIndicator(this.x, this.y, `+${Math.round(healed)}`, '#00ff00');
                    playSound('heal');
                }
            }
            
            addExp(amount) {
                this.exp += amount;
                while (this.exp >= this.maxExp) {
                    this.exp -= this.maxExp;
                    this.levelUp();
                }
                updateExpUI();
            }
            
            levelUp() {
                this.level++;
                this.maxExp = Math.floor(this.maxExp * 1.5);
                
                this.maxHealth += 20;
                this.health = this.maxHealth;
                this.maxStamina += 10;
                this.stamina = this.maxStamina;
                
                playSound('levelUp');
                showHint(`🎉 ارتقيت للمستوى ${this.level}!`);
                
                // منح مكافأة
                this.gold += 500;
                this.gems += 1;
                
                unlockAchievement('levelUp', `الوصول للمستوى ${this.level}`);
            }
            
            updateUI() {
                document.getElementById('healthText').textContent = 
                    `${Math.round(this.health)}/${this.maxHealth}`;
                document.getElementById('armorText').textContent = 
                    `${Math.round(this.armor)}/${this.maxArmor}`;
                document.getElementById('staminaText').textContent = 
                    `${Math.round(this.stamina)}/${this.maxStamina}`;
                document.getElementById('goldText').textContent = this.gold;
                document.getElementById('gemText').textContent = this.gems;
                document.getElementById('scoreText').textContent = this.score;
                
                document.getElementById('healthBar').style.width = 
                    `${(this.health / this.maxHealth) * 100}%`;
                document.getElementById('armorBar').style.width = 
                    `${(this.armor / this.maxArmor) * 100}%`;
                document.getElementById('staminaBar').style.width = 
                    `${(this.stamina / this.maxStamina) * 100}%`;
                
                document.getElementById('shopGold').textContent = this.gold;
                
                // عرض الكومبو
                if (this.combo > 1) {
                    document.getElementById('scoreText').textContent = 
                        `${this.score} (x${this.combo})`;
                }
            }
            
            updateControlPosition() {
                const controls = document.querySelector('.control-area');
                if (controls) {
                    controls.style.left = `${this.x}px`;
                    controls.style.bottom = `${canvas.height - this.y}px`;
                }
            }
            
            draw() {
                ctx.save();
                
                // تأثير الرجفة عند الضرر
                if (this.invincible > 0 && this.invincible % 4 < 2) {
                    ctx.globalAlpha = 0.6;
                }
                
                // رسم الأرجل أولاً
                this.legSystem.draw(this);
                
                // جسم المحارب
                const gradient = ctx.createLinearGradient(
                    this.x, this.y, 
                    this.x, this.y + this.height
                );
                gradient.addColorStop(0, '#dc143c');
                gradient.addColorStop(0.5, '#b22222');
                gradient.addColorStop(1, '#8b0000');
                
                ctx.fillStyle = gradient;
                ctx.fillRect(this.x, this.y, this.width, this.height);
                
                // الرأس
                ctx.fillStyle = '#ffb6c1';
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y - 5, 15, 0, Math.PI * 2);
                ctx.fill();
                
                // العيون
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x + this.width/2 - 8, this.y - 5, 3, 0, Math.PI * 2);
                ctx.arc(this.x + this.width/2 + 8, this.y - 5, 3, 0, Math.PI * 2);
                ctx.fill();
                
                // الفم
                ctx.strokeStyle = '#000';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y + 5, 6, 0.2, Math.PI - 0.2);
                ctx.stroke();
                
                // السلاح
                this.drawWeapon();
                
                // تأثير الدفاع
                if (this.isBlocking) {
                    ctx.strokeStyle = '#4169e1';
                    ctx.lineWidth = 4;
                    ctx.globalAlpha = 0.4;
                    ctx.beginPath();
                    ctx.arc(this.x + this.width/2, this.y + this.height/2, 60, 0, Math.PI * 2);
                    ctx.stroke();
                }
                
                // تأثير الدفعة السريعة
                if (this.isDashing) {
                    ctx.strokeStyle = '#00ffff';
                    ctx.lineWidth = 3;
                    ctx.globalAlpha = 0.3;
                    ctx.beginPath();
                    ctx.arc(this.x + this.width/2, this.y + this.height/2, 70, 0, Math.PI * 2);
                    ctx.stroke();
                }
                
                // تأثير الهجوم
                if (this.attackAnimation > 0) {
                    ctx.strokeStyle = '#ffd700';
                    ctx.lineWidth = 2;
                    ctx.globalAlpha = 0.5;
                    const angle = this.attackAnimation * 0.1;
                    ctx.beginPath();
                    ctx.arc(this.x + this.width/2, this.y + this.height/2, 40, angle, angle + Math.PI);
                    ctx.stroke();
                }
                
                ctx.restore();
                
                // رسم الكومبو والمستوى
                this.drawHUD();
            }
            
            drawWeapon() {
                const weaponData = this.weapons[this.weapon];
                ctx.fillStyle = this.isBlocking ? '#ffd700' : '#666';
                ctx.lineWidth = 3;
                
                switch(this.weapon) {
                    case 'sword':
                        ctx.beginPath();
                        ctx.moveTo(this.x + this.width - 5, this.y + 30);
                        ctx.lineTo(this.x + this.width + 40, this.y + 20);
                        ctx.lineTo(this.x + this.width + 35, this.y + 40);
                        ctx.lineTo(this.x + this.width - 5, this.y + 50);
                        ctx.closePath();
                        ctx.fill();
                        
                        // لمعة السيف
                        if (this.combo > 2) {
                            ctx.strokeStyle = '#ffd700';
                            ctx.lineWidth = 2;
                            ctx.beginPath();
                            ctx.moveTo(this.x + this.width + 10, this.y + 25);
                            ctx.lineTo(this.x + this.width + 45, this.y + 15);
                            ctx.stroke();
                        }
                        break;
                        
                    case 'dagger':
                        ctx.beginPath();
                        ctx.moveTo(this.x + this.width - 5, this.y + 35);
                        ctx.lineTo(this.x + this.width + 25, this.y + 30);
                        ctx.lineTo(this.x + this.width + 20, this.y + 45);
                        ctx.lineTo(this.x + this.width - 5, this.y + 50);
                        ctx.closePath();
                        ctx.fill();
                        break;
                        
                    default:
                        // أسلحة نارية
                        ctx.fillRect(this.x + this.width - 5, this.y + 35, 30, 10);
                        ctx.fillRect(this.x + this.width + 20, this.y + 30, 5, 20);
                        break;
                }
            }
            
            drawHUD() {
                // الكومبو
                if (this.combo > 1) {
                    ctx.fillStyle = '#ffd700';
                    ctx.font = 'bold 26px Arial';
                    ctx.textAlign = 'center';
                    ctx.textBaseline = 'middle';
                    ctx.fillText(`COMBO x${this.combo}`, this.x + this.width/2, this.y - 40);
                }
                
                // المستوى
                ctx.fillStyle = '#4169e1';
                ctx.font = 'bold 16px Arial';
                ctx.textAlign = 'left';
                ctx.fillText(`Lv.${this.level}`, this.x, this.y - 60);
            }
        }
        
        // ============= نظام الرصاصات المتقدم =============
        class AdvancedBullet {
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
                this.width = type === 'shotgun' ? 4 : 6;
                this.height = type === 'shotgun' ? 4 : 12;
                this.trail = [];
                this.lifetime = type === 'rocket' ? 120 : 60;
                this.age = 0;
                
                if (type === 'rocket') {
                    this.width = 10;
                    this.height = 10;
                } else if (type === 'plasma') {
                    this.width = 8;
                    this.height = 8;
                }
            }
            
            update() {
                this.x += this.vx;
                this.y += this.vy;
                this.age++;
                
                // جاذبية للصواريخ
                if (this.type === 'rocket') {
                    this.vy += 0.05;
                }
                
                // إضافة أثر
                this.trail.push({x: this.x, y: this.y});
                if (this.trail.length > 10) this.trail.shift();
                
                return this.age >= this.lifetime;
            }
            
            draw() {
                // رسم الأثر
                ctx.globalAlpha = 0.3;
                this.trail.forEach((pos, i) => {
                    const alpha = i / this.trail.length;
                    ctx.fillStyle = this.color;
                    
                    if (this.type === 'laser' || this.type === 'plasma') {
                        ctx.beginPath();
                        ctx.arc(pos.x, pos.y, this.width * alpha * 0.8, 0, Math.PI * 2);
                        ctx.fill();
                    } else {
                        ctx.fillRect(
                            pos.x - this.width/2 * alpha,
                            pos.y - this.height/2 * alpha,
                            this.width * alpha,
                            this.height * alpha
                        );
                    }
                });
                
                ctx.globalAlpha = 1;
                
                // رسم الرصاصة
                switch(this.type) {
                    case 'rocket':
                        this.drawRocket();
                        break;
                    case 'plasma':
                        this.drawPlasma();
                        break;
                    case 'laser':
                        this.drawLaser();
                        break;
                    default:
                        this.drawBasic();
                        break;
                }
            }
            
            drawRocket() {
                // جسم الصاروخ
                const gradient = ctx.createLinearGradient(
                    this.x - this.width/2, this.y,
                    this.x + this.width/2, this.y
                );
                gradient.addColorStop(0, '#ff8c00');
                gradient.addColorStop(0.5, '#ff4500');
                gradient.addColorStop(1, '#8b0000');
                
                ctx.fillStyle = gradient;
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.width/2, 0, Math.PI * 2);
                ctx.fill();
                
                // ذيل الصاروخ
                ctx.fillStyle = '#ffd700';
                ctx.beginPath();
                ctx.moveTo(this.x - this.width/2, this.y);
                ctx.lineTo(this.x - this.width, this.y + this.height);
                ctx.lineTo(this.x + this.width/2, this.y);
                ctx.closePath();
                ctx.fill();
                
                // اللهب
                ctx.fillStyle = '#ff0000';
                ctx.beginPath();
                ctx.arc(this.x - this.width, this.y + this.height, this.width/3, 0, Math.PI * 2);
                ctx.fill();
            }
            
            drawPlasma() {
                // كرة بلازما متوهجة
                const gradient = ctx.createRadialGradient(
                    this.x, this.y, 0,
                    this.x, this.y, this.width
                );
                gradient.addColorStop(0, '#00ff00');
                gradient.addColorStop(0.7, '#008000');
                gradient.addColorStop(1, 'transparent');
                
                ctx.fillStyle = gradient;
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.width, 0, Math.PI * 2);
                ctx.fill();
                
                // مركز ساطع
                ctx.fillStyle = '#ffffff';
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.width/3, 0, Math.PI * 2);
                ctx.fill();
            }
            
            drawLaser() {
                // شعاع ليزر
                const gradient = ctx.createLinearGradient(
                    this.x - this.width/2, this.y,
                    this.x + this.width/2, this.y
                );
                gradient.addColorStop(0, '#9370db');
                gradient.addColorStop(0.5, '#ffffff');
                gradient.addColorStop(1, '#9370db');
                
                ctx.fillStyle = gradient;
                ctx.beginPath();
                ctx.roundRect(this.x - this.width/2, this.y - this.height/2, 
                            this.width, this.height, 2);
                ctx.fill();
            }
            
            drawBasic() {
                ctx.fillStyle = this.color;
                if (this.type === 'sniper') {
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, this.width, 0, Math.PI * 2);
                    ctx.fill();
                } else {
                    ctx.fillRect(this.x - this.width/2, this.y - this.height/2, 
                                this.width, this.height);
                }
            }
        }
        
        // ============= الأعداء المتقدمون =============
        class AdvancedEnemy {
            constructor(wave, type = 'normal') {
                this.type = type;
                this.width = type === 'boss' ? 70 : type === 'elite' ? 55 : 45;
                this.height = type === 'boss' ? 90 : type === 'elite' ? 75 : 60;
                
                // الموضع
                this.x = -100 - Math.random() * 100;
                this.y = canvas.height - 150 - this.height + Math.random() * 50;
                
                // الحركة
                this.speed = 1.5 + Math.random() * 1.5 + wave * 0.3;
                if (type === 'boss') this.speed *= 0.6;
                if (type === 'elite') this.speed *= 0.8;
                
                this.velocityX = 0;
                this.velocityY = 0;
                this.onGround = true;
                
                // الإحصائيات
                this.health = 60 + wave * 15;
                this.maxHealth = this.health;
                this.damage = 12 + wave * 3;
                this.reward = 70 + wave * 15;
                this.expReward = 20 + wave * 5;
                
                if (type === 'elite') {
                    this.health *= 1.5;
                    this.maxHealth *= 1.5;
                    this.damage *= 1.3;
                    this.reward *= 1.5;
                    this.expReward *= 1.5;
                } else if (type === 'boss') {
                    this.health *= 3;
                    this.maxHealth *= 3;
                    this.damage *= 2;
                    this.reward *= 3;
                    this.expReward *= 3;
                }
                
                // نظام الأرجل
                this.legSystem = new LegAnimationSystem();
                this.legSystem.createLegsForEntity(this, type === 'boss' ? 4 : 2);
                
                // الهجوم
                this.attackCooldown = 0;
                this.attackRange = 50;
                this.color = type === 'boss' ? '#ff8c00' : 
                            type === 'elite' ? '#ffd700' : '#ffff00';
                
                // الرسوم المتحركة
                this.walkAnimation = 0;
                this.hitAnimation = 0;
            }
            
            update(player) {
                // الحركة نحو اللاعب
                const dx = player.x - this.x;
                const dy = player.y - this.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance > 0) {
                    this.velocityX = (dx / distance) * this.speed;
                    this.velocityY = (dy / distance) * this.speed * 0.5;
                }
                
                // تحديث الموضع
                this.x += this.velocityX;
                this.y += this.velocityY;
                
                // التحقق من الأرض
                const groundLevel = canvas.height - 150;
                if (this.y > groundLevel - this.height) {
                    this.y = groundLevel - this.height;
                    this.onGround = true;
                } else {
                    this.onGround = false;
                }
                
                // تحديث الرسوم المتحركة للأرجل
                this.legSystem.update(this, this.velocityX, this.velocityY, this.onGround);
                
                // تحديث توقيتات الهجوم
                if (this.attackCooldown > 0) this.attackCooldown--;
                if (this.hitAnimation > 0) this.hitAnimation--;
                
                // تحقق من الاصطدام باللاعب
                if (distance < this.attackRange && this.attackCooldown <= 0) {
                    this.attackCooldown = 60;
                    this.attackAnimation = 10;
                    return player.takeDamage(this.damage, this);
                }
                
                return false;
            }
            
            takeDamage(amount, source) {
                this.health -= amount;
                this.hitAnimation = 10;
                
                // تأثيرات الضرر
                const effectColor = this.type === 'boss' ? '#8b0000' : 
                                  this.type === 'elite' ? '#ff4500' : '#ff0000';
                
                particles.createEffect('blood', 
                    this.x + this.width/2, 
                    this.y + this.height/2, 
                    effectColor, 
                    15
                );
                
                // إرجاع القوة للمشتتات
                const dx = this.x - source.x;
                const dy = this.y - source.y;
                const dist = Math.sqrt(dx * dx + dy * dy);
                
                if (dist > 0) {
                    this.x += (dx / dist) * 10;
                    this.y += (dy / dist) * 5;
                }
                
                // مؤشر الضرر
                createHitIndicator(this.x + this.width/2, this.y, 
                    `-${Math.round(amount)}`, effectColor);
                
                return this.health <= 0;
            }
            
            draw() {
                ctx.save();
                
                // تأثير الضرر
                if (this.hitAnimation > 0) {
                    ctx.globalAlpha = 0.7;
                }
                
                // رسم الأرجل
                this.legSystem.draw(this);
                
                // جسم العدو
                const gradient = ctx.createLinearGradient(
                    this.x, this.y, 
                    this.x, this.y + this.height
                );
                
                if (this.type === 'boss') {
                    gradient.addColorStop(0, '#ff8c00');
                    gradient.addColorStop(0.5, '#ff4500');
                    gradient.addColorStop(1, '#8b4500');
                } else if (this.type === 'elite') {
                    gradient.addColorStop(0, '#ffd700');
                    gradient.addColorStop(0.5, '#daa520');
                    gradient.addColorStop(1, '#b8860b');
                } else {
                    gradient.addColorStop(0, '#ffff00');
                    gradient.addColorStop(0.5, '#ffd700');
                    gradient.addColorStop(1, '#b8860b');
                }
                
                ctx.fillStyle = gradient;
                ctx.fillRect(this.x, this.y, this.width, this.height);
                
                // الرأس
                ctx.fillStyle = this.type === 'boss' ? '#ff4500' : 
                               this.type === 'elite' ? '#daa520' : '#f0e68c';
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y - 10, 18, 0, Math.PI * 2);
                ctx.fill();
                
                // العيون
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x + this.width/2 - 12, this.y - 10, 4, 0, Math.PI * 2);
                ctx.arc(this.x + this.width/2 + 12, this.y - 10, 4, 0, Math.PI * 2);
                ctx.fill();
                
                // الفم
                ctx.strokeStyle = '#000';
                ctx.lineWidth = 2;
                ctx.beginPath();
                if (this.type === 'boss') {
                    ctx.arc(this.x + this.width/2, this.y, 12, 0.2, Math.PI - 0.2);
                } else {
                    ctx.arc(this.x + this.width/2, this.y + 5, 8, 0.2, Math.PI - 0.2);
                }
                ctx.stroke();
                
                // شريط الصحة
                const healthPercent = this.health / this.maxHealth;
                const barWidth = this.width;
                const barHeight = 8;
                const barY = this.y - 25;
                
                ctx.fillStyle = 'rgba(0, 0, 0, 0.5)';
                ctx.fillRect(this.x, barY, barWidth, barHeight);
                
                if (healthPercent > 0) {
                    ctx.fillStyle = healthPercent > 0.6 ? '#00ff00' : 
                                   healthPercent > 0.3 ? '#ffff00' : '#ff4500';
                    ctx.fillRect(this.x, barY, barWidth * healthPercent, barHeight);
                }
                
                // تاج للبوس
                if (this.type === 'boss') {
                    ctx.fillStyle = '#ffd700';
                    ctx.beginPath();
                    for (let i = 0; i < 7; i++) {
                        const angle = (i * Math.PI * 2) / 7;
                        const spikeX = this.x + this.width/2 + Math.cos(angle) * 25;
                        const spikeY = this.y - 30 + Math.sin(angle) * 12;
                        ctx.lineTo(spikeX, spikeY);
                    }
                    ctx.closePath();
                    ctx.fill();
                }
                
                ctx.restore();
            }
        }
        
        // ============= نظام الصوت المتقدم =============
        class AudioSystem {
            constructor() {
                this.sounds = {};
                this.music = null;
                this.musicEnabled = true;
                this.sfxEnabled = true;
                this.volume = 0.5;
            }
            
            async load() {
                // إنشاء أصوات برمجياً
                this.createSound('sword', 'sawtooth', 150, 50, 0.3);
                this.createSound('gun', 'square', 800, 200, 0.1);
                this.createSound('hit', 'sine', 300, 100, 0.2);
                this.createSound('explosion', 'sawtooth', 200, 50, 0.3);
                this.createSound('heal', 'sine', 523, 262, 0.3);
                this.createSound('coin', 'triangle', 988, 494, 0.1);
                this.createSound('levelUp', 'sine', [523, 659, 784], 0.5);
            }
            
            createSound(name, type, freqStart, freqEnd, duration) {
                const audioContext = new (window.AudioContext || window.webkitAudioContext)();
                
                this.sounds[name] = {
                    play: () => {
                        if (!this.sfxEnabled) return;
                        
                        const oscillator = audioContext.createOscillator();
                        const gainNode = audioContext.createGain();
                        
                        oscillator.connect(gainNode);
                        gainNode.connect(audioContext.destination);
                        
                        oscillator.type = type;
                        
                        if (Array.isArray(freqStart)) {
                            // تسلسل نغمات
                            freqStart.forEach((freq, i) => {
                                oscillator.frequency.setValueAtTime(
                                    freq, 
                                    audioContext.currentTime + i * 0.1
                                );
                            });
                            duration = freqStart.length * 0.1;
                        } else {
                            oscillator.frequency.setValueAtTime(freqStart, audioContext.currentTime);
                            if (freqEnd) {
                                oscillator.frequency.exponentialRampToValueAtTime(
                                    freqEnd, 
                                    audioContext.currentTime + duration
                                );
                            }
                        }
                        
                        gainNode.gain.value = this.volume * 0.3;
                        gainNode.gain.exponentialRampToValueAtTime(
                            0.001, 
                            audioContext.currentTime + duration
                        );
                        
                        oscillator.start();
                        oscillator.stop(audioContext.currentTime + duration);
                    }
                };
            }
            
            play(soundName) {
                if (this.sounds[soundName]) {
                    this.sounds[soundName].play();
                }
            }
            
            toggleMusic() {
                this.musicEnabled = !this.musicEnabled;
            }
            
            toggleSFX() {
                this.sfxEnabled = !this.sfxEnabled;
            }
        }
        
        // ============= نظام الإنجازات =============
        class AchievementSystem {
            constructor() {
                this.achievements = {
                    firstBlood: { unlocked: false, title: "الدم الأول", desc: "هزم أول عدو" },
                    comboMaster: { unlocked: false, title: "سيد الكومبو", desc: "كومبو 10x" },
                    rich: { unlocked: false, title: "الثري", desc: "احصل على 5000 ذهب" },
                    levelUp: { unlocked: false, title: "النمو", desc: "الوصول للمستوى 5" },
                    weaponMaster: { unlocked: false, title: "سيد الأسلحة", desc: "افتح 5 أسلحة" },
                    waveSurvivor: { unlocked: false, title: "الناجي", desc: "وصل للموجة 10" }
                };
            }
            
            unlock(id, customDesc = null) {
                if (this.achievements[id] && !this.achievements[id].unlocked) {
                    this.achievements[id].unlocked = true;
                    
                    const popup = document.getElementById('achievementPopup');
                    const title = document.getElementById('achievementTitle');
                    const desc = document.getElementById('achievementDesc');
                    
                    title.textContent = this.achievements[id].title;
                    desc.textContent = customDesc || this.achievements[id].desc;
                    
                    popup.style.display = 'flex';
                    
                    setTimeout(() => {
                        popup.style.display = 'none';
                    }, 3000);
                    
                    return true;
                }
                return false;
            }
        }
        
        // ============= نظام المتجر المتقدم =============
        class AdvancedShopSystem {
            static categories = {
                weapons: [
                    {
                        id: 'shotgun',
                        name: 'بندقية الصيد',
                        description: 'ضرر عالي على مدى قصير',
                        price: 1200,
                        icon: '💥'
                    },
                    {
                        id: 'rifle',
                        name: 'بندقية القنص',
                        description: 'ضرر هائل على مدى بعيد',
                        price: 2000,
                        icon: '🎯'
                    },
                    {
                        id: 'minigun',
                        name: 'رشاش سريع',
                        description: 'إطلاق نار سريع جداً',
                        price: 3000,
                        icon: '🔥'
                    },
                    {
                        id: 'rocket',
                        name: 'قاذفة صواريخ',
                        description: 'انفجارات مدمرة',
                        price: 5000,
                        icon: '🚀'
                    },
                    {
                        id: 'flamethrower',
                        name: 'قاذفة لهب',
                        description: 'حرق الأعداء على مدى قصير',
                        price: 3500,
                        icon: '🔥'
                    },
                    {
                        id: 'laser',
                        name: 'بندقية ليزر',
                        description: 'شعاع ليزر قوي',
                        price: 4000,
                        icon: '✨'
                    },
                    {
                        id: 'plasma',
                        name: 'بندقية بلازما',
                        description: 'كرة بلازما مدمرة',
                        price: 6000,
                        icon: '⚡'
                    }
                ],
                upgrades: [
                    {
                        id: 'swordUpgrade',
                        name: 'ترقية السيف',
                        description: '+15 ضرر للسيف',
                        price: 500,
                        icon: '🗡️'
                    },
                    {
                        id: 'healthUpgrade',
                        name: 'زيادة الصحة',
                        description: '+25 صحة قصوى',
                        price: 300,
                        icon: '❤️'
                    },
                    {
                        id: 'armorUpgrade',
                        name: 'زيادة الدرع',
                        description: '+25 درع قصوى',
                        price: 400,
                        icon: '🛡️'
                    },
                    {
                        id: 'staminaUpgrade',
                        name: 'زيادة الطاقة',
                        description: '+25 طاقة قصوى',
                        price: 250,
                        icon: '⚡'
                    },
                    {
                        id: 'damageUpgrade',
                        name: 'زيادة الضرر',
                        description: '+10% ضرر جميع الأسلحة',
                        price: 1000,
                        icon: '💥'
                    }
                ],
                consumables: [
                    {
                        id: 'healthPotion',
                        name: 'جرعة صحة',
                        description: 'استعادة 50 صحة',
                        price: 200,
                        icon: '🧪'
                    },
                    {
                        id: 'ammoPack',
                        name: 'حزمة ذخيرة',
                        description: 'إعادة ملء جميع الذخيرة',
                        price: 300,
                        icon: '📦'
                    },
                    {
                        id: 'grenade',
                        name: 'حزمة قنابل',
                        description: '3 قنابل يدوية',
                        price: 150,
                        icon: '💣'
                    },
                    {
                        id: 'shieldPotion',
                        name: 'جرعة درع',
                        description: 'درع كامل مؤقت',
                        price: 400,
                        icon: '🛡️'
                    }
                ],
                special: [
                    {
                        id: 'doubleJumpSkill',
                        name: 'مهارة القفز المزدوج',
                        description: 'القفز مرتين في الهواء',
                        price: 5000,
                        icon: '⬆️'
                    },
                    {
                        id: 'criticalSkill',
                        name: 'مهارة الضربة الحرجة',
                        description: '+20% فرصة ضربة حرجة',
                        price: 3000,
                        icon: '🎯'
                    },
                    {
                        id: 'lifeStealSkill',
                        name: 'مهارة امتصاص الحياة',
                        description: 'استعادة 10% من الضرر',
                        price: 4000,
                        icon: '❤️'
                    }
                ]
            };
            
            static renderShop() {
                const shopItems = document.querySelector('.shop-items');
                shopItems.innerHTML = '';
                
                const activeCategory = document.querySelector('.category-btn.active').dataset.category;
                const items = this.categories[activeCategory];
                
                items.forEach(item => {
                    const itemElement = document.createElement('div');
                    itemElement.className = 'shop-item';
                    if (player.weapons[item.id] && player.weapons[item.id].unlocked) {
                        itemElement.classList.add('disabled');
                    }
                    itemElement.dataset.item = item.id;
                    
                    itemElement.innerHTML = `
                        <div style="display: flex; align-items: center; gap: 10px;">
                            <div style="font-size: 28px;">${item.icon}</div>
                            <div class="item-info">
                                <div class="item-name">${item.name}</div>
                                <div class="item-desc">${item.description}</div>
                            </div>
                        </div>
                        <div class="item-price">${item.price} 💰</div>
                    `;
                    
                    shopItems.appendChild(itemElement);
                });
            }
            
            static buyItem(itemId, player) {
                // البحث عن العنصر في جميع الفئات
                let item = null;
                for (const category in this.categories) {
                    item = this.categories[category].find(i => i.id === itemId);
                    if (item) break;
                }
                
                if (!item) return false;
                
                if (player.gold >= item.price) {
                    player.gold -= item.price;
                    player.updateUI();
                    
                    // تطبيق التأثير
                    this.applyItemEffect(itemId, player);
                    
                    // تحديث المتجر
                    this.renderShop();
                    
                    playSound('coin');
                    showHint(`✅ تم شراء ${item.name}!`);
                    
                    return true;
                } else {
                    showHint('💰 لا تملك ذهباً كافياً!');
                    return false;
                }
            }
            
            static applyItemEffect(itemId, player) {
                switch(itemId) {
                    case 'shotgun':
                    case 'rifle':
                    case 'minigun':
                    case 'rocket':
                    case 'flamethrower':
                    case 'laser':
                    case 'plasma':
                        player.weapons[itemId].unlocked = true;
                        player.weapons[itemId].ammo = player.weapons[itemId].ammo || 30;
                        unlockWeapon(itemId);
                        break;
                        
                    case 'swordUpgrade':
                        player.weapons.sword.damage += 15;
                        break;
                        
                    case 'healthUpgrade':
                        player.maxHealth += 25;
                        player.health = player.maxHealth;
                        break;
                        
                    case 'armorUpgrade':
                        player.maxArmor += 25;
                        player.armor = player.maxArmor;
                        break;
                        
                    case 'staminaUpgrade':
                        player.maxStamina += 25;
                        player.stamina = player.maxStamina;
                        break;
                        
                    case 'damageUpgrade':
                        for (const weapon in player.weapons) {
                            player.weapons[weapon].damage *= 1.1;
                        }
                        break;
                        
                    case 'healthPotion':
                        player.heal(50);
                        break;
                        
                    case 'ammoPack':
                        for (const weapon in player.weapons) {
                            if (player.weapons[weapon].ammo !== Infinity) {
                                player.weapons[weapon].ammo = player.weapons[weapon].ammo || 30;
                            }
                        }
                        updateAmmoUI();
                        break;
                        
                    case 'grenade':
                        player.grenades += 3;
                        break;
                        
                    case 'doubleJumpSkill':
                        player.skills.doubleJump = true;
                        break;
                        
                    case 'criticalSkill':
                        player.skills.criticalHit = true;
                        break;
                        
                    case 'lifeStealSkill':
                        player.skills.lifeSteal = true;
                        break;
                }
            }
        }
        
        // ============= نظام الموجات المتقدم =============
        class AdvancedWaveSystem {
            constructor() {
                this.wave = 1;
                this.enemiesPerWave = 8;
                this.enemiesSpawned = 0;
                this.enemiesDefeated = 0;
                this.spawnTimer = 0;
                this.spawnDelay = 80;
                this.waveCooldown = 0;
                this.bossWave = 5;
                this.difficulty = 1;
            }
            
            update() {
                if (this.waveCooldown > 0) {
                    this.waveCooldown--;
                    
                    if (this.waveCooldown === 30) {
                        showHint(`🔔 الموجة ${this.wave} تبدأ!`);
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
                    
                    if (this.wave >= this.bossWave && rand < 0.1) {
                        enemyType = 'boss';
                    } else if (rand < 0.2) {
                        enemyType = 'elite';
                    }
                    
                    enemies.push(new AdvancedEnemy(this.wave, enemyType));
                    this.spawnTimer = 0;
                    
                    updateEnemiesUI();
                }
                
                if (this.enemiesDefeated >= this.enemiesPerWave) {
                    this.nextWave();
                }
                
                // تحديث شريط التقدم
                const progress = (this.enemiesDefeated / this.enemiesPerWave) * 100;
                document.getElementById('waveProgress').style.width = `${progress}%`;
            }
            
            nextWave() {
                this.wave++;
                this.enemiesPerWave = 8 + Math.floor(this.wave * 1.5);
                this.enemiesSpawned = 0;
                this.enemiesDefeated = 0;
                this.waveCooldown = 180;
                this.difficulty = 1 + (this.wave * 0.1);
                
                // مكافآت الموجة
                player.gold += 250 * this.wave;
                player.gems += this.wave % 3 === 0 ? 1 : 0;
                player.score += 1000 * this.wave;
                player.heal(30 + this.wave * 5);
                
                // تحديث الواجهة
                document.getElementById('waveText').textContent = this.wave;
                document.getElementById('enemiesText').textContent = this.enemiesPerWave;
                
                // إنجاز الموجة
                if (this.wave === 10) {
                    achievementSystem.unlock('waveSurvivor');
                }
                
                showHint(`🎉 اكتملت الموجة ${this.wave-1}! +${250 * this.wave} ذهب`);
            }
        }
        
        // ============= دوال المساعدة =============
        function unlockWeapon(weaponType) {
            const slot = document.querySelector(`[data-weapon="${weaponType}"]`);
            if (slot) {
                slot.classList.remove('locked');
                
                // إنجاز الأسلحة
                const unlockedCount = Object.values(player.weapons).filter(w => w.unlocked).length;
                if (unlockedCount >= 5) {
                    achievementSystem.unlock('weaponMaster');
                }
            }
        }
        
        function updateAmmoUI() {
            // تحديث عداد الذخيرة لكل سلاح
            for (const weapon in player.weapons) {
                const slot = document.querySelector(`[data-weapon="${weapon}"] .ammo-count`);
                if (slot && player.weapons[weapon].ammo !== Infinity) {
                    slot.textContent = player.weapons[weapon].ammo;
                    slot.style.display = player.weapons[weapon].unlocked ? 'block' : 'none';
                }
            }
        }
        
        function updateEnemiesUI() {
            const left = waveSystem.enemiesPerWave - waveSystem.enemiesDefeated;
            document.getElementById('enemiesText').textContent = left;
        }
        
        function updateExpUI() {
            const expPercent = (player.exp / player.maxExp) * 100;
            // يمكن إضافة شريط تجربة إذا لزم الأمر
        }
        
        function showDamageEffect(x, y) {
            const overlay = document.getElementById('damageOverlay');
            overlay.style.setProperty('--x', `${x}px`);
            overlay.style.setProperty('--y', `${y}px`);
            overlay.style.opacity = '0.6';
            
            setTimeout(() => {
                overlay.style.opacity = '0';
            }, 300);
        }
        
        function createHitIndicator(x, y, text, color) {
            const indicator = document.createElement('div');
            indicator.className = 'hit-indicator';
            indicator.textContent = text;
            indicator.style.left = `${x}px`;
            indicator.style.top = `${y}px`;
            indicator.style.color = color;
            
            document.querySelector('.ui-layer').appendChild(indicator);
            
            setTimeout(() => {
                indicator.remove();
            }, 1000);
        }
        
        function showHint(message, duration = 2000) {
            const hint = document.getElementById('hintMessage');
            hint.textContent = message;
            hint.style.display = 'block';
            
            setTimeout(() => {
                hint.style.display = 'none';
            }, duration);
        }
        
        function playSound(soundName) {
            audioSystem.play(soundName);
        }
        
        function unlockAchievement(id, customDesc = null) {
            achievementSystem.unlock(id, customDesc);
        }
        
        // ============= نظام التحكم =============
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
            // أزرار الحركة
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
                    if (action !== 'attack' && action !== 'jump') {
                        keys[action] = false;
                    }
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
                    if (action !== 'attack' && action !== 'jump') {
                        keys[action] = false;
                    }
                });
            });
            
            // اختيار الأسلحة
            document.querySelectorAll('.weapon-slot').forEach(slot => {
                slot.addEventListener('click', () => {
                    if (slot.classList.contains('locked')) return;
                    
                    const weapon = slot.dataset.weapon;
                    if (player.weapons[weapon].unlocked) {
                        // إلغاء تفعيل جميع الأسلحة
                        document.querySelectorAll('.weapon-slot').forEach(s => 
                            s.classList.remove('active'));
                        
                        // تفعيل السلاح المحدد
                        slot.classList.add('active');
                        player.weapon = weapon;
                        
                        showHint(`✅ تم اختيار ${player.weapons[weapon].name}`);
                    }
                });
            });
            
            // فئات المتجر
            document.querySelectorAll('.category-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    document.querySelectorAll('.category-btn').forEach(b => 
                        b.classList.remove('active'));
                    btn.classList.add('active');
                    AdvancedShopSystem.renderShop();
                });
            });
            
            // شراء من المتجر
            document.querySelector('.shop-items').addEventListener('click', (e) => {
                const itemElement = e.target.closest('.shop-item');
                if (itemElement && !itemElement.classList.contains('disabled')) {
                    const itemId = itemElement.dataset.item;
                    AdvancedShopSystem.buyItem(itemId, player);
                }
            });
            
            // فتح/إغلاق المتجر
            document.getElementById('shopToggle').addEventListener('click', () => {
                const shop = document.getElementById('shopPanel');
                shop.style.display = shop.style.display === 'block' ? 'none' : 'block';
                if (shop.style.display === 'block') {
                    AdvancedShopSystem.renderShop();
                }
            });
            
            // فتح/إغلاق المهارات
            document.addEventListener('keydown', (e) => {
                if (e.key === 'k' || e.key === 'K') {
                    const skills = document.getElementById('skillsPanel');
                    skills.style.display = skills.style.display === 'block' ? 'none' : 'block';
                }
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
                    case '1': case '2': case '3': case '4': case '5': case '6': case '7': case '8': case '9': case '0':
                        const weaponIndex = parseInt(e.key) - 1;
                        const weapons = Object.keys(player.weapons);
                        if (weaponIndex < weapons.length) {
                            const weapon = weapons[weaponIndex];
                            if (player.weapons[weapon].unlocked) {
                                player.weapon = weapon;
                                updateWeaponSelection();
                            }
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
                const message = `💀 انتهت اللعبة!\n\n⭐ النقاط: ${player.score}\n🌊 الموجة: ${waveSystem.wave}\n💰 الذهب: ${player.gold}\n🎯 المستوى: ${player.level}\n🗡️ الكومبو الأعلى: x${player.combo}\n👾 الأعداء المهزومين: ${player.kills}`;
                
                if (confirm(message + '\n\nهل تريد إعادة اللعبة؟')) {
                    location.reload();
                }
            }, 500);
        }
        
        // ============= متغيرات اللعبة =============
        let player, enemies = [], bullets = [], particles, waveSystem, camera, audioSystem, achievementSystem;
        let gameRunning = true;
        
        // ============= تهيئة اللعبة =============
        function initGame() {
            resizeCanvas();
            
            // إنشاء الأنظمة
            audioSystem = new AudioSystem();
            audioSystem.load();
            
            achievementSystem = new AchievementSystem();
            particles = new AdvancedParticleSystem();
            waveSystem = new AdvancedWaveSystem();
            camera = new AdvancedCamera();
            
            // إنشاء اللاعب
            player = new EnhancedPlayer();
            camera.follow(player);
            
            // تهيئة المصفوفات
            enemies = [];
            bullets = [];
            
            // إعداد التحكم
            setupControls();
            
            // تحديث الواجهة
            updateAmmoUI();
            updateEnemiesUI();
            updateExpUI();
            
            // عرض التعليمات
            showHint('🎮 حرك الأزرار حول اللاعب للتحكم!\n⚔️ اضغط زر الهجوم للقتال\n🏪 اضغط على المتجر للشراء');
            
            // بدء حلقة اللعبة
            gameLoop();
        }
        
        // ============= حلقة اللعبة الرئيسية =============
        function gameLoop() {
            if (!gameRunning || !gameLoaded) return;
            
            // تحديث الأنظمة
            camera.update();
            particles.update();
            
            // تطبيق الكاميرا
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            camera.apply();
            
            // رسم الخلفية
            drawBackground();
            
            // تحديث اللاعب
            player.update();
            
            // تحديث الموجات
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
                    player.addExp(enemy.expReward);
                    player.kills++;
                    waveSystem.enemiesDefeated++;
                    
                    // تأثيرات القتل
                    particles.createEffect('blood', enemy.x, enemy.y, 
                        enemy.type === 'boss' ? '#8b0000' : '#ff0000', 30);
                    
                    // إنجاز أول قتل
                    if (player.kills === 1) {
                        unlockAchievement('firstBlood');
                    }
                    
                    // إنجاز الكومبو
                    if (player.combo >= 10) {
                        unlockAchievement('comboMaster');
                    }
                    
                    // إنجاز الثروة
                    if (player.gold >= 5000) {
                        unlockAchievement('rich');
                    }
                    
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
                
                // تحقق من خروج الرصاصة
                if (bullet.x > camera.x + canvas.width * 1.5 || 
                    bullet.x < camera.x - canvas.width * 0.5 ||
                    bullet.y > camera.y + canvas.height * 1.5 ||
                    bullet.y < camera.y - canvas.height * 0.5) {
                    bullets.splice(i, 1);
                    continue;
                }
                
                // تحقق من الاصطدام بالأعداء
                let hit = false;
                for (let j = enemies.length - 1; j >= 0; j--) {
                    const enemy = enemies[j];
                    
                    const distX = Math.abs(bullet.x - (enemy.x + enemy.width/2));
                    const distY = Math.abs(bullet.y - (enemy.y + enemy.height/2));
                    const distance = Math.sqrt(distX * distX + distY * distY);
                    
                    if (distance < (enemy.width/2 + bullet.width)) {
                        if (enemy.takeDamage(bullet.damage, player)) {
                            // قتل العدو
                            player.gold += enemy.reward;
                            player.score += enemy.reward * 2;
                            player.addExp(enemy.expReward);
                            player.kills++;
                            waveSystem.enemiesDefeated++;
                            
                            particles.createEffect('blood', enemy.x, enemy.y, 
                                enemy.type === 'boss' ? '#8b0000' : '#ff0000', 40);
                            enemies.splice(j, 1);
                            updateEnemiesUI();
                        }
                        
                        // تأثيرات الاصطدام
                        if (bullet.type === 'rocket') {
                            particles.createEffect('explosion', bullet.x, bullet.y, '#ff4500', 30);
                            camera.addShake(5);
                        } else {
                            particles.createEffect('spark', bullet.x, bullet.y, bullet.color, 10);
                        }
                        
                        bullets.splice(i, 1);
                        hit = true;
                        break;
                    }
                }
                
                if (!hit) {
                    bullet.draw();
                    particles.createTrail(bullet.x, bullet.y, bullet.color);
                }
            }
            
            // رسم اللاعب
            player.draw();
            
            // رسم الجسيمات
            particles.draw();
            
            // إعادة ضبط الكاميرا
            camera.reset();
            
            // الاستمرار في حلقة اللعبة
            requestAnimationFrame(gameLoop);
        }
        
        // ============= رسم الخلفية =============
        function drawBackground() {
            // خلفية متدرجة
            const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#0f0c29');
            gradient.addColorStop(0.5, '#302b63');
            gradient.addColorStop(1, '#24243e');
            ctx.fillStyle = gradient;
            ctx.fillRect(camera.x, camera.y, canvas.width, canvas.height);
            
            // النجوم
            ctx.fillStyle = '#ffffff';
            for (let i = 0; i < 100; i++) {
                const starX = (i * 137) % (canvas.width * 2);
                const starY = (i * 97) % (canvas.height);
                const size = Math.sin(Date.now() * 0.001 + i) * 0.5 + 1;
                ctx.beginPath();
                ctx.arc(camera.x + starX, camera.y + starY, size, 0, Math.PI * 2);
                ctx.fill();
            }
            
            // الأرض
            const groundY = canvas.height - 150;
            ctx.fillStyle = '#228b22';
            ctx.fillRect(camera.x, camera.y + groundY, canvas.width * 2, 150);
            
            // العشب
            ctx.fillStyle = '#32cd32';
            for (let i = 0; i < 20; i++) {
                const x = camera.x + (i * 100) % (canvas.width * 2);
                ctx.fillRect(x, camera.y + groundY, 80, 20);
            }
            
            // القلعة في الخلفية
            ctx.fillStyle = '#8b0000';
            const castleX = camera.x + canvas.width * 1.5;
            const castleY = camera.y + groundY - 200;
            ctx.fillRect(castleX, castleY, 100, 200);
            
            // أبراج القلعة
            ctx.fillStyle = '#dc143c';
            ctx.fillRect(castleX - 20, castleY - 50, 140, 50);
            ctx.fillRect(castleX + 20, castleY - 100, 60, 50);
        }
        
        // ============= بدء اللعبة =============
        window.addEventListener('load', () => {
            resizeCanvas();
        });
        
        window.addEventListener('resize', () => {
            resizeCanvas();
        });
    </script>
</body>
</html>

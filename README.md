<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حصار القلعة - المعركة الأسطورية</title>
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
            font-family: 'Arial Arabic', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #000;
            color: white;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
            position: fixed;
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
            transition: opacity 0.8s;
        }

        .loading-container {
            background: rgba(0, 0, 0, 0.9);
            border: 4px solid #ffd700;
            border-radius: 20px;
            padding: 40px;
            width: 90%;
            max-width: 600px;
            text-align: center;
            box-shadow: 0 0 50px rgba(255, 215, 0, 0.5);
        }

        .loading-title {
            color: #ffd700;
            font-size: 2.8rem;
            margin-bottom: 30px;
            text-shadow: 0 0 20px #ffd700;
            animation: titleGlow 2s infinite;
        }

        @keyframes titleGlow {
            0%, 100% { text-shadow: 0 0 20px #ffd700; }
            50% { text-shadow: 0 0 40px #ffd700, 0 0 60px #ff4500; }
        }

        .progress-container {
            background: rgba(255, 255, 255, 0.1);
            height: 25px;
            border-radius: 12px;
            overflow: hidden;
            margin: 30px 0;
            border: 2px solid #ffd700;
        }

        .progress-bar {
            height: 100%;
            background: linear-gradient(90deg, #ff0000, #ffd700, #00ff00);
            width: 0%;
            transition: width 0.5s;
            position: relative;
            overflow: hidden;
        }

        .progress-bar::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(90deg, 
                transparent 0%, 
                rgba(255, 255, 255, 0.3) 50%, 
                transparent 100%);
            animation: shimmer 2s infinite;
        }

        @keyframes shimmer {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }

        .loading-text {
            color: #ffd700;
            font-size: 1.4rem;
            margin-top: 20px;
            min-height: 40px;
        }

        .loading-assets {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
            margin-top: 30px;
        }

        .asset-item {
            background: rgba(255, 215, 0, 0.1);
            padding: 15px;
            border-radius: 10px;
            text-align: right;
            border: 1px solid rgba(255, 215, 0, 0.3);
        }

        .asset-name {
            color: #ffd700;
            font-size: 1rem;
            margin-bottom: 8px;
        }

        .asset-bar {
            height: 8px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 4px;
            overflow: hidden;
        }

        .asset-fill {
            height: 100%;
            background: linear-gradient(90deg, #00ffff, #0080ff);
            width: 0%;
            transition: width 0.3s;
            border-radius: 4px;
        }

        /* الشاشة الرئيسية */
        #gameScreen {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
        }

        canvas {
            position: absolute;
            top: 0;
            left: 0;
            image-rendering: crisp-edges;
            image-rendering: pixelated;
        }

        /* واجهة المستخدم */
        .ui-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 100;
        }

        /* شريط المعلومات العلوي */
        .hud-container {
            position: absolute;
            top: 20px;
            left: 20px;
            right: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: linear-gradient(to bottom, 
                rgba(0, 0, 0, 0.85) 0%,
                rgba(139, 0, 0, 0.7) 100%);
            padding: 15px 25px;
            border-radius: 20px;
            border: 3px solid #ffd700;
            backdrop-filter: blur(10px);
            pointer-events: all;
            box-shadow: 0 10px 40px rgba(0, 0, 0, 0.7);
        }

        .player-stats {
            display: flex;
            gap: 25px;
            flex-wrap: wrap;
        }

        .stat-box {
            display: flex;
            align-items: center;
            gap: 15px;
            min-width: 180px;
        }

        .stat-icon {
            font-size: 2rem;
            width: 50px;
            height: 50px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(0, 0, 0, 0.5);
            border-radius: 12px;
            border: 2px solid;
        }

        .health-icon {
            color: #ff0000;
            border-color: #ff0000;
            background: rgba(255, 0, 0, 0.1);
        }

        .stamina-icon {
            color: #00ff00;
            border-color: #00ff00;
            background: rgba(0, 255, 0, 0.1);
        }

        .gold-icon {
            color: #ffd700;
            border-color: #ffd700;
            background: rgba(255, 215, 0, 0.1);
        }

        .stat-info {
            flex-grow: 1;
        }

        .stat-label {
            color: #ffd700;
            font-size: 1rem;
            margin-bottom: 5px;
        }

        .stat-value {
            color: white;
            font-size: 1.4rem;
            font-weight: bold;
            direction: ltr;
        }

        .stat-bar {
            height: 10px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 5px;
            overflow: hidden;
            margin-top: 8px;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }

        .stat-fill {
            height: 100%;
            transition: width 0.3s;
            position: relative;
        }

        .health-fill {
            background: linear-gradient(90deg, #ff0000, #ff4444);
        }

        .health-fill::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(90deg, 
                transparent 0%, 
                rgba(255, 255, 255, 0.3) 50%, 
                transparent 100%);
            animation: pulseHealth 2s infinite;
        }

        .stamina-fill {
            background: linear-gradient(90deg, #00ff00, #44ff44);
        }

        .stamina-fill::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(90deg, 
                transparent 0%, 
                rgba(255, 255, 255, 0.3) 50%, 
                transparent 100%);
            animation: pulseStamina 2s infinite;
        }

        @keyframes pulseHealth {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }

        @keyframes pulseStamina {
            0% { transform: translateX(-100%); opacity: 0.5; }
            100% { transform: translateX(100%); opacity: 1; }
        }

        .wave-info {
            background: linear-gradient(135deg, #8b0000, #dc143c);
            padding: 15px 25px;
            border-radius: 15px;
            border: 2px solid #ffd700;
            min-width: 220px;
            text-align: center;
            box-shadow: 0 0 20px rgba(255, 0, 0, 0.5);
        }

        .wave-title {
            color: #ffd700;
            font-size: 1.2rem;
            margin-bottom: 10px;
        }

        .wave-stats {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            color: white;
            font-size: 1.1rem;
        }

        .wave-stat span {
            color: #ffd700;
            font-weight: bold;
        }

        .wave-progress {
            height: 6px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 3px;
            margin-top: 10px;
            overflow: hidden;
        }

        .wave-fill {
            height: 100%;
            background: linear-gradient(90deg, #00ffff, #0080ff);
            width: 0%;
            transition: width 0.5s;
            border-radius: 3px;
        }

        /* منطقة التحكم */
        .controls-container {
            position: absolute;
            bottom: 140px;
            left: 50%;
            transform: translateX(-50%);
            width: 400px;
            height: 400px;
            pointer-events: none;
            z-index: 50;
        }

        .joystick-base {
            position: absolute;
            width: 250px;
            height: 250px;
            background: radial-gradient(
                circle at center,
                rgba(0, 0, 0, 0.6) 0%,
                rgba(0, 0, 0, 0.3) 50%,
                transparent 70%
            );
            border-radius: 50%;
            border: 3px solid rgba(255, 215, 0, 0.5);
            backdrop-filter: blur(5px);
            left: 20px;
            bottom: 20px;
            pointer-events: all;
        }

        .joystick {
            position: absolute;
            width: 90px;
            height: 90px;
            background: radial-gradient(
                circle at 30% 30%,
                #ffd700 0%,
                #ff8c00 100%
            );
            border-radius: 50%;
            border: 3px solid white;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            cursor: move;
            box-shadow: 
                0 0 20px rgba(255, 215, 0, 0.8),
                inset 0 0 20px rgba(255, 255, 255, 0.5);
            transition: transform 0.1s;
        }

        .action-buttons {
            position: absolute;
            right: 20px;
            bottom: 20px;
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
            pointer-events: all;
        }

        .action-btn {
            width: 90px;
            height: 90px;
            border-radius: 25px;
            background: linear-gradient(145deg, 
                rgba(139, 0, 0, 0.9),
                rgba(220, 20, 60, 0.9));
            border: 4px solid #ffd700;
            color: white;
            font-size: 2.2rem;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.1s;
            user-select: none;
            box-shadow: 
                0 8px 20px rgba(0, 0, 0, 0.5),
                0 0 15px rgba(255, 215, 0, 0.5);
        }

        .action-btn:active {
            transform: scale(0.92);
            background: linear-gradient(145deg, 
                rgba(220, 20, 60, 0.9),
                rgba(255, 69, 0, 0.9));
            box-shadow: 
                0 4px 10px rgba(0, 0, 0, 0.3),
                0 0 25px rgba(255, 215, 0, 0.8);
        }

        .action-label {
            position: absolute;
            bottom: -25px;
            left: 50%;
            transform: translateX(-50%);
            color: #ffd700;
            font-size: 0.9rem;
            white-space: nowrap;
            background: rgba(0, 0, 0, 0.7);
            padding: 3px 10px;
            border-radius: 10px;
            border: 1px solid #ffd700;
        }

        /* شريط الأسلحة */
        .weapons-bar {
            position: absolute;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 15px;
            background: linear-gradient(to bottom,
                rgba(0, 0, 0, 0.9),
                rgba(139, 0, 0, 0.8));
            padding: 15px 30px;
            border-radius: 25px;
            border: 3px solid #ffd700;
            pointer-events: all;
            backdrop-filter: blur(10px);
            box-shadow: 
                0 10px 40px rgba(0, 0, 0, 0.8),
                0 0 30px rgba(255, 215, 0, 0.3);
            z-index: 60;
        }

        .weapon-slot {
            width: 75px;
            height: 75px;
            border-radius: 18px;
            background: linear-gradient(135deg, 
                rgba(50, 50, 50, 0.8),
                rgba(20, 20, 20, 0.8));
            border: 3px solid #666;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            position: relative;
            overflow: hidden;
        }

        .weapon-slot::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(135deg, 
                rgba(255, 255, 255, 0.1) 0%,
                transparent 100%);
            opacity: 0;
            transition: opacity 0.3s;
        }

        .weapon-slot:hover::before {
            opacity: 1;
        }

        .weapon-slot.active {
            border-color: #ffd700;
            background: linear-gradient(135deg, 
                rgba(139, 0, 0, 0.9),
                rgba(220, 20, 60, 0.9));
            transform: scale(1.15);
            box-shadow: 
                0 0 25px #ffd700,
                inset 0 0 20px rgba(255, 215, 0, 0.4);
        }

        .weapon-slot.active::after {
            content: '';
            position: absolute;
            top: -2px;
            left: -2px;
            right: -2px;
            bottom: -2px;
            border-radius: 20px;
            background: linear-gradient(45deg, 
                #ffd700, #ff8c00, #ffd700);
            z-index: -1;
            animation: borderRotate 3s linear infinite;
        }

        @keyframes borderRotate {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .weapon-icon {
            font-size: 2.2rem;
            margin-bottom: 5px;
            filter: drop-shadow(0 0 5px currentColor);
        }

        .weapon-name {
            font-size: 0.85rem;
            color: #ffd700;
            font-weight: bold;
        }

        .ammo-count {
            position: absolute;
            bottom: 5px;
            right: 5px;
            background: linear-gradient(135deg, #dc143c, #ff0000);
            color: white;
            font-size: 0.8rem;
            padding: 3px 8px;
            border-radius: 12px;
            border: 1px solid #ffd700;
            min-width: 25px;
            text-align: center;
            font-weight: bold;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.5);
        }

        /* أزرار الجانب */
        .side-buttons {
            position: absolute;
            top: 150px;
            right: 20px;
            display: flex;
            flex-direction: column;
            gap: 20px;
            pointer-events: all;
            z-index: 70;
        }

        .side-btn {
            width: 75px;
            height: 75px;
            background: linear-gradient(135deg, 
                rgba(65, 105, 225, 0.9),
                rgba(100, 149, 237, 0.9));
            border: 3px solid #4169e1;
            border-radius: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.5rem;
            cursor: pointer;
            transition: all 0.3s;
            box-shadow: 
                0 8px 20px rgba(0, 0, 0, 0.5),
                0 0 15px rgba(65, 105, 225, 0.5);
        }

        .side-btn:hover {
            transform: rotate(10deg) scale(1.1);
            box-shadow: 
                0 12px 30px rgba(0, 0, 0, 0.7),
                0 0 25px rgba(255, 215, 0, 0.8);
        }

        .side-btn:active {
            transform: rotate(10deg) scale(0.95);
        }

        .side-label {
            position: absolute;
            left: -120px;
            top: 50%;
            transform: translateY(-50%);
            background: rgba(0, 0, 0, 0.8);
            color: #ffd700;
            padding: 8px 15px;
            border-radius: 10px;
            border: 2px solid #4169e1;
            white-space: nowrap;
            opacity: 0;
            transition: opacity 0.3s;
            pointer-events: none;
        }

        .side-btn:hover .side-label {
            opacity: 1;
        }

        /* تأثيرات الشاشة */
        .screen-effects {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 40;
        }

        .hit-flash {
            position: absolute;
            width: 100%;
            height: 100%;
            background: radial-gradient(
                circle at var(--hit-x, 50%) var(--hit-y, 50%),
                rgba(255, 0, 0, 0.4) 0%,
                transparent 70%
            );
            opacity: 0;
            transition: opacity 0.3s;
        }

        .heal-flash {
            position: absolute;
            width: 100%;
            height: 100%;
            background: radial-gradient(
                circle at var(--heal-x, 50%) var(--heal-y, 50%),
                rgba(0, 255, 0, 0.3) 0%,
                transparent 70%
            );
            opacity: 0;
            transition: opacity 0.3s;
        }

        /* تحذير البوس */
        .boss-warning {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.95),
                rgba(20, 20, 40, 0.95));
            padding: 50px 80px;
            border-radius: 30px;
            border: 5px solid #ff0000;
            text-align: center;
            z-index: 200;
            display: none;
            pointer-events: none;
            box-shadow: 
                0 0 100px rgba(255, 0, 0, 0.8),
                inset 0 0 50px rgba(255, 0, 0, 0.3);
            animation: warningPulse 1.5s infinite;
        }

        @keyframes warningPulse {
            0%, 100% { 
                transform: translate(-50%, -50%) scale(1);
                box-shadow: 
                    0 0 100px rgba(255, 0, 0, 0.8),
                    inset 0 0 50px rgba(255, 0, 0, 0.3);
            }
            50% { 
                transform: translate(-50%, -50%) scale(1.05);
                box-shadow: 
                    0 0 150px rgba(255, 0, 0, 1),
                    inset 0 0 80px rgba(255, 0, 0, 0.5);
            }
        }

        .boss-text {
            font-size: 3.5rem;
            color: #ff0000;
            font-weight: bold;
            text-shadow: 
                0 0 30px #ff0000,
                0 0 60px #ff0000;
            margin-bottom: 20px;
            animation: textGlow 1s infinite alternate;
        }

        @keyframes textGlow {
            0% { text-shadow: 0 0 30px #ff0000, 0 0 60px #ff0000; }
            100% { text-shadow: 0 0 40px #ff0000, 0 0 80px #ff0000, 0 0 100px #ff4500; }
        }

        .boss-subtext {
            font-size: 1.8rem;
            color: #ffd700;
            text-shadow: 0 0 20px #ffd700;
            margin-top: 10px;
        }

        /* مؤشرات الضرر */
        .damage-popup {
            position: absolute;
            font-size: 1.8rem;
            font-weight: bold;
            text-shadow: 
                0 0 10px currentColor,
                0 0 20px currentColor,
                0 0 30px currentColor;
            pointer-events: none;
            z-index: 90;
            animation: damageFloat 1.5s ease-out forwards;
            white-space: nowrap;
        }

        @keyframes damageFloat {
            0% {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
            50% {
                opacity: 0.9;
                transform: translateY(-40px) scale(1.3);
            }
            100% {
                opacity: 0;
                transform: translateY(-80px) scale(1.6);
            }
        }

        /* مؤشر التوجه */
        .facing-indicator {
            position: absolute;
            width: 120px;
            height: 120px;
            pointer-events: none;
            z-index: 80;
            display: none;
            filter: drop-shadow(0 0 10px #ffd700);
        }

        .facing-arrow {
            width: 0;
            height: 0;
            border-style: solid;
            border-width: 0 35px 70px 35px;
            border-color: transparent transparent #ffd700 transparent;
            position: relative;
            animation: arrowPulse 1s infinite;
        }

        .facing-arrow::after {
            content: '';
            position: absolute;
            top: 15px;
            left: -25px;
            width: 50px;
            height: 50px;
            background: radial-gradient(circle, #ffd700 0%, transparent 70%);
            border-radius: 50%;
            filter: blur(10px);
        }

        @keyframes arrowPulse {
            0%, 100% { 
                transform: scale(1);
                opacity: 0.8;
            }
            50% { 
                transform: scale(1.1);
                opacity: 1;
            }
        }

        /* النافذة المنبثقة */
        .popup-message {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.95),
                rgba(139, 0, 0, 0.9));
            padding: 40px 60px;
            border-radius: 25px;
            border: 4px solid #ffd700;
            text-align: center;
            z-index: 150;
            display: none;
            pointer-events: none;
            min-width: 300px;
            box-shadow: 
                0 0 80px rgba(255, 215, 0, 0.5),
                inset 0 0 30px rgba(255, 215, 0, 0.2);
        }

        .popup-text {
            font-size: 2rem;
            color: #ffd700;
            text-shadow: 0 0 20px #ffd700;
            margin-bottom: 20px;
        }

        .popup-subtext {
            font-size: 1.3rem;
            color: white;
        }

        /* شاشة الإعدادات */
        .settings-panel {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: linear-gradient(135deg, 
                rgba(0, 0, 0, 0.98),
                rgba(20, 20, 40, 0.98));
            padding: 50px;
            border-radius: 30px;
            border: 4px solid #ffd700;
            width: 90%;
            max-width: 500px;
            z-index: 300;
            display: none;
            backdrop-filter: blur(20px);
            box-shadow: 
                0 0 100px rgba(255, 215, 0, 0.5),
                inset 0 0 50px rgba(255, 215, 0, 0.1);
        }

        .settings-title {
            color: #ffd700;
            font-size: 2.5rem;
            text-align: center;
            margin-bottom: 40px;
            text-shadow: 0 0 20px #ffd700;
        }

        .settings-group {
            margin-bottom: 30px;
        }

        .settings-label {
            display: block;
            color: #ffd700;
            font-size: 1.3rem;
            margin-bottom: 15px;
            text-align: right;
        }

        .volume-slider {
            width: 100%;
            height: 20px;
            -webkit-appearance: none;
            background: linear-gradient(90deg, 
                #ff0000 0%, 
                #ffd700 50%, 
                #00ff00 100%);
            border-radius: 10px;
            outline: none;
            border: 2px solid #ffd700;
        }

        .volume-slider::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 35px;
            height: 35px;
            background: #ffd700;
            border-radius: 50%;
            border: 3px solid white;
            cursor: pointer;
            box-shadow: 0 0 15px rgba(255, 215, 0, 0.8);
        }

        .settings-buttons {
            display: flex;
            gap: 20px;
            margin-top: 40px;
        }

        .settings-btn {
            flex: 1;
            padding: 20px;
            border: none;
            border-radius: 15px;
            font-size: 1.3rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
            text-align: center;
        }

        .save-btn {
            background: linear-gradient(135deg, #00ff00, #008000);
            color: white;
        }

        .close-btn {
            background: linear-gradient(135deg, #ff0000, #8b0000);
            color: white;
        }

        .settings-btn:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.5);
        }

        /* تأثيرات خاصة */
        .combo-display {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 4rem;
            font-weight: bold;
            color: #ff0000;
            text-shadow: 
                0 0 30px #ff0000,
                0 0 60px #ff0000,
                0 0 90px #ff0000;
            z-index: 120;
            display: none;
            pointer-events: none;
            animation: comboPop 0.5s ease-out;
        }

        @keyframes comboPop {
            0% { transform: translate(-50%, -50%) scale(0); opacity: 0; }
            70% { transform: translate(-50%, -50%) scale(1.2); opacity: 1; }
            100% { transform: translate(-50%, -50%) scale(1); opacity: 1; }
        }

        .level-up-effect {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 300px;
            height: 300px;
            background: radial-gradient(circle, 
                rgba(255, 215, 0, 0.5) 0%,
                transparent 70%);
            border-radius: 50%;
            z-index: 110;
            display: none;
            pointer-events: none;
            animation: levelUpPulse 1s ease-out;
        }

        @keyframes levelUpPulse {
            0% { transform: translate(-50%, -50%) scale(0); opacity: 1; }
            100% { transform: translate(-50%, -50%) scale(3); opacity: 0; }
        }

        /* تصميم متجاوب */
        @media (max-width: 1024px) {
            .controls-container {
                width: 350px;
                height: 350px;
            }
            
            .joystick-base {
                width: 220px;
                height: 220px;
            }
            
            .joystick {
                width: 80px;
                height: 80px;
            }
            
            .action-btn {
                width: 80px;
                height: 80px;
                font-size: 2rem;
            }
            
            .weapons-bar {
                padding: 12px 20px;
                bottom: 25px;
            }
            
            .weapon-slot {
                width: 65px;
                height: 65px;
            }
            
            .weapon-icon {
                font-size: 1.8rem;
            }
        }

        @media (max-width: 768px) {
            .hud-container {
                flex-direction: column;
                gap: 15px;
                padding: 12px 20px;
                top: 10px;
                left: 10px;
                right: 10px;
            }
            
            .player-stats {
                width: 100%;
                justify-content: space-between;
            }
            
            .stat-box {
                min-width: 150px;
            }
            
            .controls-container {
                width: 320px;
                height: 320px;
                bottom: 120px;
            }
            
            .joystick-base {
                width: 200px;
                height: 200px;
            }
            
            .joystick {
                width: 70px;
                height: 70px;
            }
            
            .action-buttons {
                gap: 15px;
            }
            
            .action-btn {
                width: 70px;
                height: 70px;
                font-size: 1.8rem;
            }
            
            .weapons-bar {
                transform: translateX(-50%) scale(0.9);
                bottom: 20px;
            }
            
            .side-buttons {
                top: 120px;
                right: 10px;
            }
            
            .side-btn {
                width: 65px;
                height: 65px;
                font-size: 2rem;
            }
            
            .boss-text {
                font-size: 2.5rem;
            }
            
            .boss-subtext {
                font-size: 1.4rem;
            }
        }

        @media (max-width: 480px) {
            .loading-title {
                font-size: 2rem;
            }
            
            .hud-container {
                padding: 10px 15px;
            }
            
            .stat-box {
                min-width: 130px;
                gap: 10px;
            }
            
            .stat-icon {
                width: 40px;
                height: 40px;
                font-size: 1.5rem;
            }
            
            .stat-value {
                font-size: 1.2rem;
            }
            
            .wave-info {
                min-width: 180px;
                padding: 10px 15px;
            }
            
            .controls-container {
                width: 280px;
                height: 280px;
                bottom: 110px;
            }
            
            .weapons-bar {
                gap: 10px;
                padding: 10px 15px;
                bottom: 15px;
            }
            
            .weapon-slot {
                width: 55px;
                height: 55px;
                border-radius: 12px;
            }
            
            .weapon-icon {
                font-size: 1.5rem;
            }
            
            .weapon-name {
                font-size: 0.7rem;
            }
            
            .side-buttons {
                top: 100px;
            }
        }
    </style>
</head>
<body>
    <!-- شاشة التحميل -->
    <div id="loadingScreen">
        <div class="loading-container">
            <div class="loading-title">⚔️ حصار القلعة ⚔️</div>
            <div class="progress-container">
                <div class="progress-bar" id="loaderProgress"></div>
            </div>
            <div class="loading-text" id="loadingText">تهيئة محرك اللعبة...</div>
            
            <div class="loading-assets">
                <div class="asset-item">
                    <div class="asset-name">🎮 نظام التحكم</div>
                    <div class="asset-bar">
                        <div class="asset-fill" id="controlsProgress"></div>
                    </div>
                </div>
                <div class="asset-item">
                    <div class="asset-name">👾 نماذج الأعداء</div>
                    <div class="asset-bar">
                        <div class="asset-fill" id="enemiesProgress"></div>
                    </div>
                </div>
                <div class="asset-item">
                    <div class="asset-name">🔫 نظام الأسلحة</div>
                    <div class="asset-bar">
                        <div class="asset-fill" id="weaponsProgress"></div>
                    </div>
                </div>
                <div class="asset-item">
                    <div class="asset-name">✨ التأثيرات البصرية</div>
                    <div class="asset-bar">
                        <div class="asset-fill" id="effectsProgress"></div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- الشاشة الرئيسية -->
    <div id="gameScreen">
        <!-- Canvas سيتم إنشاؤه ديناميكياً -->
        
        <!-- واجهة المستخدم -->
        <div class="ui-layer">
            <!-- شريط المعلومات -->
            <div class="hud-container">
                <div class="player-stats">
                    <div class="stat-box">
                        <div class="stat-icon health-icon">❤️</div>
                        <div class="stat-info">
                            <div class="stat-label">الصحة</div>
                            <div class="stat-value" id="healthValue">100</div>
                            <div class="stat-bar">
                                <div id="healthFill" class="stat-fill health-fill" style="width: 100%"></div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="stat-box">
                        <div class="stat-icon stamina-icon">⚡</div>
                        <div class="stat-info">
                            <div class="stat-label">الطاقة</div>
                            <div class="stat-value" id="staminaValue">100</div>
                            <div class="stat-bar">
                                <div id="staminaFill" class="stat-fill stamina-fill" style="width: 100%"></div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="stat-box">
                        <div class="stat-icon gold-icon">💰</div>
                        <div class="stat-info">
                            <div class="stat-label">الذهب</div>
                            <div class="stat-value" id="goldValue">1000</div>
                            <div class="stat-label">المستوى: <span id="levelValue">1</span></div>
                        </div>
                    </div>
                </div>
                
                <div class="wave-info">
                    <div class="wave-title">🌊 الموجة الحالية</div>
                    <div class="wave-stats">
                        <div class="wave-stat">الموجة: <span id="waveValue">1</span></div>
                        <div class="wave-stat">الوحوش: <span id="enemiesValue">0/20</span></div>
                        <div class="wave-stat">النقاط: <span id="scoreValue">0</span></div>
                    </div>
                    <div class="wave-progress">
                        <div id="waveProgress" class="wave-fill" style="width: 0%"></div>
                    </div>
                </div>
            </div>
            
            <!-- منطقة التحكم -->
            <div class="controls-container">
                <div class="joystick-base" id="joystickBase">
                    <div class="joystick" id="joystick"></div>
                </div>
                
                <div class="action-buttons">
                    <div class="action-btn" id="jumpBtn" title="قفز">
                        ⬆️
                        <div class="action-label">قفز</div>
                    </div>
                    <div class="action-btn" id="attackBtn" title="هجوم">
                        ⚔️
                        <div class="action-label">هجوم</div>
                    </div>
                    <div class="action-btn" id="dashBtn" title="دفعة">
                        💨
                        <div class="action-label">دفعة</div>
                    </div>
                    <div class="action-btn" id="blockBtn" title="دفاع">
                        🛡️
                        <div class="action-label">دفاع</div>
                    </div>
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
            
            <!-- أزرار الجانب -->
            <div class="side-buttons">
                <div class="side-btn" id="pauseBtn" title="إيقاف مؤقت">
                    ⏸️
                    <div class="side-label">إيقاف مؤقت</div>
                </div>
                <div class="side-btn" id="soundBtn" title="الصوت">
                    🔊
                    <div class="side-label">الصوت</div>
                </div>
                <div class="side-btn" id="settingsBtn" title="إعدادات">
                    ⚙️
                    <div class="side-label">إعدادات</div>
                </div>
                <div class="side-btn" id="helpBtn" title="مساعدة">
                    ❓
                    <div class="side-label">مساعدة</div>
                </div>
            </div>
            
            <!-- مؤشر التوجه -->
            <div class="facing-indicator" id="facingIndicator">
                <div class="facing-arrow"></div>
            </div>
            
            <!-- تأثيرات الشاشة -->
            <div class="screen-effects">
                <div class="hit-flash" id="hitFlash"></div>
                <div class="heal-flash" id="healFlash"></div>
            </div>
            
            <!-- تحذير البوس -->
            <div class="boss-warning" id="bossWarning">
                <div class="boss-text">👹 وحش عملاق يقترب 👹</div>
                <div class="boss-subtext">استعد للمعركة النهائية!</div>
            </div>
            
            <!-- رسائل منبثقة -->
            <div class="popup-message" id="popupMessage">
                <div class="popup-text" id="popupText"></div>
                <div class="popup-subtext" id="popupSubtext"></div>
            </div>
            
            <!-- تأثيرات خاصة -->
            <div class="combo-display" id="comboDisplay"></div>
            <div class="level-up-effect" id="levelUpEffect"></div>
        </div>
        
        <!-- شاشة الإعدادات -->
        <div class="settings-panel" id="settingsPanel">
            <div class="settings-title">⚙️ الإعدادات</div>
            
            <div class="settings-group">
                <label class="settings-label">🔊 حجم الموسيقى</label>
                <input type="range" min="0" max="100" value="70" class="volume-slider" id="musicVolume">
            </div>
            
            <div class="settings-group">
                <label class="settings-label">🎵 حجم المؤثرات</label>
                <input type="range" min="0" max="100" value="80" class="volume-slider" id="sfxVolume">
            </div>
            
            <div class="settings-group">
                <label class="settings-label">⚡ جودة الرسومات</label>
                <select id="graphicsQuality" style="width: 100%; padding: 15px; background: rgba(0,0,0,0.8); color: white; border: 2px solid #ffd700; border-radius: 10px; font-size: 1.2rem;">
                    <option value="low">منخفضة ⭐</option>
                    <option value="medium" selected>متوسطة ⭐⭐</option>
                    <option value="high">عالية ⭐⭐⭐</option>
                    <option value="ultra">أسطورية ⭐⭐⭐⭐</option>
                </select>
            </div>
            
            <div class="settings-group">
                <label class="settings-label">🎮 حساسية التحكم</label>
                <input type="range" min="1" max="10" value="5" class="volume-slider" id="sensitivitySlider">
            </div>
            
            <div class="settings-buttons">
                <button class="settings-btn save-btn" id="saveSettings">💾 حفظ</button>
                <button class="settings-btn close-btn" id="closeSettings">❌ إغلاق</button>
            </div>
        </div>
    </div>

    <!-- نظام الصوت -->
    <div id="audioSystem" style="display: none;">
        <!-- سيتم إنشاء الأصوات ديناميكياً -->
    </div>

    <script>
        // ============= إعدادات اللعبة =============
        const GAME_CONFIG = {
            // حجم العالم
            WORLD_WIDTH: 5000,
            WORLD_HEIGHT: 3000,
            GROUND_LEVEL: 2500,
            
            // حجم اللاعب
            PLAYER_WIDTH: 80,
            PLAYER_HEIGHT: 120,
            
            // الكاميرا
            CAMERA_ZOOM: 1.8,
            CAMERA_SMOOTHNESS: 0.1,
            
            // مواصفات اللاعب
            PLAYER_SPEED: 12,
            PLAYER_JUMP_FORCE: 28,
            PLAYER_DASH_FORCE: 35,
            PLAYER_GRAVITY: 1.2,
            
            // مواصفات الأعداء
            ENEMY_SPAWN_RATE: 45, // إطار بين كل وحش
            WAVE_ENEMIES: 20,
            
            // إعدادات الجرافيكس
            PARTICLE_COUNT: 1000,
            MAX_BULLETS: 200,
            MAX_ENEMIES: 50
        };

        // ============= المتغيرات العالمية =============
        let gameCanvas, gameCtx;
        let player = null;
        let enemies = [];
        let bullets = [];
        let particles = [];
        let powerUps = [];
        let projectiles = [];
        let camera = null;
        let waveSystem = null;
        let audioSystem = null;
        let inputSystem = null;
        let particleSystem = null;
        
        let gameRunning = true;
        let gamePaused = false;
        let gameTime = 0;
        let frameCount = 0;
        
        // ============= نظام التحميل المحسن =============
        class LoadingSystem {
            constructor() {
                this.progress = 0;
                this.steps = [
                    { progress: 10, text: "تهيئة محرك اللعبة...", assets: { controls: 0, enemies: 0, weapons: 0, effects: 0 } },
                    { progress: 25, text: "تحميل نظام الرسومات...", assets: { controls: 20, enemies: 10, weapons: 10, effects: 10 } },
                    { progress: 40, text: "إنشاء نظام الصوت...", assets: { controls: 40, enemies: 25, weapons: 20, effects: 30 } },
                    { progress: 55, text: "تهيئة نظام التحكم...", assets: { controls: 60, enemies: 40, weapons: 35, effects: 50 } },
                    { progress: 70, text: "تحميل نماذج الأعداء...", assets: { controls: 80, enemies: 65, weapons: 60, effects: 70 } },
                    { progress: 85, text: "تهيئة نظام الأسلحة...", assets: { controls: 95, enemies: 85, weapons: 85, effects: 85 } },
                    { progress: 95, text: "تحميل التأثيرات البصرية...", assets: { controls: 100, enemies: 100, weapons: 100, effects: 95 } },
                    { progress: 100, text: "جاهز! ابدأ المعركة! 🚀", assets: { controls: 100, enemies: 100, weapons: 100, effects: 100 } }
                ];
                this.currentStep = 0;
            }
            
            update() {
                if (this.currentStep < this.steps.length) {
                    const step = this.steps[this.currentStep];
                    this.progress = step.progress;
                    
                    document.getElementById('loaderProgress').style.width = `${this.progress}%`;
                    document.getElementById('loadingText').textContent = step.text;
                    
                    // تحديث مؤشرات الأصول
                    document.getElementById('controlsProgress').style.width = `${step.assets.controls}%`;
                    document.getElementById('enemiesProgress').style.width = `${step.assets.enemies}%`;
                    document.getElementById('weaponsProgress').style.width = `${step.assets.weapons}%`;
                    document.getElementById('effectsProgress').style.width = `${step.assets.effects}%`;
                    
                    this.currentStep++;
                    
                    if (this.progress >= 100) {
                        setTimeout(() => {
                            this.complete();
                        }, 1000);
                    }
                }
            }
            
            complete() {
                document.getElementById('loadingScreen').style.opacity = '0';
                setTimeout(() => {
                    document.getElementById('loadingScreen').style.display = 'none';
                    document.getElementById('gameScreen').style.display = 'block';
                    initGame();
                }, 800);
            }
            
            start() {
                let stepInterval = setInterval(() => {
                    this.update();
                    if (this.progress >= 100) {
                        clearInterval(stepInterval);
                    }
                }, 600);
            }
        }

        // ============= نظام الصوت الأسطوري =============
        class EpicAudioSystem {
            constructor() {
                this.sounds = new Map();
                this.musicVolume = 0.7;
                this.sfxVolume = 0.8;
                this.masterVolume = 1.0;
                this.isMuted = false;
                this.audioContext = null;
                this.init();
            }
            
            init() {
                try {
                    this.audioContext = new (window.AudioContext || window.webkitAudioContext)();
                    this.createSounds();
                } catch (error) {
                    console.warn('نظام الصوت غير متاح:', error);
                }
            }
            
            createSounds() {
                // إنشاء جميع أنواع الأصوات
                const soundTypes = [
                    'sword_swing', 'pistol_shot', 'shotgun_blast',
                    'sniper_shot', 'rocket_launch', 'reload',
                    'enemy_hit', 'enemy_death', 'boss_hit',
                    'boss_roar', 'player_hit', 'player_heal',
                    'level_up', 'coin_collect', 'powerup',
                    'jump', 'dash', 'block', 'parry',
                    'wave_start', 'wave_end', 'boss_spawn',
                    'critical_hit', 'combo_break', 'ultimate'
                ];
                
                soundTypes.forEach(type => {
                    this.sounds.set(type, this.createSound(type));
                });
            }
            
            createSound(type) {
                return {
                    play: (volume = 1.0, pitch = 1.0) => {
                        if (this.isMuted || !this.audioContext) return;
                        
                        try {
                            const oscillator = this.audioContext.createOscillator();
                            const gainNode = this.audioContext.createGain();
                            const filter = this.audioContext.createBiquadFilter();
                            
                            oscillator.connect(filter);
                            filter.connect(gainNode);
                            gainNode.connect(this.audioContext.destination);
                            
                            let duration = 0.3;
                            let frequency = 440;
                            
                            switch(type) {
                                case 'sword_swing':
                                    oscillator.type = 'sawtooth';
                                    frequency = 150;
                                    duration = 0.2;
                                    filter.frequency.value = 1000;
                                    break;
                                case 'pistol_shot':
                                    oscillator.type = 'square';
                                    frequency = 800;
                                    duration = 0.1;
                                    filter.frequency.value = 2000;
                                    break;
                                case 'shotgun_blast':
                                    oscillator.type = 'sawtooth';
                                    frequency = 200;
                                    duration = 0.3;
                                    filter.frequency.value = 800;
                                    break;
                                case 'rocket_launch':
                                    oscillator.type = 'sawtooth';
                                    frequency = 100;
                                    duration = 0.5;
                                    filter.frequency.value = 500;
                                    break;
                                case 'enemy_hit':
                                    oscillator.type = 'sine';
                                    frequency = 300;
                                    duration = 0.15;
                                    break;
                                case 'boss_roar':
                                    oscillator.type = 'sawtooth';
                                    frequency = 80;
                                    duration = 1.5;
                                    filter.frequency.value = 400;
                                    break;
                                case 'level_up':
                                    oscillator.type = 'sine';
                                    frequency = 523.25; // C5
                                    duration = 0.8;
                                    break;
                            }
                            
                            oscillator.frequency.setValueAtTime(frequency * pitch, this.audioContext.currentTime);
                            gainNode.gain.setValueAtTime(this.sfxVolume * this.masterVolume * volume, this.audioContext.currentTime);
                            gainNode.gain.exponentialRampToValueAtTime(0.001, this.audioContext.currentTime + duration);
                            
                            oscillator.start();
                            oscillator.stop(this.audioContext.currentTime + duration);
                            
                        } catch (error) {
                            console.warn(`خطأ في تشغيل الصوت ${type}:`, error);
                        }
                    }
                };
            }
            
            play(soundType, volume = 1.0, pitch = 1.0) {
                const sound = this.sounds.get(soundType);
                if (sound) {
                    sound.play(volume, pitch);
                }
            }
            
            setVolume(type, value) {
                if (type === 'music') this.musicVolume = value / 100;
                if (type === 'sfx') this.sfxVolume = value / 100;
                if (type === 'master') this.masterVolume = value / 100;
            }
            
            toggleMute() {
                this.isMuted = !this.isMuted;
                return this.isMuted;
            }
        }

        // ============= نظام الكاميرا الذكية =============
        class SmartCamera {
            constructor(target) {
                this.target = target;
                this.x = 0;
                this.y = 0;
                this.zoom = GAME_CONFIG.CAMERA_ZOOM;
                this.targetZoom = GAME_CONFIG.CAMERA_ZOOM;
                this.smoothness = GAME_CONFIG.CAMERA_SMOOTHNESS;
                this.shakeIntensity = 0;
                this.shakeDuration = 0;
                this.bounds = {
                    minX: 0,
                    maxX: GAME_CONFIG.WORLD_WIDTH,
                    minY: 0,
                    maxY: GAME_CONFIG.WORLD_HEIGHT
                };
                this.effects = [];
            }
            
            update() {
                if (!this.target) return;
                
                // حساب الموضع المستهدف مع ضمان رؤية اللاعب
                let targetX = this.target.x - (window.innerWidth / 2) / this.zoom;
                let targetY = this.target.y - (window.innerHeight / 2) / this.zoom;
                
                // إبقاء الكاميرا داخل حدود العالم
                const marginX = window.innerWidth / this.zoom / 3;
                const marginY = window.innerHeight / this.zoom / 3;
                
                targetX = Math.max(this.bounds.minX + marginX,
                    Math.min(this.bounds.maxX - marginX, targetX));
                targetY = Math.max(this.bounds.minY + marginY,
                    Math.min(this.bounds.maxY - marginY, targetY));
                
                // تطبيق الحركة السلسة
                this.x += (targetX - this.x) * this.smoothness;
                this.y += (targetY - this.y) * this.smoothness;
                
                // تحديث التكبير/التصغير
                this.zoom += (this.targetZoom - this.zoom) * 0.05;
                
                // تطبيق تأثير الاهتزاز
                if (this.shakeDuration > 0) {
                    const angle = Math.random() * Math.PI * 2;
                    const intensity = this.shakeIntensity * (this.shakeDuration / 60);
                    this.x += Math.cos(angle) * intensity;
                    this.y += Math.sin(angle) * intensity;
                    this.shakeDuration--;
                    this.shakeIntensity *= 0.9;
                }
                
                // تحديث التأثيرات
                this.updateEffects();
            }
            
            updateEffects() {
                this.effects = this.effects.filter(effect => {
                    effect.duration--;
                    return effect.duration > 0;
                });
            }
            
            apply() {
                gameCtx.save();
                gameCtx.translate(window.innerWidth / 2, window.innerHeight / 2);
                gameCtx.scale(this.zoom, this.zoom);
                gameCtx.translate(-this.x, -this.y);
                
                // تطبيق تأثيرات ما بعد المعالجة
                this.effects.forEach(effect => {
                    if (effect.type === 'blur') {
                        gameCtx.filter = `blur(${effect.amount}px)`;
                    } else if (effect.type === 'grayscale') {
                        gameCtx.filter = 'grayscale(100%)';
                    }
                });
            }
            
            reset() {
                gameCtx.restore();
                gameCtx.filter = 'none';
            }
            
            shake(intensity, duration = 30) {
                this.shakeIntensity = Math.max(this.shakeIntensity, intensity);
                this.shakeDuration = Math.max(this.shakeDuration, duration);
            }
            
            zoomTo(target, duration = 60) {
                this.targetZoom = target;
            }
            
            addEffect(type, amount, duration) {
                this.effects.push({ type, amount, duration });
            }
            
            worldToScreen(worldX, worldY) {
                return {
                    x: (worldX - this.x) * this.zoom + window.innerWidth / 2,
                    y: (worldY - this.y) * this.zoom + window.innerHeight / 2
                };
            }
            
            screenToWorld(screenX, screenY) {
                return {
                    x: (screenX - window.innerWidth / 2) / this.zoom + this.x,
                    y: (screenY - window.innerHeight / 2) / this.zoom + this.y
                };
            }
            
            isInView(x, y, width, height) {
                const screenPos = this.worldToScreen(x, y);
                const screenWidth = width * this.zoom;
                const screenHeight = height * this.zoom;
                
                return screenPos.x + screenWidth > 0 &&
                       screenPos.x - screenWidth < window.innerWidth &&
                       screenPos.y + screenHeight > 0 &&
                       screenPos.y - screenHeight < window.innerHeight;
            }
        }

        // ============= نظام الجسيمات الأسطوري =============
        class EpicParticleSystem {
            constructor() {
                this.particles = [];
                this.emitters = [];
                this.maxParticles = GAME_CONFIG.PARTICLE_COUNT;
            }
            
            createEffect(type, x, y, options = {}) {
                const config = {
                    count: options.count || 25,
                    color: options.color || '#ff0000',
                    size: options.size || 8,
                    speed: options.speed || 15,
                    life: options.life || 1.5,
                    gravity: options.gravity || 0.5,
                    spread: options.spread || 1.0,
                    rotationSpeed: options.rotationSpeed || 0.2,
                    glow: options.glow !== undefined ? options.glow : true,
                    trailLength: options.trailLength || 10
                };
                
                for (let i = 0; i < config.count && this.particles.length < this.maxParticles; i++) {
                    const angle = Math.random() * Math.PI * 2;
                    const velocity = Math.random() * config.speed * config.spread + 2;
                    
                    const particle = {
                        x, y,
                        vx: Math.cos(angle) * velocity,
                        vy: Math.sin(angle) * velocity - 3,
                        life: config.life,
                        maxLife: config.life,
                        size: Math.random() * config.size + config.size/2,
                        color: config.color,
                        type,
                        rotation: Math.random() * Math.PI * 2,
                        rotationSpeed: (Math.random() - 0.5) * config.rotationSpeed,
                        gravity: config.gravity,
                        trail: [],
                        glow: config.glow,
                        trailLength: config.trailLength,
                        alpha: 1,
                        scale: 1
                    };
                    
                    // تعديلات حسب النوع
                    switch(type) {
                        case 'blood':
                            particle.color = '#ff0000';
                            particle.gravity = 0.8;
                            particle.size *= 1.5;
                            particle.life *= 1.2;
                            break;
                        case 'spark':
                            particle.color = config.color || '#ffff00';
                            particle.life *= 0.8;
                            particle.glow = true;
                            particle.trailLength = 6;
                            break;
                        case 'magic':
                            particle.color = config.color || '#9370db';
                            particle.size *= 1.8;
                            particle.rotationSpeed *= 3;
                            particle.glow = true;
                            particle.gravity = -0.2;
                            break;
                        case 'fire':
                            particle.color = config.color || '#ff4500';
                            particle.life *= 1.5;
                            particle.gravity = -0.1;
                            particle.glow = true;
                            break;
                        case 'ice':
                            particle.color = config.color || '#00ffff';
                            particle.speed *= 0.7;
                            particle.life *= 2;
                            particle.glow = true;
                            break;
                        case 'lightning':
                            particle.color = config.color || '#ffffff';
                            particle.size *= 2;
                            particle.life *= 0.3;
                            particle.glow = true;
                            break;
                    }
                    
                    this.particles.push(particle);
                }
            }
            
            update() {
                for (let i = this.particles.length - 1; i >= 0; i--) {
                    const p = this.particles[i];
                    
                    // تحديث الموضع
                    p.x += p.vx;
                    p.y += p.vy;
                    p.vy += p.gravity;
                    p.rotation += p.rotationSpeed;
                    p.life -= 0.016;
                    
                    // تحديث الأثر
                    p.trail.push({x: p.x, y: p.y, size: p.size, alpha: p.life / p.maxLife});
                    if (p.trail.length > p.trailLength) p.trail.shift();
                    
                    // تحديث الخصائص
                    p.alpha = p.life / p.maxLife;
                    p.scale = 1 + (1 - p.alpha) * 0.5;
                    
                    // إزالة الجسيمات الميتة
                    if (p.life <= 0) {
                        this.particles.splice(i, 1);
                    }
                }
                
                // تحديث مصادر الجسيمات
                this.updateEmitters();
            }
            
            updateEmitters() {
                for (let i = this.emitters.length - 1; i >= 0; i--) {
                    const emitter = this.emitters[i];
                    emitter.life--;
                    
                    if (emitter.life % emitter.rate === 0) {
                        this.createEffect(
                            emitter.type,
                            emitter.x + (Math.random() - 0.5) * emitter.spread,
                            emitter.y + (Math.random() - 0.5) * emitter.spread,
                            emitter.options
                        );
                    }
                    
                    if (emitter.life <= 0) {
                        this.emitters.splice(i, 1);
                    }
                }
            }
            
            addEmitter(x, y, type, options = {}) {
                this.emitters.push({
                    x, y,
                    type,
                    life: options.duration || 60,
                    rate: options.rate || 3,
                    spread: options.spread || 20,
                    options
                });
            }
            
            draw() {
                this.particles.forEach(p => {
                    const alpha = p.alpha;
                    
                    // رسم الأثر أولاً
                    if (p.trail.length > 1) {
                        gameCtx.save();
                        gameCtx.globalAlpha = alpha * 0.4;
                        
                        for (let i = 0; i < p.trail.length - 1; i++) {
                            const current = p.trail[i];
                            const next = p.trail[i + 1];
                            const trailAlpha = (i / p.trail.length) * alpha;
                            
                            gameCtx.strokeStyle = p.color;
                            gameCtx.lineWidth = current.size * trailAlpha;
                            gameCtx.lineCap = 'round';
                            gameCtx.lineJoin = 'round';
                            
                            gameCtx.beginPath();
                            gameCtx.moveTo(current.x, current.y);
                            gameCtx.lineTo(next.x, next.y);
                            gameCtx.stroke();
                        }
                        
                        gameCtx.restore();
                    }
                    
                    // رسم الجسيم الرئيسي
                    gameCtx.save();
                    gameCtx.globalAlpha = alpha;
                    
                    // تأثير التوهج
                    if (p.glow) {
                        const gradient = gameCtx.createRadialGradient(
                            p.x, p.y, 0,
                            p.x, p.y, p.size * 3
                        );
                        gradient.addColorStop(0, p.color);
                        gradient.addColorStop(0.3, p.color + '80');
                        gradient.addColorStop(1, 'transparent');
                        
                        gameCtx.fillStyle = gradient;
                        gameCtx.beginPath();
                        gameCtx.arc(p.x, p.y, p.size * 3, 0, Math.PI * 2);
                        gameCtx.fill();
                    }
                    
                    // الجسم الرئيسي
                    gameCtx.fillStyle = p.color;
                    gameCtx.translate(p.x, p.y);
                    gameCtx.rotate(p.rotation);
                    gameCtx.scale(p.scale, p.scale);
                    
                    switch(p.type) {
                        case 'magic':
                            // نجمة سداسية
                            gameCtx.beginPath();
                            for (let i = 0; i < 6; i++) {
                                const angle = (i * Math.PI) / 3;
                                gameCtx.lineTo(
                                    Math.cos(angle) * p.size,
                                    Math.sin(angle) * p.size
                                );
                            }
                            gameCtx.closePath();
                            gameCtx.fill();
                            break;
                            
                        case 'spark':
                            // شرارة
                            gameCtx.beginPath();
                            gameCtx.moveTo(0, -p.size);
                            gameCtx.lineTo(p.size * 0.7, -p.size * 0.3);
                            gameCtx.lineTo(p.size, 0);
                            gameCtx.lineTo(p.size * 0.3, p.size * 0.7);
                            gameCtx.lineTo(0, p.size);
                            gameCtx.lineTo(-p.size * 0.3, p.size * 0.7);
                            gameCtx.lineTo(-p.size, 0);
                            gameCtx.lineTo(-p.size * 0.7, -p.size * 0.3);
                            gameCtx.closePath();
                            gameCtx.fill();
                            break;
                            
                        case 'ice':
                            // بلورة ثلجية
                            gameCtx.beginPath();
                            gameCtx.moveTo(0, -p.size);
                            for (let i = 1; i < 6; i++) {
                                const angle = (i * Math.PI) / 3;
                                gameCtx.lineTo(
                                    Math.sin(angle) * p.size * 0.5,
                                    -Math.cos(angle) * p.size * 0.5
                                );
                                gameCtx.lineTo(
                                    Math.sin(angle + Math.PI/6) * p.size,
                                    -Math.cos(angle + Math.PI/6) * p.size
                                );
                            }
                            gameCtx.closePath();
                            gameCtx.fill();
                            break;
                            
                        default:
                            // دائرة
                            gameCtx.beginPath();
                            gameCtx.arc(0, 0, p.size, 0, Math.PI * 2);
                            gameCtx.fill();
                    }
                    
                    gameCtx.restore();
                });
            }
        }

        // ============= نظام التحكم المتطور =============
        class InputSystem {
            constructor() {
                this.keys = {
                    up: false,
                    down: false,
                    left: false,
                    right: false,
                    jump: false,
                    attack: false,
                    dash: false,
                    block: false
                };
                
                this.touch = {
                    joystick: { x: 0, y: 0, active: false },
                    buttons: new Map()
                };
                
                this.sensitivity = 5;
                this.init();
            }
            
            init() {
                // إعداد الجويستيك
                const joystickBase = document.getElementById('joystickBase');
                const joystick = document.getElementById('joystick');
                
                let joystickRect = joystickBase.getBoundingClientRect();
                let joystickCenter = { x: joystickRect.width / 2, y: joystickRect.height / 2 };
                let maxDistance = joystickRect.width / 2 - joystick.offsetWidth / 2;
                
                function updateJoystick(clientX, clientY) {
                    const rect = joystickBase.getBoundingClientRect();
                    const x = clientX - rect.left - joystickCenter.x;
                    const y = clientY - rect.top - joystickCenter.y;
                    
                    const distance = Math.sqrt(x * x + y * y);
                    const limitedDistance = Math.min(distance, maxDistance);
                    
                    if (distance > 10) {
                        const angle = Math.atan2(y, x);
                        const moveX = Math.cos(angle) * (limitedDistance / maxDistance);
                        const moveY = Math.sin(angle) * (limitedDistance / maxDistance);
                        
                        joystick.style.transform = `translate(${moveX * maxDistance}px, ${moveY * maxDistance}px)`;
                        
                        this.touch.joystick.x = moveX;
                        this.touch.joystick.y = moveY;
                        this.touch.joystick.active = true;
                        
                        // تحديث مفاتيح الحركة
                        this.keys.left = moveX < -0.2;
                        this.keys.right = moveX > 0.2;
                        this.keys.up = moveY < -0.2;
                        this.keys.down = moveY > 0.2;
                    }
                }
                
                // أحداث اللمس
                joystickBase.addEventListener('touchstart', (e) => {
                    e.preventDefault();
                    const touch = e.touches[0];
                    updateJoystick.call(this, touch.clientX, touch.clientY);
                });
                
                joystickBase.addEventListener('touchmove', (e) => {
                    e.preventDefault();
                    const touch = e.touches[0];
                    updateJoystick.call(this, touch.clientX, touch.clientY);
                });
                
                joystickBase.addEventListener('touchend', () => {
                    joystick.style.transform = 'translate(0, 0)';
                    this.touch.joystick = { x: 0, y: 0, active: false };
                    this.keys.left = this.keys.right = this.keys.up = this.keys.down = false;
                });
                
                // أحداث الماوس (للتطوير)
                joystickBase.addEventListener('mousedown', (e) => {
                    updateJoystick.call(this, e.clientX, e.clientY);
                });
                
                document.addEventListener('mousemove', (e) => {
                    if (this.touch.joystick.active) {
                        updateJoystick.call(this, e.clientX, e.clientY);
                    }
                });
                
                document.addEventListener('mouseup', () => {
                    joystick.style.transform = 'translate(0, 0)';
                    this.touch.joystick = { x: 0, y: 0, active: false };
                    this.keys.left = this.keys.right = this.keys.up = this.keys.down = false;
                });
                
                // تحديث حجم الجويستيك عند تغيير حجم النافذة
                window.addEventListener('resize', () => {
                    joystickRect = joystickBase.getBoundingClientRect();
                    joystickCenter = { x: joystickRect.width / 2, y: joystickRect.height / 2 };
                    maxDistance = joystickRect.width / 2 - joystick.offsetWidth / 2;
                });
                
                // إعداد أزرار الإجراءات
                const actionButtons = ['jumpBtn', 'attackBtn', 'dashBtn', 'blockBtn'];
                actionButtons.forEach(btnId => {
                    const button = document.getElementById(btnId);
                    const action = btnId.replace('Btn', '');
                    
                    button.addEventListener('touchstart', (e) => {
                        e.preventDefault();
                        this.keys[action] = true;
                        this.touch.buttons.set(action, true);
                    });
                    
                    button.addEventListener('touchend', (e) => {
                        e.preventDefault();
                        this.keys[action] = false;
                        this.touch.buttons.delete(action);
                    });
                    
                    button.addEventListener('mousedown', () => {
                        this.keys[action] = true;
                    });
                    
                    button.addEventListener('mouseup', () => {
                        this.keys[action] = false;
                    });
                });
            }
            
            update() {
                // يمكن إضافة منطق إضافي هنا إذا لزم الأمر
            }
            
            getMovement() {
                if (this.touch.joystick.active) {
                    return {
                        x: this.touch.joystick.x * this.sensitivity,
                        y: this.touch.joystick.y * this.sensitivity
                    };
                }
                
                let x = 0, y = 0;
                if (this.keys.left) x -= 1;
                if (this.keys.right) x += 1;
                if (this.keys.up) y -= 1;
                if (this.keys.down) y += 1;
                
                // تطبيع الحركة القطرية
                if (x !== 0 && y !== 0) {
                    x *= 0.7071;
                    y *= 0.7071;
                }
                
                return { x, y };
            }
        }

        // ============= اللاعب الأسطوري =============
        class LegendaryPlayer {
            constructor(x, y) {
                // الموضع والأبعاد
                this.x = x || GAME_CONFIG.WORLD_WIDTH / 2;
                this.y = y || GAME_CONFIG.GROUND_LEVEL - GAME_CONFIG.PLAYER_HEIGHT;
                this.width = GAME_CONFIG.PLAYER_WIDTH;
                this.height = GAME_CONFIG.PLAYER_HEIGHT;
                
                // الحركة والفيزياء
                this.vx = 0;
                this.vy = 0;
                this.speed = GAME_CONFIG.PLAYER_SPEED;
                this.jumpForce = GAME_CONFIG.PLAYER_JUMP_FORCE;
                this.dashForce = GAME_CONFIG.PLAYER_DASH_FORCE;
                this.gravity = GAME_CONFIG.PLAYER_GRAVITY;
                this.isGrounded = false;
                this.isJumping = false;
                this.isDashing = false;
                this.dashCooldown = 0;
                this.dashDuration = 0;
                
                // التوجه والاتجاه
                this.facing = 'right';
                this.lastDirection = 'right';
                this.lookAngle = 0;
                
                // الإحصائيات
                this.health = 100;
                this.maxHealth = 100;
                this.stamina = 100;
                this.maxStamina = 100;
                this.level = 1;
                this.exp = 0;
                this.expToNextLevel = 100;
                this.gold = 1000;
                this.score = 0;
                this.kills = 0;
                this.combo = 0;
                this.comboTimer = 0;
                this.maxCombo = 0;
                
                // الأسلحة والمعدات
                this.currentWeapon = 'sword';
                this.weapons = {
                    sword: {
                        name: 'سيف الفرسان',
                        damage: 45,
                        range: 90,
                        cooldown: 25,
                        currentCooldown: 0,
                        ammo: Infinity,
                        color: '#ffd700',
                        type: 'melee'
                    },
                    pistol: {
                        name: 'مسدس القناص',
                        damage: 35,
                        range: 500,
                        cooldown: 15,
                        currentCooldown: 0,
                        ammo: 30,
                        maxAmmo: 30,
                        color: '#ffff00',
                        type: 'ranged'
                    },
                    shotgun: {
                        name: 'بندقية الدمار',
                        damage: 80,
                        range: 250,
                        cooldown: 60,
                        currentCooldown: 0,
                        ammo: 12,
                        maxAmmo: 12,
                        color: '#ff4500',
                        type: 'ranged'
                    },
                    rifle: {
                        name: 'بندقية الصياد',
                        damage: 100,
                        range: 800,
                        cooldown: 90,
                        currentCooldown: 0,
                        ammo: 10,
                        maxAmmo: 10,
                        color: '#00ffff',
                        type: 'ranged'
                    },
                    rocket: {
                        name: 'قاذفة الصواريخ',
                        damage: 200,
                        range: 600,
                        cooldown: 120,
                        currentCooldown: 0,
                        ammo: 5,
                        maxAmmo: 5,
                        color: '#ff8c00',
                        type: 'ranged'
                    }
                };
                
                // الحالة الخاصة
                this.isBlocking = false;
                this.isAttacking = false;
                this.invincible = 0;
                this.parryWindow = 0;
                this.attackAnimation = 0;
                this.hitAnimation = 0;
                this.healAnimation = 0;
                
                // الرسوم المتحركة
                this.animationTimer = 0;
                this.walkCycle = 0;
                this.legSwing = 0;
                this.armSwing = 0;
                this.bodyBob = 0;
                this.jumpArc = 0;
                this.dashTrail = [];
                
                // المهارات والتحسينات
                this.skills = {
                    doubleJump: false,
                    tripleShot: false,
                    explosiveAmmo: false,
                    lifeSteal: false,
                    criticalChance: 0.1
                };
                
                // المؤثرات البصرية
                this.glowColor = '#ff0000';
                this.glowIntensity = 0;
                this.trailEffect = false;
            }
            
            update(input) {
                if (gamePaused) return;
                
                // تحديث التوجه أولاً
                this.updateFacing(input);
                
                // تطبيق الحركة
                this.applyMovement(input);
                
                // تطبيق الفيزياء
                this.applyPhysics();
                
                // تحديث الموضع
                this.x += this.vx;
                this.y += this.vy;
                
                // حدود العالم
                this.enforceBounds();
                
                // تحديث التوقيتات والتحسينات
                this.updateTimers();
                
                // تحديث الرسوم المتحركة
                this.updateAnimations();
                
                // تحديث المؤثرات
                this.updateEffects();
                
                // تحديث الواجهة
                this.updateUI();
            }
            
            updateFacing(input) {
                const movement = input.getMovement();
                
                // تحديث التوجه بناءً على الحركة
                if (movement.x > 0.1) {
                    this.facing = 'right';
                    this.lastDirection = 'right';
                } else if (movement.x < -0.1) {
                    this.facing = 'left';
                    this.lastDirection = 'left';
                }
                
                // تحديث مؤشر التوجه
                this.updateFacingIndicator();
            }
            
            updateFacingIndicator() {
                const indicator = document.getElementById('facingIndicator');
                const screenPos = camera.worldToScreen(this.x, this.y - 100);
                
                indicator.style.display = 'block';
                indicator.style.left = `${screenPos.x - 60}px`;
                indicator.style.top = `${screenPos.y - 60}px`;
                
                // تدوير السهم حسب التوجه
                let rotation = 0;
                switch(this.facing) {
                    case 'right': rotation = 0; break;
                    case 'up': rotation = -90; break;
                    case 'left': rotation = 180; break;
                    case 'down': rotation = 90; break;
                }
                
                indicator.querySelector('.facing-arrow').style.transform = `rotate(${rotation}deg)`;
            }
            
            applyMovement(input) {
                const movement = input.getMovement();
                
                // الحركة الأساسية
                this.vx = movement.x * this.speed;
                this.vy = movement.y * this.speed;
                
                // القفز
                if (input.keys.jump && this.isGrounded && this.stamina >= 20) {
                    this.vy = -this.jumpForce;
                    this.isGrounded = false;
                    this.isJumping = true;
                    this.stamina -= 20;
                    this.jumpArc = 15;
                    
                    // تأثيرات القفز
                    particleSystem.createEffect('spark', this.x, this.y + this.height, {
                        count: 20,
                        color: '#ffff00',
                        size: 6,
                        speed: 12
                    });
                    
                    audioSystem.play('jump');
                }
                
                // الدفعة السريعة
                if (input.keys.dash && this.dashCooldown <= 0 && this.stamina >= 30) {
                    this.isDashing = true;
                    this.dashDuration = 15;
                    this.dashCooldown = 90;
                    this.stamina -= 30;
                    
                    // تطبيق الدفعة في اتجاه الحركة أو التوجه
                    const dashX = this.facing === 'right' ? 1 : this.facing === 'left' ? -1 : 0;
                    const dashY = this.facing === 'up' ? -1 : this.facing === 'down' ? 1 : 0;
                    
                    this.vx += dashX * this.dashForce * 3;
                    this.vy += dashY * this.dashForce * 2;
                    
                    // تأثيرات الدفعة
                    particleSystem.addEmitter(this.x, this.y, 'spark', {
                        color: '#00ffff',
                        size: 8,
                        speed: 20,
                        duration: 30,
                        rate: 2
                    });
                    
                    camera.shake(8, 25);
                    audioSystem.play('dash');
                }
                
                // الهجوم
                if (input.keys.attack) {
                    this.attack();
                }
                
                // الدفاع
                this.isBlocking = input.keys.block && this.stamina >= 5;
                if (this.isBlocking) {
                    this.stamina -= 0.2;
                }
            }
            
            applyPhysics() {
                // الجاذبية
                if (!this.isGrounded) {
                    this.vy += this.gravity;
                }
                
                // الاحتكاك
                if (this.isGrounded) {
                    this.vx *= 0.85;
                } else {
                    this.vx *= 0.95;
                }
                
                // الكبح الهوائي
                this.vy *= 0.98;
                
                // التحقق من الأرض
                if (this.y >= GAME_CONFIG.GROUND_LEVEL - this.height) {
                    this.y = GAME_CONFIG.GROUND_LEVEL - this.height;
                    this.vy = 0;
                    this.isGrounded = true;
                    this.isJumping = false;
                } else {
                    this.isGrounded = false;
                }
            }
            
            enforceBounds() {
                // الحدود الأفقية
                if (this.x < this.width / 2) {
                    this.x = this.width / 2;
                    this.vx = Math.max(0, this.vx);
                }
                if (this.x > GAME_CONFIG.WORLD_WIDTH - this.width / 2) {
                    this.x = GAME_CONFIG.WORLD_WIDTH - this.width / 2;
                    this.vx = Math.min(0, this.vx);
                }
                
                // الحدود العمودية
                if (this.y < 0) {
                    this.y = 0;
                    this.vy = Math.max(0, this.vy);
                }
            }
            
            updateTimers() {
                // تحديث توقيتات الأسلحة
                Object.values(this.weapons).forEach(weapon => {
                    if (weapon.currentCooldown > 0) {
                        weapon.currentCooldown--;
                    }
                });
                
                // تحديث التوقيتات الخاصة
                if (this.dashCooldown > 0) this.dashCooldown--;
                if (this.dashDuration > 0) this.dashDuration--;
                if (this.invincible > 0) this.invincible--;
                if (this.parryWindow > 0) this.parryWindow--;
                if (this.attackAnimation > 0) this.attackAnimation--;
                if (this.hitAnimation > 0) this.hitAnimation--;
                if (this.healAnimation > 0) this.healAnimation--;
                if (this.comboTimer > 0) this.comboTimer--;
                
                // إنهاء الدفعة
                if (this.dashDuration <= 0) {
                    this.isDashing = false;
                }
                
                // إنهاء الكومبو
                if (this.comboTimer <= 0 && this.combo > 0) {
                    if (this.combo > this.maxCombo) {
                        this.maxCombo = this.combo;
                    }
                    this.combo = 0;
                }
                
                // استعادة الطاقة
                if (!this.isJumping && !this.isDashing && !this.isBlocking) {
                    this.stamina = Math.min(this.maxStamina, this.stamina + 1.5);
                }
                
                // استعادة الصحة عند الطاقة الكاملة
                if (this.stamina >= this.maxStamina * 0.8) {
                    this.health = Math.min(this.maxHealth, this.health + 0.1);
                }
            }
            
            updateAnimations() {
                this.animationTimer++;
                
                // حركة المشي
                if (Math.abs(this.vx) > 1 || Math.abs(this.vy) > 1) {
                    this.walkCycle += 0.2;
                    this.bodyBob = Math.sin(this.walkCycle) * 4;
                    this.legSwing = Math.sin(this.walkCycle * 2) * 0.4;
                } else {
                    this.bodyBob *= 0.9;
                    this.legSwing *= 0.9;
                }
                
                // حركة الذراع للهجوم
                if (this.attackAnimation > 0) {
                    this.armSwing = Math.sin(this.attackAnimation * 0.2) * 0.8;
                } else {
                    this.armSwing *= 0.9;
                }
                
                // حركة القفز
                if (this.jumpArc > 0) {
                    this.bodyBob -= this.jumpArc;
                    this.jumpArc--;
                }
                
                // تأثير الضرر
                if (this.hitAnimation > 0) {
                    this.glowIntensity = Math.sin(this.hitAnimation * 0.3) * 0.5 + 0.5;
                    this.glowColor = '#ff0000';
                }
                
                // تأثير الشفاء
                if (this.healAnimation > 0) {
                    this.glowIntensity = Math.sin(this.healAnimation * 0.2) * 0.3 + 0.7;
                    this.glowColor = '#00ff00';
                }
            }
            
            updateEffects() {
                // أثر الدفعة
                if (this.isDashing) {
                    this.dashTrail.push({
                        x: this.x,
                        y: this.y + this.height / 2,
                        alpha: 1,
                        size: this.width * 0.8
                    });
                    
                    if (this.dashTrail.length > 10) {
                        this.dashTrail.shift();
                    }
                } else {
                    this.dashTrail = [];
                }
                
                // تحديث الشفافية للأثر
                this.dashTrail.forEach((trail, index) => {
                    trail.alpha = index / this.dashTrail.length;
                });
            }
            
            attack() {
                const weapon = this.weapons[this.currentWeapon];
                
                if (!weapon || weapon.currentCooldown > 0 || weapon.ammo <= 0) {
                    return;
                }
                
                weapon.currentCooldown = weapon.cooldown;
                this.isAttacking = true;
                this.attackAnimation = 20;
                
                // استخدام الذخيرة
                if (weapon.ammo !== Infinity) {
                    weapon.ammo--;
                }
                
                // الكومبو
                if (this.comboTimer > 0) {
                    this.combo++;
                    this.comboTimer = 120;
                    
                    // عرض الكومبو
                    if (this.combo % 5 === 0) {
                        this.showCombo();
                    }
                } else {
                    this.combo = 1;
                    this.comboTimer = 120;
                }
                
                // تشغيل الصوت
                const soundPitch = 1 + (this.combo * 0.05);
                audioSystem.play(`${this.currentWeapon}_shot`, 1, soundPitch);
                
                // اهتزاز الكاميرا
                camera.shake(weapon.damage * 0.1, 20);
                
                // تأثيرات بصرية
                this.createAttackEffects(weapon);
                
                // تنفيذ الهجوم
                if (weapon.type === 'melee') {
                    this.executeMeleeAttack(weapon);
                } else {
                    this.executeRangedAttack(weapon);
                }
            }
            
            createAttackEffects(weapon) {
                const attackX = this.x + (this.facing === 'right' ? this.width : 0);
                const attackY = this.y + this.height / 2;
                
                particleSystem.createEffect('spark', attackX, attackY, {
                    count: weapon.type === 'melee' ? 15 : 25,
                    color: weapon.color,
                    size: weapon.type === 'melee' ? 6 : 8,
                    speed: weapon.type === 'melee' ? 15 : 25,
                    life: weapon.type === 'melee' ? 0.8 : 1.2
                });
                
                // تأثير خاص للصاروخ
                if (this.currentWeapon === 'rocket') {
                    particleSystem.addEmitter(attackX, attackY, 'fire', {
                        color: '#ff8c00',
                        size: 12,
                        speed: 5,
                        duration: 45,
                        rate: 3,
                        gravity: -0.1
                    });
                }
            }
            
            executeMeleeAttack(weapon) {
                const attackRange = weapon.range;
                const attackAngle = this.facing === 'right' ? 0 : Math.PI;
                
                enemies.forEach(enemy => {
                    const dx = enemy.x - this.x;
                    const dy = enemy.y - this.y;
                    const distance = Math.sqrt(dx * dx + dy * dy);
                    
                    if (distance < attackRange) {
                        const angleToEnemy = Math.atan2(dy, dx);
                        const angleDiff = Math.abs(angleToEnemy - attackAngle);
                        
                        if (angleDiff < 0.8) { // 45 درجة
                            this.hitEnemy(enemy, weapon);
                        }
                    }
                });
            }
            
            executeRangedAttack(weapon) {
                const bulletCount = this.currentWeapon === 'shotgun' ? 8 : 1;
                const spread = this.currentWeapon === 'shotgun' ? 0.3 : 
                              this.currentWeapon === 'pistol' ? 0.1 : 0.05;
                
                for (let i = 0; i < bulletCount; i++) {
                    const bullet = {
                        x: this.x + (this.facing === 'right' ? this.width : 0),
                        y: this.y + this.height / 2,
                        vx: (this.facing === 'right' ? 1 : -1) * 25 + (Math.random() - 0.5) * spread * 10,
                        vy: (Math.random() - 0.5) * spread * 10,
                        damage: weapon.damage / (this.currentWeapon === 'shotgun' ? 2.5 : 1),
                        color: weapon.color,
                        type: this.currentWeapon,
                        life: 120,
                        size: this.currentWeapon === 'rocket' ? 12 : 6,
                        owner: this
                    };
                    
                    bullets.push(bullet);
                }
            }
            
            hitEnemy(enemy, weapon) {
                // حساب الضرر
                let damage = weapon.damage;
                
                // مضاعفات الكومبو
                if (this.combo > 5) {
                    damage *= 1 + (this.combo - 5) * 0.1;
                }
                
                // فرصة الضربة القاضية
                if (Math.random() < this.skills.criticalChance) {
                    damage *= 2;
                    this.showCriticalHit();
                }
                
                // سرقة الحياة
                if (this.skills.lifeSteal) {
                    this.heal(damage * 0.1);
                }
                
                // تطبيق الضرر
                const killed = enemy.takeDamage(damage, this);
                
                if (killed) {
                    this.onEnemyKilled(enemy);
                }
            }
            
            onEnemyKilled(enemy) {
                this.kills++;
                this.score += enemy.scoreValue;
                this.addExp(enemy.expValue);
                this.gold += enemy.goldValue;
                
                waveSystem.onEnemyKilled();
                
                // تأثيرات القتل
                particleSystem.createEffect('blood', enemy.x, enemy.y, {
                    count: 35,
                    color: '#ff0000',
                    size: enemy.type === 'boss' ? 15 : 8,
                    speed: 20,
                    life: 2
                });
                
                audioSystem.play('enemy_death');
                
                // فرصة إسقاط قوة
                if (Math.random() < 0.2) {
                    this.createPowerUp(enemy.x, enemy.y);
                }
            }
            
            createPowerUp(x, y) {
                const powerUpTypes = ['health', 'stamina', 'ammo', 'coin', 'speed'];
                const type = powerUpTypes[Math.floor(Math.random() * powerUpTypes.length)];
                
                powerUps.push({
                    x, y,
                    type,
                    size: 20,
                    color: this.getPowerUpColor(type),
                    life: 600,
                    value: this.getPowerUpValue(type),
                    collected: false
                });
            }
            
            getPowerUpColor(type) {
                switch(type) {
                    case 'health': return '#ff0000';
                    case 'stamina': return '#00ff00';
                    case 'ammo': return '#ffff00';
                    case 'coin': return '#ffd700';
                    case 'speed': return '#00ffff';
                    default: return '#ffffff';
                }
            }
            
            getPowerUpValue(type) {
                switch(type) {
                    case 'health': return 25;
                    case 'stamina': return 50;
                    case 'ammo': return 10;
                    case 'coin': return 100;
                    case 'speed': return 300;
                    default: return 0;
                }
            }
            
            takeDamage(amount, source) {
                if (this.invincible > 0 || this.isBlocking) {
                    // محاولة صد الهجوم
                    if (this.parryWindow > 0) {
                        this.parryAttack(source);
                        return false;
                    }
                    
                    // تقليل الضرر عند الدفاع
                    if (this.isBlocking) {
                        amount *= 0.3;
                        particleSystem.createEffect('spark', this.x, this.y, {
                            count: 10,
                            color: '#4169e1',
                            size: 6,
                            speed: 8
                        });
                    } else {
                        return false;
                    }
                }
                
                this.health -= amount;
                this.invincible = 45;
                this.hitAnimation = 30;
                
                // كسر الكومبو
                if (this.combo > 0) {
                    this.combo = 0;
                    this.comboTimer = 0;
                }
                
                // تأثيرات الضرر
                this.showHitEffect();
                camera.shake(amount * 0.2, 30);
                this.showDamageText(`-${Math.round(amount)}`, '#ff0000');
                
                particleSystem.createEffect('blood', this.x, this.y + this.height / 2, {
                    count: 25,
                    color: '#ff0000',
                    size: 8,
                    speed: 15,
                    life: 1.5
                });
                
                audioSystem.play('player_hit', 1, 0.8 + Math.random() * 0.4);
                
                if (this.health <= 0) {
                    this.die();
                    return true;
                }
                
                return false;
            }
            
            parryAttack(source) {
                // تأثيرات الصد
                particleSystem.createEffect('lightning', this.x, this.y, {
                    count: 30,
                    color: '#ffffff',
                    size: 10,
                    speed: 25,
                    life: 1
                });
                
                camera.shake(15, 40);
                audioSystem.play('parry');
                
                // صدمة العدو
                if (source) {
                    source.stun(60);
                }
                
                // استعادة الطاقة
                this.stamina = Math.min(this.maxStamina, this.stamina + 50);
                
                this.showDamageText('صد!', '#00ffff');
            }
            
            heal(amount) {
                const oldHealth = this.health;
                this.health = Math.min(this.maxHealth, this.health + amount);
                const healed = this.health - oldHealth;
                
                if (healed > 0) {
                    this.healAnimation = 45;
                    
                    particleSystem.createEffect('magic', this.x, this.y + this.height / 2, {
                        count: 30,
                        color: '#00ff00',
                        size: 8,
                        speed: 10,
                        life: 1.8
                    });
                    
                    this.showDamageText(`+${Math.round(healed)}`, '#00ff00');
                    this.showHealEffect();
                    audioSystem.play('player_heal');
                }
            }
            
            addExp(amount) {
                this.exp += amount;
                
                if (this.exp >= this.expToNextLevel) {
                    this.levelUp();
                }
            }
            
            levelUp() {
                this.level++;
                this.exp -= this.expToNextLevel;
                this.expToNextLevel = Math.floor(this.expToNextLevel * 1.5);
                
                // تحسينات المستوى
                this.maxHealth += 25;
                this.health = this.maxHealth;
                this.maxStamina += 20;
                this.stamina = this.maxStamina;
                this.speed += 0.5;
                
                // مكافأة الارتقاء
                this.gold += 500;
                this.heal(50);
                
                // فتح مهارات جديدة
                if (this.level >= 5 && !this.skills.doubleJump) {
                    this.skills.doubleJump = true;
                }
                if (this.level >= 10 && !this.skills.tripleShot) {
                    this.skills.tripleShot = true;
                }
                if (this.level >= 15 && !this.skills.explosiveAmmo) {
                    this.skills.explosiveAmmo = true;
                }
                if (this.level >= 20 && !this.skills.lifeSteal) {
                    this.skills.lifeSteal = true;
                }
                
                // تأثيرات الارتقاء
                this.showLevelUpEffect();
                camera.shake(10, 60);
                audioSystem.play('level_up');
                
                this.showMessage(`🎉 ارتقيت للمستوى ${this.level}!`, '#00ff00');
            }
            
            die() {
                gameRunning = false;
                
                particleSystem.createEffect('blood', this.x, this.y, {
                    count: 100,
                    color: '#ff0000',
                    size: 12,
                    speed: 30,
                    life: 3
                });
                
                camera.shake(20, 120);
                audioSystem.play('player_hit', 2, 0.5);
                
                setTimeout(() => {
                    this.showGameOver();
                }, 2000);
            }
            
            updateUI() {
                // تحديث القيم
                document.getElementById('healthValue').textContent = Math.round(this.health);
                document.getElementById('staminaValue').textContent = Math.round(this.stamina);
                document.getElementById('goldValue').textContent = this.gold;
                document.getElementById('levelValue').textContent = this.level;
                document.getElementById('scoreValue').textContent = this.score;
                
                // تحديث الأشرطة
                const healthPercent = (this.health / this.maxHealth) * 100;
                const staminaPercent = (this.stamina / this.maxStamina) * 100;
                
                document.getElementById('healthFill').style.width = `${healthPercent}%`;
                document.getElementById('staminaFill').style.width = `${staminaPercent}%`;
                
                // تحذير الصحة المنخفضة
                if (healthPercent < 30) {
                    document.getElementById('healthValue').style.animation = 'pulse 0.5s infinite';
                } else {
                    document.getElementById('healthValue').style.animation = 'none';
                }
                
                // تحديث الذخيرة
                document.getElementById('pistolAmmo').textContent = this.weapons.pistol.ammo;
                document.getElementById('shotgunAmmo').textContent = this.weapons.shotgun.ammo;
                document.getElementById('rifleAmmo').textContent = this.weapons.rifle.ammo;
                document.getElementById('rocketAmmo').textContent = this.weapons.rocket.ammo;
            }
            
            showHitEffect() {
                const effect = document.getElementById('hitFlash');
                const screenPos = camera.worldToScreen(this.x, this.y);
                
                effect.style.setProperty('--hit-x', `${screenPos.x}px`);
                effect.style.setProperty('--hit-y', `${screenPos.y}px`);
                effect.style.opacity = '1';
                
                setTimeout(() => {
                    effect.style.opacity = '0';
                }, 300);
            }
            
            showHealEffect() {
                const effect = document.getElementById('healFlash');
                const screenPos = camera.worldToScreen(this.x, this.y);
                
                effect.style.setProperty('--heal-x', `${screenPos.x}px`);
                effect.style.setProperty('--heal-y', `${screenPos.y}px`);
                effect.style.opacity = '1';
                
                setTimeout(() => {
                    effect.style.opacity = '0';
                }, 300);
            }
            
            showDamageText(text, color) {
                const screenPos = camera.worldToScreen(this.x, this.y - 50);
                
                const damagePopup = document.createElement('div');
                damagePopup.className = 'damage-popup';
                damagePopup.textContent = text;
                damagePopup.style.color = color;
                damagePopup.style.left = `${screenPos.x}px`;
                damagePopup.style.top = `${screenPos.y}px`;
                
                document.querySelector('.ui-layer').appendChild(damagePopup);
                
                setTimeout(() => {
                    damagePopup.remove();
                }, 1500);
            }
            
            showMessage(text, color) {
                const popup = document.getElementById('popupMessage');
                const popupText = document.getElementById('popupText');
                const popupSubtext = document.getElementById('popupSubtext');
                
                popupText.textContent = text;
                popupText.style.color = color;
                popupSubtext.textContent = '';
                
                popup.style.display = 'block';
                
                setTimeout(() => {
                    popup.style.display = 'none';
                }, 2000);
            }
            
            showCombo() {
                const comboDisplay = document.getElementById('comboDisplay');
                comboDisplay.textContent = `COMBO x${this.combo}!`;
                comboDisplay.style.display = 'block';
                
                setTimeout(() => {
                    comboDisplay.style.display = 'none';
                }, 1000);
            }
            
            showCriticalHit() {
                this.showDamageText('CRITICAL!', '#ffff00');
                
                particleSystem.createEffect('lightning', this.x, this.y, {
                    count: 20,
                    color: '#ffff00',
                    size: 15,
                    speed: 20,
                    life: 1
                });
                
                audioSystem.play('critical_hit');
            }
            
            showLevelUpEffect() {
                const effect = document.getElementById('levelUpEffect');
                effect.style.display = 'block';
                
                setTimeout(() => {
                    effect.style.display = 'none';
                }, 1000);
            }
            
            showGameOver() {
                this.showMessage('💀 لقد هزمت! 💀', '#ff0000');
                
                setTimeout(() => {
                    const restart = confirm(
                        `🎮 انتهت اللعبة!\n\n` +
                        `🎯 النقاط: ${this.score}\n` +
                        `👹 الوحوش: ${this.kills}\n` +
                        `💰 الذهب: ${this.gold}\n` +
                        `⭐ المستوى: ${this.level}\n` +
                        `🔥 أقصى كومبو: ${this.maxCombo}\n\n` +
                        `هل تريد إعادة المحاولة؟`
                    );
                    
                    if (restart) {
                        location.reload();
                    }
                }, 1500);
            }
            
            draw() {
                gameCtx.save();
                
                // تطبيق تأثيرات اللاعب
                if (this.invincible > 0 && Math.floor(this.invincible / 5) % 2 === 0) {
                    gameCtx.globalAlpha = 0.6;
                }
                
                // تطبيق تأثير التوهج
                if (this.glowIntensity > 0) {
                    gameCtx.shadowBlur = 30 * this.glowIntensity;
                    gameCtx.shadowColor = this.glowColor;
                }
                
                // رسم أثر الدفعة
                this.drawDashTrail();
                
                // تطبيق حركة التأرجح
                gameCtx.translate(0, this.bodyBob);
                
                // تدوير اللاعب حسب التوجه
                if (this.facing === 'left') {
                    gameCtx.scale(-1, 1);
                    gameCtx.translate(-this.x * 2 - this.width, 0);
                }
                
                // رسم اللاعب
                this.drawBody();
                this.drawWeapon();
                
                gameCtx.restore();
                
                // رسم معلومات إضافية
                if (this.combo > 1) {
                    this.drawCombo();
                }
            }
            
            drawDashTrail() {
                this.dashTrail.forEach((trail, index) => {
                    gameCtx.save();
                    gameCtx.globalAlpha = trail.alpha * 0.5;
                    
                    const gradient = gameCtx.createRadialGradient(
                        trail.x, trail.y, 0,
                        trail.x, trail.y, trail.size
                    );
                    gradient.addColorStop(0, '#00ffff');
                    gradient.addColorStop(1, 'transparent');
                    
                    gameCtx.fillStyle = gradient;
                    gameCtx.beginPath();
                    gameCtx.arc(trail.x, trail.y, trail.size, 0, Math.PI * 2);
                    gameCtx.fill();
                    
                    gameCtx.restore();
                });
            }
            
            drawBody() {
                const x = this.x;
                const y = this.y;
                
                // الجسم
                const bodyGradient = gameCtx.createLinearGradient(
                    x, y,
                    x, y + this.height
                );
                bodyGradient.addColorStop(0, '#dc143c');
                bodyGradient.addColorStop(0.2, '#b22222');
                bodyGradient.addColorStop(0.5, '#8b0000');
                bodyGradient.addColorStop(1, '#660000');
                
                gameCtx.fillStyle = bodyGradient;
                gameCtx.beginPath();
                gameCtx.roundRect(x, y, this.width, this.height, 20);
                gameCtx.fill();
                
                // تفاصيل الدروع
                gameCtx.strokeStyle = '#ffd700';
                gameCtx.lineWidth = 4;
                gameCtx.beginPath();
                gameCtx.roundRect(x + 10, y + 20, this.width - 20, this.height - 40, 12);
                gameCtx.stroke();
                
                // الرأس
                const headRadius = 25;
                gameCtx.fillStyle = '#ffb6c1';
                gameCtx.beginPath();
                gameCtx.arc(x + this.width / 2, y - headRadius / 2, headRadius, 0, Math.PI * 2);
                gameCtx.fill();
                
                // الخوذة
                gameCtx.strokeStyle = '#ffd700';
                gameCtx.lineWidth = 5;
                gameCtx.beginPath();
                gameCtx.arc(x + this.width / 2, y - headRadius / 2, headRadius + 6, 0, Math.PI * 2);
                gameCtx.stroke();
                
                // العيون
                gameCtx.fillStyle = '#000';
                gameCtx.beginPath();
                gameCtx.arc(x + this.width / 2 - 10, y - 8, 5, 0, Math.PI * 2);
                gameCtx.arc(x + this.width / 2 + 10, y - 8, 5, 0, Math.PI * 2);
                gameCtx.fill();
                
                // الفم
                gameCtx.strokeStyle = '#000';
                gameCtx.lineWidth = 4;
                gameCtx.beginPath();
                gameCtx.arc(x + this.width / 2, y + headRadius / 2, 12, 0.2, Math.PI - 0.2);
                gameCtx.stroke();
                
                // الشعر (إذا كان مرئياً)
                gameCtx.fillStyle = '#8b4513';
                gameCtx.beginPath();
                gameCtx.ellipse(x + this.width / 2, y - headRadius - 5, 15, 8, 0, 0, Math.PI * 2);
                gameCtx.fill();
            }
            
            drawWeapon() {
                const weapon = this.weapons[this.currentWeapon];
                if (!weapon) return;
                
                const attackOffset = this.attackAnimation * 3;
                const armRotation = this.armSwing * (this.facing === 'right' ? 1 : -1);
                
                gameCtx.save();
                gameCtx.translate(
                    this.x + (this.facing === 'right' ? this.width + attackOffset : -attackOffset),
                    this.y + this.height * 0.4
                );
                gameCtx.rotate(armRotation);
                
                if (this.facing === 'left') {
                    gameCtx.scale(-1, 1);
                }
                
                if (this.currentWeapon === 'sword') {
                    this.drawSword(weapon);
                } else {
                    this.drawGun(weapon);
                }
                
                gameCtx.restore();
            }
            
            drawSword(weapon) {
                // نصل السيف
                const bladeGradient = gameCtx.createLinearGradient(0, -20, 70, 20);
                bladeGradient.addColorStop(0, '#ffffff');
                bladeGradient.addColorStop(0.2, '#ffd700');
                bladeGradient.addColorStop(0.5, '#c0c0c0');
                bladeGradient.addColorStop(1, '#808080');
                
                gameCtx.fillStyle = bladeGradient;
                gameCtx.beginPath();
                gameCtx.moveTo(0, -15);
                gameCtx.lineTo(65, -10);
                gameCtx.lineTo(60, 10);
                gameCtx.lineTo(0, 15);
                gameCtx.closePath();
                gameCtx.fill();
                
                // قبضة السيف
                gameCtx.fillStyle = '#8b4513';
                gameCtx.fillRect(-18, -12, 18, 24);
                
                // تفاصيل القبضة
                gameCtx.fillStyle = '#654321';
                for (let i = 0; i < 3; i++) {
                    gameCtx.fillRect(-15, -10 + i * 10, 12, 5);
                }
                
                // تأثير التوهج للكومبو العالي
                if (this.combo > 5) {
                    gameCtx.shadowBlur = 25;
                    gameCtx.shadowColor = weapon.color;
                    gameCtx.stroke();
                    gameCtx.shadowBlur = 0;
                }
            }
            
            drawGun(weapon) {
                const length = this.currentWeapon === 'pistol' ? 40 :
                              this.currentWeapon === 'shotgun' ? 50 :
                              this.currentWeapon === 'rifle' ? 60 : 55;
                
                const height = this.currentWeapon === 'pistol' ? 15 :
                              this.currentWeapon === 'shotgun' ? 22 :
                              this.currentWeapon === 'rifle' ? 18 : 30;
                
                // جسم السلاح
                const gunGradient = gameCtx.createLinearGradient(0, -height / 2, length, height / 2);
                gunGradient.addColorStop(0, '#2f4f4f');
                gunGradient.addColorStop(0.4, '#696969');
                gunGradient.addColorStop(0.8, '#a9a9a9');
                gunGradient.addColorStop(1, '#d3d3d3');
                
                gameCtx.fillStyle = gunGradient;
                gameCtx.fillRect(0, -height / 2, length, height);
                
                // تفاصيل السلاح
                gameCtx.fillStyle = '#000';
                gameCtx.fillRect(length - 12, -height / 2, 12, height);
                
                // فتحة السلاح
                gameCtx.fillStyle = '#000';
                gameCtx.beginPath();
                gameCtx.arc(length, 0, height / 3, 0, Math.PI * 2);
                gameCtx.fill();
                
                // ماسورة السلاح
                gameCtx.fillStyle = '#444';
                gameCtx.fillRect(length, -height / 4, 10, height / 2);
            }
            
            drawCombo() {
                const screenPos = camera.worldToScreen(this.x, this.y - 100);
                
                gameCtx.save();
                gameCtx.font = `bold ${30 + this.combo}px Arial`;
                gameCtx.fillStyle = this.combo > 10 ? '#ff0000' :
                                   this.combo > 5 ? '#ffd700' : '#00ff00';
                gameCtx.textAlign = 'center';
                gameCtx.textBaseline = 'middle';
                
                // تأثير النص
                gameCtx.shadowBlur = 15;
                gameCtx.shadowColor = gameCtx.fillStyle;
                
                gameCtx.fillText(`COMBO x${this.combo}`, screenPos.x, screenPos.y);
                gameCtx.restore();
            }
        }

        // ============= نظام الموجات المتطور =============
        class WaveSystem {
            constructor() {
                this.currentWave = 1;
                this.enemiesSpawned = 0;
                this.enemiesKilled = 0;
                this.totalEnemies = GAME_CONFIG.WAVE_ENEMIES;
                this.spawnTimer = 0;
                this.spawnRate = GAME_CONFIG.ENEMY_SPAWN_RATE;
                this.bossSpawned = false;
                this.bossActive = false;
                this.waveTimer = 0;
                this.isWaveActive = true;
                
                this.enemyTypes = [
                    { type: 'goblin', weight: 40, minWave: 1 },
                    { type: 'orc', weight: 30, minWave: 2 },
                    { type: 'skeleton', weight: 25, minWave: 3 },
                    { type: 'giant', weight: 5, minWave: 4 }
                ];
            }
            
            update() {
                if (!this.isWaveActive || gamePaused) return;
                
                this.spawnTimer++;
                this.waveTimer++;
                
                // تجديد الوحوش
                if (this.spawnTimer >= this.spawnRate && 
                    this.enemiesSpawned < this.totalEnemies &&
                    enemies.length < GAME_CONFIG.MAX_ENEMIES) {
                    this.spawnEnemy();
                    this.spawnTimer = 0;
                }
                
                // تحقق إذا تم قتل 20 وحش لاستدعاء البوس
                if (this.enemiesKilled >= 20 && !this.bossSpawned && !this.bossActive) {
                    this.spawnBoss();
                }
                
                // تحديث الواجهة
                this.updateUI();
            }
            
            spawnEnemy() {
                // اختيار نوع الوحش بناءً على الموجة والأوزان
                let availableTypes = this.enemyTypes.filter(type => this.currentWave >= type.minWave);
                let totalWeight = availableTypes.reduce((sum, type) => sum + type.weight, 0);
                let randomWeight = Math.random() * totalWeight;
                
                let selectedType = 'goblin';
                let weightSum = 0;
                
                for (let type of availableTypes) {
                    weightSum += type.weight;
                    if (randomWeight <= weightSum) {
                        selectedType = type.type;
                        break;
                    }
                }
                
                // إحداثيات عشوائية حول اللاعب
                const angle = Math.random() * Math.PI * 2;
                const distance = 500 + Math.random() * 300;
                let x = player.x + Math.cos(angle) * distance;
                let y = player.y + Math.sin(angle) * distance;
                
                // التأكد من أن الوحش داخل حدود العالم
                x = Math.max(100, Math.min(GAME_CONFIG.WORLD_WIDTH - 100, x));
                y = Math.max(100, Math.min(GAME_CONFIG.GROUND_LEVEL - 100, y));
                
                // إنشاء الوحش
                const enemy = new Enemy(selectedType, x, y);
                enemies.push(enemy);
                this.enemiesSpawned++;
            }
            
            spawnBoss() {
                this.bossSpawned = true;
                this.bossActive = true;
                
                // عرض تحذير البوس
                this.showBossWarning();
                
                // تشغيل صوت البوس
                audioSystem.play('boss_spawn', 1.5);
                
                // اهتزاز الكاميرا
                camera.shake(20, 120);
                
                setTimeout(() => {
                    // إنشاء البوس في موقع عشوائي بعيد
                    const angle = Math.random() * Math.PI * 2;
                    const distance = 800;
                    const x = player.x + Math.cos(angle) * distance;
                    const y = player.y + Math.sin(angle) * distance;
                    
                    const boss = new Enemy('boss', x, y);
                    enemies.push(boss);
                    
                    // تأثيرات ظهور البوس
                    particleSystem.addEmitter(x, y, 'magic', {
                        color: '#4b0082',
                        size: 15,
                        speed: 30,
                        duration: 180,
                        rate: 5,
                        gravity: -0.2
                    });
                    
                    // موسيقى البوس
                    camera.zoomTo(2.0, 120);
                }, 3000);
            }
            
            showBossWarning() {
                const warning = document.getElementById('bossWarning');
                warning.style.display = 'block';
                
                setTimeout(() => {
                    warning.style.display = 'none';
                }, 4000);
            }
            
            onEnemyKilled() {
                this.enemiesKilled++;
                
                // تحقق إذا تم إنهاء الموجة
                if (this.enemiesKilled >= this.totalEnemies && !this.bossActive) {
                    this.endWave();
                }
                
                // تحقق إذا تم هزيمة البوس
                if (this.bossActive) {
                    const bossAlive = enemies.some(enemy => enemy.type === 'boss');
                    if (!bossAlive) {
                        this.defeatBoss();
                    }
                }
            }
            
            endWave() {
                this.isWaveActive = false;
                
                // مكافأة إنهاء الموجة
                player.gold += this.currentWave * 200;
                player.score += this.currentWave * 500;
                player.heal(50);
                
                // عرض رسالة النجاح
                player.showMessage(`✅ تم إنهاء الموجة ${this.currentWave}!`, '#00ff00');
                
                // الانتقال للموجة التالية بعد تأخير
                setTimeout(() => {
                    this.nextWave();
                }, 3000);
            }
            
            defeatBoss() {
                this.bossActive = false;
                
                // مكافأة هزيمة البوس
                player.gold += 1000;
                player.score += 5000;
                player.heal(100);
                player.levelUp();
                
                // عرض رسالة النصر
                player.showMessage(`🏆 هزمت الوحش العملاق!`, '#ffd700');
                
                // عودة الكاميرا للحجم الطبيعي
                camera.zoomTo(GAME_CONFIG.CAMERA_ZOOM, 90);
                
                // الانتقال للموجة التالية
                setTimeout(() => {
                    this.nextWave();
                }, 5000);
            }
            
            nextWave() {
                this.currentWave++;
                this.enemiesSpawned = 0;
                this.enemiesKilled = 0;
                this.totalEnemies = Math.floor(GAME_CONFIG.WAVE_ENEMIES * (1 + this.currentWave * 0.2));
                this.spawnRate = Math.max(20, GAME_CONFIG.ENEMY_SPAWN_RATE - this.currentWave * 2);
                this.bossSpawned = false;
                this.isWaveActive = true;
                this.waveTimer = 0;
                
                // زيادة صعوبة الأعداء
                this.enemyTypes.forEach(type => {
                    type.weight = Math.min(100, type.weight + 5);
                });
                
                // عرض رسالة بداية الموجة
                player.showMessage(`🌊 بداية الموجة ${this.currentWave}`, '#00ffff');
                
                audioSystem.play('wave_start');
            }
            
            updateUI() {
                document.getElementById('waveValue').textContent = this.currentWave;
                document.getElementById('enemiesValue').textContent = 
                    `${this.enemiesKilled}/${this.totalEnemies}`;
                
                // تحديث شريط تقدم الموجة
                const waveProgress = (this.enemiesKilled / this.totalEnemies) * 100;
                document.getElementById('waveProgress').style.width = `${waveProgress}%`;
            }
        }

        // ============= نظام الأعداء المتطور =============
        class Enemy {
            constructor(type, x, y) {
                this.type = type;
                this.x = x;
                this.y = y;
                this.vx = 0;
                this.vy = 0;
                this.health = 100;
                this.maxHealth = 100;
                this.speed = 2;
                this.damage = 10;
                this.scoreValue = 100;
                this.expValue = 20;
                this.goldValue = 10;
                this.width = 60;
                this.height = 80;
                this.attackRange = 80;
                this.attackCooldown = 0;
                this.stunTimer = 0;
                this.animationTimer = 0;
                this.wobble = 0;
                this.flashTimer = 0;
                this.target = null;
                
                // إعدادات خاصة بالنوع
                this.setupType(type);
            }
            
            setupType(type) {
                switch(type) {
                    case 'goblin':
                        this.health = 60;
                        this.maxHealth = 60;
                        this.speed = 4;
                        this.damage = 8;
                        this.width = 50;
                        this.height = 60;
                        this.scoreValue = 50;
                        this.expValue = 10;
                        this.goldValue = 5;
                        this.color = '#00ff00';
                        break;
                        
                    case 'orc':
                        this.health = 180;
                        this.maxHealth = 180;
                        this.speed = 1.8;
                        this.damage = 25;
                        this.width = 80;
                        this.height = 110;
                        this.scoreValue = 200;
                        this.expValue = 40;
                        this.goldValue = 25;
                        this.color = '#228b22';
                        break;
                        
                    case 'skeleton':
                        this.health = 100;
                        this.maxHealth = 100;
                        this.speed = 3;
                        this.damage = 15;
                        this.width = 55;
                        this.height = 75;
                        this.scoreValue = 150;
                        this.expValue = 30;
                        this.goldValue = 15;
                        this.color = '#c0c0c0';
                        break;
                        
                    case 'giant':
                        this.health = 600;
                        this.maxHealth = 600;
                        this.speed = 1;
                        this.damage = 60;
                        this.width = 140;
                        this.height = 200;
                        this.scoreValue = 500;
                        this.expValue = 100;
                        this.goldValue = 75;
                        this.color = '#8b0000';
                        this.attackRange = 120;
                        break;
                        
                    case 'boss':
                        this.health = 2500;
                        this.maxHealth = 2500;
                        this.speed = 0.8;
                        this.damage = 100;
                        this.width = 250;
                        this.height = 350;
                        this.scoreValue = 5000;
                        this.expValue = 500;
                        this.goldValue = 500;
                        this.color = '#4b0082';
                        this.attackRange = 180;
                        break;
                }
                
                this.originalColor = this.color;
            }
            
            update() {
                if (gamePaused || this.stunTimer > 0) {
                    this.stunTimer--;
                    return;
                }
                
                // تحديث الرسوم المتحركة
                this.animationTimer++;
                this.wobble = Math.sin(this.animationTimer * 0.1) * 3;
                
                // تحديث توقيت الهجوم
                if (this.attackCooldown > 0) {
                    this.attackCooldown--;
                }
                
                // تحديث تأثير الوميض
                if (this.flashTimer > 0) {
                    this.flashTimer--;
                }
                
                // تتبع اللاعب
                this.followPlayer();
                
                // تحديث الموضع
                this.x += this.vx;
                this.y += this.vy;
                
                // تطبيق الفيزياء البسيطة
                this.vx *= 0.9;
                this.vy *= 0.9;
                
                // التحقق من الهجوم
                this.checkAttack();
            }
            
            followPlayer() {
                if (!player) return;
                
                const dx = player.x - this.x;
                const dy = player.y - this.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance > 0) {
                    // التحرك نحو اللاعب
                    this.vx += (dx / distance) * this.speed * 0.1;
                    this.vy += (dy / distance) * this.speed * 0.1;
                    
                    // تحديد السرعة القصوى
                    const currentSpeed = Math.sqrt(this.vx * this.vx + this.vy * this.vy);
                    if (currentSpeed > this.speed) {
                        this.vx = (this.vx / currentSpeed) * this.speed;
                        this.vy = (this.vy / currentSpeed) * this.speed;
                    }
                }
            }
            
            checkAttack() {
                if (!player || this.attackCooldown > 0) return;
                
                const dx = player.x - this.x;
                const dy = player.y - this.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance < this.attackRange) {
                    this.attack();
                }
            }
            
            attack() {
                this.attackCooldown = 90;
                
                // هزيمة الكاميرا
                camera.shake(this.damage * 0.05, 15);
                
                // تأثيرات الهجوم
                particleSystem.createEffect('spark', this.x, this.y, {
                    count: 10,
                    color: this.color,
                    size: 6,
                    speed: 10
                });
                
                // إصابة اللاعب
                if (player) {
                    const hit = player.takeDamage(this.damage, this);
                    
                    if (hit && this.type === 'boss') {
                        audioSystem.play('boss_hit');
                    }
                }
            }
            
            takeDamage(amount, source) {
                this.health -= amount;
                this.flashTimer = 10;
                
                // دفع الوحش للخلف
                if (source) {
                    const dx = this.x - source.x;
                    const dy = this.y - source.y;
                    const distance = Math.sqrt(dx * dx + dy * dy);
                    
                    if (distance > 0) {
                        const knockback = Math.min(amount * 0.5, 30);
                        this.vx += (dx / distance) * knockback;
                        this.vy += (dy / distance) * knockback;
                    }
                }
                
                // تأثيرات الضرر
                particleSystem.createEffect('blood', this.x, this.y, {
                    count: 15,
                    color: '#ff0000',
                    size: this.type === 'boss' ? 12 : 6,
                    speed: 15
                });
                
                // عرض نص الضرر
                if (player) {
                    const screenPos = camera.worldToScreen(this.x, this.y - 50);
                    
                    const damagePopup = document.createElement('div');
                    damagePopup.className = 'damage-popup';
                    damagePopup.textContent = `-${Math.round(amount)}`;
                    damagePopup.style.color = '#ff0000';
                    damagePopup.style.left = `${screenPos.x}px`;
                    damagePopup.style.top = `${screenPos.y}px`;
                    
                    document.querySelector('.ui-layer').appendChild(damagePopup);
                    
                    setTimeout(() => {
                        damagePopup.remove();
                    }, 1000);
                }
                
                audioSystem.play('enemy_hit', 1, 0.9 + Math.random() * 0.2);
                
                if (this.health <= 0) {
                    this.die();
                    return true;
                }
                
                return false;
            }
            
            stun(duration) {
                this.stunTimer = duration;
                
                // تأثيرات الصدمة
                particleSystem.createEffect('spark', this.x, this.y, {
                    count: 20,
                    color: '#ffff00',
                    size: 8,
                    speed: 5
                });
            }
            
            die() {
                // إزالة الوحش من المصفوفة
                const index = enemies.indexOf(this);
                if (index > -1) {
                    enemies.splice(index, 1);
                }
                
                // تأثيرات الموت
                particleSystem.createEffect('blood', this.x, this.y, {
                    count: this.type === 'boss' ? 100 : 40,
                    color: '#ff0000',
                    size: this.type === 'boss' ? 20 : 10,
                    speed: 25,
                    life: 2
                });
                
                // اهتزاز الكاميرا حسب حجم الوحش
                camera.shake(this.type === 'boss' ? 25 : 10, this.type === 'boss' ? 60 : 30);
                
                audioSystem.play('enemy_death', this.type === 'boss' ? 2 : 1);
            }
            
            draw() {
                if (!camera.isInView(this.x, this.y, this.width, this.height)) {
                    return;
                }
                
                gameCtx.save();
                
                // تأثير الوميض عند الضرر
                if (this.flashTimer > 0) {
                    gameCtx.globalAlpha = 0.7 + Math.sin(this.flashTimer * 0.5) * 0.3;
                }
                
                // تطبيق حركة التأرجح
                gameCtx.translate(0, this.wobble);
                
                // رسم الوحش حسب النوع
                switch(this.type) {
                    case 'goblin':
                        this.drawGoblin();
                        break;
                    case 'orc':
                        this.drawOrc();
                        break;
                    case 'skeleton':
                        this.drawSkeleton();
                        break;
                    case 'giant':
                        this.drawGiant();
                        break;
                    case 'boss':
                        this.drawBoss();
                        break;
                }
                
                // رسم شريط الصحة
                this.drawHealthBar();
                
                gameCtx.restore();
            }
            
            drawGoblin() {
                const x = this.x - this.width / 2;
                const y = this.y - this.height / 2;
                
                // الجسم
                gameCtx.fillStyle = this.color;
                gameCtx.beginPath();
                gameCtx.ellipse(
                    this.x, 
                    this.y, 
                    this.width / 2, 
                    this.height / 2, 
                    0, 0, Math.PI * 2
                );
                gameCtx.fill();
                
                // العيون
                gameCtx.fillStyle = '#000';
                gameCtx.beginPath();
                gameCtx.arc(this.x - 12, this.y - 10, 4, 0, Math.PI * 2);
                gameCtx.arc(this.x + 12, this.y - 10, 4, 0, Math.PI * 2);
                gameCtx.fill();
                
                // الفم
                gameCtx.strokeStyle = '#000';
                gameCtx.lineWidth = 3;
                gameCtx.beginPath();
                gameCtx.arc(this.x, this.y + 10, 10, 0.1, Math.PI - 0.1);
                gameCtx.stroke();
                
                // السلاح
                gameCtx.fillStyle = '#8b4513';
                gameCtx.fillRect(this.x + 30, this.y - 5, 25, 6);
            }
            
            drawOrc() {
                const x = this.x - this.width / 2;
                const y = this.y - this.height / 2;
                
                // الجسم
                gameCtx.fillStyle = this.color;
                gameCtx.beginPath();
                gameCtx.roundRect(x, y, this.width, this.height, 15);
                gameCtx.fill();
                
                // العضلات
                gameCtx.fillStyle = '#32cd32';
                gameCtx.beginPath();
                // عضلات الذراعين
                gameCtx.ellipse(this.x - 30, this.y + 20, 10, 15, 0, 0, Math.PI * 2);
                gameCtx.ellipse(this.x + 30, this.y + 20, 10, 15, 0, 0, Math.PI * 2);
                // عضلات الساقين
                gameCtx.ellipse(this.x - 20, this.y + 60, 12, 18, 0, 0, Math.PI * 2);
                gameCtx.ellipse(this.x + 20, this.y + 60, 12, 18, 0, 0, Math.PI * 2);
                gameCtx.fill();
                
                // الوجه
                gameCtx.fillStyle = '#228b22';
                gameCtx.beginPath();
                gameCtx.arc(this.x, this.y - 15, 20, 0, Math.PI * 2);
                gameCtx.fill();
                
                // العيون
                gameCtx.fillStyle = '#000';
                gameCtx.beginPath();
                gameCtx.arc(this.x - 10, this.y - 20, 5, 0, Math.PI * 2);
                gameCtx.arc(this.x + 10, this.y - 20, 5, 0, Math.PI * 2);
                gameCtx.fill();
                
                // الأنياب
                gameCtx.fillStyle = '#ffffff';
                gameCtx.beginPath();
                // ناب علوي أيسر
                gameCtx.moveTo(this.x - 8, this.y - 5);
                gameCtx.lineTo(this.x - 15, this.y - 10);
                gameCtx.lineTo(this.x - 8, this.y - 10);
                // ناب علوي أيمن
                gameCtx.moveTo(this.x + 8, this.y - 5);
                gameCtx.lineTo(this.x + 15, this.y - 10);
                gameCtx.lineTo(this.x + 8, this.y - 10);
                gameCtx.closePath();
                gameCtx.fill();
                
                // السلاح
                gameCtx.fillStyle = '#2f4f4f';
                gameCtx.fillRect(this.x + 40, this.y, 40, 8);
            }
            
            drawSkeleton() {
                const x = this.x - this.width / 2;
                const y = this.y - this.height / 2;
                
                // الجمجمة
                gameCtx.fillStyle = '#ffffff';
                gameCtx.beginPath();
                gameCtx.arc(this.x, this.y - 25, 18, 0, Math.PI * 2);
                gameCtx.fill();
                
                // العيون
                gameCtx.fillStyle = '#000';
                gameCtx.beginPath();
                gameCtx.arc(this.x - 7, this.y - 28, 4, 0, Math.PI * 2);
                gameCtx.arc(this.x + 7, this.y - 28, 4, 0, Math.PI * 2);
                gameCtx.fill();
                
                // الأنف
                gameCtx.beginPath();
                gameCtx.moveTo(this.x, this.y - 25);
                gameCtx.lineTo(this.x - 4, this.y - 20);
                gameCtx.lineTo(this.x + 4, this.y - 20);
                gameCtx.closePath();
                gameCtx.fill();
                
                // الجسم
                gameCtx.fillStyle = '#c0c0c0';
                gameCtx.fillRect(x + 10, y + 10, this.width - 20, 50);
                
                // الذراعين
                gameCtx.fillRect(x - 15, y + 15, 15, 6);
                gameCtx.fillRect(this.x + this.width / 2, y + 15, 15, 6);
                
                // الساقين
                gameCtx.fillRect(x + 15, y + 60, 10, 35);
                gameCtx.fillRect(x + this.width - 25, y + 60, 10, 35);
                
                // السلاح
                gameCtx.fillStyle = '#8b4513';
                gameCtx.fillRect(this.x + 25, y + 10, 35, 6);
            }
            
            drawGiant() {
                const x = this.x - this.width / 2;
                const y = this.y - this.height / 2;
                
                // الجسم
                gameCtx.fillStyle = this.color;
                gameCtx.beginPath();
                gameCtx.roundRect(x, y, this.width, this.height, 25);
                gameCtx.fill();
                
                // العيون
                gameCtx.fillStyle = '#ffff00';
                gameCtx.beginPath();
                gameCtx.arc(this.x - 25, this.y - 20, 12, 0, Math.PI * 2);
                gameCtx.arc(this.x + 25, this.y - 20, 12, 0, Math.PI * 2);
                gameCtx.fill();
                
                // بؤبؤ العين
                gameCtx.fillStyle = '#000';
                gameCtx.beginPath();
                gameCtx.arc(this.x - 25, this.y - 20, 5, 0, Math.PI * 2);
                gameCtx.arc(this.x + 25, this.y - 20, 5, 0, Math.PI * 2);
                gameCtx.fill();
                
                // الفم
                gameCtx.strokeStyle = '#000';
                gameCtx.lineWidth = 6;
                gameCtx.beginPath();
                gameCtx.arc(this.x, this.y + 30, 30, 0.1, Math.PI - 0.1);
                gameCtx.stroke();
                
                // الأسنان
                gameCtx.fillStyle = '#ffffff';
                for (let i = 0; i < 8; i++) {
                    const toothX = this.x - 30 + i * 8.5;
                    gameCtx.fillRect(toothX, this.y, 6, 12);
                }
                
                // السلاح
                gameCtx.fillStyle = '#2f4f4f';
                gameCtx.fillRect(this.x + 70, this.y - 10, 60, 15);
            }
            
            drawBoss() {
                const x = this.x - this.width / 2;
                const y = this.y - this.height / 2;
                
                // الجسم الرئيسي
                const bossGradient = gameCtx.createRadialGradient(
                    this.x, this.y, 0,
                    this.x, this.y, this.width
                );
                bossGradient.addColorStop(0, '#4b0082');
                bossGradient.addColorStop(0.5, '#8a2be2');
                bossGradient.addColorStop(1, '#9370db');
                
                gameCtx.fillStyle = bossGradient;
                gameCtx.beginPath();
                gameCtx.ellipse(
                    this.x, 
                    this.y, 
                    this.width / 2, 
                    this.height / 2, 
                    0, 0, Math.PI * 2
                );
                gameCtx.fill();
                
                // تأثير التوهج
                gameCtx.shadowBlur = 50;
                gameCtx.shadowColor = '#4b0082';
                gameCtx.fill();
                gameCtx.shadowBlur = 0;
                
                // العيون المتعددة
                gameCtx.fillStyle = '#ff0000';
                for (let i = 0; i < 8; i++) {
                    const angle = (i * Math.PI * 2) / 8;
                    const eyeX = this.x + Math.cos(angle) * 60;
                    const eyeY = this.y + Math.sin(angle) * 60;
                    
                    gameCtx.beginPath();
                    gameCtx.arc(eyeX, eyeY, 10, 0, Math.PI * 2);
                    gameCtx.fill();
                    
                    // بؤبؤ العين
                    gameCtx.fillStyle = '#000';
                    gameCtx.beginPath();
                    gameCtx.arc(eyeX, eyeY, 4, 0, Math.PI * 2);
                    gameCtx.fill();
                    
                    gameCtx.fillStyle = '#ff0000';
                }
                
                // الفم الكبير
                gameCtx.fillStyle = '#000';
                gameCtx.beginPath();
                gameCtx.arc(this.x, this.y, 40, 0, Math.PI);
                gameCtx.fill();
                
                // الأنياب
                gameCtx.fillStyle = '#ffffff';
                for (let i = 0; i < 12; i++) {
                    const toothAngle = (i * Math.PI) / 11;
                    const toothX = this.x + Math.cos(toothAngle) * 40;
                    const toothY = this.y + Math.sin(toothAngle) * 40;
                    
                    gameCtx.save();
                    gameCtx.translate(toothX, toothY);
                    gameCtx.rotate(toothAngle);
                    gameCtx.fillRect(-4, -20, 8, 40);
                    gameCtx.restore();
                }
                
                // المخالب
                for (let i = 0; i < 6; i++) {
                    const clawAngle = (i * Math.PI * 2) / 6;
                    const clawX = this.x + Math.cos(clawAngle) * 100;
                    const clawY = this.y + Math.sin(clawAngle) * 100;
                    
                    gameCtx.save();
                    gameCtx.translate(clawX, clawY);
                    gameCtx.rotate(clawAngle);
                    
                    gameCtx.fillStyle = '#8b0000';
                    gameCtx.beginPath();
                    gameCtx.moveTo(0, 0);
                    gameCtx.lineTo(30, -15);
                    gameCtx.lineTo(60, 0);
                    gameCtx.lineTo(30, 15);
                    gameCtx.closePath();
                    gameCtx.fill();
                    
                    gameCtx.restore();
                }
            }
            
            drawHealthBar() {
                const barWidth = 80;
                const barHeight = 8;
                const healthPercent = this.health / this.maxHealth;
                
                // خلفية شريط الصحة
                gameCtx.fillStyle = '#000000';
                gameCtx.fillRect(
                    this.x - barWidth / 2, 
                    this.y - this.height / 2 - 25, 
                    barWidth, 
                    barHeight
                );
                
                // شريط الصحة
                const healthColor = healthPercent > 0.6 ? '#00ff00' : 
                                  healthPercent > 0.3 ? '#ffff00' : '#ff0000';
                
                gameCtx.fillStyle = healthColor;
                gameCtx.fillRect(
                    this.x - barWidth / 2, 
                    this.y - this.height / 2 - 25, 
                    barWidth * healthPercent, 
                    barHeight
                );
                
                // حدود شريط الصحة
                gameCtx.strokeStyle = '#ffffff';
                gameCtx.lineWidth = 1;
                gameCtx.strokeRect(
                    this.x - barWidth / 2, 
                    this.y - this.height / 2 - 25, 
                    barWidth, 
                    barHeight
                );
                
                // اسم الوحش
                if (this.type === 'boss') {
                    gameCtx.fillStyle = '#ff0000';
                    gameCtx.font = 'bold 20px Arial';
                    gameCtx.textAlign = 'center';
                    gameCtx.fillText('👹 الوحش العملاق 👹', this.x, this.y - this.height / 2 - 40);
                }
            }
        }

        // ============= نظام الرصاصات =============
        class BulletSystem {
            static update() {
                for (let i = bullets.length - 1; i >= 0; i--) {
                    const bullet = bullets[i];
                    
                    // تحديث الموضع
                    bullet.x += bullet.vx;
                    bullet.y += bullet.vy;
                    bullet.life--;
                    
                    // تطبيق الجاذبية للصواريخ
                    if (bullet.type === 'rocket') {
                        bullet.vy += 0.1;
                    }
                    
                    // التحقق من اصطدام الرصاصة بالأعداء
                    let hitEnemy = false;
                    for (let j = enemies.length - 1; j >= 0; j--) {
                        const enemy = enemies[j];
                        
                        if (Math.abs(bullet.x - enemy.x) < enemy.width / 2 &&
                            Math.abs(bullet.y - enemy.y) < enemy.height / 2) {
                            
                            const killed = enemy.takeDamage(bullet.damage, bullet.owner);
                            
                            if (killed && bullet.owner) {
                                bullet.owner.onEnemyKilled(enemy);
                            }
                            
                            hitEnemy = true;
                            break;
                        }
                    }
                    
                    // تأثيرات الاصطدام
                    if (hitEnemy || bullet.life <= 0) {
                        // تأثيرات الانفجار
                        particleSystem.createEffect(
                            bullet.type === 'rocket' ? 'fire' : 'spark',
                            bullet.x, bullet.y,
                            {
                                count: bullet.type === 'rocket' ? 40 : 20,
                                color: bullet.color,
                                size: bullet.type === 'rocket' ? 15 : 8,
                                speed: bullet.type === 'rocket' ? 30 : 20,
                                life: bullet.type === 'rocket' ? 2 : 1
                            }
                        );
                        
                        // اهتزاز الكاميرا للانفجارات الكبيرة
                        if (bullet.type === 'rocket') {
                            camera.shake(15, 25);
                            
                            // ضرر انفجار الصاروخ للأعداء القريبين
                            enemies.forEach(enemy => {
                                const dx = enemy.x - bullet.x;
                                const dy = enemy.y - bullet.y;
                                const distance = Math.sqrt(dx * dx + dy * dy);
                                
                                if (distance < 200) {
                                    enemy.takeDamage(bullet.damage * 0.5, bullet.owner);
                                }
                            });
                        }
                        
                        bullets.splice(i, 1);
                        continue;
                    }
                    
                    // حدود العالم
                    if (bullet.x < 0 || bullet.x > GAME_CONFIG.WORLD_WIDTH ||
                        bullet.y < 0 || bullet.y > GAME_CONFIG.WORLD_HEIGHT) {
                        bullets.splice(i, 1);
                    }
                }
            }
            
            static draw() {
                bullets.forEach(bullet => {
                    if (!camera.isInView(bullet.x, bullet.y, bullet.size * 2, bullet.size * 2)) {
                        return;
                    }
                    
                    gameCtx.save();
                    
                    if (bullet.type === 'rocket') {
                        // رسم الصاروخ
                        const rocketGradient = gameCtx.createLinearGradient(
                            bullet.x - 20, bullet.y,
                            bullet.x + 20, bullet.y
                        );
                        rocketGradient.addColorStop(0, '#ff8c00');
                        rocketGradient.addColorStop(0.5, '#ff4500');
                        rocketGradient.addColorStop(1, '#8b0000');
                        
                        gameCtx.fillStyle = rocketGradient;
                        gameCtx.beginPath();
                        gameCtx.moveTo(bullet.x - 20, bullet.y);
                        gameCtx.lineTo(bullet.x + 20, bullet.y - 12);
                        gameCtx.lineTo(bullet.x + 20, bullet.y + 12);
                        gameCtx.closePath();
                        gameCtx.fill();
                        
                        // نيران العادم
                        const flameGradient = gameCtx.createRadialGradient(
                            bullet.x - 25, bullet.y, 0,
                            bullet.x - 40, bullet.y, 25
                        );
                        flameGradient.addColorStop(0, '#ffff00');
                        flameGradient.addColorStop(0.3, '#ff4500');
                        flameGradient.addColorStop(1, 'transparent');
                        
                        gameCtx.fillStyle = flameGradient;
                        gameCtx.beginPath();
                        gameCtx.arc(bullet.x - 25, bullet.y, 25, 0, Math.PI * 2);
                        gameCtx.fill();
                        
                        // أثر الصاروخ
                        for (let i = 0; i < 3; i++) {
                            const trailX = bullet.x - 25 - i * 10;
                            const trailSize = 15 - i * 4;
                            
                            gameCtx.globalAlpha = 0.3 - i * 0.1;
                            gameCtx.fillStyle = '#ff4500';
                            gameCtx.beginPath();
                            gameCtx.arc(trailX, bullet.y, trailSize, 0, Math.PI * 2);
                            gameCtx.fill();
                        }
                        
                    } else {
                        // رسم الرصاصات العادية
                        gameCtx.fillStyle = bullet.color;
                        gameCtx.beginPath();
                        gameCtx.arc(bullet.x, bullet.y, bullet.size, 0, Math.PI * 2);
                        gameCtx.fill();
                        
                        // توهج الرصاصة
                        const glowGradient = gameCtx.createRadialGradient(
                            bullet.x, bullet.y, 0,
                            bullet.x, bullet.y, bullet.size * 3
                        );
                        glowGradient.addColorStop(0, bullet.color + '80');
                        glowGradient.addColorStop(1, 'transparent');
                        
                        gameCtx.globalAlpha = 0.4;
                        gameCtx.fillStyle = glowGradient;
                        gameCtx.beginPath();
                        gameCtx.arc(bullet.x, bullet.y, bullet.size * 3, 0, Math.PI * 2);
                        gameCtx.fill();
                        
                        // أثر الرصاصة
                        gameCtx.globalAlpha = 0.2;
                        gameCtx.beginPath();
                        gameCtx.moveTo(bullet.x - bullet.vx * 0.5, bullet.y - bullet.vy * 0.5);
                        gameCtx.lineTo(bullet.x, bullet.y);
                        gameCtx.lineWidth = bullet.size * 2;
                        gameCtx.strokeStyle = bullet.color;
                        gameCtx.stroke();
                    }
                    
                    gameCtx.restore();
                });
            }
        }

        // ============= نظام القوى =============
        class PowerUpSystem {
            static update() {
                for (let i = powerUps.length - 1; i >= 0; i--) {
                    const powerUp = powerUps[i];
                    
                    powerUp.life--;
                    
                    // تحريك القوى بلطف
                    powerUp.y += Math.sin(gameTime * 0.05 + i) * 0.5;
                    
                    // التحقق من التجميع
                    if (player && !powerUp.collected) {
                        const dx = powerUp.x - player.x;
                        const dy = powerUp.y - player.y;
                        const distance = Math.sqrt(dx * dx + dy * dy);
                        
                        if (distance < player.width / 2 + powerUp.size) {
                            powerUp.collected = true;
                            this.collectPowerUp(powerUp, i);
                        }
                    }
                    
                    // إزالة القوى المنتهية
                    if (powerUp.life <= 0) {
                        powerUps.splice(i, 1);
                    }
                }
            }
            
            static collectPowerUp(powerUp, index) {
                // تأثيرات التجميع
                particleSystem.createEffect('magic', powerUp.x, powerUp.y, {
                    count: 30,
                    color: powerUp.color,
                    size: 10,
                    speed: 15,
                    life: 1.5
                });
                
                audioSystem.play('powerup');
                
                // تطبيق تأثير القوة
                switch(powerUp.type) {
                    case 'health':
                        player.heal(powerUp.value);
                        player.showDamageText(`+${powerUp.value} صحة`, '#ff0000');
                        break;
                        
                    case 'stamina':
                        player.stamina = Math.min(player.maxStamina, player.stamina + powerUp.value);
                        player.showDamageText(`+${powerUp.value} طاقة`, '#00ff00');
                        break;
                        
                    case 'ammo':
                        Object.values(player.weapons).forEach(weapon => {
                            if (weapon.ammo !== Infinity) {
                                weapon.ammo = Math.min(weapon.maxAmmo, weapon.ammo + powerUp.value);
                            }
                        });
                        player.showDamageText(`+${powerUp.value} ذخيرة`, '#ffff00');
                        break;
                        
                    case 'coin':
                        player.gold += powerUp.value;
                        player.showDamageText(`+${powerUp.value} ذهب`, '#ffd700');
                        break;
                        
                    case 'speed':
                        player.speed += 2;
                        setTimeout(() => {
                            player.speed -= 2;
                        }, powerUp.value);
                        player.showDamageText(`سرعة!`, '#00ffff');
                        break;
                }
                
                // إزالة القوة
                powerUps.splice(index, 1);
            }
            
            static draw() {
                powerUps.forEach(powerUp => {
                    if (!camera.isInView(powerUp.x, powerUp.y, powerUp.size * 2, powerUp.size * 2)) {
                        return;
                    }
                    
                    gameCtx.save();
                    
                    // توهج القوة
                    const glowGradient = gameCtx.createRadialGradient(
                        powerUp.x, powerUp.y, 0,
                        powerUp.x, powerUp.y, powerUp.size * 3
                    );
                    glowGradient.addColorStop(0, powerUp.color + '80');
                    glowGradient.addColorStop(1, 'transparent');
                    
                    gameCtx.globalAlpha = 0.5 + Math.sin(gameTime * 0.1) * 0.3;
                    gameCtx.fillStyle = glowGradient;
                    gameCtx.beginPath();
                    gameCtx.arc(powerUp.x, powerUp.y, powerUp.size * 3, 0, Math.PI * 2);
                    gameCtx.fill();
                    
                    // الجسم الرئيسي
                    gameCtx.globalAlpha = 1;
                    gameCtx.fillStyle = powerUp.color;
                    
                    // شكل القوة حسب النوع
                    switch(powerUp.type) {
                        case 'health':
                            // قلب
                            gameCtx.beginPath();
                            gameCtx.moveTo(powerUp.x, powerUp.y - powerUp.size);
                            gameCtx.bezierCurveTo(
                                powerUp.x, powerUp.y - powerUp.size * 1.3,
                                powerUp.x - powerUp.size * 1.2, powerUp.y - powerUp.size * 0.7,
                                powerUp.x, powerUp.y + powerUp.size
                            );
                            gameCtx.bezierCurveTo(
                                powerUp.x + powerUp.size * 1.2, powerUp.y - powerUp.size * 0.7,
                                powerUp.x, powerUp.y - powerUp.size * 1.3,
                                powerUp.x, powerUp.y - powerUp.size
                            );
                            gameCtx.closePath();
                            gameCtx.fill();
                            break;
                            
                        case 'stamina':
                            // برق
                            gameCtx.beginPath();
                            gameCtx.moveTo(powerUp.x, powerUp.y - powerUp.size * 1.5);
                            gameCtx.lineTo(powerUp.x - powerUp.size, powerUp.y);
                            gameCtx.lineTo(powerUp.x, powerUp.y + powerUp.size * 0.5);
                            gameCtx.lineTo(powerUp.x + powerUp.size, powerUp.y - powerUp.size * 0.5);
                            gameCtx.lineTo(powerUp.x, powerUp.y + powerUp.size * 1.5);
                            gameCtx.lineTo(powerUp.x + powerUp.size * 0.5, powerUp.y);
                            gameCtx.closePath();
                            gameCtx.fill();
                            break;
                            
                        case 'ammo':
                            // رصاصات
                            gameCtx.beginPath();
                            gameCtx.arc(powerUp.x, powerUp.y, powerUp.size, 0, Math.PI * 2);
                            gameCtx.fill();
                            
                            gameCtx.fillStyle = '#000';
                            gameCtx.beginPath();
                            gameCtx.arc(powerUp.x, powerUp.y, powerUp.size * 0.6, 0, Math.PI * 2);
                            gameCtx.fill();
                            break;
                            
                        case 'coin':
                            // عملة
                            gameCtx.beginPath();
                            gameCtx.arc(powerUp.x, powerUp.y, powerUp.size, 0, Math.PI * 2);
                            gameCtx.fill();
                            
                            gameCtx.fillStyle = '#ffd700';
                            gameCtx.font = 'bold 16px Arial';
                            gameCtx.textAlign = 'center';
                            gameCtx.textBaseline = 'middle';
                            gameCtx.fillText('💰', powerUp.x, powerUp.y);
                            break;
                            
                        default:
                            // نجمة
                            gameCtx.beginPath();
                            for (let i = 0; i < 5; i++) {
                                const angle = (i * Math.PI * 2) / 5;
                                const spikeAngle = angle - Math.PI / 2;
                                
                                const x1 = powerUp.x + Math.cos(spikeAngle) * powerUp.size;
                                const y1 = powerUp.y + Math.sin(spikeAngle) * powerUp.size;
                                
                                const innerAngle = spikeAngle + Math.PI / 5;
                                const x2 = powerUp.x + Math.cos(innerAngle) * powerUp.size * 0.5;
                                const y2 = powerUp.y + Math.sin(innerAngle) * powerUp.size * 0.5;
                                
                                const nextSpikeAngle = spikeAngle + Math.PI * 2 / 5;
                                const x3 = powerUp.x + Math.cos(nextSpikeAngle) * powerUp.size;
                                const y3 = powerUp.y + Math.sin(nextSpikeAngle) * powerUp.size;
                                
                                if (i === 0) {
                                    gameCtx.moveTo(x1, y1);
                                } else {
                                    gameCtx.lineTo(x1, y1);
                                }
                                
                                gameCtx.lineTo(x2, y2);
                                gameCtx.lineTo(x3, y3);
                            }
                            gameCtx.closePath();
                            gameCtx.fill();
                    }
                    
                    gameCtx.restore();
                });
            }
        }

        // ============= رسم الخلفية الأسطورية =============
        function drawEpicBackground() {
            // السماء المتدرجة
            const skyGradient = gameCtx.createLinearGradient(
                0, 0,
                0, GAME_CONFIG.WORLD_HEIGHT
            );
            skyGradient.addColorStop(0, '#0a0a2a');
            skyGradient.addColorStop(0.3, '#1a1a40');
            skyGradient.addColorStop(0.6, '#2a2a5a');
            skyGradient.addColorStop(1, '#3a3a6a');
            
            gameCtx.fillStyle = skyGradient;
            gameCtx.fillRect(0, 0, GAME_CONFIG.WORLD_WIDTH, GAME_CONFIG.WORLD_HEIGHT);
            
            // النجوم
            gameCtx.fillStyle = '#ffffff';
            for (let i = 0; i < 200; i++) {
                const starSeed = i * 7;
                const x = (Math.sin(starSeed) * GAME_CONFIG.WORLD_WIDTH + gameTime * 0.2) % GAME_CONFIG.WORLD_WIDTH;
                const y = (Math.cos(starSeed * 1.3) * GAME_CONFIG.WORLD_HEIGHT + gameTime * 0.15) % GAME_CONFIG.WORLD_HEIGHT;
                const size = Math.sin(starSeed * 0.5 + gameTime * 0.01) * 2 + 1;
                const alpha = Math.sin(starSeed * 0.3 + gameTime * 0.005) * 0.5 + 0.5;
                
                gameCtx.globalAlpha = alpha;
                gameCtx.beginPath();
                gameCtx.arc(x, y, size, 0, Math.PI * 2);
                gameCtx.fill();
            }
            gameCtx.globalAlpha = 1;
            
            // السحب
            for (let i = 0; i < 10; i++) {
                const cloudX = (i * 800 + gameTime * 0.1) % (GAME_CONFIG.WORLD_WIDTH + 800) - 400;
                const cloudY = 300 + Math.sin(i * 0.7) * 100;
                const cloudSize = 150 + Math.sin(i * 1.3) * 50;
                
                gameCtx.fillStyle = 'rgba(255, 255, 255, 0.1)';
                gameCtx.beginPath();
                gameCtx.arc(cloudX, cloudY, cloudSize * 0.5, 0, Math.PI * 2);
                gameCtx.arc(cloudX + cloudSize * 0.4, cloudY - cloudSize * 0.2, cloudSize * 0.4, 0, Math.PI * 2);
                gameCtx.arc(cloudX - cloudSize * 0.4, cloudY - cloudSize * 0.2, cloudSize * 0.4, 0, Math.PI * 2);
                gameCtx.arc(cloudX + cloudSize * 0.2, cloudY + cloudSize * 0.3, cloudSize * 0.3, 0, Math.PI * 2);
                gameCtx.arc(cloudX - cloudSize * 0.2, cloudY + cloudSize * 0.3, cloudSize * 0.3, 0, Math.PI * 2);
                gameCtx.fill();
            }
            
            // الجبال البعيدة
            for (let i = 0; i < 15; i++) {
                const mountainX = (i * 700) % GAME_CONFIG.WORLD_WIDTH;
                const mountainHeight = 400 + Math.sin(i * 0.8) * 150;
                const mountainY = GAME_CONFIG.GROUND_LEVEL - mountainHeight;
                
                const mountainGradient = gameCtx.createLinearGradient(
                    mountainX, mountainY,
                    mountainX, mountainY + mountainHeight
                );
                mountainGradient.addColorStop(0, '#2f4f4f');
                mountainGradient.addColorStop(1, '#1a1a1a');
                
                gameCtx.fillStyle = mountainGradient;
                gameCtx.beginPath();
                gameCtx.moveTo(mountainX - 350, GAME_CONFIG.GROUND_LEVEL);
                gameCtx.lineTo(mountainX, mountainY);
                gameCtx.lineTo(mountainX + 350, GAME_CONFIG.GROUND_LEVEL);
                gameCtx.closePath();
                gameCtx.fill();
                
                // ثلوج الجبال
                if (mountainHeight > 500) {
                    gameCtx.fillStyle = '#ffffff';
                    gameCtx.beginPath();
                    gameCtx.moveTo(mountainX - 100, mountainY + 50);
                    gameCtx.lineTo(mountainX, mountainY);
                    gameCtx.lineTo(mountainX + 100, mountainY + 50);
                    gameCtx.closePath();
                    gameCtx.fill();
                }
            }
            
            // الأرض
            const groundGradient = gameCtx.createLinearGradient(
                0, GAME_CONFIG.GROUND_LEVEL,
                0, GAME_CONFIG.WORLD_HEIGHT
            );
            groundGradient.addColorStop(0, '#2d5016');
            groundGradient.addColorStop(0.5, '#1e3a0a');
            groundGradient.addColorStop(1, '#0f1d05');
            
            gameCtx.fillStyle = groundGradient;
            gameCtx.fillRect(0, GAME_CONFIG.GROUND_LEVEL, GAME_CONFIG.WORLD_WIDTH, 
                GAME_CONFIG.WORLD_HEIGHT - GAME_CONFIG.GROUND_LEVEL);
            
            // العشب
            gameCtx.fillStyle = '#3a7d1e';
            for (let i = 0; i < 80; i++) {
                const grassX = (i * 100 + gameTime * 0.05) % GAME_CONFIG.WORLD_WIDTH;
                const grassHeight = 40 + Math.sin(i * 0.3 + gameTime * 0.02) * 20;
                
                gameCtx.beginPath();
                gameCtx.moveTo(grassX - 50, GAME_CONFIG.GROUND_LEVEL);
                gameCtx.quadraticCurveTo(
                    grassX, 
                    GAME_CONFIG.GROUND_LEVEL - grassHeight * 1.5,
                    grassX + 50, 
                    GAME_CONFIG.GROUND_LEVEL
                );
                gameCtx.fill();
            }
            
            // الأشجار
            for (let i = 0; i < 40; i++) {
                const treeX = (i * 250 + 100) % GAME_CONFIG.WORLD_WIDTH;
                const treeHeight = 150 + Math.sin(i * 0.5) * 50;
                const treeWidth = 40 + Math.sin(i * 0.7) * 10;
                
                // جذع الشجرة
                gameCtx.fillStyle = '#8b4513';
                gameCtx.fillRect(
                    treeX - treeWidth / 2,
                    GAME_CONFIG.GROUND_LEVEL - treeHeight * 0.3,
                    treeWidth,
                    treeHeight * 0.3
                );
                
                // تاج الشجرة
                const treeGradient = gameCtx.createRadialGradient(
                    treeX, GAME_CONFIG.GROUND_LEVEL - treeHeight * 0.3, 0,
                    treeX, GAME_CONFIG.GROUND_LEVEL - treeHeight * 0.3, treeWidth * 2
                );
                treeGradient.addColorStop(0, '#228b22');
                treeGradient.addColorStop(1, '#006400');
                
                gameCtx.fillStyle = treeGradient;
                gameCtx.beginPath();
                gameCtx.arc(
                    treeX, 
                    GAME_CONFIG.GROUND_LEVEL - treeHeight * 0.3, 
                    treeWidth * 2, 
                    0, 
                    Math.PI * 2
                );
                gameCtx.fill();
            }
            
            // القلعة الأسطورية
            const castleX = GAME_CONFIG.WORLD_WIDTH / 2;
            const castleY = GAME_CONFIG.GROUND_LEVEL - 400;
            
            // أساس القلعة
            gameCtx.fillStyle = '#696969';
            gameCtx.fillRect(castleX - 400, castleY + 200, 800, 200);
            
            // الجدران الرئيسية
            gameCtx.fillStyle = '#808080';
            gameCtx.fillRect(castleX - 350, castleY, 700, 200);
            
            // الأبراج
            const towers = [
                { x: castleX - 400, y: castleY - 100, width: 100, height: 300 },
                { x: castleX + 300, y: castleY - 100, width: 100, height: 300 },
                { x: castleX - 200, y: castleY - 50, width: 80, height: 250 },
                { x: castleX + 120, y: castleY - 50, width: 80, height: 250 }
            ];
            
            towers.forEach(tower => {
                // البرج
                const towerGradient = gameCtx.createLinearGradient(
                    tower.x, tower.y,
                    tower.x, tower.y + tower.height
                );
                towerGradient.addColorStop(0, '#a9a9a9');
                towerGradient.addColorStop(1, '#696969');
                
                gameCtx.fillStyle = towerGradient;
                gameCtx.fillRect(tower.x, tower.y, tower.width, tower.height);
                
                // نوافذ البرج
                gameCtx.fillStyle = '#ffff00';
                for (let i = 0; i < 3; i++) {
                    const windowY = tower.y + 50 + i * 70;
                    gameCtx.fillRect(tower.x + 20, windowY, 20, 30);
                    gameCtx.fillRect(tower.x + tower.width - 40, windowY, 20, 30);
                }
                
                // سقف البرج
                const roofGradient = gameCtx.createRadialGradient(
                    tower.x + tower.width / 2, tower.y, 0,
                    tower.x + tower.width / 2, tower.y, tower.width * 0.8
                );
                roofGradient.addColorStop(0, '#8b0000');
                roofGradient.addColorStop(1, '#660000');
                
                gameCtx.fillStyle = roofGradient;
                gameCtx.beginPath();
                gameCtx.moveTo(tower.x, tower.y);
                gameCtx.lineTo(tower.x + tower.width, tower.y);
                gameCtx.lineTo(tower.x + tower.width / 2, tower.y - 60);
                gameCtx.closePath();
                gameCtx.fill();
            });
            
            // البوابة الرئيسية
            const gateGradient = gameCtx.createRadialGradient(
                castleX, castleY + 200, 0,
                castleX, castleY + 200, 100
            );
            gateGradient.addColorStop(0, '#8b4513');
            gateGradient.addColorStop(1, '#654321');
            
            gameCtx.fillStyle = gateGradient;
            gameCtx.beginPath();
            gameCtx.arc(castleX, castleY + 200, 80, 0, Math.PI * 2);
            gameCtx.fill();
            
            // تفاصيل البوابة
            gameCtx.fillStyle = '#ffd700';
            gameCtx.beginPath();
            gameCtx.arc(castleX, castleY + 200, 60, 0, Math.PI * 2);
            gameCtx.fill();
            
            // العلم
            gameCtx.fillStyle = '#ff0000';
            gameCtx.beginPath();
            gameCtx.moveTo(castleX + 320, castleY - 160);
            gameCtx.lineTo(castleX + 380, castleY - 160);
            gameCtx.lineTo(castleX + 380, castleY - 120);
            gameCtx.lineTo(castleX + 350, castleY - 140);
            gameCtx.closePath();
            gameCtx.fill();
            
            // تأثيرات إضافية
            gameCtx.fillStyle = 'rgba(255, 215, 0, 0.1)';
            gameCtx.beginPath();
            gameCtx.arc(castleX, castleY + 100, 200, 0, Math.PI * 2);
            gameCtx.fill();
            
            // جسر القلعة
            gameCtx.fillStyle = '#8b4513';
            gameCtx.fillRect(castleX - 100, castleY + 200, 200, 50);
            
            // تفاصيل الجسر
            gameCtx.fillStyle = '#654321';
            for (let i = 0; i < 5; i++) {
                gameCtx.fillRect(castleX - 90 + i * 40, castleY + 200, 20, 50);
            }
        }

        // ============= تهيئة اللعبة الرئيسية =============
        function initGame() {
            // إنشاء عنصر canvas
            gameCanvas = document.createElement('canvas');
            gameCanvas.id = 'gameCanvas';
            gameCanvas.width = GAME_CONFIG.WORLD_WIDTH;
            gameCanvas.height = GAME_CONFIG.WORLD_HEIGHT;
            gameCanvas.style.position = 'absolute';
            gameCanvas.style.top = '0';
            gameCanvas.style.left = '0';
            gameCanvas.style.width = '100%';
            gameCanvas.style.height = '100%';
            
            document.getElementById('gameScreen').appendChild(gameCanvas);
            gameCtx = gameCanvas.getContext('2d');
            
            // تحسين الجودة
            gameCtx.imageSmoothingEnabled = true;
            gameCtx.imageSmoothingQuality = 'high';
            
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
                };
            }
            
            // إنشاء الأنظمة
            audioSystem = new EpicAudioSystem();
            particleSystem = new EpicParticleSystem();
            inputSystem = new InputSystem();
            waveSystem = new WaveSystem();
            
            // إنشاء اللاعب
            player = new LegendaryPlayer();
            
            // إنشاء الكاميرا ومتابعة اللاعب
            camera = new SmartCamera(player);
            
            // إعداد الأسلحة
            setupWeapons();
            
            // إعداد الإعدادات
            setupSettings();
            
            // إعداد أزرار الجانب
            setupSideButtons();
            
            // بدء دورة اللعبة
            gameLoop();
        }
        
        function setupWeapons() {
            const weaponSlots = document.querySelectorAll('.weapon-slot');
            
            weaponSlots.forEach(slot => {
                slot.addEventListener('click', () => {
                    selectWeapon(slot.dataset.weapon);
                });
                
                slot.addEventListener('touchstart', (e) => {
                    e.preventDefault();
                    selectWeapon(slot.dataset.weapon);
                });
            });
        }
        
        function selectWeapon(weaponType) {
            if (!player || !player.weapons[weaponType]) return;
            
            // تحديث الواجهة
            document.querySelectorAll('.weapon-slot').forEach(slot => {
                slot.classList.remove('active');
            });
            
            const selectedSlot = document.querySelector(`[data-weapon="${weaponType}"]`);
            if (selectedSlot) {
                selectedSlot.classList.add('active');
            }
            
            // تحديث سلاح اللاعب
            player.currentWeapon = weaponType;
            
            // تأثير صوتي
            audioSystem.play('reload', 0.5);
        }
        
        function setupSettings() {
            const settingsPanel = document.getElementById('settingsPanel');
            const settingsBtn = document.getElementById('settingsBtn');
            const closeBtn = document.getElementById('closeSettings');
            const saveBtn = document.getElementById('saveSettings');
            const musicSlider = document.getElementById('musicVolume');
            const sfxSlider = document.getElementById('sfxVolume');
            const graphicsSelect = document.getElementById('graphicsQuality');
            const sensitivitySlider = document.getElementById('sensitivitySlider');
            
            // فتح الإعدادات
            settingsBtn.addEventListener('click', () => {
                settingsPanel.style.display = 'block';
                gamePaused = true;
            });
            
            // حفظ الإعدادات
            saveBtn.addEventListener('click', () => {
                audioSystem.setVolume('music', musicSlider.value);
                audioSystem.setVolume('sfx', sfxSlider.value);
                
                if (inputSystem) {
                    inputSystem.sensitivity = sensitivitySlider.value;
                }
                
                // تطبيق جودة الرسومات
                const quality = graphicsSelect.value;
                switch(quality) {
                    case 'low':
                        gameCanvas.style.imageRendering = 'pixelated';
                        break;
                    case 'medium':
                        gameCanvas.style.imageRendering = 'auto';
                        break;
                    case 'high':
                    case 'ultra':
                        gameCanvas.style.imageRendering = 'auto';
                        break;
                }
                
                settingsPanel.style.display = 'none';
                gamePaused = false;
            });
            
            // إغلاق الإعدادات
            closeBtn.addEventListener('click', () => {
                settingsPanel.style.display = 'none';
                gamePaused = false;
            });
        }
        
        function setupSideButtons() {
            const pauseBtn = document.getElementById('pauseBtn');
            const soundBtn = document.getElementById('soundBtn');
            const helpBtn = document.getElementById('helpBtn');
            
            // زر الإيقاف المؤقت
            pauseBtn.addEventListener('click', () => {
                gamePaused = !gamePaused;
                pauseBtn.textContent = gamePaused ? '▶️' : '⏸️';
                
                if (gamePaused) {
                    player.showMessage('⏸️ اللعبة متوقفة', '#ffd700');
                }
            });
            
            // زر الصوت
            soundBtn.addEventListener('click', () => {
                const muted = audioSystem.toggleMute();
                soundBtn.textContent = muted ? '🔇' : '🔊';
            });
            
            // زر المساعدة
            helpBtn.addEventListener('click', () => {
                player.showMessage(
                    '🎮 كيفية اللعب:\n' +
                    '• استخدم الجويستيك للتحرك\n' +
                    '• ⚔️ للهجوم\n' +
                    '• ⬆️ للقفز\n' +
                    '• 💨 للدفعة السريعة\n' +
                    '• 🛡️ للدفاع\n' +
                    '• اختر السلاح من الشريط السفلي',
                    '#00ffff'
                );
            });
        }

        // ============= دورة اللعبة الرئيسية =============
        function gameLoop() {
            if (!gameRunning) {
                requestAnimationFrame(gameLoop);
                return;
            }
            
            // زيادة الوقت
            gameTime++;
            frameCount++;
            
            // تحديث الأنظمة
            if (!gamePaused) {
                inputSystem.update();
                player.update(inputSystem);
                camera.update();
                waveSystem.update();
                particleSystem.update();
                
                // تحديث الأعداء
                enemies.forEach(enemy => enemy.update());
                
                // تحديث الرصاصات
                BulletSystem.update();
                
                // تحديث القوى
                PowerUpSystem.update();
            }
            
            // الرسم
            render();
            
            // الاستمرار في الدورة
            requestAnimationFrame(gameLoop);
        }
        
        function render() {
            // مسح الشاشة
            gameCtx.clearRect(0, 0, GAME_CONFIG.WORLD_WIDTH, GAME_CONFIG.WORLD_HEIGHT);
            
            // تطبيق الكاميرا
            camera.apply();
            
            // رسم الخلفية
            drawEpicBackground();
            
            // رسم الجسيمات
            particleSystem.draw();
            
            // رسم القوى
            PowerUpSystem.draw();
            
            // رسم الرصاصات
            BulletSystem.draw();
            
            // رسم الأعداء
            enemies.forEach(enemy => enemy.draw());
            
            // رسم اللاعب
            player.draw();
            
            // إعادة ضبط الكاميرا
            camera.reset();
        }

        // ============= بدء التحميل =============
        const loadingSystem = new LoadingSystem();
        setTimeout(() => {
            loadingSystem.start();
        }, 500);
        
        // تعديل حجم الكانفاس عند تغيير حجم النافذة
        window.addEventListener('resize', () => {
            if (gameCanvas) {
                gameCanvas.style.width = '100%';
                gameCanvas.style.height = '100%';
            }
        });
    </script>
</body>
</html>

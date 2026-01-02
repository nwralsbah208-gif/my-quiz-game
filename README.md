<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حصار القلعة - المحارب الأسطوري</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            touch-action: manipulation;
            -webkit-tap-highlight-color: transparent;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            background: #000;
            color: white;
            overflow: hidden;
            height: 100vh;
            width: 100vw;
        }
        
        /* الشاشة الرئيسية للعبة */
        #gameScreen {
            width: 100vw;
            height: 100vh;
            position: relative;
            background: linear-gradient(to bottom, #0f0c29, #302b63, #24243e);
            overflow: hidden;
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
        
        /* طبقة واجهة المستخدم فوق الكانفاس */
        .ui-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 2;
            pointer-events: none;
        }
        
        /* شريط المعلومات العلوي */
        .top-bar {
            position: absolute;
            top: 10px;
            left: 10px;
            right: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            background: rgba(0, 0, 0, 0.8);
            padding: 10px 15px;
            border-radius: 15px;
            border: 2px solid #ffd700;
            pointer-events: all;
        }
        
        .stats {
            display: flex;
            gap: 20px;
        }
        
        .stat-item {
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .stat-bar {
            width: 100px;
            height: 12px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 6px;
            overflow: hidden;
            border: 1px solid rgba(255, 255, 255, 0.3);
        }
        
        .stat-fill {
            height: 100%;
            border-radius: 6px;
            transition: width 0.3s;
        }
        
        .health-fill { background: linear-gradient(90deg, #ff0000, #ff4500); }
        .armor-fill { background: linear-gradient(90deg, #4169e1, #00bfff); }
        .stamina-fill { background: linear-gradient(90deg, #32cd32, #7cfc00); }
        
        /* شريط الموجة */
        .wave-bar {
            background: rgba(139, 0, 0, 0.8);
            padding: 8px 15px;
            border-radius: 10px;
            border: 2px solid #ffd700;
            font-weight: bold;
        }
        
        /* منطقة الأزرار المتحركة حول اللاعب */
        .player-controls {
            position: absolute;
            bottom: 120px;
            left: 50%;
            transform: translateX(-50%);
            width: 400px;
            height: 400px;
            z-index: 3;
            pointer-events: none;
        }
        
        /* الأزرار الدائرية حول اللاعب */
        .control-ring {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 350px;
            height: 350px;
            border-radius: 50%;
            border: 2px dashed rgba(255, 215, 0, 0.3);
            pointer-events: all;
        }
        
        .control-btn {
            position: absolute;
            width: 70px;
            height: 70px;
            border-radius: 50%;
            background: linear-gradient(135deg, rgba(139, 0, 0, 0.9), rgba(220, 20, 60, 0.9));
            border: 3px solid #ffd700;
            color: white;
            font-size: 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            user-select: none;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.5);
            transition: all 0.1s;
            pointer-events: all;
            z-index: 4;
        }
        
        .control-btn:active {
            transform: scale(0.85);
            background: linear-gradient(135deg, rgba(220, 20, 60, 0.9), rgba(255, 69, 0, 0.9));
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
        }
        
        /* تحديد مواقع الأزرار حول الدائرة */
        .move-left { transform: translate(-130px, 0); }
        .move-right { transform: translate(130px, 0); }
        .move-up { transform: translate(0, -130px); }
        .move-down { transform: translate(0, 130px); }
        .attack-btn { transform: translate(92px, -92px); }
        .jump-btn { transform: translate(-92px, -92px); }
        .dash-btn { transform: translate(92px, 92px); }
        .block-btn { transform: translate(-92px, 92px); }
        
        /* منطقة اختيار الأسلحة */
        .weapon-selector {
            position: absolute;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 15px;
            background: rgba(0, 0, 0, 0.7);
            padding: 15px;
            border-radius: 20px;
            border: 2px solid #dc143c;
            pointer-events: all;
            z-index: 3;
        }
        
        .weapon-slot {
            width: 60px;
            height: 60px;
            border-radius: 12px;
            background: rgba(50, 50, 50, 0.8);
            border: 2px solid #666;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 28px;
            cursor: pointer;
            transition: all 0.2s;
            position: relative;
        }
        
        .weapon-slot.active {
            border-color: #ffd700;
            background: rgba(139, 0, 0, 0.6);
            box-shadow: 0 0 15px #ffd700;
            transform: scale(1.1);
        }
        
        .weapon-slot.locked {
            opacity: 0.5;
            cursor: not-allowed;
        }
        
        .ammo-count {
            position: absolute;
            bottom: -5px;
            right: -5px;
            background: #dc143c;
            color: white;
            font-size: 12px;
            padding: 2px 6px;
            border-radius: 10px;
            font-weight: bold;
        }
        
        /* المتجر العائم */
        .shop-floating {
            position: absolute;
            top: 80px;
            right: 20px;
            width: 300px;
            background: rgba(0, 0, 0, 0.85);
            border-radius: 15px;
            border: 3px solid #ffd700;
            padding: 15px;
            pointer-events: all;
            z-index: 3;
            display: none;
        }
        
        .shop-toggle {
            position: absolute;
            top: 80px;
            right: 20px;
            width: 60px;
            height: 60px;
            background: linear-gradient(135deg, #8b0000, #dc143c);
            border: 3px solid #ffd700;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 28px;
            cursor: pointer;
            z-index: 3;
            pointer-events: all;
        }
        
        .shop-item {
            background: rgba(50, 50, 50, 0.6);
            border: 1px solid #666;
            padding: 12px;
            margin: 8px 0;
            border-radius: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
            transition: all 0.2s;
        }
        
        .shop-item:hover {
            background: rgba(220, 20, 60, 0.4);
            transform: translateX(-5px);
        }
        
        .shop-item.disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        
        .item-price {
            color: #ffd700;
            font-weight: bold;
            font-size: 18px;
        }
        
        /* تأثيرات الجرافيكس */
        .damage-effect {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(255, 0, 0, 0.3);
            z-index: 10;
            pointer-events: none;
            opacity: 0;
        }
        
        /* تلميحات */
        .hint {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.8);
            color: #ffd700;
            padding: 20px 30px;
            border-radius: 15px;
            border: 2px solid #ffd700;
            text-align: center;
            font-size: 20px;
            z-index: 100;
            display: none;
        }
        
        /* التكيف مع الشاشات الصغيرة */
        @media (max-width: 768px) {
            .player-controls {
                width: 350px;
                height: 350px;
                bottom: 100px;
            }
            
            .control-ring {
                width: 300px;
                height: 300px;
            }
            
            .control-btn {
                width: 60px;
                height: 60px;
                font-size: 20px;
            }
            
            .move-left { transform: translate(-110px, 0); }
            .move-right { transform: translate(110px, 0); }
            .move-up { transform: translate(0, -110px); }
            .move-down { transform: translate(0, 110px); }
            .attack-btn { transform: translate(78px, -78px); }
            .jump-btn { transform: translate(-78px, -78px); }
            .dash-btn { transform: translate(78px, 78px); }
            .block-btn { transform: translate(-78px, 78px); }
            
            .top-bar {
                flex-direction: column;
                gap: 10px;
                padding: 8px;
            }
            
            .stats {
                flex-wrap: wrap;
                justify-content: center;
            }
        }
    </style>
</head>
<body>
    <div id="gameScreen">
        <canvas id="gameCanvas"></canvas>
        
        <div class="ui-layer">
            <!-- شريط المعلومات العلوي -->
            <div class="top-bar">
                <div class="stats">
                    <div class="stat-item">
                        <span>❤️</span>
                        <div class="stat-bar">
                            <div id="healthBar" class="stat-fill health-fill" style="width: 100%"></div>
                        </div>
                        <span id="healthText">100/100</span>
                    </div>
                    
                    <div class="stat-item">
                        <span>🛡️</span>
                        <div class="stat-bar">
                            <div id="armorBar" class="stat-fill armor-fill" style="width: 100%"></div>
                        </div>
                        <span id="armorText">50/50</span>
                    </div>
                    
                    <div class="stat-item">
                        <span>⚡</span>
                        <div class="stat-bar">
                            <div id="staminaBar" class="stat-fill stamina-fill" style="width: 100%"></div>
                        </div>
                        <span id="staminaText">100/100</span>
                    </div>
                </div>
                
                <div class="resource-info">
                    <div style="margin-bottom: 5px;">💰 <span id="goldText">1000</span> ذهب</div>
                    <div>⭐ <span id="scoreText">0</span> نقاط</div>
                </div>
                
                <div class="wave-bar">
                    🌊 الموجة: <span id="waveText">1</span>
                    | 👾 متبقية: <span id="enemiesText">10</span>
                </div>
            </div>
            
            <!-- الأزرار المتحركة حول اللاعب -->
            <div class="player-controls">
                <div class="control-ring"></div>
                
                <!-- أزرار الحركة الأساسية -->
                <div class="control-btn move-left" data-action="moveLeft">←</div>
                <div class="control-btn move-right" data-action="moveRight">→</div>
                <div class="control-btn move-up" data-action="moveUp">↑</div>
                <div class="control-btn move-down" data-action="moveDown">↓</div>
                
                <!-- أزرار الإجراءات الخاصة -->
                <div class="control-btn attack-btn" data-action="attack">⚔️</div>
                <div class="control-btn jump-btn" data-action="jump">⬆️</div>
                <div class="control-btn dash-btn" data-action="dash">💨</div>
                <div class="control-btn block-btn" data-action="block">🛡️</div>
            </div>
            
            <!-- اختيار الأسلحة -->
            <div class="weapon-selector">
                <div class="weapon-slot active" data-weapon="sword" title="السيف الأساسي">
                    🗡️
                </div>
                <div class="weapon-slot" data-weapon="pistol" title="المسدس">
                    🔫<span class="ammo-count" id="pistolAmmo">30</span>
                </div>
                <div class="weapon-slot locked" data-weapon="shotgun" title="بندقية الصيد - تحتاج شراء">
                    🔫
                </div>
                <div class="weapon-slot locked" data-weapon="rifle" title="بندقية القنص - تحتاج شراء">
                    🎯
                </div>
            </div>
            
            <!-- زر فتح المتجر -->
            <div class="shop-toggle" id="shopToggle">🏪</div>
            
            <!-- المتجر العائم -->
            <div class="shop-floating" id="shopPanel">
                <h3 style="text-align: center; margin-bottom: 15px; color: #ffd700;">🏪 متجر المحارب</h3>
                <div class="shop-item" data-item="swordUpgrade">
                    <div>🗡️ سيف فولاذي متطور</div>
                    <div class="item-price">500 💰</div>
                </div>
                <div class="shop-item" data-item="pistolUpgrade">
                    <div>🔫 مسدس ثنائي الرصاص</div>
                    <div class="item-price">800 💰</div>
                </div>
                <div class="shop-item" data-item="shotgunUpgrade">
                    <div>🔫 بندقية الصيد (فتح)</div>
                    <div class="item-price">1200 💰</div>
                </div>
                <div class="shop-item" data-item="rifleUpgrade">
                    <div>🎯 بندقية القنص (فتح)</div>
                    <div class="item-price">2000 💰</div>
                </div>
                <div class="shop-item" data-item="healthUpgrade">
                    <div>❤️ زيادة الصحة +25</div>
                    <div class="item-price">300 💰</div>
                </div>
                <div class="shop-item" data-item="armorUpgrade">
                    <div>🛡️ درع حديدي +25</div>
                    <div class="item-price">400 💰</div>
                </div>
                <div class="shop-item" data-item="staminaUpgrade">
                    <div>⚡ زيادة الطاقة +25</div>
                    <div class="item-price">250 💰</div>
                </div>
                <div class="shop-item" data-item="grenade">
                    <div>💣 حزمة قنابل (3)</div>
                    <div class="item-price">150 💰</div>
                </div>
            </div>
            
            <!-- تأثير الضرر -->
            <div class="damage-effect" id="damageEffect"></div>
            
            <!-- تلميحات اللعبة -->
            <div class="hint" id="gameHint">
                🎮 اضغط على الأزرار حولك للتحكم!
            </div>
        </div>
    </div>

    <script>
        // ============= تهيئة اللعبة =============
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // تحجيم الكانفاس لملء الشاشة كاملة
        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            
            // إعادة ضبط موضع الأزرار
            updateControlPosition();
        }
        
        // نظام الجسيمات المتقدم للجرافيكس
        class ParticleSystem {
            constructor() {
                this.particles = [];
                this.emitters = [];
            }
            
            createEffect(type, x, y, color = '#ff0000', count = 15) {
                for (let i = 0; i < count; i++) {
                    const particle = {
                        x, y,
                        vx: (Math.random() - 0.5) * 12,
                        vy: (Math.random() - 0.5) * 8 - 3,
                        life: 1,
                        color,
                        size: Math.random() * 6 + 2,
                        type: type
                    };
                    
                    if (type === 'blood') {
                        particle.vy -= 2; // قوة دفع للدم
                        particle.color = '#ff0000';
                    } else if (type === 'spark') {
                        particle.color = color;
                        particle.vx *= 0.5;
                        particle.vy *= 0.5;
                    } else if (type === 'heal') {
                        particle.color = '#00ff00';
                        particle.vy = -Math.random() * 4 - 2;
                    }
                    
                    this.particles.push(particle);
                }
            }
            
            createTrail(x, y, color) {
                for (let i = 0; i < 3; i++) {
                    this.particles.push({
                        x: x + (Math.random() - 0.5) * 10,
                        y: y + (Math.random() - 0.5) * 10,
                        vx: (Math.random() - 0.5) * 2,
                        vy: (Math.random() - 0.5) * 2,
                        life: 0.5,
                        color,
                        size: Math.random() * 3 + 1,
                        type: 'trail'
                    });
                }
            }
            
            update() {
                for (let i = this.particles.length - 1; i >= 0; i--) {
                    const p = this.particles[i];
                    
                    p.x += p.vx;
                    p.y += p.vy;
                    p.life -= 0.02;
                    
                    // جاذبية للدم فقط
                    if (p.type === 'blood') {
                        p.vy += 0.2;
                    }
                    
                    if (p.life <= 0) {
                        this.particles.splice(i, 1);
                    }
                }
            }
            
            draw() {
                this.particles.forEach(p => {
                    ctx.globalAlpha = p.life;
                    
                    if (p.type === 'spark' || p.type === 'heal') {
                        // جسيمات متوهجة
                        ctx.fillStyle = p.color;
                        ctx.beginPath();
                        ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                        ctx.fill();
                        
                        // هالة حول الجسيمات
                        ctx.globalAlpha = p.life * 0.3;
                        ctx.beginPath();
                        ctx.arc(p.x, p.y, p.size * 2, 0, Math.PI * 2);
                        ctx.fill();
                    } else {
                        // جسيمات عادية
                        ctx.fillStyle = p.color;
                        ctx.beginPath();
                        ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                        ctx.fill();
                    }
                });
                ctx.globalAlpha = 1;
            }
        }
        
        // نظام الكاميرا الذكية
        class Camera {
            constructor() {
                this.x = 0;
                this.y = 0;
                this.scale = 1;
                this.target = null;
                this.shake = 0;
            }
            
            follow(target) {
                this.target = target;
            }
            
            update() {
                if (this.target) {
                    // تتبع سلس مع تأخير بسيط
                    this.x += (this.target.x - canvas.width/2 - this.x) * 0.1;
                    this.y += (this.target.y - canvas.height/2 - this.y) * 0.1;
                }
                
                // تأثير الاهتزاز
                if (this.shake > 0) {
                    this.x += (Math.random() - 0.5) * this.shake * 2;
                    this.y += (Math.random() - 0.5) * this.shake * 2;
                    this.shake *= 0.9;
                    
                    if (this.shake < 0.1) this.shake = 0;
                }
            }
            
            apply() {
                ctx.save();
                ctx.translate(-this.x, -this.y);
                ctx.scale(this.scale, this.scale);
            }
            
            reset() {
                ctx.restore();
            }
            
            addShake(amount) {
                this.shake = Math.max(this.shake, amount);
            }
        }
        
        // اللاعب - نسخة محسنة برسومات أكبر
        class Player {
            constructor() {
                this.x = canvas.width / 2;
                this.y = canvas.height - 200;
                this.width = 50;
                this.height = 80;
                this.speed = 10;
                this.jumpPower = 20;
                this.dashPower = 25;
                this.velocityY = 0;
                this.gravity = 0.8;
                this.isJumping = false;
                this.isDashing = false;
                this.dashCooldown = 0;
                
                // الإحصائيات
                this.health = 100;
                this.maxHealth = 100;
                this.armor = 50;
                this.maxArmor = 50;
                this.stamina = 100;
                this.maxStamina = 100;
                
                // الأسلحة والذخيرة
                this.weapon = 'sword';
                this.weapons = {
                    sword: { damage: 30, range: 60, cooldown: 400, unlocked: true },
                    pistol: { damage: 25, range: 300, cooldown: 300, unlocked: true, ammo: 30 },
                    shotgun: { damage: 40, range: 150, cooldown: 800, unlocked: false, ammo: 12 },
                    rifle: { damage: 60, range: 500, cooldown: 1000, unlocked: false, ammo: 10 }
                };
                
                this.grenades = 0;
                this.lastAttack = 0;
                
                // الترقيات
                this.upgrades = {
                    sword: 1,
                    pistol: 1,
                    shotgun: 0,
                    rifle: 0
                };
                
                // الموارد
                this.gold = 1000;
                this.score = 0;
                
                // الحالة
                this.isBlocking = false;
                this.blockStamina = 0;
                this.invincible = 0;
                this.combo = 0;
                this.lastComboTime = 0;
            }
            
            update() {
                // الحركة مع الكاميرا
                if (keys.moveLeft && this.x > 100) {
                    this.x -= this.speed;
                    camera.x -= this.speed * 0.5;
                }
                if (keys.moveRight && this.x < canvas.width - 100) {
                    this.x += this.speed;
                    camera.x += this.speed * 0.5;
                }
                if (keys.moveUp && this.y > 100) this.y -= this.speed;
                if (keys.moveDown && this.y < canvas.height - 150) this.y += this.speed;
                
                // القفز
                if (keys.jump && !this.isJumping && this.stamina >= 20) {
                    this.velocityY = -this.jumpPower;
                    this.isJumping = true;
                    this.stamina -= 20;
                    particles.createEffect('spark', this.x, this.y + this.height, '#ffff00', 10);
                }
                
                // الدفعة السريعة
                if (keys.dash && this.dashCooldown <= 0 && this.stamina >= 30) {
                    this.isDashing = true;
                    this.dashCooldown = 60;
                    this.stamina -= 30;
                    
                    const dashX = keys.moveLeft ? -1 : keys.moveRight ? 1 : 0;
                    const dashY = keys.moveUp ? -1 : keys.moveDown ? 1 : 0;
                    
                    this.x += dashX * this.dashPower * 5;
                    this.y += dashY * this.dashPower * 3;
                    
                    particles.createEffect('spark', this.x, this.y, '#00ffff', 20);
                }
                
                // الجاذبية
                this.y += this.velocityY;
                this.velocityY += this.gravity;
                
                // الأرض
                const groundLevel = canvas.height - 150;
                if (this.y > groundLevel - this.height) {
                    this.y = groundLevel - this.height;
                    this.velocityY = 0;
                    this.isJumping = false;
                }
                
                // تحديث التوقيتات
                if (this.dashCooldown > 0) this.dashCooldown--;
                if (this.invincible > 0) this.invincible--;
                
                // استعادة الطاقة
                if (!this.isJumping && !this.isDashing) {
                    this.stamina = Math.min(this.maxStamina, this.stamina + 0.8);
                }
                
                // الدفاع
                if (keys.block && this.stamina >= 2) {
                    this.isBlocking = true;
                    this.stamina -= 2;
                } else {
                    this.isBlocking = false;
                }
                
                // تحديث الواجهة
                this.updateUI();
                
                // تحديث موقع الأزرار حول اللاعب
                updateControlPosition();
            }
            
            attack(enemies) {
                const now = Date.now();
                const weaponData = this.weapons[this.weapon];
                
                if (now - this.lastAttack < weaponData.cooldown) return;
                if (!weaponData.unlocked) return;
                if (this.weapon !== 'sword' && weaponData.ammo <= 0) return;
                
                this.lastAttack = now;
                playSound(this.weapon);
                
                // زيادة الكومبو
                if (now - this.lastComboTime < 2000) {
                    this.combo++;
                } else {
                    this.combo = 1;
                }
                this.lastComboTime = now;
                
                let damage = weaponData.damage * this.upgrades[this.weapon];
                damage *= (1 + this.combo * 0.1); // زيادة الضرر مع الكومبو
                
                switch(this.weapon) {
                    case 'sword':
                        particles.createEffect('spark', this.x + this.width, this.y + 40, '#ffd700', 15);
                        
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
                        break;
                        
                    case 'pistol':
                    case 'shotgun':
                    case 'rifle':
                        weaponData.ammo--;
                        updateAmmoUI();
                        
                        const bulletCount = this.weapon === 'shotgun' ? 8 : 1;
                        const spread = this.weapon === 'shotgun' ? 0.3 : 0.05;
                        
                        for (let i = 0; i < bulletCount; i++) {
                            const bullet = new Bullet(
                                this.x + this.width,
                                this.y + this.height / 2,
                                15 + (this.weapon === 'rifle' ? 10 : 0),
                                damage / (this.weapon === 'shotgun' ? 2 : 1),
                                this.weapon === 'pistol' ? '#ffff00' : 
                                this.weapon === 'shotgun' ? '#ff4500' : '#00ffff',
                                this.weapon,
                                spread
                            );
                            bullets.push(bullet);
                        }
                        
                        particles.createEffect('spark', this.x + this.width, this.y + 40, 
                            this.weapon === 'pistol' ? '#ffff00' : '#ff4500', 10);
                        camera.addShake(this.weapon === 'shotgun' ? 5 : 2);
                        break;
                }
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
                showDamageEffect();
                camera.addShake(3);
                particles.createEffect('blood', this.x + this.width/2, this.y + this.height/2, '#ff0000', 10);
                
                if (this.health <= 0) {
                    gameOver();
                    return true;
                }
                
                this.invincible = 10;
                return false;
            }
            
            heal(amount) {
                this.health = Math.min(this.maxHealth, this.health + amount);
                particles.createEffect('heal', this.x + this.width/2, this.y + this.height/2, '#00ff00', 15);
            }
            
            updateUI() {
                document.getElementById('healthText').textContent = 
                    `${Math.round(this.health)}/${this.maxHealth}`;
                document.getElementById('armorText').textContent = 
                    `${Math.round(this.armor)}/${this.maxArmor}`;
                document.getElementById('staminaText').textContent = 
                    `${Math.round(this.stamina)}/${this.maxStamina}`;
                document.getElementById('goldText').textContent = this.gold;
                document.getElementById('scoreText').textContent = this.score;
                
                document.getElementById('healthBar').style.width = 
                    `${(this.health / this.maxHealth) * 100}%`;
                document.getElementById('armorBar').style.width = 
                    `${(this.armor / this.maxArmor) * 100}%`;
                document.getElementById('staminaBar').style.width = 
                    `${(this.stamina / this.maxStamina) * 100}%`;
                
                // عرض الكومبو
                if (this.combo > 1) {
                    document.getElementById('scoreText').textContent = 
                        `${this.score} (x${this.combo})`;
                }
            }
            
            draw() {
                ctx.save();
                
                // تأثير الرجفة عند الضرر
                if (this.invincible > 0 && this.invincible % 4 < 2) {
                    ctx.globalAlpha = 0.6;
                }
                
                // جسم المحارب
                const gradient = ctx.createLinearGradient(
                    this.x, this.y, 
                    this.x, this.y + this.height
                );
                gradient.addColorStop(0, '#dc143c');
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
                
                // السلاح
                ctx.fillStyle = this.isBlocking ? '#ffd700' : '#666';
                ctx.lineWidth = 3;
                
                if (this.weapon === 'sword') {
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
                } else {
                    ctx.fillRect(this.x + this.width - 5, this.y + 35, 30, 10);
                    ctx.fillRect(this.x + this.width + 20, this.y + 30, 5, 20);
                }
                
                // تأثير الدفاع
                if (this.isBlocking) {
                    ctx.strokeStyle = '#4169e1';
                    ctx.lineWidth = 4;
                    ctx.globalAlpha = 0.6;
                    ctx.beginPath();
                    ctx.arc(this.x + this.width/2, this.y + this.height/2, 60, 0, Math.PI * 2);
                    ctx.stroke();
                }
                
                // تأثير الدفعة السريعة
                if (this.isDashing) {
                    ctx.strokeStyle = '#00ffff';
                    ctx.lineWidth = 3;
                    ctx.globalAlpha = 0.4;
                    ctx.beginPath();
                    ctx.arc(this.x + this.width/2, this.y + this.height/2, 70, 0, Math.PI * 2);
                    ctx.stroke();
                }
                
                ctx.restore();
                
                // رسم الكومبو
                if (this.combo > 1) {
                    ctx.fillStyle = '#ffd700';
                    ctx.font = 'bold 24px Arial';
                    ctx.textAlign = 'center';
                    ctx.fillText(`COMBO x${this.combo}`, this.x + this.width/2, this.y - 40);
                }
            }
        }
        
        // نظام الرصاصات
        class Bullet {
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
            }
            
            update() {
                this.x += this.vx;
                this.y += this.vy;
                
                // إضافة أثر
                this.trail.push({x: this.x, y: this.y});
                if (this.trail.length > 10) this.trail.shift();
                
                // جاذبية خفيفة للرصاصات
                if (this.type === 'shotgun') {
                    this.vy += 0.05;
                }
            }
            
            draw() {
                // رسم الأثر
                ctx.globalAlpha = 0.3;
                this.trail.forEach((pos, i) => {
                    const alpha = i / this.trail.length;
                    ctx.fillStyle = this.color;
                    ctx.beginPath();
                    ctx.arc(pos.x, pos.y, this.width * alpha, 0, Math.PI * 2);
                    ctx.fill();
                });
                
                ctx.globalAlpha = 1;
                
                // الرصاصة نفسها
                ctx.fillStyle = this.color;
                if (this.type === 'rifle') {
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, this.width, 0, Math.PI * 2);
                    ctx.fill();
                    
                    // لمعة الرصاصة القناصة
                    ctx.fillStyle = '#ffffff';
                    ctx.beginPath();
                    ctx.arc(this.x - 2, this.y - 2, 1, 0, Math.PI * 2);
                    ctx.fill();
                } else {
                    ctx.fillRect(this.x - this.width/2, this.y - this.height/2, this.width, this.height);
                }
                
                // ذيل الرصاصة
                ctx.fillStyle = `${this.color}80`;
                ctx.fillRect(this.x - this.width/2 - 8, this.y - this.height/4, 8, this.height/2);
            }
        }
        
        // الأعداء - نسخة محسنة
        class Enemy {
            constructor(wave) {
                this.width = 45;
                this.height = 60;
                this.x = -100; // يبدأ من خارج الشاشة يساراً
                this.y = canvas.height - 150 - this.height + Math.random() * 50;
                this.speed = 1.5 + Math.random() * 1.5 + wave * 0.3;
                this.health = 60 + wave * 15;
                this.maxHealth = 60 + wave * 15;
                this.damage = 12 + wave * 3;
                this.reward = 70 + wave * 15;
                this.type = Math.random() > 0.85 ? 'boss' : 
                           Math.random() > 0.7 ? 'elite' : 'normal';
                this.attackCooldown = 0;
                
                // تعديلات حسب النوع
                if (this.type === 'elite') {
                    this.health *= 1.5;
                    this.maxHealth *= 1.5;
                    this.damage *= 1.3;
                    this.reward *= 1.5;
                    this.speed *= 0.8;
                } else if (this.type === 'boss') {
                    this.health *= 3;
                    this.maxHealth *= 3;
                    this.damage *= 2;
                    this.reward *= 3;
                    this.speed *= 0.6;
                    this.width *= 1.3;
                    this.height *= 1.3;
                }
            }
            
            update(player) {
                // التحرك نحو اللاعب
                const dx = player.x - this.x;
                const dy = player.y - this.y;
                const distance = Math.sqrt(dx * dx + dy * dy);
                
                if (distance > 0) {
                    this.x += (dx / distance) * this.speed;
                    this.y += (dy / distance) * this.speed;
                }
                
                // تحديث توقيت الهجوم
                if (this.attackCooldown > 0) this.attackCooldown--;
                
                // تحقق من الاصطدام باللاعب
                if (distance < 50 && this.attackCooldown <= 0) {
                    this.attackCooldown = 60;
                    return player.takeDamage(this.damage, this);
                }
                
                return false;
            }
            
            takeDamage(amount, source) {
                this.health -= amount;
                
                // تأثيرات الضرر
                particles.createEffect('blood', this.x + this.width/2, this.y + this.height/2, 
                    this.type === 'boss' ? '#8b0000' : '#ff0000', 15);
                
                // إرجاع القوة للمشتتات
                const dx = this.x - source.x;
                const dy = this.y - source.y;
                const dist = Math.sqrt(dx * dx + dy * dy);
                
                if (dist > 0) {
                    this.x += (dx / dist) * 10;
                    this.y += (dy / dist) * 5;
                }
                
                return this.health <= 0;
            }
            
            draw() {
                // جسم العدو
                const gradient = ctx.createLinearGradient(
                    this.x, this.y, 
                    this.x, this.y + this.height
                );
                
                if (this.type === 'boss') {
                    gradient.addColorStop(0, '#ff8c00');
                    gradient.addColorStop(1, '#8b4500');
                } else if (this.type === 'elite') {
                    gradient.addColorStop(0, '#ffd700');
                    gradient.addColorStop(1, '#b8860b');
                } else {
                    gradient.addColorStop(0, '#ffff00');
                    gradient.addColorStop(1, '#b8860b');
                }
                
                ctx.fillStyle = gradient;
                ctx.fillRect(this.x, this.y, this.width, this.height);
                
                // العيون
                ctx.fillStyle = '#000';
                ctx.beginPath();
                ctx.arc(this.x + this.width/2 - 10, this.y + 20, 4, 0, Math.PI * 2);
                ctx.arc(this.x + this.width/2 + 10, this.y + 20, 4, 0, Math.PI * 2);
                ctx.fill();
                
                // الفم
                ctx.strokeStyle = '#000';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.arc(this.x + this.width/2, this.y + 35, 8, 0.2, Math.PI - 0.2);
                ctx.stroke();
                
                // شريط الصحة
                const healthPercent = this.health / this.maxHealth;
                ctx.fillStyle = '#ff0000';
                ctx.fillRect(this.x, this.y - 15, this.width, 8);
                ctx.fillStyle = healthPercent > 0.6 ? '#00ff00' : 
                               healthPercent > 0.3 ? '#ffff00' : '#ff4500';
                ctx.fillRect(this.x, this.y - 15, this.width * healthPercent, 8);
                
                // تاج للبوس
                if (this.type === 'boss') {
                    ctx.fillStyle = '#ffd700';
                    ctx.beginPath();
                    for (let i = 0; i < 5; i++) {
                        const angle = (i * Math.PI * 2) / 5;
                        const spikeX = this.x + this.width/2 + Math.cos(angle) * 20;
                        const spikeY = this.y - 25 + Math.sin(angle) * 10;
                        ctx.lineTo(spikeX, spikeY);
                    }
                    ctx.closePath();
                    ctx.fill();
                }
            }
        }
        
        // ============= نظام الصوت =============
        function playSound(type) {
            try {
                const audioContext = new (window.AudioContext || window.webkitAudioContext)();
                
                if (type === 'sword') {
                    const oscillator = audioContext.createOscillator();
                    const gainNode = audioContext.createGain();
                    oscillator.connect(gainNode);
                    gainNode.connect(audioContext.destination);
                    
                    oscillator.type = 'sawtooth';
                    oscillator.frequency.setValueAtTime(150, audioContext.currentTime);
                    oscillator.frequency.exponentialRampToValueAtTime(50, audioContext.currentTime + 0.3);
                    gainNode.gain.value = 0.2;
                    
                    oscillator.start();
                    oscillator.stop(audioContext.currentTime + 0.3);
                }
                else if (type === 'pistol') {
                    const oscillator = audioContext.createOscillator();
                    const gainNode = audioContext.createGain();
                    oscillator.connect(gainNode);
                    gainNode.connect(audioContext.destination);
                    
                    oscillator.frequency.setValueAtTime(800, audioContext.currentTime);
                    oscillator.frequency.exponentialRampToValueAtTime(200, audioContext.currentTime + 0.1);
                    gainNode.gain.value = 0.1;
                    
                    oscillator.start();
                    oscillator.stop(audioContext.currentTime + 0.1);
                }
            } catch(e) {}
        }
        
        // ============= نظام الموجات =============
        class WaveSystem {
            constructor() {
                this.wave = 1;
                this.enemiesPerWave = 8;
                this.enemiesSpawned = 0;
                this.enemiesDefeated = 0;
                this.spawnTimer = 0;
                this.spawnDelay = 80;
                this.waveCooldown = 0;
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
                    enemies.push(new Enemy(this.wave));
                    this.spawnTimer = 0;
                    
                    updateEnemiesUI();
                }
                
                if (this.enemiesDefeated >= this.enemiesPerWave) {
                    this.nextWave();
                }
            }
            
            nextWave() {
                this.wave++;
                this.enemiesPerWave = 8 + this.wave * 2;
                this.enemiesSpawned = 0;
                this.enemiesDefeated = 0;
                this.waveCooldown = 180; // 3 ثواني بين الموجات
                
                player.gold += 250 * this.wave;
                player.score += 1000 * this.wave;
                player.heal(30); // علاج بين الموجات
                
                document.getElementById('waveText').textContent = this.wave;
                document.getElementById('enemiesText').textContent = this.enemiesPerWave;
                
                showHint(`🎉 اكتملت الموجة ${this.wave-1}! الراحة 3 ثواني`);
            }
        }
        
        // ============= نظام المتجر =============
        class ShopSystem {
            static buyItem(itemType, player) {
                const prices = {
                    swordUpgrade: 500,
                    pistolUpgrade: 800,
                    shotgunUpgrade: 1200,
                    rifleUpgrade: 2000,
                    healthUpgrade: 300,
                    armorUpgrade: 400,
                    staminaUpgrade: 250,
                    grenade: 150
                };
                
                if (player.gold >= prices[itemType]) {
                    player.gold -= prices[itemType];
                    
                    switch(itemType) {
                        case 'swordUpgrade':
                            player.upgrades.sword++;
                            player.weapons.sword.damage += 15;
                            showHint(`🗡️ السيف تم تطويره! الضرر: ${player.weapons.sword.damage}`);
                            break;
                        case 'pistolUpgrade':
                            player.upgrades.pistol++;
                            player.weapons.pistol.damage += 20;
                            player.weapons.pistol.ammo += 15;
                            showHint(`🔫 المسدس تم تطويره!`);
                            break;
                        case 'shotgunUpgrade':
                            player.upgrades.shotgun = 1;
                            player.weapons.shotgun.unlocked = true;
                            player.weapons.shotgun.ammo = 12;
                            unlockWeapon('shotgun');
                            showHint(`🔫 بندقية الصيد متاحة الآن!`);
                            break;
                        case 'rifleUpgrade':
                            player.upgrades.rifle = 1;
                            player.weapons.rifle.unlocked = true;
                            player.weapons.rifle.ammo = 10;
                            unlockWeapon('rifle');
                            showHint(`🎯 بندقية القنص متاحة الآن!`);
                            break;
                        case 'healthUpgrade':
                            player.maxHealth += 25;
                            player.health = player.maxHealth;
                            showHint(`❤️ الصحة زادت إلى ${player.maxHealth}`);
                            break;
                        case 'armorUpgrade':
                            player.maxArmor += 25;
                            player.armor = player.maxArmor;
                            showHint(`🛡️ الدروع زادت إلى ${player.maxArmor}`);
                            break;
                        case 'staminaUpgrade':
                            player.maxStamina += 25;
                            player.stamina = player.maxStamina;
                            showHint(`⚡ الطاقة زادت إلى ${player.maxStamina}`);
                            break;
                        case 'grenade':
                            player.grenades += 3;
                            showHint(`💣 حصلت على 3 قنابل!`);
                            break;
                    }
                    
                    return true;
                } else {
                    showHint('💰 لا تملك ذهباً كافياً!');
                    return false;
                }
            }
        }
        
        // ============= دوال المساعدة =============
        function unlockWeapon(weaponType) {
            const slot = document.querySelector(`[data-weapon="${weaponType}"]`);
            if (slot) {
                slot.classList.remove('locked');
            }
        }
        
        function updateAmmoUI() {
            document.getElementById('pistolAmmo').textContent = player.weapons.pistol.ammo;
            
            const shotgunSlot = document.querySelector('[data-weapon="shotgun"] .ammo-count');
            const rifleSlot = document.querySelector('[data-weapon="rifle"] .ammo-count');
            
            if (shotgunSlot && player.weapons.shotgun.unlocked) {
                shotgunSlot.textContent = player.weapons.shotgun.ammo;
            }
            if (rifleSlot && player.weapons.rifle.unlocked) {
                rifleSlot.textContent = player.weapons.rifle.ammo;
            }
        }
        
        function updateEnemiesUI() {
            const left = waveSystem.enemiesPerWave - waveSystem.enemiesDefeated;
            document.getElementById('enemiesText').textContent = left;
        }
        
        function showDamageEffect() {
            const effect = document.getElementById('damageEffect');
            effect.style.opacity = '0.6';
            
            setTimeout(() => {
                effect.style.opacity = '0';
            }, 200);
        }
        
        function showHint(message) {
            const hint = document.getElementById('gameHint');
            hint.textContent = message;
            hint.style.display = 'block';
            
            setTimeout(() => {
                hint.style.display = 'none';
            }, 2000);
        }
        
        function updateControlPosition() {
            const controls = document.querySelector('.player-controls');
            if (controls && player) {
                controls.style.left = `${player.x}px`;
                controls.style.bottom = `${canvas.height - player.y}px`;
            }
        }
        
        // ============= نهاية اللعبة =============
        function gameOver() {
            gameRunning = false;
            
            setTimeout(() => {
                const message = `💀 انتهت اللعبة!\n\n⭐ النقاط: ${player.score}\n🌊 الموجة: ${waveSystem.wave}\n💰 الذهب: ${player.gold}\n🗡️ الكومبو الأعلى: x${player.combo}`;
                
                if (confirm(message + '\n\nهل تريد إعادة اللعبة؟')) {
                    location.reload();
                }
            }, 500);
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
            // أزرار الحركة
            const actions = ['moveLeft', 'moveRight', 'moveUp', 'moveDown', 'jump', 'dash', 'block', 'attack'];
            
            actions.forEach(action => {
                const btn = document.querySelector(`[data-action="${action}"]`);
                if (btn) {
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
                    
                    // لمنع سحب الصفحة
                    btn.addEventListener('touchmove', (e) => e.preventDefault());
                }
            });
            
            // اختيار الأسلحة
            document.querySelectorAll('.weapon-slot').forEach(slot => {
                if (!slot.classList.contains('locked')) {
                    slot.addEventListener('click', () => {
                        const weapon = slot.dataset.weapon;
                        
                        if (player.weapons[weapon].unlocked) {
                            document.querySelectorAll('.weapon-slot').forEach(s => 
                                s.classList.remove('active'));
                            slot.classList.add('active');
                            player.weapon = weapon;
                            
                            showHint(`✅ تم اختيار ${weapon === 'sword' ? 'السيف' : 
                                    weapon === 'pistol' ? 'المسدس' : 
                                    weapon === 'shotgun' ? 'بندقية الصيد' : 'بندقية القنص'}`);
                        }
                    });
                }
            });
            
            // فتح وإغلاق المتجر
            document.getElementById('shopToggle').addEventListener('click', () => {
                const shop = document.getElementById('shopPanel');
                shop.style.display = shop.style.display === 'block' ? 'none' : 'block';
            });
            
            // شراء من المتجر
            document.querySelectorAll('.shop-item').forEach(item => {
                item.addEventListener('click', () => {
                    if (!item.classList.contains('disabled')) {
                        const itemType = item.dataset.item;
                        const success = ShopSystem.buyItem(itemType, player);
                        
                        if (success) {
                            item.style.background = 'rgba(0, 255, 0, 0.2)';
                            setTimeout(() => {
                                item.style.background = '';
                            }, 300);
                        }
                    }
                });
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
                    case '1': player.weapon = 'sword'; updateWeaponSelection(); break;
                    case '2': if (player.weapons.pistol.unlocked) { player.weapon = 'pistol'; updateWeaponSelection(); } break;
                    case '3': if (player.weapons.shotgun.unlocked) { player.weapon = 'shotgun'; updateWeaponSelection(); } break;
                    case '4': if (player.weapons.rifle.unlocked) { player.weapon = 'rifle'; updateWeaponSelection(); } break;
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
        
        // ============= متغيرات اللعبة =============
        let player, enemies = [], bullets = [], particles, waveSystem, camera;
        let gameRunning = true;
        
        // ============= تهيئة اللعبة =============
        function initGame() {
            resizeCanvas();
            
            player = new Player();
            particles = new ParticleSystem();
            waveSystem = new WaveSystem();
            camera = new Camera();
            
            camera.follow(player);
            
            enemies = [];
            bullets = [];
            
            setupControls();
            updateAmmoUI();
            updateEnemiesUI();
            
            showHint('🎮 حرك الأزرار حول اللاعب للتحكم!\n⚔️ اضغط زر الهجوم للقتال');
            
            gameLoop();
        }
        
        // ============= حلقة اللعبة الرئيسية =============
        function gameLoop() {
            if (!gameRunning) return;
            
            // تطبيق الكاميرا
            camera.update();
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // رسم الخلفية
            const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#0f0c29');
            gradient.addColorStop(0.5, '#302b63');
            gradient.addColorStop(1, '#24243e');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // تطبيق تحويلات الكاميرا
            camera.apply();
            
            // رسم الأرض
            ctx.fillStyle = '#228b22';
            ctx.fillRect(0, canvas.height - 150, canvas.width * 2, 150);
            
            // رسم العشب
            ctx.fillStyle = '#32cd32';
            for (let i = 0; i < 50; i++) {
                const x = (i * 100) % (canvas.width * 2);
                ctx.fillRect(x, canvas.height - 150, 80, 20);
            }
            
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
                    player.gold += enemy.reward;
                    player.score += enemy.reward * 2;
                    waveSystem.enemiesDefeated++;
                    particles.createEffect('blood', enemy.x, enemy.y, 
                        enemy.type === 'boss' ? '#8b0000' : '#ff0000', 30);
                    enemies.splice(i, 1);
                    updateEnemiesUI();
                }
            }
            
            // تحديث الرصاص
            for (let i = bullets.length - 1; i >= 0; i--) {
                const bullet = bullets[i];
                bullet.update();
                
                // تحقق من خروج الرصاصة
                if (bullet.x > camera.x + canvas.width * 1.5 || bullet.x < camera.x - canvas.width * 0.5) {
                    bullets.splice(i, 1);
                    continue;
                }
                
                // تحقق من الاصطدام بالأعداء
                let hit = false;
                for (let j = enemies.length - 1; j >= 0; j--) {
                    const enemy = enemies[j];
                    if (bullet.x < enemy.x + enemy.width &&
                        bullet.x + bullet.width > enemy.x &&
                        bullet.y < enemy.y + enemy.height &&
                        bullet.y + bullet.height > enemy.y) {
                        
                        if (enemy.takeDamage(bullet.damage, player)) {
                            player.gold += enemy.reward;
                            player.score += enemy.reward * 2;
                            waveSystem.enemiesDefeated++;
                            particles.createEffect('blood', enemy.x, enemy.y, 
                                enemy.type === 'boss' ? '#8b0000' : '#ff0000', 40);
                            enemies.splice(j, 1);
                            updateEnemiesUI();
                        }
                        
                        particles.createEffect('spark', bullet.x, bullet.y, bullet.color, 10);
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
            
            requestAnimationFrame(gameLoop);
        }
        
        // ============= بدء اللعبة =============
        window.addEventListener('load', initGame);
        window.addEventListener('resize', resizeCanvas);
    </script>
</body>
</html>

<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حصار القلعة الحمراء - المحارب المتقدم</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            touch-action: manipulation;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #0c0c0c 0%, #1a1a2e 100%);
            color: white;
            overflow: hidden;
            height: 100vh;
        }
        
        .game-wrapper {
            display: flex;
            height: 100vh;
            position: relative;
        }
        
        /* القلعة والعالم */
        .game-world {
            flex: 3;
            position: relative;
            background: linear-gradient(to bottom, #1e3c72 0%, #2a5298 30%, #0f3460 100%);
            overflow: hidden;
            border-right: 3px solid #8b0000;
        }
        
        #gameCanvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
        }
        
        /* اللوحة الجانبية */
        .side-panel {
            flex: 1;
            background: linear-gradient(135deg, #2d3436 0%, #1a1a2e 100%);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 20px;
            border-left: 3px solid #ffd700;
            min-width: 300px;
            max-width: 400px;
        }
        
        .player-stats {
            background: rgba(0, 0, 0, 0.7);
            padding: 15px;
            border-radius: 10px;
            border: 2px solid #dc143c;
        }
        
        .stat-bar {
            background: #333;
            height: 10px;
            border-radius: 5px;
            margin: 5px 0;
            overflow: hidden;
        }
        
        .stat-fill {
            height: 100%;
            border-radius: 5px;
            transition: width 0.3s;
        }
        
        .health-bar { background: linear-gradient(90deg, #ff0000, #ff4500); }
        .armor-bar { background: linear-gradient(90deg, #4169e1, #00bfff); }
        .stamina-bar { background: linear-gradient(90deg, #32cd32, #7cfc00); }
        
        /* المتجر */
        .shop-section {
            background: rgba(0, 0, 0, 0.7);
            padding: 15px;
            border-radius: 10px;
            border: 2px solid #ffd700;
            flex: 1;
            overflow-y: auto;
        }
        
        .shop-item {
            background: rgba(139, 0, 0, 0.3);
            border: 1px solid #8b0000;
            padding: 10px;
            margin: 8px 0;
            border-radius: 8px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            cursor: pointer;
            transition: all 0.2s;
        }
        
        .shop-item:hover {
            background: rgba(220, 20, 60, 0.5);
            transform: translateX(5px);
        }
        
        .shop-item.disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        
        .item-price {
            color: #ffd700;
            font-weight: bold;
        }
        
        /* التحكم */
        .controls {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            margin-top: 10px;
        }
        
        .control-btn {
            flex: 1;
            min-width: 70px;
            padding: 12px;
            background: linear-gradient(135deg, #8b0000, #dc143c);
            border: 2px solid #ffd700;
            color: white;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.2s;
            text-align: center;
        }
        
        .control-btn:active {
            transform: scale(0.95);
            background: linear-gradient(135deg, #dc143c, #ff4500);
        }
        
        .weapon-select {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }
        
        .weapon-btn {
            flex: 1;
            padding: 10px;
            background: rgba(0, 0, 0, 0.5);
            border: 2px solid #666;
            border-radius: 6px;
            cursor: pointer;
            text-align: center;
            transition: all 0.2s;
        }
        
        .weapon-btn.active {
            border-color: #ffd700;
            background: rgba(139, 0, 0, 0.5);
            box-shadow: 0 0 10px #ffd700;
        }
        
        /* الرسومات الخاصة */
        .castle {
            position: absolute;
            bottom: 0;
            right: 100px;
            width: 200px;
            height: 250px;
            z-index: 10;
        }
        
        .castle-gate {
            position: absolute;
            bottom: 0;
            left: 50px;
            width: 100px;
            height: 150px;
            background: linear-gradient(to bottom, #8b4513, #654321);
            border: 5px solid #333;
            border-bottom: none;
            border-radius: 10px 10px 0 0;
        }
        
        .game-overlay {
            position: absolute;
            top: 20px;
            left: 20px;
            background: rgba(0, 0, 0, 0.8);
            padding: 15px;
            border-radius: 10px;
            border: 2px solid #dc143c;
            z-index: 100;
        }
        
        .wave-info {
            position: absolute;
            top: 20px;
            right: 20px;
            background: rgba(0, 0, 0, 0.8);
            padding: 15px;
            border-radius: 10px;
            border: 2px solid #ffd700;
            z-index: 100;
        }
        
        .particles {
            position: absolute;
            pointer-events: none;
            z-index: 5;
        }
        
        /* التحسينات للجوال */
        @media (max-width: 768px) {
            .game-wrapper {
                flex-direction: column;
            }
            
            .side-panel {
                min-width: 100%;
                max-height: 40vh;
            }
        }
    </style>
</head>
<body>
    <div class="game-wrapper">
        <div class="game-world">
            <canvas id="gameCanvas"></canvas>
            <div class="castle-gate" id="castleGate"></div>
            
            <div class="game-overlay">
                <h3>🛡️ المحارب</h3>
                <div>💰 الذهب: <span id="gold">1000</span></div>
                <div>⭐ النقاط: <span id="score">0</span></div>
            </div>
            
            <div class="wave-info">
                <h3>🌊 الموجة</h3>
                <div>الموجة الحالية: <span id="wave">1</span></div>
                <div>الأعداء المتبقية: <span id="enemiesLeft">10</span></div>
            </div>
        </div>
        
        <div class="side-panel">
            <div class="player-stats">
                <h3>👤 إحصائيات المحارب</h3>
                <div>❤️ الصحة: <span id="healthValue">100/100</span></div>
                <div class="stat-bar"><div id="healthBar" class="stat-fill health-bar" style="width: 100%"></div></div>
                
                <div>🛡️ الدروع: <span id="armorValue">50/50</span></div>
                <div class="stat-bar"><div id="armorBar" class="stat-fill armor-bar" style="width: 100%"></div></div>
                
                <div>⚡ الطاقة: <span id="staminaValue">100/100</span></div>
                <div class="stat-bar"><div id="staminaBar" class="stat-fill stamina-bar" style="width: 100%"></div></div>
            </div>
            
            <div class="weapon-select">
                <div class="weapon-btn active" data-weapon="sword">🗡️ سيف</div>
                <div class="weapon-btn" data-weapon="pistol">🔫 مسدس</div>
                <div class="weapon-btn" data-weapon="shotgun">🔫 بندقية</div>
                <div class="weapon-btn" data-weapon="rifle">🎯 بندقية قنص</div>
            </div>
            
            <div class="controls">
                <button class="control-btn" id="moveLeft">← يسار</button>
                <button class="control-btn" id="moveRight">→ يمين</button>
                <button class="control-btn" id="jumpBtn">⬆️ قفز</button>
                <button class="control-btn" id="attackBtn">⚔️ هجوم</button>
                <button class="control-btn" id="dashBtn">💨 اندفاع</button>
                <button class="control-btn" id="blockBtn">🛡️ دفاع</button>
            </div>
            
            <div class="shop-section">
                <h3>🏪 متجر الأسلحة</h3>
                <div class="shop-item" data-item="swordUpgrade">
                    <div>🗡️ سيف فولاذي</div>
                    <div class="item-price">💰 500</div>
                </div>
                <div class="shop-item" data-item="pistolUpgrade">
                    <div>🔫 مسدس ثنائي</div>
                    <div class="item-price">💰 800</div>
                </div>
                <div class="shop-item" data-item="shotgunUpgrade">
                    <div>🔫 بندقية الصيد</div>
                    <div class="item-price">💰 1200</div>
                </div>
                <div class="shop-item" data-item="rifleUpgrade">
                    <div>🎯 بندقية قنص متطورة</div>
                    <div class="item-price">💰 2000</div>
                </div>
                <div class="shop-item" data-item="healthUpgrade">
                    <div>❤️ زيادة الصحة</div>
                    <div class="item-price">💰 300</div>
                </div>
                <div class="shop-item" data-item="armorUpgrade">
                    <div>🛡️ درع حديدي</div>
                    <div class="item-price">💰 400</div>
                </div>
                <div class="shop-item" data-item="staminaUpgrade">
                    <div>⚡ زيادة الطاقة</div>
                    <div class="item-price">💰 250</div>
                </div>
                <div class="shop-item" data-item="grenade">
                    <div>💣 قنبلة يدوية</div>
                    <div class="item-price">💰 150</div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // تهيئة اللعبة
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // تحجيم الكانفاس
        function resizeCanvas() {
            const gameWorld = document.querySelector('.game-world');
            canvas.width = gameWorld.clientWidth;
            canvas.height = gameWorld.clientHeight;
        }
        
        // نظام الجسيمات للجرافيكس
        class ParticleSystem {
            constructor() {
                this.particles = [];
            }
            
            createBlood(x, y, count = 10) {
                for (let i = 0; i < count; i++) {
                    this.particles.push({
                        x, y,
                        vx: (Math.random() - 0.5) * 10,
                        vy: (Math.random() - 0.5) * 10,
                        life: 1,
                        color: '#ff0000',
                        size: Math.random() * 4 + 2
                    });
                }
            }
            
            createSpark(x, y, color = '#ffd700') {
                for (let i = 0; i < 5; i++) {
                    this.particles.push({
                        x, y,
                        vx: (Math.random() - 0.5) * 8,
                        vy: (Math.random() - 0.5) * 8,
                        life: 1,
                        color,
                        size: Math.random() * 3 + 1
                    });
                }
            }
            
            update() {
                for (let i = this.particles.length - 1; i >= 0; i--) {
                    const p = this.particles[i];
                    p.x += p.vx;
                    p.y += p.vy;
                    p.life -= 0.02;
                    p.vy += 0.1; // الجاذبية
                    
                    if (p.life <= 0) {
                        this.particles.splice(i, 1);
                    }
                }
            }
            
            draw() {
                this.particles.forEach(p => {
                    ctx.globalAlpha = p.life;
                    ctx.fillStyle = p.color;
                    ctx.beginPath();
                    ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                    ctx.fill();
                });
                ctx.globalAlpha = 1;
            }
        }
        
        // نظام الصوت
        class AudioSystem {
            constructor() {
                this.sounds = {};
                this.audioContext = new (window.AudioContext || window.webkitAudioContext)();
            }
            
            playWeaponSound(weaponType) {
                try {
                    const oscillator = this.audioContext.createOscillator();
                    const gainNode = this.audioContext.createGain();
                    
                    oscillator.connect(gainNode);
                    gainNode.connect(this.audioContext.destination);
                    
                    switch(weaponType) {
                        case 'sword':
                            oscillator.type = 'sawtooth';
                            oscillator.frequency.value = 100;
                            oscillator.frequency.setValueAtTime(100, this.audioContext.currentTime);
                            oscillator.frequency.exponentialRampToValueAtTime(50, this.audioContext.currentTime + 0.2);
                            gainNode.gain.value = 0.3;
                            oscillator.start();
                            oscillator.stop(this.audioContext.currentTime + 0.2);
                            break;
                            
                        case 'pistol':
                            oscillator.type = 'sine';
                            oscillator.frequency.value = 800;
                            oscillator.frequency.exponentialRampToValueAtTime(200, this.audioContext.currentTime + 0.1);
                            gainNode.gain.value = 0.2;
                            oscillator.start();
                            oscillator.stop(this.audioContext.currentTime + 0.1);
                            break;
                            
                        case 'shotgun':
                            oscillator.type = 'square';
                            oscillator.frequency.value = 200;
                            gainNode.gain.value = 0.4;
                            oscillator.start();
                            oscillator.stop(this.audioContext.currentTime + 0.3);
                            break;
                    }
                } catch(e) {}
            }
        }
        
        // اللاعب
        class Player {
            constructor() {
                this.x = canvas.width / 2;
                this.y = canvas.height - 150;
                this.width = 40;
                this.height = 60;
                this.speed = 8;
                this.jumpPower = 18;
                this.velocityY = 0;
                this.gravity = 0.8;
                this.isJumping = false;
                
                // الإحصائيات
                this.health = 100;
                this.maxHealth = 100;
                this.armor = 50;
                this.maxArmor = 50;
                this.stamina = 100;
                this.maxStamina = 100;
                
                // الأسلحة
                this.weapon = 'sword';
                this.weaponDamage = {
                    sword: 30,
                    pistol: 25,
                    shotgun: 40,
                    rifle: 60
                };
                this.ammo = {
                    pistol: 30,
                    shotgun: 12,
                    rifle: 10
                };
                
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
                this.lastAttack = 0;
                this.attackCooldown = 500;
            }
            
            update() {
                // الحركة الأفقية
                if (keys.left) this.x -= this.speed;
                if (keys.right) this.x += this.speed;
                
                // حدود الشاشة
                this.x = Math.max(100, Math.min(canvas.width - 100, this.x));
                
                // الجاذبية والقفز
                this.y += this.velocityY;
                this.velocityY += this.gravity;
                
                // الأرض
                const groundLevel = canvas.height - 150;
                if (this.y > groundLevel - this.height) {
                    this.y = groundLevel - this.height;
                    this.velocityY = 0;
                    this.isJumping = false;
                }
                
                // استعادة الطاقة
                if (!this.isJumping && !keys.left && !keys.right) {
                    this.stamina = Math.min(this.maxStamina, this.stamina + 0.5);
                }
                
                // تحديث الواجهة
                this.updateUI();
            }
            
            jump() {
                if (!this.isJumping && this.stamina >= 20) {
                    this.velocityY = -this.jumpPower;
                    this.isJumping = true;
                    this.stamina -= 20;
                }
            }
            
            attack(enemies, particles) {
                const now = Date.now();
                if (now - this.lastAttack < this.attackCooldown) return;
                
                this.lastAttack = now;
                const audio = new AudioSystem();
                audio.playWeaponSound(this.weapon);
                
                let damage = this.weaponDamage[this.weapon];
                damage *= this.upgrades[this.weapon] || 1;
                
                switch(this.weapon) {
                    case 'sword':
                        particles.createSpark(this.x + this.width, this.y + 30, '#ffd700');
                        // ضرب الأعداء القريبين
                        enemies.forEach(enemy => {
                            if (Math.abs(enemy.x - this.x) < 100 && 
                                Math.abs(enemy.y - this.y) < 50) {
                                enemy.takeDamage(damage);
                                particles.createBlood(enemy.x, enemy.y);
                            }
                        });
                        break;
                        
                    case 'pistol':
                        if (this.ammo.pistol > 0) {
                            this.ammo.pistol--;
                            particles.createSpark(this.x + this.width, this.y + 20, '#ffff00');
                            // إطلاق رصاصة
                            bullets.push(new Bullet(
                                this.x + this.width,
                                this.y + 30,
                                15,
                                damage,
                                '#ffff00',
                                this.weapon
                            ));
                        }
                        break;
                        
                    case 'shotgun':
                        if (this.ammo.shotgun > 0) {
                            this.ammo.shotgun--;
                            particles.createSpark(this.x + this.width, this.y + 20, '#ff4500');
                            // إطلاق عدة رصاصات
                            for (let i = 0; i < 5; i++) {
                                bullets.push(new Bullet(
                                    this.x + this.width,
                                    this.y + 30,
                                    10 + Math.random() * 5,
                                    damage / 2,
                                    '#ff4500',
                                    this.weapon
                                ));
                            }
                        }
                        break;
                }
                
                this.updateAmmoUI();
            }
            
            takeDamage(amount) {
                if (this.isBlocking) {
                    amount *= 0.3;
                    this.stamina -= 15;
                }
                
                if (this.armor > 0) {
                    const armorDamage = Math.min(amount, this.armor);
                    this.armor -= armorDamage;
                    amount -= armorDamage;
                }
                
                this.health -= amount;
                
                if (this.health <= 0) {
                    gameOver();
                }
            }
            
            updateUI() {
                document.getElementById('healthValue').textContent = 
                    `${Math.round(this.health)}/${this.maxHealth}`;
                document.getElementById('armorValue').textContent = 
                    `${Math.round(this.armor)}/${this.maxArmor}`;
                document.getElementById('staminaValue').textContent = 
                    `${Math.round(this.stamina)}/${this.maxStamina}`;
                document.getElementById('gold').textContent = this.gold;
                document.getElementById('score').textContent = this.score;
                
                document.getElementById('healthBar').style.width = 
                    `${(this.health / this.maxHealth) * 100}%`;
                document.getElementById('armorBar').style.width = 
                    `${(this.armor / this.maxArmor) * 100}%`;
                document.getElementById('staminaBar').style.width = 
                    `${(this.stamina / this.maxStamina) * 100}%`;
            }
            
            updateAmmoUI() {
                // سيتم تحديثها في الواجهة
            }
            
            draw() {
                // جسم المحارب
                ctx.fillStyle = '#dc143c';
                ctx.fillRect(this.x, this.y, this.width, this.height);
                
                // الرأس
                ctx.fillStyle = '#ffb6c1';
                ctx.fillRect(this.x + 10, this.y - 10, this.width - 20, 15);
                
                // السلاح
                ctx.fillStyle = this.isBlocking ? '#ffd700' : '#666';
                switch(this.weapon) {
                    case 'sword':
                        ctx.fillRect(this.x + this.width - 5, this.y + 20, 30, 5);
                        ctx.fillRect(this.x + this.width + 20, this.y + 10, 5, 20);
                        break;
                    case 'pistol':
                        ctx.fillRect(this.x + this.width - 5, this.y + 25, 25, 8);
                        ctx.fillRect(this.x + this.width + 15, this.y + 20, 5, 15);
                        break;
                }
                
                // تأثير الدفاع
                if (this.isBlocking) {
                    ctx.strokeStyle = '#ffd700';
                    ctx.lineWidth = 3;
                    ctx.beginPath();
                    ctx.arc(this.x + this.width/2, this.y + this.height/2, 40, 0, Math.PI * 2);
                    ctx.stroke();
                }
            }
        }
        
        // الرصاص
        class Bullet {
            constructor(x, y, speed, damage, color, type) {
                this.x = x;
                this.y = y;
                this.speed = speed;
                this.damage = damage;
                this.color = color;
                this.type = type;
                this.width = type === 'shotgun' ? 3 : 5;
                this.height = type === 'shotgun' ? 3 : 8;
            }
            
            update() {
                this.x += this.speed;
            }
            
            draw() {
                ctx.fillStyle = this.color;
                ctx.fillRect(this.x, this.y, this.width, this.height);
                
                // ذيل الرصاصة
                ctx.fillStyle = `${this.color}80`;
                ctx.fillRect(this.x - 5, this.y + 2, 5, this.height - 4);
            }
        }
        
        // العدو
        class Enemy {
            constructor(wave) {
                this.width = 35;
                this.height = 45;
                this.x = 50; // البوابة اليسرى
                this.y = canvas.height - 150 - this.height;
                this.speed = 1 + Math.random() * 1 + wave * 0.2;
                this.health = 50 + wave * 10;
                this.maxHealth = 50 + wave * 10;
                this.damage = 10 + wave * 2;
                this.color = '#ffd700';
                this.reward = 50 + wave * 10;
                this.type = Math.random() > 0.8 ? 'strong' : 'normal';
                
                if (this.type === 'strong') {
                    this.health *= 2;
                    this.maxHealth *= 2;
                    this.damage *= 1.5;
                    this.reward *= 2;
                    this.color = '#ff8c00';
                }
            }
            
            update(player) {
                // التحرك نحو اللاعب
                if (this.x < player.x) {
                    this.x += this.speed;
                } else if (this.x > player.x) {
                    this.x -= this.speed;
                }
                
                // تحقق من الاصطدام باللاعب
                if (this.x < player.x + player.width &&
                    this.x + this.width > player.x &&
                    this.y < player.y + player.height &&
                    this.y + this.height > player.y) {
                    
                    player.takeDamage(this.damage);
                    return 'hit';
                }
                
                return 'alive';
            }
            
            takeDamage(amount) {
                this.health -= amount;
                return this.health <= 0;
            }
            
            draw() {
                // جسم العدو
                ctx.fillStyle = this.color;
                ctx.fillRect(this.x, this.y, this.width, this.height);
                
                // العيون
                ctx.fillStyle = '#000';
                ctx.fillRect(this.x + 8, this.y + 10, 6, 6);
                ctx.fillRect(this.x + 21, this.y + 10, 6, 6);
                
                // شريط الصحة
                const healthPercent = this.health / this.maxHealth;
                ctx.fillStyle = '#ff0000';
                ctx.fillRect(this.x, this.y - 10, this.width, 5);
                ctx.fillStyle = healthPercent > 0.5 ? '#00ff00' : 
                               healthPercent > 0.25 ? '#ffff00' : '#ff4500';
                ctx.fillRect(this.x, this.y - 10, this.width * healthPercent, 5);
                
                // العدو القوي له تاج
                if (this.type === 'strong') {
                    ctx.fillStyle = '#ffd700';
                    ctx.beginPath();
                    ctx.moveTo(this.x + this.width/2 - 10, this.y - 5);
                    ctx.lineTo(this.x + this.width/2, this.y - 15);
                    ctx.lineTo(this.x + this.width/2 + 10, this.y - 5);
                    ctx.closePath();
                    ctx.fill();
                }
            }
        }
        
        // نظام الموجات
        class WaveSystem {
            constructor() {
                this.wave = 1;
                this.enemiesPerWave = 10;
                this.enemiesSpawned = 0;
                this.enemiesDefeated = 0;
                this.spawnTimer = 0;
                this.spawnDelay = 60;
            }
            
            update() {
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
                this.enemiesPerWave = 10 + this.wave * 2;
                this.enemiesSpawned = 0;
                this.enemiesDefeated = 0;
                player.gold += 200 * this.wave;
                player.score += 500 * this.wave;
                
                document.getElementById('wave').textContent = this.wave;
                document.getElementById('enemiesLeft').textContent = this.enemiesPerWave;
            }
        }
        
        // نظام المتجر
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
                            player.weaponDamage.sword += 10;
                            break;
                        case 'pistolUpgrade':
                            player.upgrades.pistol++;
                            player.weaponDamage.pistol += 15;
                            break;
                        case 'shotgunUpgrade':
                            player.upgrades.shotgun = 1;
                            player.weaponDamage.shotgun = 40;
                            break;
                        case 'rifleUpgrade':
                            player.upgrades.rifle = 1;
                            player.weaponDamage.rifle = 60;
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
                        case 'grenade':
                            // إضافة قنبلة
                            break;
                    }
                    
                    return true;
                }
                return false;
            }
        }
        
        // متغيرات اللعبة
        let player, enemies = [], bullets = [], particles, waveSystem;
        let keys = {};
        let gameRunning = true;
        
        // تهيئة اللعبة
        function initGame() {
            player = new Player();
            particles = new ParticleSystem();
            waveSystem = new WaveSystem();
            enemies = [];
            bullets = [];
            
            setupControls();
            gameLoop();
        }
        
        // إعداد التحكم
        function setupControls() {
            // لوحة المفاتيح
            document.addEventListener('keydown', (e) => {
                switch(e.key.toLowerCase()) {
                    case 'arrowleft': case 'a': keys.left = true; break;
                    case 'arrowright': case 'd': keys.right = true; break;
                    case ' ': case 'w': player.jump(); break;
                    case 'x': case 'f': player.attack(enemies, particles); break;
                    case 'shift': player.isBlocking = true; break;
                }
            });
            
            document.addEventListener('keyup', (e) => {
                switch(e.key.toLowerCase()) {
                    case 'arrowleft': case 'a': keys.left = false; break;
                    case 'arrowright': case 'd': keys.right = false; break;
                    case 'shift': player.isBlocking = false; break;
                }
            });
            
            // أزرار التحكم
            const setupButton = (id, action) => {
                const btn = document.getElementById(id);
                btn.addEventListener('touchstart', (e) => {
                    e.preventDefault();
                    action(true);
                });
                btn.addEventListener('touchend', (e) => {
                    e.preventDefault();
                    if (id !== 'jumpBtn' && id !== 'attackBtn') action(false);
                });
                btn.addEventListener('mousedown', () => action(true));
                btn.addEventListener('mouseup', () => {
                    if (id !== 'jumpBtn' && id !== 'attackBtn') action(false);
                });
            };
            
            setupButton('moveLeft', (state) => keys.left = state);
            setupButton('moveRight', (state) => keys.right = state);
            setupButton('jumpBtn', () => player.jump());
            setupButton('attackBtn', () => player.attack(enemies, particles));
            setupButton('blockBtn', (state) => player.isBlocking = state);
            setupButton('dashBtn', () => {
                if (player.stamina >= 30) {
                    player.x += keys.left ? -50 : keys.right ? 50 : 0;
                    player.stamina -= 30;
                }
            });
            
            // اختيار الأسلحة
            document.querySelectorAll('.weapon-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    const weapon = btn.dataset.weapon;
                    if (player.upgrades[weapon] > 0 || weapon === 'sword' || weapon === 'pistol') {
                        document.querySelectorAll('.weapon-btn').forEach(b => 
                            b.classList.remove('active'));
                        btn.classList.add('active');
                        player.weapon = weapon;
                    }
                });
            });
            
            // المتجر
            document.querySelectorAll('.shop-item').forEach(item => {
                item.addEventListener('click', () => {
                    if (!item.classList.contains('disabled')) {
                        const itemType = item.dataset.item;
                        if (ShopSystem.buyItem(itemType, player)) {
                            item.style.opacity = '0.5';
                            setTimeout(() => item.style.opacity = '1', 500);
                        }
                    }
                });
            });
        }
        
        // تحديث واجهة الأعداء
        function updateEnemiesUI() {
            const left = waveSystem.enemiesPerWave - waveSystem.enemiesDefeated;
            document.getElementById('enemiesLeft').textContent = left;
        }
        
        // نهاية اللعبة
        function gameOver() {
            gameRunning = false;
            alert(`💀 انتهت اللعبة!\nالنتيجة: ${player.score}\nالموجة: ${waveSystem.wave}`);
            setTimeout(() => {
                if (confirm('هل تريد إعادة اللعبة؟')) {
                    location.reload();
                }
            }, 500);
        }
        
        // حلقة اللعبة
        function gameLoop() {
            if (!gameRunning) return;
            
            // مسح الشاشة
            ctx.fillStyle = '#0f3460';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // رسم الأرض
            ctx.fillStyle = '#228b22';
            ctx.fillRect(0, canvas.height - 150, canvas.width, 150);
            
            // رسم البوابة
            ctx.fillStyle = '#8b4513';
            ctx.fillRect(30, canvas.height - 150, 70, 150);
            ctx.fillStyle = '#654321';
            ctx.fillRect(35, canvas.height - 100, 60, 100);
            
            // تحديث الأنظمة
            particles.update();
            player.update();
            waveSystem.update();
            
            // تحديث الأعداء
            for (let i = enemies.length - 1; i >= 0; i--) {
                const enemy = enemies[i];
                const result = enemy.update(player);
                
                if (result === 'hit') {
                    particles.createBlood(enemy.x, enemy.y);
                    enemies.splice(i, 1);
                    continue;
                }
                
                enemy.draw();
                
                // تحقق من موت العدو
                if (enemy.health <= 0) {
                    player.gold += enemy.reward;
                    player.score += enemy.reward * 2;
                    waveSystem.enemiesDefeated++;
                    particles.createBlood(enemy.x, enemy.y, 20);
                    enemies.splice(i, 1);
                    updateEnemiesUI();
                }
            }
            
            // تحديث الرصاص
            for (let i = bullets.length - 1; i >= 0; i--) {
                const bullet = bullets[i];
                bullet.update();
                
                // تحقق من خروج الرصاصة
                if (bullet.x > canvas.width || bullet.x < 0) {
                    bullets.splice(i, 1);
                    continue;
                }
                
                // تحقق من الاصطدام بالأعداء
                for (let j = enemies.length - 1; j >= 0; j--) {
                    const enemy = enemies[j];
                    if (bullet.x < enemy.x + enemy.width &&
                        bullet.x + bullet.width > enemy.x &&
                        bullet.y < enemy.y + enemy.height &&
                        bullet.y + bullet.height > enemy.y) {
                        
                        if (enemy.takeDamage(bullet.damage)) {
                            player.gold += enemy.reward;
                            player.score += enemy.reward * 2;
                            waveSystem.enemiesDefeated++;
                            particles.createBlood(enemy.x, enemy.y, 20);
                            enemies.splice(j, 1);
                            updateEnemiesUI();
                        }
                        
                        particles.createSpark(bullet.x, bullet.y, bullet.color);
                        bullets.splice(i, 1);
                        break;
                    }
                }
                
                bullet.draw();
            }
            
            // الرسم
            particles.draw();
            player.draw();
            
            requestAnimationFrame(gameLoop);
        }
        
        // بدء اللعبة
        window.addEventListener('load', () => {
            resizeCanvas();
            initGame();
            
            window.addEventListener('resize', () => {
                resizeCanvas();
            });
        });
    </script>
</body>
</html>

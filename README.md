<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>واتساب المملكة - LEX-Ω</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #0a0a0a 0%, #1a1a1a 100%);
            color: #fff;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }

        /* الهيدر الملكي */
        .royal-header {
            background: linear-gradient(135deg, #000 0%, #1a0a0a 100%);
            border-bottom: 3px solid #FFD700;
            padding: 20px;
            text-align: center;
            box-shadow: 0 0 50px rgba(255, 215, 0, 0.3);
        }

        .kingdom-title {
            font-size: 48px;
            font-weight: 900;
            color: #FFD700;
            text-shadow: 0 0 20px #FFD700;
            margin-bottom: 10px;
        }

        .kingdom-subtitle {
            color: #fff;
            opacity: 0.8;
            margin-bottom: 15px;
        }

        .visitor-counter {
            display: inline-block;
            background: #8B0000;
            color: #FFD700;
            padding: 10px 30px;
            border-radius: 50px;
            font-weight: bold;
            border: 2px solid #FFD700;
            margin-top: 10px;
        }

        /* الحاوية الرئيسية */
        .main-container {
            display: flex;
            flex: 1;
            padding: 20px;
            gap: 20px;
            max-width: 1400px;
            margin: 0 auto;
            width: 100%;
        }

        /* الشريط الجانبي */
        .sidebar {
            width: 300px;
            background: rgba(255, 215, 0, 0.05);
            border: 2px solid #FFD700;
            border-radius: 20px;
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .user-profile {
            text-align: center;
            padding: 20px;
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
        }

        .user-avatar {
            width: 80px;
            height: 80px;
            background: linear-gradient(135deg, #FFD700, #8B0000);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 40px;
            margin: 0 auto 15px;
            border: 3px solid #FFD700;
        }

        .user-name {
            font-size: 20px;
            font-weight: bold;
            color: #FFD700;
            margin-bottom: 5px;
        }

        .user-status {
            display: inline-block;
            background: #00ff00;
            color: #000;
            padding: 5px 15px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: bold;
        }

        .visitors-list {
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            padding: 15px;
            flex: 1;
            overflow-y: auto;
            max-height: 400px;
        }

        .visitors-title {
            color: #FFD700;
            font-size: 18px;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid #FFD700;
        }

        .visitor-item {
            display: flex;
            align-items: center;
            padding: 10px;
            margin-bottom: 10px;
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid #333;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .visitor-item:hover {
            background: rgba(255, 215, 0, 0.1);
            border-color: #FFD700;
            transform: translateX(-5px);
        }

        .visitor-item.active {
            background: rgba(255, 215, 0, 0.15);
            border-color: #FFD700;
        }

        .visitor-avatar {
            width: 40px;
            height: 40px;
            background: #333;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-left: 10px;
            font-weight: bold;
        }

        .visitor-info {
            flex: 1;
        }

        .visitor-name {
            font-weight: bold;
            color: #FFD700;
        }

        .visitor-id {
            font-size: 11px;
            color: #666;
        }

        .visitor-online {
            width: 8px;
            height: 8px;
            background: #00ff00;
            border-radius: 50%;
            margin-right: 5px;
        }

        /* منطقة الدردشة */
        .chat-area {
            flex: 1;
            background: rgba(255, 215, 0, 0.05);
            border: 2px solid #FFD700;
            border-radius: 20px;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .chat-header {
            padding: 20px;
            background: rgba(0, 0, 0, 0.3);
            border-bottom: 2px solid #FFD700;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .chat-with {
            font-size: 20px;
            font-weight: bold;
            color: #FFD700;
        }

        .chat-status {
            color: #00ff00;
            font-size: 14px;
        }

        .messages-container {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 10px;
            max-height: 500px;
        }

        .message {
            max-width: 70%;
            padding: 10px 15px;
            border-radius: 10px;
            position: relative;
            animation: fadeIn 0.3s;
        }

        .message.sent {
            background: #FFD700;
            color: #000;
            align-self: flex-end;
            border-bottom-left-radius: 3px;
        }

        .message.received {
            background: #333;
            color: #fff;
            align-self: flex-start;
            border-bottom-right-radius: 3px;
        }

        .message-sender {
            font-size: 11px;
            opacity: 0.7;
            margin-bottom: 3px;
        }

        .message-time {
            font-size: 9px;
            opacity: 0.5;
            margin-top: 3px;
            text-align: left;
        }

        .input-area {
            padding: 20px;
            background: rgba(0, 0, 0, 0.3);
            border-top: 2px solid #FFD700;
            display: flex;
            gap: 10px;
        }

        .message-input {
            flex: 1;
            padding: 12px 20px;
            background: #222;
            border: 2px solid #FFD700;
            border-radius: 30px;
            color: #fff;
            font-size: 14px;
            outline: none;
        }

        .message-input:focus {
            box-shadow: 0 0 20px rgba(255, 215, 0, 0.3);
        }

        .send-btn {
            padding: 12px 30px;
            background: linear-gradient(135deg, #FFD700, #8B0000);
            border: none;
            border-radius: 30px;
            color: #fff;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .send-btn:hover {
            transform: scale(1.05);
        }

        /* نظام تسجيل الدخول */
        .login-overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.95);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 1000;
            backdrop-filter: blur(10px);
        }

        .login-box {
            background: linear-gradient(135deg, #1a1a1a, #000);
            border: 3px solid #FFD700;
            border-radius: 30px;
            padding: 40px;
            width: 400px;
            text-align: center;
            box-shadow: 0 0 100px rgba(255, 215, 0, 0.3);
        }

        .login-title {
            font-size: 32px;
            color: #FFD700;
            margin-bottom: 10px;
        }

        .login-subtitle {
            color: #fff;
            margin-bottom: 30px;
            opacity: 0.8;
        }

        .login-input {
            width: 100%;
            padding: 15px 20px;
            margin-bottom: 20px;
            background: #222;
            border: 2px solid #FFD700;
            border-radius: 30px;
            color: #fff;
            font-size: 16px;
            outline: none;
        }

        .login-btn {
            width: 100%;
            padding: 15px;
            background: linear-gradient(135deg, #FFD700, #8B0000);
            border: none;
            border-radius: 30px;
            color: #fff;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .login-btn:hover {
            transform: scale(1.05);
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>
    <!-- نافذة تسجيل الدخول -->
    <div id="loginOverlay" class="login-overlay">
        <div class="login-box">
            <div class="login-title">👑 واتساب المملكة</div>
            <div class="login-subtitle">أدخل اسمك للدخول إلى المملكة</div>
            <input type="text" id="usernameInput" class="login-input" placeholder="اسمك في المملكة..." maxlength="20">
            <button class="login-btn" onclick="joinKingdom()">دخول المملكة</button>
        </div>
    </div>

    <!-- واجهة الدردشة (مخفية في البداية) -->
    <div id="chatInterface" style="display: none; width: 100%;">
        <div class="royal-header">
            <div class="kingdom-title">👑 واتساب المملكة 👑</div>
            <div class="kingdom-subtitle">مرحباً بك في مملكة LEX-Ω</div>
            <div class="visitor-counter" id="visitorCounter">عدد الزوار المتصلين: 0</div>
        </div>

        <div class="main-container">
            <!-- الشريط الجانبي -->
            <div class="sidebar">
                <div class="user-profile">
                    <div class="user-avatar" id="userAvatar">👤</div>
                    <div class="user-name" id="displayUsername"></div>
                    <div class="user-status" id="userStatus">🟢 متصل</div>
                </div>

                <div class="visitors-list">
                    <div class="visitors-title">🟢 الزوار المتصلين</div>
                    <div id="visitorsContainer"></div>
                </div>
            </div>

            <!-- منطقة الدردشة -->
            <div class="chat-area">
                <div class="chat-header">
                    <span class="chat-with" id="chatWith">اختر شخصاً للتحدث معه</span>
                    <span class="chat-status" id="chatStatus"></span>
                </div>

                <div class="messages-container" id="messagesContainer"></div>

                <div class="input-area">
                    <input type="text" id="messageInput" class="message-input" placeholder="اكتب رسالتك هنا..." onkeypress="handleKeyPress(event)">
                    <button class="send-btn" onclick="sendMessage()">إرسال</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // بيانات المستخدمين والرسائل
        let currentUser = null;
        let currentChatUser = null;
        let visitors = [];
        let messages = {};

        // إنشاء معرف فريد للمستخدم
        function generateUserId() {
            return 'USER-' + Math.random().toString(36).substr(2, 9).toUpperCase();
        }

        // الانضمام إلى المملكة
        function joinKingdom() {
            const username = document.getElementById('usernameInput').value.trim();
            if (!username) {
                alert('الرجاء إدخال اسمك');
                return;
            }

            // إنشاء مستخدم جديد
            currentUser = {
                id: generateUserId(),
                name: username,
                avatar: username.charAt(0).toUpperCase()
            };

            // إخفاء نافذة تسجيل الدخول
            document.getElementById('loginOverlay').style.display = 'none';
            document.getElementById('chatInterface').style.display = 'block';

            // عرض اسم المستخدم
            document.getElementById('displayUsername').textContent = currentUser.name;
            document.getElementById('userAvatar').textContent = currentUser.avatar;

            // إضافة المستخدم إلى قائمة الزوار
            addVisitor(currentUser);

            // بدء محاكاة وصول زوار جدد
            simulateNewVisitors();

            // تحديث عداد الزوار كل ثانية
            setInterval(updateVisitorCounter, 1000);
        }

        // إضافة زائر
        function addVisitor(visitor) {
            if (!visitors.find(v => v.id === visitor.id)) {
                visitors.push(visitor);
                renderVisitors();
                updateVisitorCounter();
                
                // إنشاء مجلد رسائل للزائر الجديد
                if (!messages[visitor.id]) {
                    messages[visitor.id] = [];
                }
            }
        }

        // إزالة زائر
        function removeVisitor(visitorId) {
            visitors = visitors.filter(v => v.id !== visitorId);
            renderVisitors();
            updateVisitorCounter();
            
            if (currentChatUser && currentChatUser.id === visitorId) {
                currentChatUser = null;
                document.getElementById('chatWith').textContent = 'اختر شخصاً للتحدث معه';
                document.getElementById('chatStatus').textContent = '';
            }
        }

        // عرض قائمة الزوار
        function renderVisitors() {
            const container = document.getElementById('visitorsContainer');
            container.innerHTML = '';

            visitors
                .filter(v => v.id !== currentUser?.id)
                .forEach(visitor => {
                    const item = document.createElement('div');
                    item.className = `visitor-item ${currentChatUser && currentChatUser.id === visitor.id ? 'active' : ''}`;
                    item.onclick = () => selectChatUser(visitor);
                    
                    item.innerHTML = `
                        <div class="visitor-avatar">${visitor.avatar}</div>
                        <div class="visitor-info">
                            <div class="visitor-name">${visitor.name}</div>
                            <div class="visitor-id">${visitor.id.substr(0, 8)}</div>
                        </div>
                        <div class="visitor-online"></div>
                    `;
                    
                    container.appendChild(item);
                });
        }

        // تحديث عداد الزوار
        function updateVisitorCounter() {
            document.getElementById('visitorCounter').textContent = `عدد الزوار المتصلين: ${visitors.length}`;
        }

        // اختيار مستخدم للدردشة
        function selectChatUser(user) {
            currentChatUser = user;
            document.getElementById('chatWith').textContent = `محادثة مع ${user.name}`;
            document.getElementById('chatStatus').textContent = '🟢 متصل';
            loadMessages(user.id);
        }

        // تحميل الرسائل
        function loadMessages(userId) {
            const container = document.getElementById('messagesContainer');
            container.innerHTML = '';

            const userMessages = messages[userId] || [];
            userMessages.forEach(msg => {
                addMessageToContainer(msg.text, msg.sender === currentUser.id ? 'sent' : 'received', msg.senderName, msg.time);
            });
            
            container.scrollTop = container.scrollHeight;
        }

        // إضافة رسالة إلى الحاوية
        function addMessageToContainer(text, type, senderName, time) {
            const container = document.getElementById('messagesContainer');
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${type}`;
            
            if (type === 'received') {
                messageDiv.innerHTML = `
                    <div class="message-sender">${senderName}</div>
                    ${text}
                    <div class="message-time">${time}</div>
                `;
            } else {
                messageDiv.innerHTML = `
                    ${text}
                    <div class="message-time">${time}</div>
                `;
            }
            
            container.appendChild(messageDiv);
            container.scrollTop = container.scrollHeight;
        }

        // إرسال رسالة
        function sendMessage() {
            if (!currentChatUser) {
                alert('الرجاء اختيار شخص للتحدث معه أولاً');
                return;
            }

            const input = document.getElementById('messageInput');
            const text = input.value.trim();

            if (text) {
                const now = new Date();
                const time = now.getHours() + ':' + (now.getMinutes() < 10 ? '0' : '') + now.getMinutes();

                // حفظ الرسالة
                if (!messages[currentChatUser.id]) {
                    messages[currentChatUser.id] = [];
                }
                
                messages[currentChatUser.id].push({
                    sender: currentUser.id,
                    senderName: currentUser.name,
                    text: text,
                    time: time
                });

                // عرض الرسالة
                addMessageToContainer(text, 'sent', currentUser.name, time);

                // محاكاة رد تلقائي (اختياري)
                setTimeout(() => {
                    simulateReply(currentChatUser);
                }, 2000);

                input.value = '';
            }
        }

        // محاكاة رد
        function simulateReply(user) {
            const now = new Date();
            const time = now.getHours() + ':' + (now.getMinutes() < 10 ? '0' : '') + now.getMinutes();
            
            const replies = [
                'مرحباً، كيف حالك؟',
                'تسلم على الرسالة',
                'نعم يا صديقي',
                'تحت أمرك',
                'شكراً لك'
            ];
            
            const reply = replies[Math.floor(Math.random() * replies.length)];
            
            if (!messages[user.id]) {
                messages[user.id] = [];
            }
            
            messages[user.id].push({
                sender: user.id,
                senderName: user.name,
                text: reply,
                time: time
            });
            
            if (currentChatUser && currentChatUser.id === user.id) {
                addMessageToContainer(reply, 'received', user.name, time);
            }
        }

        // إرسال بالضغط على Enter
        function handleKeyPress(event) {
            if (event.key === 'Enter') {
                sendMessage();
            }
        }

        // محاكاة وصول زوار جدد
        function simulateNewVisitors() {
            const names = ['محارب', 'فارس', 'حارس', 'سيف', 'رمح', 'درع', 'نبال', 'فتى'];
            
            setInterval(() => {
                if (Math.random() > 0.7) { // 30% فرصة لوصول زائر جديد
                    const randomName = names[Math.floor(Math.random() * names.length)] + Math.floor(Math.random() * 100);
                    const newVisitor = {
                        id: generateUserId(),
                        name: randomName,
                        avatar: randomName.charAt(0).toUpperCase()
                    };
                    addVisitor(newVisitor);
                }
            }, 10000); // كل 10 ثواني

            // محاكاة مغادرة زوار
            setInterval(() => {
                if (visitors.length > 1 && Math.random() > 0.8) { // 20% فرصة لمغادرة زائر
                    const randomVisitor = visitors.find(v => v.id !== currentUser?.id);
                    if (randomVisitor) {
                        removeVisitor(randomVisitor.id);
                    }
                }
            }, 15000); // كل 15 ثانية
        }

        // معالجة إغلاق الصفحة
        window.addEventListener('beforeunload', function() {
            if (currentUser) {
                removeVisitor(currentUser.id);
            }
        });
    </script>
</body>
</html>

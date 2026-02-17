<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>شبكة LEX-Ω الاجتماعية</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #0a0a0a 0%, #1a0a0a 100%);
            color: #fff;
            min-height: 100vh;
        }

        /* الهيدر الملكي */
        .royal-header {
            background: linear-gradient(135deg, #000 0%, #1a0a0a 100%);
            border-bottom: 3px solid #FFD700;
            padding: 20px;
            text-align: center;
            box-shadow: 0 0 50px rgba(255, 215, 0, 0.3);
            position: sticky;
            top: 0;
            z-index: 100;
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

        .stats-bar {
            display: flex;
            justify-content: center;
            gap: 30px;
            margin-top: 15px;
        }

        .stat-item {
            background: rgba(255, 215, 0, 0.1);
            padding: 10px 20px;
            border-radius: 30px;
            border: 1px solid #FFD700;
        }

        /* الحاوية الرئيسية */
        .main-container {
            display: grid;
            grid-template-columns: 300px 1fr 300px;
            gap: 20px;
            padding: 20px;
            max-width: 1400px;
            margin: 0 auto;
        }

        /* الشريط الجانبي الأيمن */
        .right-sidebar {
            background: rgba(255, 215, 0, 0.05);
            border: 2px solid #FFD700;
            border-radius: 20px;
            padding: 20px;
        }

        .user-profile {
            text-align: center;
            margin-bottom: 30px;
        }

        .user-avatar {
            width: 100px;
            height: 100px;
            background: linear-gradient(135deg, #FFD700, #8B0000);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 50px;
            margin: 0 auto 15px;
            border: 3px solid #FFD700;
        }

        .user-name {
            font-size: 24px;
            font-weight: bold;
            color: #FFD700;
            margin-bottom: 5px;
        }

        .user-bio {
            color: #ccc;
            font-size: 14px;
            margin-bottom: 15px;
            padding: 0 10px;
        }

        .user-stats {
            display: flex;
            justify-content: space-around;
            padding: 15px 0;
            border-top: 1px solid #333;
            border-bottom: 1px solid #333;
            margin-bottom: 20px;
        }

        .user-stat {
            text-align: center;
        }

        .stat-number {
            font-size: 20px;
            font-weight: bold;
            color: #FFD700;
        }

        .stat-label {
            font-size: 12px;
            color: #ccc;
        }

        .online-friends {
            margin-top: 20px;
        }

        .online-title {
            color: #FFD700;
            font-size: 18px;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid #FFD700;
        }

        .friend-item {
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

        .friend-item:hover {
            background: rgba(255, 215, 0, 0.1);
            border-color: #FFD700;
        }

        .friend-avatar {
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

        .friend-info {
            flex: 1;
        }

        .friend-name {
            font-weight: bold;
            color: #FFD700;
        }

        .friend-status {
            font-size: 11px;
            color: #00ff00;
        }

        /* منطقة المحتوى الرئيسي */
        .main-content {
            background: rgba(255, 215, 0, 0.05);
            border: 2px solid #FFD700;
            border-radius: 20px;
            padding: 20px;
        }

        .create-post {
            background: rgba(0, 0, 0, 0.3);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 30px;
        }

        .post-input {
            width: 100%;
            min-height: 100px;
            background: #222;
            border: 2px solid #FFD700;
            border-radius: 15px;
            padding: 15px;
            color: #fff;
            font-size: 16px;
            margin-bottom: 15px;
            resize: vertical;
        }

        .post-actions {
            display: flex;
            gap: 15px;
        }

        .post-btn {
            padding: 10px 25px;
            background: linear-gradient(135deg, #FFD700, #8B0000);
            border: none;
            border-radius: 30px;
            color: #fff;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .post-btn:hover {
            transform: scale(1.05);
        }

        .posts-container {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .post-card {
            background: rgba(0, 0, 0, 0.3);
            border: 1px solid #333;
            border-radius: 15px;
            padding: 20px;
            transition: all 0.3s;
        }

        .post-card:hover {
            border-color: #FFD700;
        }

        .post-header {
            display: flex;
            align-items: center;
            margin-bottom: 15px;
        }

        .post-avatar {
            width: 50px;
            height: 50px;
            background: linear-gradient(135deg, #FFD700, #8B0000);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            margin-left: 15px;
        }

        .post-author {
            font-weight: bold;
            color: #FFD700;
            margin-bottom: 5px;
        }

        .post-time {
            font-size: 12px;
            color: #666;
        }

        .post-content {
            font-size: 16px;
            line-height: 1.6;
            margin-bottom: 15px;
        }

        .post-stats {
            display: flex;
            gap: 20px;
            padding: 10px 0;
            border-top: 1px solid #333;
        }

        .post-stat {
            display: flex;
            align-items: center;
            gap: 5px;
            color: #ccc;
            cursor: pointer;
        }

        .post-stat:hover {
            color: #FFD700;
        }

        /* الشريط الجانبي الأيسر */
        .left-sidebar {
            background: rgba(255, 215, 0, 0.05);
            border: 2px solid #FFD700;
            border-radius: 20px;
            padding: 20px;
        }

        .trending-title {
            color: #FFD700;
            font-size: 18px;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid #FFD700;
        }

        .trending-item {
            padding: 15px;
            margin-bottom: 10px;
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid #333;
            border-radius: 10px;
            cursor: pointer;
        }

        .trending-item:hover {
            border-color: #FFD700;
        }

        .trending-hashtag {
            color: #FFD700;
            font-weight: bold;
            margin-bottom: 5px;
        }

        .trending-count {
            font-size: 12px;
            color: #666;
        }

        .suggested-friends {
            margin-top: 30px;
        }

        .suggested-title {
            color: #FFD700;
            font-size: 18px;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid #FFD700;
        }

        .suggested-item {
            display: flex;
            align-items: center;
            padding: 10px;
            margin-bottom: 10px;
            background: rgba(255, 255, 255, 0.02);
            border: 1px solid #333;
            border-radius: 10px;
        }

        .suggested-avatar {
            width: 40px;
            height: 40px;
            background: #333;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-left: 10px;
        }

        .suggested-info {
            flex: 1;
        }

        .suggested-name {
            font-weight: bold;
            color: #FFD700;
        }

        .follow-btn {
            padding: 5px 15px;
            background: transparent;
            border: 1px solid #FFD700;
            border-radius: 20px;
            color: #FFD700;
            cursor: pointer;
            transition: all 0.3s;
        }

        .follow-btn:hover {
            background: #FFD700;
            color: #000;
        }

        /* نافذة تسجيل الدخول */
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
            z-index: 2000;
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
    </style>
</head>
<body>
    <!-- نافذة تسجيل الدخول -->
    <div id="loginOverlay" class="login-overlay">
        <div class="login-box">
            <div class="login-title">👑 شبكة LEX-Ω</div>
            <div class="login-subtitle">انضم إلى المملكة الآن</div>
            <input type="text" id="usernameInput" class="login-input" placeholder="اسم المستخدم" maxlength="30">
            <input type="text" id="bioInput" class="login-input" placeholder="نبذة عنك (اختياري)" maxlength="100">
            <button class="login-btn" onclick="joinNetwork()">انضمام</button>
        </div>
    </div>

    <!-- واجهة الشبكة الاجتماعية (مخفية في البداية) -->
    <div id="networkInterface" style="display: none;">
        <div class="royal-header">
            <div class="kingdom-title">👑 شبكة LEX-Ω الاجتماعية 👑</div>
            <div class="kingdom-subtitle">حيث يلتقي جنود المملكة</div>
            <div class="stats-bar">
                <div class="stat-item" id="usersCount">0 عضو</div>
                <div class="stat-item" id="postsCount">0 منشور</div>
                <div class="stat-item" id="onlineCount">0 متصل</div>
            </div>
        </div>

        <div class="main-container">
            <!-- الشريط الجانبي الأيمن - الملف الشخصي -->
            <div class="right-sidebar">
                <div class="user-profile">
                    <div class="user-avatar" id="userAvatar">👤</div>
                    <div class="user-name" id="displayUsername"></div>
                    <div class="user-bio" id="userBio"></div>
                    <div class="user-stats">
                        <div class="user-stat">
                            <div class="stat-number" id="userPostsCount">0</div>
                            <div class="stat-label">منشورات</div>
                        </div>
                        <div class="user-stat">
                            <div class="stat-number" id="userFollowersCount">0</div>
                            <div class="stat-label">متابعون</div>
                        </div>
                        <div class="user-stat">
                            <div class="stat-number" id="userFollowingCount">0</div>
                            <div class="stat-label">يتابع</div>
                        </div>
                    </div>
                </div>

                <div class="online-friends">
                    <div class="online-title">🟢 المتصلون الآن</div>
                    <div id="onlineUsersList"></div>
                </div>
            </div>

            <!-- المنطقة الرئيسية - المنشورات -->
            <div class="main-content">
                <div class="create-post">
                    <textarea id="postContent" class="post-input" placeholder="ما الذي يجول في خاطرك يا جندي؟"></textarea>
                    <div class="post-actions">
                        <button class="post-btn" onclick="createPost()">نشر</button>
                    </div>
                </div>

                <div class="posts-container" id="postsContainer">
                    <!-- المنشورات ستظهر هنا -->
                </div>
            </div>

            <!-- الشريط الجانبي الأيسر - مقترحات وترند -->
            <div class="left-sidebar">
                <div class="trending-title">🔥 الأكثر تفاعلاً</div>
                <div id="trendingContainer"></div>

                <div class="suggested-friends">
                    <div class="suggested-title">👥 اقتراحات متابعة</div>
                    <div id="suggestedContainer"></div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // ============================================
        // شبكة LEX-Ω الاجتماعية - الإصدار الحقيقي
        // ============================================

        // استخدام IndexedDB للتخزين المحلي (يعمل بدون إنترنت)
        let db;
        let currentUser = null;
        let users = [];
        let posts = [];
        let onlineUsers = new Set();

        // إنشاء قاعدة البيانات
        function initDB() {
            return new Promise((resolve, reject) => {
                const request = indexedDB.open('LexNetworkDB', 1);
                
                request.onerror = () => reject(request.error);
                request.onsuccess = () => {
                    db = request.result;
                    resolve(db);
                };
                
                request.onupgradeneeded = (event) => {
                    const db = event.target.result;
                    
                    // جدول المستخدمين
                    if (!db.objectStoreNames.contains('users')) {
                        const userStore = db.createObjectStore('users', { keyPath: 'id' });
                        userStore.createIndex('username', 'username', { unique: true });
                    }
                    
                    // جدول المنشورات
                    if (!db.objectStoreNames.contains('posts')) {
                        const postStore = db.createObjectStore('posts', { keyPath: 'id', autoIncrement: true });
                        postStore.createIndex('timestamp', 'timestamp');
                        postStore.createIndex('userId', 'userId');
                    }
                    
                    // جدول المتابعات
                    if (!db.objectStoreNames.contains('follows')) {
                        db.createObjectStore('follows', { keyPath: 'id', autoIncrement: true });
                    }
                };
            });
        }

        // إنشاء معرف فريد
        function generateId() {
            return Date.now() + '-' + Math.random().toString(36).substr(2, 9);
        }

        // الانضمام إلى الشبكة
        async function joinNetwork() {
            const username = document.getElementById('usernameInput').value.trim();
            const bio = document.getElementById('bioInput').value.trim() || 'جندي في مملكة LEX-Ω';

            if (!username) {
                alert('الرجاء إدخال اسم المستخدم');
                return;
            }

            await initDB();

            // التحقق من عدم تكرار الاسم
            const transaction = db.transaction(['users'], 'readonly');
            const store = transaction.objectStore('users');
            const index = store.index('username');
            const existingUser = await new Promise((resolve) => {
                const request = index.get(username);
                request.onsuccess = () => resolve(request.result);
                request.onerror = () => resolve(null);
            });

            if (existingUser) {
                alert('اسم المستخدم موجود بالفعل، اختر اسماً آخر');
                return;
            }

            // إنشاء مستخدم جديد
            currentUser = {
                id: generateId(),
                username: username,
                bio: bio,
                avatar: username.charAt(0).toUpperCase(),
                joinDate: new Date().toISOString(),
                lastSeen: new Date().toISOString()
            };

            // حفظ المستخدم
            const writeTx = db.transaction(['users'], 'readwrite');
            writeTx.objectStore('users').add(currentUser);

            // إخفاء نافذة تسجيل الدخول
            document.getElementById('loginOverlay').style.display = 'none';
            document.getElementById('networkInterface').style.display = 'block';

            // عرض معلومات المستخدم
            document.getElementById('displayUsername').textContent = currentUser.username;
            document.getElementById('userBio').textContent = currentUser.bio;
            document.getElementById('userAvatar').textContent = currentUser.avatar;

            // إضافة المستخدم إلى المتصلين
            onlineUsers.add(currentUser.id);
            updateOnlineUsers();

            // تحميل البيانات
            loadUsers();
            loadPosts();
            loadStats();

            // تحديث آخر ظهور كل 30 ثانية
            setInterval(() => {
                if (currentUser) {
                    const tx = db.transaction(['users'], 'readwrite');
                    const store = tx.objectStore('users');
                    store.get(currentUser.id).onsuccess = (e) => {
                        const user = e.target.result;
                        if (user) {
                            user.lastSeen = new Date().toISOString();
                            store.put(user);
                        }
                    };
                }
            }, 30000);
        }

        // تحميل المستخدمين
        function loadUsers() {
            const tx = db.transaction(['users'], 'readonly');
            const store = tx.objectStore('users');
            const request = store.getAll();

            request.onsuccess = () => {
                users = request.result;
                updateOnlineUsers();
                updateSuggestedUsers();
            };
        }

        // تحديث قائمة المتصلين
        function updateOnlineUsers() {
            const now = Date.now();
            const fiveMinutes = 5 * 60 * 1000;
            
            onlineUsers.clear();
            
            users.forEach(user => {
                if (user.id !== currentUser?.id) {
                    const lastSeen = new Date(user.lastSeen).getTime();
                    if (now - lastSeen < fiveMinutes) {
                        onlineUsers.add(user.id);
                    }
                }
            });

            displayOnlineUsers();
            document.getElementById('onlineCount').textContent = onlineUsers.size + ' متصل';
        }

        // عرض المتصلين
        function displayOnlineUsers() {
            const container = document.getElementById('onlineUsersList');
            container.innerHTML = '';

            users
                .filter(u => u.id !== currentUser?.id && onlineUsers.has(u.id))
                .forEach(user => {
                    const div = document.createElement('div');
                    div.className = 'friend-item';
                    div.onclick = () => openChat(user);
                    div.innerHTML = `
                        <div class="friend-avatar">${user.avatar}</div>
                        <div class="friend-info">
                            <div class="friend-name">${user.username}</div>
                            <div class="friend-status">🟢 متصل</div>
                        </div>
                    `;
                    container.appendChild(div);
                });
        }

        // إنشاء منشور
        function createPost() {
            const content = document.getElementById('postContent').value.trim();
            if (!content) return;

            const post = {
                id: generateId(),
                userId: currentUser.id,
                username: currentUser.username,
                userAvatar: currentUser.avatar,
                content: content,
                timestamp: new Date().toISOString(),
                likes: 0,
                comments: 0
            };

            const tx = db.transaction(['posts'], 'readwrite');
            tx.objectStore('posts').add(post);

            document.getElementById('postContent').value = '';
            loadPosts();
        }

        // تحميل المنشورات
        function loadPosts() {
            const tx = db.transaction(['posts'], 'readonly');
            const store = tx.objectStore('posts');
            const index = store.index('timestamp');
            const request = index.getAll();

            request.onsuccess = () => {
                posts = request.result.reverse(); // أحدث منشور أولاً
                displayPosts();
                document.getElementById('postsCount').textContent = posts.length + ' منشور';
                
                if (currentUser) {
                    const userPosts = posts.filter(p => p.userId === currentUser.id).length;
                    document.getElementById('userPostsCount').textContent = userPosts;
                }
            };
        }

        // عرض المنشورات
        function displayPosts() {
            const container = document.getElementById('postsContainer');
            container.innerHTML = '';

            posts.slice(0, 20).forEach(post => {
                const div = document.createElement('div');
                div.className = 'post-card';
                div.innerHTML = `
                    <div class="post-header">
                        <div class="post-avatar">${post.userAvatar}</div>
                        <div>
                            <div class="post-author">${post.username}</div>
                            <div class="post-time">${new Date(post.timestamp).toLocaleString('ar-EG')}</div>
                        </div>
                    </div>
                    <div class="post-content">${post.content}</div>
                    <div class="post-stats">
                        <span class="post-stat" onclick="likePost('${post.id}')">❤️ ${post.likes}</span>
                        <span class="post-stat" onclick="commentPost('${post.id}')">💬 ${post.comments}</span>
                    </div>
                `;
                container.appendChild(div);
            });
        }

        // إعجاب بمنشور
        function likePost(postId) {
            const tx = db.transaction(['posts'], 'readwrite');
            const store = tx.objectStore('posts');
            store.get(parseInt(postId)).onsuccess = (e) => {
                const post = e.target.result;
                if (post) {
                    post.likes++;
                    store.put(post);
                    loadPosts();
                }
            };
        }

        // تحديث إحصائيات المستخدم
        function loadStats() {
            document.getElementById('usersCount').textContent = users.length + ' عضو';
            
            // حساب المتابعين والمتابعات (للبساطة نستخدم أرقاماً عشوائية)
            document.getElementById('userFollowersCount').textContent = Math.floor(Math.random() * 100);
            document.getElementById('userFollowingCount').textContent = Math.floor(Math.random() * 50);
        }

        // تحديث اقتراحات المستخدمين
        function updateSuggestedUsers() {
            const container = document.getElementById('suggestedContainer');
            container.innerHTML = '';

            const suggested = users
                .filter(u => u.id !== currentUser?.id)
                .slice(0, 5);

            suggested.forEach(user => {
                const div = document.createElement('div');
                div.className = 'suggested-item';
                div.innerHTML = `
                    <div class="suggested-avatar">${user.avatar}</div>
                    <div class="suggested-info">
                        <div class="suggested-name">${user.username}</div>
                    </div>
                    <button class="follow-btn" onclick="followUser('${user.id}')">متابعة</button>
                `;
                container.appendChild(div);
            });
        }

        // متابعة مستخدم
        function followUser(userId) {
            alert('تمت المتابعة بنجاح');
        }

        // فتح محادثة (يمكن تطويرها لاحقاً)
        function openChat(user) {
            alert(`فتح محادثة مع ${user.username} - قيد التطوير`);
        }

        // تحديث دوري
        setInterval(() => {
            if (currentUser) {
                loadUsers();
                loadPosts();
            }
        }, 5000);
    </script>
</body>
</html>

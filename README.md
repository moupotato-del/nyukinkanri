<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>カラフル落書き掲示板</title>
    <style>
        :root {
            --primary-color: #ff3366;
            --secondary-color: #00f0ff;
            --bg-color: #121214;
            --card-bg: #1e1e24;
            --text-color: #f0f0f5;
            --border-color: #2d2d38;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            display: flex;
            flex-direction: column;
            height: 100vh;
            height: 100dvh;
            overflow: hidden;
        }

        header {
            background: rgba(30, 30, 36, 0.9);
            backdrop-filter: blur(10px);
            padding: 12px 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid var(--border-color);
            z-index: 10;
        }

        header h1 {
            font-size: 1.1rem;
            font-weight: 700;
            background: linear-gradient(45deg, var(--primary-color), var(--secondary-color));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .main-container {
            flex: 1;
            position: relative;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        .view-section {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: none;
            flex-direction: column;
            background-color: var(--bg-color);
        }

        .view-section.active {
            display: flex;
        }

        /* --- Draw View --- */
        .canvas-container {
            flex: 1;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 10px;
            background: #0b0b0e;
            position: relative;
            overflow: hidden;
        }

        #drawCanvas {
            background: #18181c;
            border-radius: 12px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.5);
            touch-action: none;
            cursor: crosshair;
        }

        .tool-panel {
            background: var(--card-bg);
            padding: 12px 16px;
            border-top: 1px solid var(--border-color);
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .palette-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 8px;
            overflow-x: auto;
            padding-bottom: 4px;
        }

        .color-btn {
            width: 32px;
            height: 32px;
            border-radius: 50%;
            border: 2px solid rgba(255,255,255,0.2);
            cursor: pointer;
            flex-shrink: 0;
            transition: transform 0.1s;
        }

        .color-btn.active {
            transform: scale(1.2);
            border-color: #fff;
            box-shadow: 0 0 10px rgba(255,255,255,0.5);
        }

        .color-picker-wrapper {
            position: relative;
            width: 32px;
            height: 32px;
            border-radius: 50%;
            overflow: hidden;
            border: 2px solid rgba(255,255,255,0.2);
            flex-shrink: 0;
            background: conic-gradient(red, yellow, lime, cyan, blue, magenta, red);
        }

        .color-picker-wrapper input[type="color"] {
            position: absolute;
            top: -50%;
            left: -50%;
            width: 200%;
            height: 200%;
            border: none;
            cursor: pointer;
        }

        .tools-row {
            display: flex;
            gap: 8px;
            align-items: center;
        }

        .range-slider {
            flex: 1;
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.8rem;
            color: #aaa;
        }

        .range-slider input {
            flex: 1;
            accent-color: var(--primary-color);
        }

        .btn {
            background: #2a2a35;
            color: var(--text-color);
            border: 1px solid var(--border-color);
            padding: 8px 14px;
            border-radius: 8px;
            font-size: 0.85rem;
            font-weight: 600;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 4px;
            transition: background 0.2s;
        }

        .btn:active {
            transform: scale(0.96);
        }

        .btn-primary {
            background: linear-gradient(135deg, var(--primary-color), #ff6b8b);
            color: #fff;
            border: none;
        }

        .btn-neon {
            background: transparent;
            border: 1px solid var(--secondary-color);
            color: var(--secondary-color);
        }
        .btn-neon.active {
            background: var(--secondary-color);
            color: #000;
            box-shadow: 0 0 12px rgba(0, 240, 255, 0.4);
        }

        .action-row {
            display: flex;
            gap: 10px;
        }

        .action-row .btn {
            flex: 1;
            padding: 10px;
        }

        /* --- Gallery View --- */
        .gallery-container {
            flex: 1;
            overflow-y: auto;
            padding: 16px;
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
            gap: 12px;
            align-content: start;
        }

        .post-card {
            background: var(--card-bg);
            border-radius: 12px;
            overflow: hidden;
            border: 1px solid var(--border-color);
            display: flex;
            flex-direction: column;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .post-card:active {
            transform: scale(0.98);
        }

        .post-card img {
            width: 100%;
            aspect-ratio: 1;
            object-fit: cover;
            background: #000;
        }

        .post-info {
            padding: 10px;
            display: flex;
            flex-direction: column;
            gap: 4px;
        }

        .post-title {
            font-size: 0.9rem;
            font-weight: 700;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .post-author {
            font-size: 0.75rem;
            color: #aaa;
        }

        .post-meta {
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 0.75rem;
            color: #888;
            margin-top: 4px;
        }

        /* --- Footer Nav Tabs --- */
        nav {
            height: 64px;
            background: rgba(30, 30, 36, 0.95);
            backdrop-filter: blur(10px);
            border-top: 1px solid var(--border-color);
            display: flex;
            justify-content: space-around;
            align-items: center;
            z-index: 10;
        }

        .nav-tab {
            flex: 1;
            height: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: #777;
            font-size: 0.75rem;
            gap: 4px;
            cursor: pointer;
            transition: color 0.2s;
        }

        .nav-tab svg {
            width: 22px;
            height: 22px;
            fill: currentColor;
        }

        .nav-tab.active {
            color: var(--primary-color);
            font-weight: bold;
        }

        /* --- Modal / Post Form --- */
        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.8);
            backdrop-filter: blur(5px);
            display: none;
            justify-content: center;
            align-items: flex-end;
            z-index: 100;
        }

        .modal.active {
            display: flex;
        }

        .modal-content {
            background: var(--card-bg);
            width: 100%;
            max-height: 90vh;
            border-radius: 20px 20px 0 0;
            padding: 24px;
            display: flex;
            flex-direction: column;
            gap: 16px;
            animation: slideUp 0.3s ease-out;
            overflow-y: auto;
        }

        @keyframes slideUp {
            from { transform: translateY(100%); }
            to { transform: translateY(0); }
        }

        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .modal-header h2 {
            font-size: 1.1rem;
        }

        .close-btn {
            background: none;
            border: none;
            color: #aaa;
            font-size: 1.5rem;
            cursor: pointer;
        }

        .form-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .form-group label {
            font-size: 0.8rem;
            color: #aaa;
        }

        .form-group input, .form-group textarea {
            background: #121214;
            border: 1px solid var(--border-color);
            padding: 12px;
            border-radius: 8px;
            color: var(--text-color);
            font-size: 0.95rem;
            outline: none;
        }

        .form-group input:focus, .form-group textarea:focus {
            border-color: var(--primary-color);
        }

        .preview-img-container {
            width: 120px;
            height: 120px;
            border-radius: 8px;
            overflow: hidden;
            margin: 0 auto;
            border: 1px solid var(--border-color);
            background: #000;
        }

        .preview-img-container img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        /* Detail Modal specific */
        .detail-view img {
            width: 100%;
            border-radius: 12px;
            border: 1px solid var(--border-color);
            background: #000;
        }

        .comments-section {
            display: flex;
            flex-direction: column;
            gap: 8px;
            max-height: 150px;
            overflow-y: auto;
            background: #121214;
            padding: 10px;
            border-radius: 8px;
            border: 1px solid var(--border-color);
        }

        .comment-item {
            font-size: 0.85rem;
            border-bottom: 1px solid rgba(255,255,255,0.05);
            padding-bottom: 4px;
        }

        .comment-item:last-child {
            border-bottom: none;
        }

        .comment-input-row {
            display: flex;
            gap: 8px;
        }

        .comment-input-row input {
            flex: 1;
            background: #121214;
            border: 1px solid var(--border-color);
            padding: 8px 12px;
            border-radius: 8px;
            color: var(--text-color);
            font-size: 0.9rem;
            outline: none;
        }
    </style>
</head>
<body>

    <header>
        <h1>🎨 落書き掲示板</h1>
        <div id="header-action"></div>
    </header>

    <div class="main-container">
        <!-- お絵描きビュー -->
        <section id="view-draw" class="view-section active">
            <div class="canvas-container" id="canvasWrapper">
                <canvas id="drawCanvas"></canvas>
            </div>
            <div class="tool-panel">
                <div class="palette-row">
                    <button class="color-btn active" style="background: #ffffff;" data-color="#ffffff"></button>
                    <button class="color-btn" style="background: #ff3366;" data-color="#ff3366"></button>
                    <button class="color-btn" style="background: #ff9900;" data-color="#ff9900"></button>
                    <button class="color-btn" style="background: #ffee00;" data-color="#ffee00"></button>
                    <button class="color-btn" style="background: #00ff66;" data-color="#00ff66"></button>
                    <button class="color-btn" style="background: #00f0ff;" data-color="#00f0ff"></button>
                    <button class="color-btn" style="background: #9933ff;" data-color="#9933ff"></button>
                    <button class="color-btn" style="background: #000000;" data-color="#000000"></button>
                    <div class="color-picker-wrapper" title="自由な色">
                        <input type="color" id="colorPicker" value="#ff3366">
                    </div>
                </div>
                <div class="tools-row">
                    <div class="range-slider">
                        <span>太さ</span>
                        <input type="range" id="brushSize" min="2" max="40" value="8">
                    </div>
                    <button class="btn btn-neon" id="neonToggle">⚡ ネオン</button>
                    <button class="btn" id="eraserBtn">🧹 消しゴム</button>
                </div>
                <div class="action-row">
                    <button class="btn" id="clearBtn">🗑️ クリア</button>
                    <button class="btn btn-primary" id="openPostModalBtn">✨ 投稿する</button>
                </div>
            </div>
        </section>

        <!-- ギャラリービュー -->
        <section id="view-gallery" class="view-section">
            <div class="gallery-container" id="galleryContainer">
                <!-- 投稿カードがここに入ります -->
            </div>
        </section>
    </div>

    <!-- 下部ナビゲーションバー -->
    <nav>
        <div class="nav-tab active" data-target="view-draw">
            <svg viewBox="0 0 24 24"><path d="M3 17.25V21h3.75L17.81 9.94l-3.75-3.75L3 17.25zM21.41 6.34l-3.75-3.75-2.53 2.54 3.75 3.75 2.53-2.54z"/></svg>
            <span>お絵描き</span>
        </div>
        <div class="nav-tab" data-target="view-gallery">
            <svg viewBox="0 0 24 24"><path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zm0 16H5V5h14v14zm-5.04-6.71l-2.75 3.54-1.96-2.36L6.5 17h11l-3.54-4.71z"/></svg>
            <span>ギャラリー</span>
        </div>
    </nav>

    <!-- 投稿モーダル -->
    <div class="modal" id="postModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2>作品を投稿する</h2>
                <button class="close-btn" id="closePostModal">&times;</button>
            </div>
            <div class="preview-img-container">
                <img id="previewImg" src="" alt="プレビュー">
            </div>
            <div class="form-group">
                <label>作品タイトル</label>
                <input type="text" id="postTitle" placeholder="例: 爆走ネオンキャット" maxlength="30">
            </div>
            <div class="form-group">
                <label>ペンネーム</label>
                <input type="text" id="postAuthor" placeholder="例: 名無しの絵師" maxlength="20">
            </div>
            <button class="btn btn-primary" id="submitPostBtn" style="width: 100%; padding: 12px;">掲示板にアップロード</button>
        </div>
    </div>

    <!-- 詳細・コメントモーダル -->
    <div class="modal" id="detailModal">
        <div class="modal-content">
            <div class="modal-header">
                <h2 id="detailTitleText">作品詳細</h2>
                <button class="close-btn" id="closeDetailModal">&times;</button>
            </div>
            <div class="detail-view">
                <img id="detailImg" src="" alt="詳細画像">
            </div>
            <div style="display: flex; justify-content: space-between; align-items: center;">
                <div>
                    <div id="detailAuthor" style="font-size: 0.85rem; color: #aaa;"></div>
                    <div id="detailDate" style="font-size: 0.75rem; color: #666;"></div>
                </div>
                <button class="btn" id="likeBtn">❤️ <span id="likeCount">0</span></button>
            </div>
            <div class="form-group">
                <label>コメント ({<span id="commentCount">0</span>})</label>
                <div class="comments-section" id="commentsList"></div>
            </div>
            <div class="comment-input-row">
                <input type="text" id="commentInput" placeholder="コメントを入力..." maxlength="50">
                <button class="btn btn-primary" id="sendCommentBtn">送信</button>
            </div>
        </div>
    </div>

    <script>
        // --- キャンバス初期設定 (スマホ画面幅に合わせる) ---
        const canvas = document.getElementById('drawCanvas');
        const ctx = canvas.getContext('2d');
        const canvasWrapper = document.getElementById('canvasWrapper');

        let canvasSize = 0;
        function initCanvasSize() {
            const maxWidth = canvasWrapper.clientWidth - 20;
            const maxHeight = canvasWrapper.clientHeight - 20;
            canvasSize = Math.min(maxWidth, maxHeight, 400);
            
            // 現在の描画内容を保存してリサイズ
            const tempCanvas = document.createElement('canvas');
            const tempCtx = tempCanvas.getContext('2d');
            tempCanvas.width = canvas.width || canvasSize;
            tempCanvas.height = canvas.height || canvasSize;
            if(canvas.width > 0) tempCtx.drawImage(canvas, 0, 0);

            canvas.width = canvasSize;
            canvas.height = canvasSize;

            ctx.fillStyle = '#18181c';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            if(canvas.width > 0) {
                ctx.drawImage(tempCanvas, 0, 0, tempCanvas.width, tempCanvas.height, 0, 0, canvas.width, canvas.height);
            }
        }
        window.addEventListener('resize', initCanvasSize);
        window.addEventListener('DOMContentLoaded', () => {
            initCanvasSize();
            loadGallery();
        });

        // 描画ステート
        let isDrawing = false;
        let currentColor = '#ffffff';
        let currentSize = 8;
        let isNeon = false;
        let isEraser = false;
        let lastX = 0;
        let lastY = 0;

        // --- 描画イベント (タッチ & マウス) ---
        function getPos(e) {
            const rect = canvas.getBoundingClientRect();
            const clientX = e.touches ? e.touches[0].clientX : e.clientX;
            const clientY = e.touches ? e.touches[0].clientY : e.clientY;
            return {
                x: clientX - rect.left,
                y: clientY - rect.top
            };
        }

        function startDraw(e) {
            isDrawing = true;
            const pos = getPos(e);
            lastX = pos.x;
            lastY = pos.y;
        }

        function draw(e) {
            if (!isDrawing) return;
            e.preventDefault();
            const pos = getPos(e);

            ctx.beginPath();
            ctx.moveTo(lastX, lastY);
            ctx.lineTo(pos.x, pos.y);

            if (isEraser) {
                ctx.strokeStyle = '#18181c';
                ctx.lineWidth = currentSize * 2;
                ctx.shadowBlur = 0;
                ctx.globalCompositeOperation = 'source-over';
            } else {
                ctx.strokeStyle = currentColor;
                ctx.lineWidth = currentSize;
                if (isNeon) {
                    ctx.shadowColor = currentColor;
                    ctx.shadowBlur = 15;
                } else {
                    ctx.shadowBlur = 0;
                }
                ctx.globalCompositeOperation = 'source-over';
            }

            ctx.lineCap = 'round';
            ctx.lineJoin = 'round';
            ctx.stroke();

            lastX = pos.x;
            lastY = pos.y;
        }

        function stopDraw() {
            isDrawing = false;
            ctx.shadowBlur = 0; // シャドウリセット
        }

        canvas.addEventListener('mousedown', startDraw);
        canvas.addEventListener('mousemove', draw);
        canvas.addEventListener('mouseup', stopDraw);
        canvas.addEventListener('mouseleave', stopDraw);

        canvas.addEventListener('touchstart', startDraw, { passive: false });
        canvas.addEventListener('touchmove', draw, { passive: false });
        canvas.addEventListener('touchend', stopDraw);

        // --- ツール操作 ---
        document.querySelectorAll('.color-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                document.querySelectorAll('.color-btn').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
                currentColor = btn.getAttribute('data-color');
                isEraser = false;
                document.getElementById('eraserBtn').style.borderColor = 'var(--border-color)';
            });
        });

        const colorPicker = document.getElementById('colorPicker');
        colorPicker.addEventListener('input', (e) => {
            currentColor = e.target.value;
            document.querySelectorAll('.color-btn').forEach(b => b.classList.remove('active'));
            isEraser = false;
        });

        const brushSizeInput = document.getElementById('brushSize');
        brushSizeInput.addEventListener('input', (e) => {
            currentSize = e.target.value;
        });

        const neonToggle = document.getElementById('neonToggle');
        neonToggle.addEventListener('click', () => {
            isNeon = !isNeon;
            neonToggle.classList.toggle('active', isNeon);
        });

        const eraserBtn = document.getElementById('eraserBtn');
        eraserBtn.addEventListener('click', () => {
            isEraser = !isEraser;
            eraserBtn.style.borderColor = isEraser ? 'var(--primary-color)' : 'var(--border-color)';
        });

        document.getElementById('clearBtn').addEventListener('click', () => {
            if(confirm('キャンバスをクリアしますか？')) {
                ctx.fillStyle = '#18181c';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
            }
        });

        // --- タブ切り替え ---
        const tabs = document.querySelectorAll('.nav-tab');
        tabs.forEach(tab => {
            tab.addEventListener('click', () => {
                tabs.forEach(t => t.classList.remove('active'));
                tab.classList.add('active');

                const targetId = tab.getAttribute('data-target');
                document.querySelectorAll('.view-section').forEach(sec => {
                    sec.classList.remove('active');
                });
                document.getElementById(targetId).classList.add('active');

                if(targetId === 'view-gallery') {
                    loadGallery();
                }
            });
        });

        // --- 投稿機能 & LocalStorage ---
        const postModal = document.getElementById('postModal');
        document.getElementById('openPostModalBtn').addEventListener('click', () => {
            document.getElementById('previewImg').src = canvas.toDataURL('image/png');
            postModal.classList.add('active');
        });
        document.getElementById('closePostModal').addEventListener('click', () => {
            postModal.classList.remove('active');
        });

        document.getElementById('submitPostBtn').addEventListener('click', () => {
            const title = document.getElementById('postTitle').value.trim() || '無題の作品';
            const author = document.getElementById('postAuthor').value.trim() || '名無し';
            const imageData = canvas.toDataURL('image/png');

            const newPost = {
                id: Date.now(),
                title,
                author,
                imageData,
                date: new Date().toLocaleDateString('ja-JP'),
                likes: 0,
                liked: false,
                comments: []
            };

            let posts = JSON.parse(localStorage.getItem('graffiti_posts') || '[]');
            posts.unshift(newPost);
            localStorage.setItem('graffiti_posts', JSON.stringify(posts));

            postModal.classList.remove('active');
            document.getElementById('postTitle').value = '';
            
            // ギャラリーに切り替え
            tabs[1].click();
        });

        // --- ギャラリー描画 ---
        function loadGallery() {
            const container = document.getElementById('galleryContainer');
            const posts = JSON.parse(localStorage.getItem('graffiti_posts') || '[]');

            if(posts.length === 0) {
                container.innerHTML = `<div style="grid-column: 1/-1; text-align: center; color: #777; padding: 40px;">まだ投稿がありません。<br>最初にお絵描きして投稿しよう！</div>`;
                return;
            }

            container.innerHTML = '';
            posts.forEach(post => {
                const card = document.createElement('div');
                card.className = 'post-card';
                card.innerHTML = `
                    <img src="${post.imageData}" alt="${post.title}">
                    <div class="post-info">
                        <div class="post-title">${escapeHTML(post.title)}</div>
                        <div class="post-author">${escapeHTML(post.author)}</div>
                        <div class="post-meta">
                            <span>❤️ ${post.likes}</span>
                            <span>💬 ${post.comments.length}</span>
                        </div>
                    </div>
                `;
                card.addEventListener('click', () => openDetailModal(post.id));
                container.appendChild(card);
            });
        }

        // --- 詳細・コメントモーダル ---
        const detailModal = document.getElementById('detailModal');
        let currentPostId = null;

        function openDetailModal(postId) {
            currentPostId = postId;
            const posts = JSON.parse(localStorage.getItem('graffiti_posts') || '[]');
            const post = posts.find(p => p.id === postId);
            if(!post) return;

            document.getElementById('detailTitleText').textContent = post.title;
            document.getElementById('detailImg').src = post.imageData;
            document.getElementById('detailAuthor').textContent = `作者: ${post.author}`;
            document.getElementById('detailDate').textContent = post.date;
            document.getElementById('likeCount').textContent = post.likes;
            
            const likeBtn = document.getElementById('likeBtn');
            likeBtn.style.borderColor = post.liked ? 'var(--primary-color)' : 'var(--border-color)';

            renderComments(post.comments);
            detailModal.classList.add('active');
        }

        document.getElementById('closeDetailModal').addEventListener('click', () => {
            detailModal.classList.remove('active');
            loadGallery();
        });

        // いいねボタン
        document.getElementById('likeBtn').addEventListener('click', () => {
            let posts = JSON.parse(localStorage.getItem('graffiti_posts') || '[]');
            const post = posts.find(p => p.id === currentPostId);
            if(!post) return;

            if(!post.liked) {
                post.likes++;
                post.liked = true;
            } else {
                post.likes = Math.max(0, post.likes - 1);
                post.liked = false;
            }

            localStorage.setItem('graffiti_posts', JSON.stringify(posts));
            document.getElementById('likeCount').textContent = post.likes;
            document.getElementById('likeBtn').style.borderColor = post.liked ? 'var(--primary-color)' : 'var(--border-color)';
        });

        // コメント送信
        document.getElementById('sendCommentBtn').addEventListener('click', () => {
            const input = document.getElementById('commentInput');
            const text = input.value.trim();
            if(!text) return;

            let posts = JSON.parse(localStorage.getItem('graffiti_posts') || '[]');
            const post = posts.find(p => p.id === currentPostId);
            if(!post) return;

            post.comments.push({
                text: text,
                date: new Date().toLocaleTimeString('ja-JP', {hour: '2-digit', minute:'2-digit'})
            });

            localStorage.setItem('graffiti_posts', JSON.stringify(posts));
            input.value = '';
            renderComments(post.comments);
        });

        function renderComments(comments) {
            const list = document.getElementById('commentsList');
            document.getElementById('commentCount').textContent = comments.length;
            if(comments.length === 0) {
                list.innerHTML = `<div style="color: #666; font-size: 0.8rem; text-align: center;">まだコメントはありません</div>`;
                return;
            }
            list.innerHTML = '';
            comments.forEach(c => {
                const item = document.createElement('div');
                item.className = 'comment-item';
                item.innerHTML = `<div>${escapeHTML(c.text)}</div><div style="font-size: 0.7rem; color: #666; text-align: right;">${c.date}</div>`;
                list.appendChild(item);
            });
        }

        function escapeHTML(str) {
            return str.replace(/[&<>'"]/g, 
                tag => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;' }[tag] || tag)
            );
        }
    </script>
</body>
</html>

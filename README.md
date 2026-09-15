<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>カラフル落書き掲示板</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Poppins & Zen Maru Gothic -->
    <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;800&family=Zen+Maru+Gothic:wght@500;700;900&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Zen Maru Gothic', 'Poppins', sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
        }
        /* ネオン発光エフェクトのクラス */
        .neon-shadow {
            box-shadow: 0 0 15px currentColor;
        }
        .neon-text {
            text-shadow: 0 0 10px currentColor, 0 0 20px currentColor;
        }
        /* カスタムスクロールバー */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #1e293b;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb {
            background: #475569;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #64748b;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-slate-950 text-slate-100 selection:bg-pink-500 selection:text-white">

    <!-- ヘッダー -->
    <header class="bg-slate-900/80 backdrop-blur border-b border-slate-800 sticky top-0 z-30 px-4 py-3 shadow-lg">
        <div class="max-w-6xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-4">
            <div class="flex items-center space-x-3">
                <span class="text-3xl animate-bounce">🎨</span>
                <h1 class="text-2xl font-black bg-gradient-to-r from-pink-500 via-purple-400 to-cyan-400 bg-clip-text text-transparent neon-text">
                    落書きパレット広場
                </h1>
            </div>
            
            <!-- タブ切り替えボタン -->
            <div class="flex bg-slate-800 p-1.5 rounded-2xl border border-slate-700 shadow-inner">
                <button id="tab-draw" onclick="switchTab('draw')" class="px-6 py-2 rounded-xl font-bold text-sm transition-all duration-300 bg-pink-600 text-white shadow-md shadow-pink-600/30">
                    ✍️ お絵描きする
                </button>
                <button id="tab-gallery" onclick="switchTab('gallery')" class="px-6 py-2 rounded-xl font-bold text-sm transition-all duration-300 text-slate-400 hover:text-white">
                    🖼️ みんなの掲示板 (<span id="gallery-count">0</span>)
                </button>
            </div>
        </div>
    </header>

    <!-- メインコンテンツ -->
    <main class="flex-1 max-w-6xl w-full mx-auto p-4 sm:p-6 flex flex-col">

        <!-- 1. お絵描きビュー -->
        <div id="view-draw" class="flex-1 flex flex-col gap-4">
            <!-- ツールバー -->
            <div class="bg-slate-900/90 border border-slate-800 rounded-2xl p-4 shadow-xl flex flex-wrap items-center justify-between gap-4">
                
                <!-- カラーパレット＆カスタムカラー -->
                <div class="flex items-center gap-2 flex-wrap">
                    <span class="text-xs font-bold text-slate-400 mr-1">カラー:</span>
                    <div id="color-palette" class="flex items-center gap-1.5 flex-wrap">
                        <!-- 動的生成または静的ボタン -->
                        <button onclick="setColor('#ffffff')" class="w-8 h-8 rounded-full bg-white border-2 border-slate-700 hover:scale-110 transition shadow" title="白"></button>
                        <button onclick="setColor('#ef4444')" class="w-8 h-8 rounded-full bg-red-500 border-2 border-slate-700 hover:scale-110 transition shadow" title="赤"></button>
                        <button onclick="setColor('#f97316')" class="w-8 h-8 rounded-full bg-orange-500 border-2 border-slate-700 hover:scale-110 transition shadow" title="オレンジ"></button>
                        <button onclick="setColor('#eab308')" class="w-8 h-8 rounded-full bg-yellow-400 border-2 border-slate-700 hover:scale-110 transition shadow" title="黄"></button>
                        <button onclick="setColor('#22c55e')" class="w-8 h-8 rounded-full bg-green-500 border-2 border-slate-700 hover:scale-110 transition shadow" title="緑"></button>
                        <button onclick="setColor('#06b6d4')" class="w-8 h-8 rounded-full bg-cyan-400 border-2 border-slate-700 hover:scale-110 transition shadow" title="シアン"></button>
                        <button onclick="setColor('#3b82f6')" class="w-8 h-8 rounded-full bg-blue-500 border-2 border-slate-700 hover:scale-110 transition shadow" title="青"></button>
                        <button onclick="setColor('#a855f7')" class="w-8 h-8 rounded-full bg-purple-500 border-2 border-slate-700 hover:scale-110 transition shadow" title="紫"></button>
                        <button onclick="setColor('#ec4899')" class="w-8 h-8 rounded-full bg-pink-500 border-2 border-slate-700 hover:scale-110 transition shadow" title="ピンク"></button>
                        <button onclick="setColor('#000000')" class="w-8 h-8 rounded-full bg-black border-2 border-slate-600 hover:scale-110 transition shadow" title="黒"></button>
                    </div>
                    <!-- カスタムカラーピッカー -->
                    <div class="flex items-center ml-2 bg-slate-800 px-2 py-1 rounded-xl border border-slate-700">
                        <label for="colorPicker" class="text-xs text-slate-400 mr-2 cursor-pointer">自由色:</label>
                        <input type="color" id="colorPicker" value="#ec4899" onchange="setColor(this.value)" class="w-7 h-7 rounded cursor-pointer bg-transparent border-0">
                    </div>
                </div>

                <!-- ペンサイズ＆エフェクト -->
                <div class="flex items-center gap-4 flex-wrap">
                    <!-- 太さ -->
                    <div class="flex items-center gap-2">
                        <span class="text-xs font-bold text-slate-400">太さ: <span id="size-val" class="text-pink-400 font-bold">8</span>px</span>
                        <input type="range" id="brushSize" min="1" max="50" value="8" oninput="setBrushSize(this.value)" class="w-24 accent-pink-500 cursor-pointer">
                    </div>

                    <!-- ネオン発光トグル -->
                    <label class="flex items-center gap-2 cursor-pointer bg-slate-800 px-3 py-1.5 rounded-xl border border-slate-700 hover:bg-slate-750 transition">
                        <input type="checkbox" id="neonGlow" onchange="toggleNeon(this.checked)" class="w-4 h-4 accent-pink-500 rounded cursor-pointer">
                        <span class="text-xs font-bold text-cyan-300">✨ ネオン発光</span>
                    </label>

                    <!-- 消しゴム -->
                    <button id="eraser-btn" onclick="toggleEraser()" class="px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-slate-300 font-bold text-xs rounded-xl border border-slate-700 transition flex items-center gap-1">
                        🧹 消しゴム
                    </button>

                    <!-- 全クリア -->
                    <button onclick="clearCanvas()" class="px-3 py-1.5 bg-red-950/60 hover:bg-red-900 text-red-300 font-bold text-xs rounded-xl border border-red-800 transition flex items-center gap-1">
                        🗑️ 全部消す
                    </button>
                </div>
            </div>

            <!-- キャンバス本体領域 -->
            <div class="flex-1 bg-slate-900 border-2 border-slate-800 rounded-3xl overflow-hidden relative shadow-2xl flex items-center justify-center min-h-[450px]">
                <canvas id="paintCanvas" class="cursor-crosshair w-full h-full block touch-none"></canvas>
                <!-- 現在のブラシプレビュー/インジケーター -->
                <div id="brush-cursor" class="absolute pointer-events-none rounded-full border-2 border-white/50 hidden"></div>
            </div>

            <!-- 投稿フォームエリア -->
            <div class="bg-slate-900 border border-slate-800 rounded-2xl p-4 shadow-xl flex flex-col sm:flex-row items-center gap-4">
                <div class="flex-1 grid grid-cols-1 sm:grid-cols-2 gap-3 w-full">
                    <div>
                        <label class="block text-xs font-bold text-slate-400 mb-1">作品のタイトル</label>
                        <input type="text" id="post-title" placeholder="例：ネオンの夜空を駆ける猫" maxlength="30" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-100 focus:outline-none focus:border-pink-500 transition">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-400 mb-1">ペンネーム（描いた人）</label>
                        <input type="text" id="post-author" placeholder="例：お絵描き太郎" maxlength="20" class="w-full bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-100 focus:outline-none focus:border-pink-500 transition">
                    </div>
                </div>
                <button onclick="submitDrawing()" class="w-full sm:w-auto px-8 py-3 bg-gradient-to-r from-pink-500 to-purple-600 hover:from-pink-600 hover:to-purple-700 font-black text-white rounded-xl shadow-lg shadow-pink-500/25 transition-all transform hover:scale-105 active:scale-95 flex items-center justify-center gap-2 whitespace-nowrap">
                    🚀 掲示板に投稿する！
                </button>
            </div>
        </div>

        <!-- 2. 掲示板（ギャラリー）ビュー -->
        <div id="view-gallery" class="flex-1 hidden flex-col gap-6">
            <!-- ギャラリーヘッダーフィルター等 -->
            <div class="flex flex-col sm:flex-row items-center justify-between gap-4 bg-slate-900/60 p-4 rounded-2xl border border-slate-800">
                <div>
                    <h2 class="text-xl font-bold text-slate-100">みんなの落書き一覧</h2>
                    <p class="text-xs text-slate-400">みんなが投稿した素敵なイラストに「いいね」や「コメント」を残そう！</p>
                </div>
                <div class="flex items-center gap-2 w-full sm:w-auto">
                    <select id="sort-select" onchange="renderGallery()" class="bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-xs text-slate-300 focus:outline-none focus:border-pink-500">
                        <option value="newest">新しい順</option>
                        <option value="likes">いいね！多い順</option>
                    </select>
                </div>
            </div>

            <!-- ギャラリーカードグリッド -->
            <div id="gallery-grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- 動的にカードが挿入されます -->
            </div>

            <!-- 投稿がない場合の空状態 -->
            <div id="empty-gallery" class="hidden flex-col items-center justify-center py-20 text-center">
                <div class="text-6xl mb-4 animate-pulse">🖌️</div>
                <h3 class="text-lg font-bold text-slate-300">まだ投稿がありません</h3>
                <p class="text-sm text-slate-500 mb-6">最初の落書きを投稿して掲示板を彩ろう！</p>
                <button onclick="switchTab('draw')" class="px-6 py-2.5 bg-pink-600 hover:bg-pink-500 font-bold text-white rounded-xl shadow transition">
                    今すぐ描く
                </button>
            </div>
        </div>

    </main>

    <!-- 作品詳細・コメントモーダル -->
    <div id="detail-modal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm hidden items-center justify-center p-4">
        <div class="bg-slate-900 border border-slate-700 rounded-3xl max-w-2xl w-full max-h-[90vh] flex flex-col overflow-hidden shadow-2xl animate-in fade-in zoom-in duration-200">
            <div class="p-4 border-b border-slate-800 flex items-center justify-between">
                <div>
                    <h3 id="modal-title" class="text-lg font-bold text-white">タイトル</h3>
                    <p id="modal-author" class="text-xs text-slate-400">作者名</p>
                </div>
                <button onclick="closeModal()" class="w-8 h-8 rounded-full bg-slate-800 hover:bg-slate-700 text-slate-400 hover:text-white flex items-center justify-center font-bold">
                    ✕
                </button>
            </div>
            
            <div class="p-4 bg-slate-950 flex items-center justify-center overflow-hidden">
                <img id="modal-image" src="" alt="落書き詳細" class="max-h-[350px] object-contain rounded-xl border border-slate-800 shadow-inner">
            </div>

            <div class="p-4 border-t border-slate-800 flex items-center justify-between bg-slate-900/80">
                <div class="flex items-center gap-3">
                    <button id="modal-like-btn" onclick="modalLike()" class="px-4 py-2 bg-pink-600/20 hover:bg-pink-600/30 text-pink-400 border border-pink-500/30 rounded-xl font-bold text-sm transition flex items-center gap-1.5">
                        ❤️ <span id="modal-likes-count">0</span> いいね！
                    </button>
                </div>
                <span id="modal-date" class="text-xs text-slate-500">2026/06/07 12:00</span>
            </div>

            <!-- コメントセクション -->
            <div class="flex-1 overflow-y-auto p-4 space-y-3 bg-slate-900/40 border-t border-slate-800 max-h-[200px]">
                <h4 class="text-xs font-bold text-slate-400">コメント一覧</h4>
                <div id="modal-comments-list" class="space-y-2">
                    <!-- コメントアイテム -->
                </div>
            </div>

            <!-- コメント投稿フォーム -->
            <div class="p-3 border-t border-slate-800 bg-slate-900 flex gap-2">
                <input type="text" id="comment-input" placeholder="一言コメントを残す..." maxlength="50" class="flex-1 bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-xs text-slate-100 focus:outline-none focus:border-pink-500">
                <button onclick="submitComment()" class="px-4 py-2 bg-pink-600 hover:bg-pink-500 text-white font-bold text-xs rounded-xl transition">
                    送信
                </button>
            </div>
        </div>
    </div>

    <!-- トースト通知 -->
    <div id="toast" class="fixed bottom-6 right-6 z-50 bg-slate-900 border border-slate-700 text-white px-5 py-3 rounded-2xl shadow-2xl transform translate-y-20 opacity-0 transition-all duration-300 flex items-center gap-3">
        <span id="toast-icon" class="text-xl">✨</span>
        <span id="toast-message" class="text-sm font-bold">通知メッセージ</span>
    </div>

    <!-- スクリプト部 -->
    <script>
        // 状態管理
        let currentTab = 'draw';
        let currentColor = '#ec4899';
        let currentSize = 8;
        let isNeon = false;
        let isEraser = false;
        let isDrawing = false;
        let lastX = 0;
        let lastY = 0;
        let activePostId = null;

        // ローカルストレージからの読み込み
        let posts = JSON.parse(localStorage.getItem('graffiti_posts')) || [];

        // キャンバス初期設定
        const canvas = document.getElementById('paintCanvas');
        const ctx = canvas.getContext('2d');

        function initCanvasSize() {
            // 親要素の大きさに合わせる
            const container = canvas.parentElement;
            const width = container.clientWidth;
            const height = container.clientHeight;
            
            // 既存の描画内容を退避
            const tempCanvas = document.createElement('canvas');
            const tempCtx = tempCanvas.getContext('2d');
            tempCanvas.width = canvas.width || width;
            tempCanvas.height = canvas.height || height;
            if (canvas.width > 0 && canvas.height > 0) {
                tempCtx.drawImage(canvas, 0, 0);
            }

            canvas.width = width;
            canvas.height = height;

            // 背景をダークなキャンバスカラーで初期化（透明ではなく黒に近い下地）
            ctx.fillStyle = '#090d16';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 退避画像を復元
            if (tempCanvas.width > 0 && tempCanvas.height > 0) {
                ctx.drawImage(tempCanvas, 0, 0, tempCanvas.width, tempCanvas.height, 0, 0, canvas.width, canvas.height);
            }
        }

        window.addEventListener('load', () => {
            initCanvasSize();
            setupCanvasEvents();
            renderGallery();
        });

        window.addEventListener('resize', () => {
            // リサイズ時のキャンバス保持
            initCanvasSize();
        });

        // タブ切り替え
        function switchTab(tab) {
            currentTab = tab;
            const drawView = document.getElementById('view-draw');
            const galleryView = document.getElementById('view-gallery');
            const btnDraw = document.getElementById('tab-draw');
            const btnGallery = document.getElementById('tab-gallery');

            if (tab === 'draw') {
                drawView.classList.remove('hidden');
                galleryView.classList.add('hidden');
                btnDraw.className = "px-6 py-2 rounded-xl font-bold text-sm transition-all duration-300 bg-pink-600 text-white shadow-md shadow-pink-600/30";
                btnGallery.className = "px-6 py-2 rounded-xl font-bold text-sm transition-all duration-300 text-slate-400 hover:text-white";
                initCanvasSize();
            } else {
                drawView.classList.add('hidden');
                galleryView.classList.remove('hidden');
                btnGallery.className = "px-6 py-2 rounded-xl font-bold text-sm transition-all duration-300 bg-purple-600 text-white shadow-md shadow-purple-600/30";
                btnDraw.className = "px-6 py-2 rounded-xl font-bold text-sm transition-all duration-300 text-slate-400 hover:text-white";
                renderGallery();
            }
        }

        // 描画ツールの制御
        function setColor(color) {
            currentColor = color;
            if (isEraser) toggleEraser();
            document.getElementById('colorPicker').value = color;
        }

        function setBrushSize(size) {
            currentSize = parseInt(size);
            document.getElementById('size-val').innerText = currentSize;
        }

        function toggleNeon(checked) {
            isNeon = checked;
        }

        function toggleEraser() {
            isEraser = !isEraser;
            const btn = document.getElementById('eraser-btn');
            if (isEraser) {
                btn.className = "px-3 py-1.5 bg-pink-600 text-white font-bold text-xs rounded-xl border border-pink-500 transition flex items-center gap-1 shadow-md shadow-pink-600/30";
            } else {
                btn.className = "px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-slate-300 font-bold text-xs rounded-xl border border-slate-700 transition flex items-center gap-1";
            }
        }

        function clearCanvas() {
            if (confirm('キャンバスをすべてクリアしますか？')) {
                ctx.fillStyle = '#090d16';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                showToast('🗑️ キャンバスをクリアしました', '✨');
            }
        }

        // 描画イベントのセットアップ（マウス＆タッチ対応）
        function setupCanvasEvents() {
            function getPos(e) {
                const rect = canvas.getBoundingClientRect();
                let clientX = e.clientX;
                let clientY = e.clientY;
                if (e.touches && e.touches.length > 0) {
                    clientX = e.touches[0].clientX;
                    clientY = e.touches[0].clientY;
                }
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
                    ctx.strokeStyle = '#090d16';
                    ctx.lineWidth = currentSize * 2;
                    ctx.shadowBlur = 0;
                } else {
                    ctx.strokeStyle = currentColor;
                    ctx.lineWidth = currentSize;
                    if (isNeon) {
                        ctx.shadowColor = currentColor;
                        ctx.shadowBlur = 15;
                    } else {
                        ctx.shadowBlur = 0;
                    }
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

            // マウスイベント
            canvas.addEventListener('mousedown', startDraw);
            canvas.addEventListener('mousemove', draw);
            canvas.addEventListener('mouseup', stopDraw);
            canvas.addEventListener('mouseleave', stopDraw);

            // タッチイベント
            canvas.addEventListener('touchstart', startDraw, { passive: false });
            canvas.addEventListener('touchmove', draw, { passive: false });
            canvas.addEventListener('touchend', stopDraw);
        }

        // 投稿機能
        function submitDrawing() {
            const titleInput = document.getElementById('post-title');
            const authorInput = document.getElementById('post-author');
            
            const title = titleInput.value.trim() || '無題の落書き';
            const author = authorInput.value.trim() || '名無しさん';

            // キャンバス画像をデータURLに変換
            const dataUrl = canvas.toDataURL('image/png');

            const newPost = {
                id: 'post_' + Date.now(),
                title: title,
                author: author,
                image: dataUrl,
                likes: 0,
                likedByMe: false,
                comments: [],
                createdAt: new Date().toLocaleString()
            };

            posts.unshift(newPost);
            localStorage.setItem('graffiti_posts', JSON.stringify(posts));

            // フォームリセット
            titleInput.value = '';
            authorInput.value = '';

            showToast('🎉 落書きを掲示板に投稿しました！', '🚀');
            switchTab('gallery');
        }

        // ギャラリー描画
        function renderGallery() {
            const grid = document.getElementById('gallery-grid');
            const emptyEl = document.getElementById('empty-gallery');
            const countEl = document.getElementById('gallery-count');
            const sortVal = document.getElementById('sort-select').value;

            countEl.innerText = posts.length;

            if (posts.length === 0) {
                grid.innerHTML = '';
                emptyEl.classList.remove('hidden');
                emptyEl.classList.add('flex');
                return;
            }

            emptyEl.classList.add('hidden');
            emptyEl.classList.remove('flex');

            // ソート処理
            let sortedPosts = [...posts];
            if (sortVal === 'likes') {
                sortedPosts.sort((a, b) => b.likes - a.likes);
            } else {
                sortedPosts.sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt));
            }

            grid.innerHTML = sortedPosts.map(post => `
                <div class="bg-slate-900 border border-slate-800 rounded-3xl overflow-hidden shadow-xl hover:border-pink-500/50 transition-all duration-300 flex flex-col group">
                    <div class="bg-slate-950 h-52 flex items-center justify-center overflow-hidden cursor-pointer relative" onclick="openModal('${post.id}')">
                        <img src="${post.image}" alt="${post.title}" class="w-full h-full object-contain group-hover:scale-105 transition duration-300">
                        <div class="absolute inset-0 bg-black/40 opacity-0 group-hover:opacity-100 transition flex items-center justify-center text-white font-bold text-sm gap-1">
                            🔍 詳細を見る
                        </div>
                    </div>
                    <div class="p-4 flex-1 flex flex-col justify-between gap-3">
                        <div>
                            <h3 class="font-bold text-slate-100 text-base truncate" title="${post.title}">${post.title}</h3>
                            <p class="text-xs text-slate-400 mt-0.5">描いた人: <span class="text-pink-400">${post.author}</span></p>
                        </div>
                        <div class="flex items-center justify-between pt-2 border-t border-slate-800 text-xs">
                            <button onclick="toggleLike('${post.id}')" class="flex items-center gap-1.5 px-3 py-1.5 rounded-xl transition ${post.likedByMe ? 'bg-pink-600/20 text-pink-400 border border-pink-500/30' : 'bg-slate-800 hover:bg-slate-700 text-slate-300'}">
                                ${post.likedByMe ? '❤️' : '🤍'} <span>${post.likes}</span>
                            </button>
                            <button onclick="openModal('${post.id}')" class="flex items-center gap-1 px-3 py-1.5 bg-slate-800 hover:bg-slate-700 text-slate-300 rounded-xl transition">
                                💬 <span>${post.comments.length}</span>
                            </button>
                            <span class="text-slate-500 text-[10px]">${post.createdAt.split(' ')[0]}</span>
                        </div>
                    </div>
                </div>
            `).join('');
        }

        // いいね機能
        function toggleLike(postId) {
            const post = posts.find(p => p.id === postId);
            if (!post) return;

            if (post.likedByMe) {
                post.likes--;
                post.likedByMe = false;
            } else {
                post.likes++;
                post.likedByMe = true;
                showToast('❤️ いいねしました！', '✨');
            }

            localStorage.setItem('graffiti_posts', JSON.stringify(posts));
            renderGallery();
            if (activePostId === postId) {
                updateModalContent();
            }
        }

        // モーダル操作
        function openModal(postId) {
            activePostId = postId;
            updateModalContent();
            const modal = document.getElementById('detail-modal');
            modal.classList.remove('hidden');
            modal.classList.add('flex');
        }

        function closeModal() {
            activePostId = null;
            const modal = document.getElementById('detail-modal');
            modal.classList.remove('flex');
            modal.classList.add('hidden');
        }

        function updateModalContent() {
            const post = posts.find(p => p.id === activePostId);
            if (!post) return;

            document.getElementById('modal-title').innerText = post.title;
            document.getElementById('modal-author').innerText = '作者: ' + post.author;
            document.getElementById('modal-image').src = post.image;
            document.getElementById('modal-likes-count').innerText = post.likes;
            document.getElementById('modal-date').innerText = post.createdAt;

            const likeBtn = document.getElementById('modal-like-btn');
            if (post.likedByMe) {
                likeBtn.className = "px-4 py-2 bg-pink-600/30 text-pink-400 border border-pink-500/50 rounded-xl font-bold text-sm transition flex items-center gap-1.5 shadow-lg shadow-pink-600/20";
                likeBtn.innerHTML = `❤️ <span id="modal-likes-count">${post.likes}</span> いいね！解除`;
            } else {
                likeBtn.className = "px-4 py-2 bg-pink-600/20 hover:bg-pink-600/30 text-pink-400 border border-pink-500/30 rounded-xl font-bold text-sm transition flex items-center gap-1.5";
                likeBtn.innerHTML = `❤️ <span id="modal-likes-count">${post.likes}</span> いいね！`;
            }

            const commentsList = document.getElementById('modal-comments-list');
            if (post.comments.length === 0) {
                commentsList.innerHTML = '<p class="text-xs text-slate-500 italic">まだコメントはありません。最初のメッセージを残そう！</p>';
            } else {
                commentsList.innerHTML = post.comments.map(c => `
                    <div class="bg-slate-950 p-2.5 rounded-xl border border-slate-800 text-xs">
                        <div class="flex items-center justify-between text-slate-400 mb-1">
                            <span class="font-bold text-pink-400">${c.author}</span>
                            <span class="text-[10px] text-slate-600">${c.date}</span>
                        </div>
                        <p class="text-slate-200">${c.text}</p>
                    </div>
                `).join('');
            }
        }

        function modalLike() {
            if (activePostId) {
                toggleLike(activePostId);
            }
        }

        function submitComment() {
            const input = document.getElementById('comment-input');
            const text = input.value.trim();
            if (!text) return;

            const post = posts.find(p => p.id === activePostId);
            if (!post) return;

            post.comments.push({
                author: '匿名ユーザー',
                text: text,
                date: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
            });

            localStorage.setItem('graffiti_posts', JSON.stringify(posts));
            input.value = '';
            updateModalContent();
            renderGallery();
            showToast('💬 コメントを投稿しました', '✨');
        }

        // トースト通知表示
        function setToast(message, icon) {
            const toast = document.getElementById('toast');
            document.getElementById('toast-message').innerText = message;
            document.getElementById('toast-icon').innerText = icon || '✨';
            
            toast.classList.remove('translate-y-20', 'opacity-0');
            toast.classList.add('translate-y-0', 'opacity-100');

            setTimeout(() => {
                toast.classList.remove('translate-y-0', 'opacity-100');
                toast.classList.add('translate-y-20', 'opacity-0');
            }, 3000);
        }
        window.showToast = setToast;

        // モーダル外クリックで閉じる
        document.getElementById('detail-modal').addEventListener('click', (e) => {
            if (e.target === document.getElementById('detail-modal')) {
                closeModal();
            }
        });
    </script>
</body>
</html>

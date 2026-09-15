<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  <title>DEPOSIT MANAGER</title>
  <style>
    :root {
      --bg: #030712; --card: #111827; --card-border: #374151; --primary: #10b981; --text: #f9fafb; --sub: #9ca3af; --border: #4b5563; --red: #ef4444; --reply: #3b82f6;
    }
    * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; touch-action: manipulation; }
    html, body { width: 100%; height: 100%; height: 100dvh; overflow: hidden; background: var(--bg); color: var(--text); font-family: sans-serif; }
    .app { display: flex; flex-direction: column; height: 100dvh; max-width: 600px; margin: 0 auto; padding: 10px; gap: 14px; }
    header { display: flex; justify-content: space-between; align-items: center; padding: 4px 2px; flex-shrink: 0; }
    .brand { font-size: 1rem; font-weight: 900; color: var(--primary); display: flex; gap: 4px; align-items: center; letter-spacing: 0.5px; }
    .btns { display: flex; gap: 6px; align-items: center; }
    .ibtn { background: #1f2937; border: 2px solid var(--border); color: var(--text); padding: 6px 10px; border-radius: 10px; font-size: 0.75rem; font-weight: bold; cursor: pointer; box-shadow: 0 2px 4px rgba(0,0,0,0.4); }
    
    .date-banner {
      background: #111827; border: 2px solid var(--card-border); border-radius: 12px; padding: 8px 14px;
      font-size: 0.8rem; font-weight: bold; color: var(--sub); display: flex; align-items: center; justify-content: space-between; flex-shrink: 0;
      box-shadow: 0 4px 6px rgba(0,0,0,0.4);
    }

    .main { flex: 1; overflow-y: auto; display: flex; flex-direction: column; gap: 16px; min-height: 0; padding-bottom: 12px; }
    
    .card { background: var(--card); border: 3px solid var(--card-border); border-radius: 18px; padding: 14px; display: flex; flex-direction: column; gap: 12px; box-shadow: 0 6px 12px rgba(0,0,0,0.5); }
    
    .new-post-card {
      background: linear-gradient(145deg, #06241b, #03110c);
      border: 4px solid var(--primary);
      box-shadow: 0 10px 25px rgba(16, 185, 129, 0.3);
    }
    .new-post-card .c-title { border-bottom: 2px solid rgba(16, 185, 129, 0.5); }

    .c-title { font-size: 0.9rem; font-weight: bold; color: var(--primary); display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid #1f2937; padding-bottom: 8px; flex-shrink: 0; }
    
    .form-group { display: flex; flex-direction: column; gap: 6px; }
    .form-label { font-size: 0.75rem; color: var(--sub); font-weight: bold; }
    .d-inp, select { background: #020408; border: 2px solid var(--border); color: white; padding: 10px; border-radius: 8px; font-size: 0.9rem; width: 100%; outline: none; }
    .d-inp:focus, select:focus { border-color: var(--primary); }

    .preview-container { display: flex; gap: 8px; overflow-x: auto; padding: 4px 0; min-height: 60px; }
    .preview-thumb { position: relative; width: 50px; height: 50px; border-radius: 8px; overflow: hidden; border: 2px solid var(--primary); flex-shrink: 0; background: #000; }
    .preview-thumb img { width: 100%; height: 100%; object-fit: cover; }
    .preview-thumb .del-thumb { position: absolute; top: 2px; right: 2px; background: rgba(0,0,0,0.7); color: white; border: none; font-size: 0.6rem; width: 16px; height: 16px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; }

    .tools { display: flex; gap: 6px; justify-content: space-between; align-items: center; margin-top: 4px; }
    .btn { background: #1f2937; color: var(--text); border: 2px solid var(--border); padding: 8px 12px; border-radius: 8px; font-weight: bold; font-size: 0.75rem; cursor: pointer; }
    .sbtn { background: var(--primary); color: #000; border: none; padding: 10px 20px; border-radius: 8px; font-weight: 900; font-size: 0.85rem; cursor: pointer; margin-left: auto; box-shadow: 0 3px 6px rgba(16,185,129,0.4); }

    .table-container { width: 100%; overflow-x: auto; background: #020408; border-radius: 10px; border: 2px solid var(--border); }
    table { width: 100%; border-collapse: collapse; font-size: 0.75rem; text-align: left; }
    th { background: #1f2937; color: var(--primary); padding: 8px; font-weight: bold; border-bottom: 2px solid var(--border); white-space: nowrap; }
    td { padding: 8px; border-bottom: 1px solid #1f2937; color: var(--text); white-space: nowrap; }
    tr:last-child td { border-bottom: none; }

    .summary-box { background: #0b0f19; border: 2px solid var(--card-border); border-radius: 12px; padding: 10px 14px; display: flex; flex-direction: column; gap: 6px; font-size: 0.8rem; }
    .summary-row { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px dashed #1f2937; padding-bottom: 4px; }
    .summary-row:last-child { border-bottom: none; }

    .del { background: transparent; border: 1px solid #4b5563; color: #9ca3af; font-size: 0.65rem; cursor: pointer; padding: 2px 6px; border-radius: 4px; }

    .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.88); z-index: 10000; justify-content: center; align-items: center; padding: 10px; }
    .m-card { background: var(--card); border: 3px solid var(--card-border); border-radius: 18px; width: 100%; max-width: 480px; max-height: 90vh; display: flex; flex-direction: column; padding: 16px; gap: 12px; box-shadow: 0 12px 30px rgba(0,0,0,0.7); }
    .m-head { font-weight: bold; font-size: 0.95rem; color: var(--primary); display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid #1f2937; padding-bottom: 8px; }
  </style>
</head>
<body>

  <div class="app">
    <header>
      <div class="brand">DEPOSIT MANAGER</div>
      <div class="btns">
        <button class="ibtn" onclick="exportData()">データ書き出し</button>
      </div>
    </header>

    <div class="date-banner" id="todayDateBanner">読み込み中...</div>

    <div class="main" id="mainContainer">
      <!-- 新規入力カード -->
      <section class="card new-post-card">
        <div class="c-title"><span>新規入金データの登録</span></div>
        
        <div style="display:grid; grid-template-columns: 1fr 1fr; gap: 10px;">
          <div class="form-group">
            <label class="form-label">入金日</label>
            <input type="date" id="inputDate" class="d-inp">
          </div>
          <div class="form-group">
            <label class="form-label">担当 / 店舗</label>
            <select id="inputStaff" class="d-inp" style="padding:10px;">
              <option value="大和地">大和地 (大)</option>
              <option value="草野">草野 (草)</option>
              <option value="菊池">菊池 (菊)</option>
              <option value="林">林 (林)</option>
              <option value="高橋">高橋 (千)</option>
              <option value="デコレ">デコレ</option>
              <option value="未記入不明店舗">未記入不明店舗</option>
            </select>
          </div>
        </div>

        <div style="display:grid; grid-template-columns: 1fr 1fr; gap: 10px;">
          <div class="form-group">
            <label class="form-label">入金名義 (振込人)</label>
            <input type="text" id="inputPayer" class="d-inp" placeholder="例: カ）ヤマダストア">
          </div>
          <div class="form-group">
            <label class="form-label">金額 (円)</label>
            <input type="number" id="inputAmount" class="d-inp" placeholder="例: 50000">
          </div>
        </div>

        <div class="form-group">
          <label class="form-label">書類・通帳画像 (複数選択可)</label>
          <div class="preview-container" id="previewContainer">
            <span style="font-size: 0.75rem; color: var(--sub); align-self: center;">画像未選択</span>
          </div>
          <div class="tools">
            <label class="btn" style="cursor:pointer; background:var(--reply); color:white; border-color:var(--reply);">
              ＋ 画像を選ぶ
              <input type="file" accept="image/*" multiple style="display:none;" onchange="handleImagesSelect(event)">
            </label>
            <button class="sbtn" onclick="submitDeposit()">リストに追加</button>
          </div>
        </div>
      </section>

      <!-- 集計サマリー -->
      <section class="card" style="background:#0b0f19;">
        <div class="c-title"><span>担当・店舗別 合計集計</span></div>
        <div class="summary-box" id="dailySummaryBox">
          <div style="color:var(--sub); text-align:center; padding:6px;">データはありません</div>
        </div>
      </section>

      <!-- 一覧テーブル -->
      <section class="card">
        <div class="c-title"><span>入金一覧テーブル</span></div>
        <div class="table-container" id="tableContainer">
          <div style="padding: 16px; text-align: center; color: var(--sub); font-size: 0.75rem;">データはありません</div>
        </div>
      </section>
    </div>
  </div>

  <div class="modal" id="imageModal" onclick="closeModal('imageModal')">
    <div class="m-card" style="max-width:90vw; background:#000; padding:10px;" onclick="event.stopPropagation()">
      <div class="m-head"><span>添付画像</span><button class="ibtn" onclick="closeModal('imageModal')">✕</button></div>
      <div style="display:flex; justify-content:center; align-items:center; flex:1; overflow:hidden;">
        <img id="modalFullImg" style="max-width:100%; max-height:75vh; object-fit:contain; border-radius:8px;">
      </div>
    </div>
  </div>

  <script>
    let deposits = JSON.parse(localStorage.getItem('DM_LIGHT_DEPOSITS')) || [];
    let currentUploadedImages = [];

    window.onload = () => {
      initDate();
      renderTable();
      renderDailySummary();
    };

    function initDate() {
      const now = new Date();
      const weekdays = ['日', '月', '火', '水', '木', '金', '土'];
      document.getElementById('todayDateBanner').innerHTML = `<span>${now.getFullYear()}/${now.getMonth()+1}/${now.getDate()} (${weekdays[now.getDay()].toUpperCase()})</span><span>入金管理システム</span>`;
      
      const dateStr = `${now.getFullYear()}-${String(now.getMonth()+1).padStart(2,'0')}-${String(now.getDate()).padStart(2,'0')}`;
      document.getElementById('inputDate').value = dateStr;
    }

    function handleImagesSelect(e) {
      const files = e.target.files;
      if (!files || files.length === 0) return;

      Array.from(files).forEach(file => {
        const reader = new FileReader();
        reader.onload = ev => {
          const img = new Image();
          img.onload = () => {
            const canvas = document.createElement('canvas');
            let w = img.width, h = img.height;
            const MAX_SIZE = 800;
            if (w > MAX_SIZE || h > MAX_SIZE) {
              if (w > h) { h = Math.round(h * (MAX_SIZE / w)); w = MAX_SIZE; }
              else { w = Math.round(w * (MAX_SIZE / h)); h = MAX_SIZE; }
            }
            canvas.width = w; canvas.height = h;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(img, 0, 0, w, h);
            currentUploadedImages.push(canvas.toDataURL('image/jpeg', 0.7));
            renderPreviews();
          };
          img.src = ev.target.result;
        };
        reader.readAsDataURL(file);
      });
      e.target.value = '';
    }

    function renderPreviews() {
      const container = document.getElementById('previewContainer');
      if (currentUploadedImages.length === 0) {
        container.innerHTML = `<span style="font-size: 0.75rem; color: var(--sub); align-self: center;">画像未選択</span>`;
        return;
      }
      container.innerHTML = currentUploadedImages.map((imgSrc, idx) => `
        <div class="preview-thumb">
          <img src="${imgSrc}">
          <button class="del-thumb" onclick="removePreview(${idx})">✕</button>
        </div>
      `).join('');
    }

    function removePreview(idx) {
      currentUploadedImages.splice(idx, 1);
      renderPreviews();
    }

    document.getElementById('inputPayer').addEventListener('input', (e) => {
      const val = e.target.value;
      const staffSelect = document.getElementById('inputStaff');
      if (val.includes('デコレ')) staffSelect.value = 'デコレ';
      else if (val.includes('大')) staffSelect.value = '大和地';
      else if (val.includes('草')) staffSelect.value = '草野';
      else if (val.includes('菊')) staffSelect.value = '菊池';
      else if (val.includes('林')) staffSelect.value = '林';
      else if (val.includes('千') || val.includes('高橋')) staffSelect.value = '高橋';
    });

    function submitDeposit() {
      const date = document.getElementById('inputDate').value;
      const staff = document.getElementById('inputStaff').value;
      const payer = document.getElementById('inputPayer').value.trim();
      const amount = Number(document.getElementById('inputAmount').value);

      if (!date || !payer || !amount) {
        alert('「入金日」「入金名義」「金額」を入力してください。');
        return;
      }

      const mainContainer = document.getElementById('mainContainer');
      const currentScrollTop = mainContainer.scrollTop;

      const newDeposit = {
        id: 'D_' + Date.now(),
        date: date,
        staff: staff,
        payer: payer,
        amount: amount,
        images: [...currentUploadedImages],
        createdAt: new Date().getTime()
      };

      deposits.unshift(newDeposit);
      saveAndRefresh();

      document.getElementById('inputPayer').value = '';
      document.getElementById('inputAmount').value = '';
      currentUploadedImages = [];
      renderPreviews();

      setTimeout(() => {
        mainContainer.scrollTop = currentScrollTop;
      }, 50);
    }

    function deleteDeposit(id) {
      if (confirm('この入金データを削除しますか？')) {
        const mainContainer = document.getElementById('mainContainer');
        const currentScrollTop = mainContainer.scrollTop;

        deposits = deposits.filter(d => d.id !== id);
        saveAndRefresh();

        setTimeout(() => {
          mainContainer.scrollTop = currentScrollTop;
        }, 50);
      }
    }

    function saveAndRefresh() {
      localStorage.setItem('DM_LIGHT_DEPOSITS', JSON.stringify(deposits));
      renderTable();
      renderDailySummary();
    }

    function renderTable() {
      const container = document.getElementById('tableContainer');
      if (deposits.length === 0) {
        container.innerHTML = '<div style="padding: 16px; text-align: center; color: var(--sub); font-size: 0.75rem;">データはありません</div>';
        return;
      }

      let html = `
        <table>
          <thead>
            <tr>
              <th>画像</th>
              <th>入金日</th>
              <th>担当/店舗</th>
              <th>入金名義</th>
              <th>金額</th>
              <th>操作</th>
            </tr>
          </thead>
          <tbody>
      `;

      deposits.forEach(d => {
        const firstImg = d.images && d.images.length > 0 ? d.images[0] : null;
        html += `
          <tr>
            <td>
              ${firstImg ? `<div style="width:32px; height:32px; border-radius:4px; overflow:hidden; border:1px solid var(--border); cursor:pointer;" onclick="openImageModal('${firstImg}')"><img src="${firstImg}" style="width:100%; height:100%; object-fit:cover;"></div>` : '-'}
            </td>
            <td>${d.date}</td>
            <td><span style="background:rgba(16,185,129,0.2); color:var(--primary); padding:2px 6px; border-radius:4px; font-weight:bold;">${d.staff}</span></td>
            <td>${d.payer}</td>
            <td style="font-weight:900; color:var(--primary);">¥${d.amount.toLocaleString()}</td>
            <td><button class="del" onclick="deleteDeposit('${d.id}')">削除</button></td>
          </tr>
        `;
      });

      html += `</tbody></table>`;
      container.innerHTML = html;
    }

    function renderDailySummary() {
      const summaryBox = document.getElementById('dailySummaryBox');
      const staffList = ['大和地', '草野', '菊池', '林', '高橋', 'デコレ', '未記入不明店舗'];
      let totals = {};
      staffList.forEach(s => totals[s] = 0);
      let grandTotal = 0;

      deposits.forEach(d => {
        if (totals[d.staff] !== undefined) {
          totals[d.staff] += d.amount;
        } else {
          totals['未記入不明店舗'] = (totals['未記入不明店舗'] || 0) + d.amount;
        }
        grandTotal += d.amount;
      });

      let html = '';
      staffList.forEach(staff => {
        if (totals[staff] > 0) {
          html += `
            <div class="summary-row">
              <span style="font-weight:bold; color:var(--text);">担当/店舗: <span style="color:var(--primary);">${staff}</span></span>
              <span style="font-weight:900; color:var(--text);">¥${totals[staff].toLocaleString()}</span>
            </div>
          `;
        }
      });

      if (grandTotal === 0) {
        summaryBox.innerHTML = `<div style="color:var(--sub); text-align:center; padding:6px;">入金データがまだありません</div>`;
      } else {
        html += `
          <div class="summary-row" style="border-top:2px solid var(--border); padding-top:6px; margin-top:4px;">
            <span style="font-weight:900; color:var(--primary);">総合計金額</span>
            <span style="font-weight:900; font-size:1rem; color:var(--primary);">¥${grandTotal.toLocaleString()}</span>
          </div>
        `;
        summaryBox.innerHTML = html;
      }
    }

    function openImageModal(src) {
      document.getElementById('modalFullImg').src = src;
      document.getElementById('imageModal').style.display = 'flex';
    }

    function exportData() {
      const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(deposits, null, 2));
      const downloadAnchor = document.createElement('a');
      downloadAnchor.setAttribute("href", dataStr);
      downloadAnchor.setAttribute("download", `deposit_data_${new Date().toISOString().split('T')[0]}.json`);
      document.body.appendChild(downloadAnchor);
      downloadAnchor.click();
      downloadAnchor.remove();
    }

    function closeModal(id) {
      document.getElementById(id).style.display = 'none';
    }
  </script>
</body>
</html>

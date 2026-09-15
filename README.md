<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  <title>DEPOSIT MANAGER + FULL OCR</title>
  <!-- Tesseract.js (OCR用ライブラリ) -->
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@v5/dist/tesseract.min.js"></script>
  <style>
    :root {
      --bg: #030712; --card: #111827; --card-border: #374151; --primary: #10b981; --text: #f9fafb; --sub: #9ca3af; --border: #4b5563; --red: #ef4444; --reply: #3b82f6; --did-color: #6b7280;
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

    .preview-container { display: flex; gap: 8px; overflow-x: auto; padding: 4px 0; min-height: 70px; }
    .preview-thumb { position: relative; width: 60px; height: 60px; border-radius: 8px; overflow: hidden; border: 2px solid var(--primary); flex-shrink: 0; background: #000; }
    .preview-thumb img { width: 100%; height: 100%; object-fit: cover; }
    .preview-thumb .del-thumb { position: absolute; top: 2px; right: 2px; background: rgba(0,0,0,0.7); color: white; border: none; font-size: 0.6rem; width: 16px; height: 16px; border-radius: 50%; cursor: pointer; display: flex; align-items: center; justify-content: center; }

    .tools { display: flex; gap: 6px; justify-content: space-between; align-items: center; margin-top: 4px; }
    .btn { background: #1f2937; color: var(--text); border: 2px solid var(--border); padding: 8px 12px; border-radius: 8px; font-weight: bold; font-size: 0.75rem; cursor: pointer; box-shadow: 0 2px 4px rgba(0,0,0,0.3); }
    .sbtn { background: var(--primary); color: #000; border: none; padding: 9px 20px; border-radius: 8px; font-weight: 900; font-size: 0.85rem; cursor: pointer; margin-left: auto; box-shadow: 0 3px 6px rgba(16,185,129,0.4); }
    
    .deposit-card { background: #172033; border: 3px solid #4b5563; border-radius: 14px; padding: 12px; display: flex; flex-direction: column; gap: 10px; box-shadow: 0 4px 8px rgba(0,0,0,0.4); }
    .p-head { display: flex; justify-content: space-between; font-size: 0.75rem; color: var(--sub); border-bottom: 2px solid #27303f; padding-bottom: 6px; align-items: center; }
    
    .deposit-info { display: flex; justify-content: space-between; align-items: center; background: #020408; padding: 10px; border-radius: 8px; border: 2px solid rgba(16,185,129,0.3); }
    .dep-amount { font-size: 1.2rem; font-weight: 900; color: var(--primary); }
    .dep-name { font-size: 0.9rem; font-weight: bold; color: var(--text); }
    .dep-tag { background: rgba(16,185,129,0.2); color: var(--primary); padding: 2px 8px; border-radius: 6px; font-size: 0.75rem; font-weight: bold; border: 1px solid var(--primary); }

    .img-grid { display: flex; gap: 6px; overflow-x: auto; margin-top: 4px; }
    .img-thumb { width: 50px; height: 50px; border-radius: 6px; overflow: hidden; border: 1px solid var(--border); cursor: pointer; flex-shrink: 0; }
    .img-thumb img { width: 100%; height: 100%; object-fit: cover; }

    .summary-box { background: #0b0f19; border: 2px solid var(--card-border); border-radius: 12px; padding: 10px 14px; display: flex; flex-direction: column; gap: 6px; font-size: 0.8rem; }
    .summary-row { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px dashed #1f2937; padding-bottom: 4px; }
    .summary-row:last-child { border-bottom: none; }

    .del { background: transparent; border: 1px solid #4b5563; color: #9ca3af; font-size: 0.65rem; cursor: pointer; padding: 2px 6px; border-radius: 4px; }

    .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.88); z-index: 10000; justify-content: center; align-items: center; padding: 10px; }
    .m-card { background: var(--card); border: 3px solid var(--card-border); border-radius: 18px; width: 100%; max-width: 480px; max-height: 90vh; display: flex; flex-direction: column; padding: 16px; gap: 12px; box-shadow: 0 12px 30px rgba(0,0,0,0.7); }
    .m-head { font-weight: bold; font-size: 0.95rem; color: var(--primary); display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid #1f2937; padding-bottom: 8px; }
    
    #loadingOverlay { display: none; position: fixed; inset: 0; background: rgba(3,7,18,0.5); z-index: 30000; justify-content: center; align-items: center; pointer-events: none; }
    .loading-box { background: linear-gradient(145deg, #111827, #06241b); border: 4px solid var(--primary); padding: 20px 30px; border-radius: 20px; display: flex; align-items: center; gap: 14px; box-shadow: 0 10px 30px rgba(16,185,129,0.4); pointer-events: auto; }
    .spinner { width: 32px; height: 32px; border: 4px solid rgba(16, 185, 129, 0.2); border-top-color: var(--primary); border-radius: 50%; animation: spin 0.8s linear infinite; }
    @keyframes spin { to { transform: rotate(360deg); } }
  </style>
</head>
<body>

  <div id="loadingOverlay">
    <div class="loading-box">
      <div class="spinner"></div>
      <div id="loadingText" style="font-size: 1.05rem; font-weight: 900; color: var(--primary);">画像を全自動解析中...</div>
    </div>
  </div>

  <div class="app">
    <header>
      <div class="brand">DEPOSIT MANAGER + FULL OCR</div>
      <div class="btns">
        <button class="ibtn" onclick="exportData()">データ書き出し</button>
      </div>
    </header>

    <div class="date-banner" id="todayDateBanner">読み込み中...</div>

    <div class="main">
      <!-- 新規入金登録カード -->
      <section class="card new-post-card">
        <div class="c-title"><span>新規入金データの登録 (フルOCR)</span></div>
        
        <div style="display:grid; grid-template-columns: 1fr 1fr; gap: 10px;">
          <div class="form-group">
            <label class="form-label">入金日 (自動解析)</label>
            <input type="date" id="inputDate" class="d-inp">
          </div>
          <div class="form-group">
            <label class="form-label">担当 / 店舗 (自動解析)</label>
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
            <label class="form-label">入金名義 (自動解析)</label>
            <input type="text" id="inputPayer" class="d-inp" placeholder="自動入力または手入力">
          </div>
          <div class="form-group">
            <label class="form-label">金額 (円) (自動解析)</label>
            <input type="number" id="inputAmount" class="d-inp" placeholder="自動入力または手入力">
          </div>
        </div>

        <div class="form-group">
          <label class="form-label">入金書類・通帳の写し (複数選択可・全自動読取)</label>
          <div class="preview-container" id="previewContainer">
            <span style="font-size: 0.75rem; color: var(--sub); align-self: center;">画像を選ぶと日付・担当・名義・金額を自動抽出します</span>
          </div>
          <div class="tools">
            <label class="btn" style="cursor:pointer; background:var(--reply); color:white; border-color:var(--reply);">
              ＋ 画像を選択して自動解析
              <input type="file" accept="image/*" multiple style="display:none;" onchange="handleImagesSelect(event)">
            </label>
            <button class="sbtn" onclick="submitDeposit()">入金を登録する</button>
          </div>
        </div>
      </section>

      <!-- 担当・店舗別 合計集計 -->
      <section class="card" style="background:#0b0f19;">
        <div class="c-title"><span>担当・店舗別 合計集計</span><span id="summaryDateLabel" style="font-size:0.75rem; color:var(--sub);">累計</span></div>
        <div class="summary-box" id="dailySummaryBox">
          <div style="color:var(--sub); text-align:center; padding:6px;">データはありません</div>
        </div>
      </section>

      <!-- 入金履歴・タイムライン -->
      <section class="card">
        <div class="c-title"><span>入金履歴一覧</span></div>
        <div id="timeline" style="display:flex; flex-direction:column; gap:12px;"></div>
      </section>
    </div>
  </div>

  <!-- 画像拡大モーダル -->
  <div class="modal" id="imageModal" onclick="closeModal('imageModal')">
    <div class="m-card" style="max-width:90vw; background:#000; padding:10px;" onclick="event.stopPropagation()">
      <div class="m-head"><span>書類プレビュー</span><button class="ibtn" onclick="closeModal('imageModal')">✕</button></div>
      <div style="display:flex; justify-content:center; align-items:center; flex:1; overflow:hidden;">
        <img id="modalFullImg" style="max-width:100%; max-height:75vh; object-fit:contain; border-radius:8px;">
      </div>
    </div>
  </div>

  <script>
    let deposits = JSON.parse(localStorage.getItem('DM_DEPOSITS')) || [];
    let currentUploadedImages = [];

    window.onload = () => {
      initDate();
      renderTimeline();
      renderDailySummary();
    };

    function initDate() {
      const now = new Date();
      const weekdays = ['日', '月', '火', '水', '木', '金', '土'];
      document.getElementById('todayDateBanner').innerHTML = `<span>${now.getFullYear()}/${now.getMonth()+1}/${now.getDate()} (${weekdays[now.getDay()]})</span><span>FULL OCR 入金管理</span>`;
      
      const dateStr = `${now.getFullYear()}-${String(now.getMonth()+1).padStart(2,'0')}-${String(now.getDate()).padStart(2,'0')}`;
      document.getElementById('inputDate').value = dateStr;
    }

    // 複数画像選択 ＆ リサイズ ＆ 全自動OCR解析
    async function handleImagesSelect(e) {
      const files = e.target.files;
      if (!files || files.length === 0) return;

      showLoading('画像を最適化＆全自動解析中...');

      for (let i = 0; i < files.length; i++) {
        const file = files[i];
        const compressedDataUrl = await resizeImage(file);
        currentUploadedImages.push(compressedDataUrl);

        // 最初の1枚から「日付・担当・名義・金額」をまとめて抽出
        if (i === 0) {
          try {
            const result = await Tesseract.recognize(compressedDataUrl, 'jpn+eng', {
              logger: m => {}
            });
            const text = result.data.text;
            parseFullOcrText(text);
          } catch (err) {
            console.log('OCR解析エラー:', err);
          }
        }
      }

      hideLoading();
      renderPreviews();
      e.target.value = '';
    }

    function resizeImage(file) {
      return new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = ev => {
          const img = new Image();
          img.onload = () => {
            const canvas = document.createElement('canvas');
            let w = img.width, h = img.height;
            const MAX_SIZE = 900;
            if (w > MAX_SIZE || h > MAX_SIZE) {
              if (w > h) { h = Math.round(h * (MAX_SIZE / w)); w = MAX_SIZE; }
              else { w = Math.round(w * (MAX_SIZE / h)); h = MAX_SIZE; }
            }
            canvas.width = w; canvas.height = h;
            const ctx = canvas.getContext('2d');
            ctx.imageSmoothingEnabled = true;
            ctx.imageSmoothingQuality = 'high';
            ctx.drawImage(img, 0, 0, w, h);
            resolve(canvas.toDataURL('image/jpeg', 0.75));
          };
          img.src = ev.target.result;
        };
        reader.readAsDataURL(file);
      });
    }

    // 全自動解析ロジック（日付・担当・名義・金額）
    function parseFullOcrText(text) {
      console.log("OCR読取全文:", text);

      // 1. 【入金日付の自動解析】 (例: 2026/09/15, 2026年9月15日, 9月15日 など)
      const nowYear = new Date().getFullYear();
      
      // パターンA: 2026/9/15 または 2026年9月15日
      let dateMatch = text.match(/(20[2-3][0-9])[\/\-年\s]+([1-9]|1[0-2])[\/\-月\s]+([1-9]|[1-2][0-9]|3[0-1])/);
      if (dateMatch) {
        let y = dateMatch[1];
        let m = String(dateMatch[2]).padStart(2, '0');
        let d = String(dateMatch[3]).padStart(2, '0');
        document.getElementById('inputDate').value = `${y}-${m}-${d}`;
      } else {
        // パターンB: 月/日 のみ (例: 9/15, 9月15日)
        let shortDateMatch = text.match(/([1-9]|1[0-2])[\/\-月\s]+([1-9]|[1-2][0-9]|3[0-1])日?/);
        if (shortDateMatch) {
          let m = String(shortDateMatch[1]).padStart(2, '0');
          let d = String(shortDateMatch[2]).padStart(2, '0');
          document.getElementById('inputDate').value = `${nowYear}-${m}-${d}`;
        }
      }

      // 2. 【担当者 / 店舗の自動解析】
      const staffSelect = document.getElementById('inputStaff');
      if (text.includes('デコレ')) {
        staffSelect.value = 'デコレ';
      } else if (text.includes('大') || text.includes('大和地')) {
        staffSelect.value = '大和地';
      } else if (text.includes('草') || text.includes('草野')) {
        staffSelect.value = '草野';
      } else if (text.includes('菊') || text.includes('菊池')) {
        staffSelect.value = '菊池';
      } else if (text.includes('林')) {
        staffSelect.value = '林';
      } else if (text.includes('千') || text.includes('高橋')) {
        staffSelect.value = '高橋';
      } else {
        staffSelect.value = '未記入不明店舗';
      }

      // 3. 【金額の自動解析】
      const cleanedText = text.replace(/[,，]/g, '');
      const amountMatches = cleanedText.match(/(?:¥|￥)?\s*([1-9][0-9]{3,6})\s*(?:円)?/g);
      if (amountMatches && amountMatches.length > 0) {
        let nums = amountMatches.map(m => m.replace(/[^0-9]/g, '')).map(Number);
        let maxNum = Math.max(...nums);
        if (maxNum >= 1000) {
          document.getElementById('inputAmount').value = maxNum;
        }
      }

      // 4. 【入金名義の自動解析】
      const companyMatch = text.match(/(?:株式会社|有限会社|合同会社|カ\)|ｺ\)).{1,10}/);
      if (companyMatch) {
        document.getElementById('inputPayer').value = companyMatch[0];
      }
    }

    function renderPreviews() {
      const container = document.getElementById('previewContainer');
      if (currentUploadedImages.length === 0) {
        container.innerHTML = `<span style="font-size: 0.75rem; color: var(--sub); align-self: center;">画像を選ぶと日付・担当・名義・金額を自動抽出します</span>`;
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

    // 名義入力時の連動
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
        alert('「入金日」「入金名義」「金額」を確認してください。');
        return;
      }

      showLoading('登録中...');

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
      hideLoading();
    }

    function deleteDeposit(id) {
      if (confirm('この入金データを削除しますか？')) {
        deposits = deposits.filter(d => d.id !== id);
        saveAndRefresh();
      }
    }

    function saveAndRefresh() {
      localStorage.setItem('DM_DEPOSITS', JSON.stringify(deposits));
      renderTimeline();
      renderDailySummary();
    }

    function renderTimeline() {
      const container = document.getElementById('timeline');
      if (deposits.length === 0) {
        container.innerHTML = '<p style="color:var(--sub); font-size:0.75rem;">登録された入金データはありません</p>';
        return;
      }

      container.innerHTML = deposits.map(d => {
        const imagesHtml = d.images && d.images.length > 0 ? `
          <div class="img-grid">
            ${d.images.map(img => `<div class="img-thumb" onclick="openImageModal('${img}')"><img src="${img}"></div>`).join('')}
          </div>
        ` : '';

        return `
          <div class="deposit-card">
            <div class="p-head">
              <span>入金日: <strong>${d.date}</strong></span>
              <button class="del" onclick="deleteDeposit('${d.id}')">削除</button>
            </div>
            <div class="deposit-info">
              <div>
                <div style="display:flex; gap:6px; align-items:center; margin-bottom:4px;">
                  <span class="dep-tag">${d.staff}</span>
                  <span class="dep-name">${d.payer}</span>
                </div>
                <div style="font-size:0.7rem; color:var(--sub);">ID: ${d.id}</div>
              </div>
              <div class="dep-amount">¥${d.amount.toLocaleString()}</div>
            </div>
            ${imagesHtml}
          </div>
        `;
      }).join('');
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

    function showLoading(text) {
      document.getElementById('loadingText').innerText = text;
      document.getElementById('loadingOverlay').style.display = 'flex';
    }
    function hideLoading() {
      document.getElementById('loadingOverlay').style.display = 'none';
    }
    function closeModal(id) {
      document.getElementById(id).style.display = 'none';
    }
  </script>
</body>
</html>

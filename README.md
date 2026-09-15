

<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  <title>DEPOSIT AUTO MANAGER - High Accuracy</title>
  <!-- Tesseract.js (OCR用ライブラリ) -->
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@v5/dist/tesseract.min.js"></script>
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
    
    .drop-zone {
      border: 3px dashed var(--primary); border-radius: 14px; padding: 24px; text-align: center;
      background: rgba(16, 185, 129, 0.05); cursor: pointer; display: flex; flex-direction: column; gap: 8px; align-items: center; justify-content: center;
    }
    .drop-zone:active { background: rgba(16, 185, 129, 0.15); }

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
    
    #loadingOverlay { display: none; position: fixed; inset: 0; background: rgba(3,7,18,0.6); z-index: 30000; justify-content: center; align-items: center; pointer-events: none; }
    .loading-box { background: linear-gradient(145deg, #111827, #06241b); border: 4px solid var(--primary); padding: 20px 30px; border-radius: 20px; display: flex; align-items: center; gap: 14px; box-shadow: 0 10px 30px rgba(16,185,129,0.4); pointer-events: auto; }
    .spinner { width: 32px; height: 32px; border: 4px solid rgba(16, 185, 129, 0.2); border-top-color: var(--primary); border-radius: 50%; animation: spin 0.8s linear infinite; }
    @keyframes spin { to { transform: rotate(360deg); } }
  </style>
</head>
<body>

  <div id="loadingOverlay">
    <div class="loading-box">
      <div class="spinner"></div>
      <div id="loadingText" style="font-size: 1.05rem; font-weight: 900; color: var(--primary);">高精度解析中...</div>
    </div>
  </div>

  <div class="app">
    <header>
      <div class="brand">DEPOSIT AUTO MANAGER (HQ)</div>
      <div class="btns">
        <button class="ibtn" onclick="exportData()">データ書き出し</button>
      </div>
    </header>

    <div class="date-banner" id="todayDateBanner">読み込み中...</div>

    <div class="main">
      <section class="card new-post-card">
        <div class="c-title"><span>入金書類・通帳のアップロード (高精度OCR)</span></div>
        <label class="drop-zone">
          <div style="font-size: 1.5rem;">📸</div>
          <div style="font-weight: 900; font-size: 0.9rem; color: var(--primary);">タップして画像を選択・撮影</div>
          <div style="font-size: 0.7rem; color: var(--sub);">複数枚同時選択可。前処理をかけて高精度に自動整理します。</div>
          <input type="file" accept="image/*" multiple style="display:none;" onchange="handleImagesUpload(event)">
        </label>
      </section>

      <section class="card" style="background:#0b0f19;">
        <div class="c-title"><span>担当・店舗別 合計集計</span></div>
        <div class="summary-box" id="dailySummaryBox">
          <div style="color:var(--sub); text-align:center; padding:6px;">データはありません</div>
        </div>
      </section>

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
    let deposits = JSON.parse(localStorage.getItem('DAM_HQ_DEPOSITS')) || [];

    window.onload = () => {
      initDate();
      renderTable();
      renderDailySummary();
    };

    function initDate() {
      const now = new Date();
      const weekdays = ['日', '月', '火', '水', '木', '金', '土'];
      document.getElementById('todayDateBanner').innerHTML = `<span>${now.getFullYear()}/${now.getMonth()+1}/${now.getDate()} (${weekdays[now.getDay()].toUpperCase()})</span><span>高精度解析モード</span>`;
    }

    async function handleImagesUpload(e) {
      const files = e.target.files;
      if (!files || files.length === 0) return;

      showLoading(`全 ${files.length} 枚を高精度前処理＆解析中...`);

      for (let i = 0; i < files.length; i++) {
        const file = files[i];
        // 高精度化のための前処理済み画像データ生成
        const processedDataUrl = await preprocessImageForOCR(file);

        try {
          const result = await Tesseract.recognize(processedDataUrl, 'jpn+eng', {
            logger: m => {}
          });
          const text = result.data.text;
          const parsed = parseOcrDataHighAccuracy(text);

          deposits.unshift({
            id: 'D_' + Date.now() + '_' + i,
            date: parsed.date,
            staff: parsed.staff,
            payer: parsed.payer,
            amount: parsed.amount,
            image: processedDataUrl,
            createdAt: new Date().getTime()
          });
        } catch (err) {
          console.log('OCR解析エラー:', err);
          deposits.unshift({
            id: 'D_' + Date.now() + '_' + i,
            date: new Date().toISOString().split('T')[0],
            staff: '未記入不明店舗',
            payer: '解析エラー/不明',
            amount: 0,
            image: processedDataUrl,
            createdAt: new Date().getTime()
          });
        }
      }

      saveAndRefresh();
      hideLoading();
      e.target.value = '';
    }

    // 画像の前処理（コントラスト強調・グレースケール化によるOCR精度向上）
    function preprocessImageForOCR(file) {
      return new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = ev => {
          const img = new Image();
          img.onload = () => {
            const canvas = document.createElement('canvas');
            let w = img.width, h = img.height;
            const MAX_SIZE = 1200; // 解像度を高めに維持して文字潰れを防ぐ
            if (w > MAX_SIZE || h > MAX_SIZE) {
              if (w > h) { h = Math.round(h * (MAX_SIZE / w)); w = MAX_SIZE; }
              else { w = Math.round(w * (MAX_SIZE / h)); h = MAX_SIZE; }
            }
            canvas.width = w; canvas.height = h;
            const ctx = canvas.getContext('2d');
            ctx.drawImage(img, 0, 0, w, h);

            // 画像のコントラストとシャープネスを高める簡易フィルター処理
            let imgData = ctx.getImageData(0, 0, w, h);
            let d = imgData.data;
            for (let i = 0; i < d.length; i += 4) {
              // グレースケール化＆コントラスト強調
              let avg = (d[i] * 0.299 + d[i+1] * 0.587 + d[i+2] * 0.114);
              let enhanced = avg < 110 ? avg * 0.7 : (avg > 200 ? 255 : avg); // 黒文字を濃く、背景を白く
              d[i] = enhanced;
              d[i+1] = enhanced;
              d[i+2] = enhanced;
            }
            ctx.putImageData(imgData, 0, 0);
            resolve(canvas.toDataURL('image/jpeg', 0.85));
          };
          img.src = ev.target.result;
        };
        reader.readAsDataURL(file);
      });
    }

    // 高精度なキーワードマッチングとパターン抽出ロジック
    function parseOcrDataHighAccuracy(text) {
      const nowYear = new Date().getFullYear();
      let date = `${nowYear}-${String(new Date().getMonth()+1).padStart(2,'0')}-${String(new Date().getDate()).padStart(2,'0')}`;
      let staff = '未記入不明店舗';
      let payer = '不明名義';
      let amount = 0;

      // 1. 日付抽出 (年月日パターン)
      let dateMatch = text.match(/(20[2-3][0-9])[\/\-年\s]*([1-9]|1[0-2])[\/\-月\s]*([1-9]|[1-2][0-9]|3[0-1])/);
      if (dateMatch) {
        date = `${dateMatch[1]}-${String(dateMatch[2]).padStart(2, '0')}-${String(dateMatch[3]).padStart(2, '0')}`;
      } else {
        let shortDateMatch = text.match(/([1-9]|1[0-2])[\/\-月\s]+([1-9]|[1-2][0-9]|3[0-1])日?/);
        if (shortDateMatch) {
          date = `${nowYear}-${String(shortDateMatch[1]).padStart(2, '0')}-${String(shortDateMatch[2]).padStart(2, '0')}`;
        }
      }

      // 2. 担当 / 店舗 抽出（あいまい・部分一致対応）
      const cleanText = text.replace(/[\s\n\r]/g, '');
      if (cleanText.includes('デコレ')) {
        staff = 'デコレ';
      } else if (cleanText.includes('大和地') || cleanText.includes('大和') || cleanText.includes('豊')) {
        staff = '大和地';
      } else if (cleanText.includes('草野') || cleanText.includes('草')) {
        staff = '草野';
      } else if (cleanText.includes('菊池') || cleanText.includes('菊')) {
        staff = '菊池';
      } else if (cleanText.includes('林')) {
        staff = '林';
      } else if (cleanText.includes('高橋') || cleanText.includes('千')) {
        staff = '高橋';
      } else {
        staff = '未記入不明店舗';
      }

      // 3. 金額抽出（数値の最大値を検出、カンマ除去）
      const cleanedNumText = text.replace(/[,，]/g, '');
      const amountMatches = cleanedNumText.match(/(?:¥|￥|円)?\s*([1-9][0-9]{3,7})\s*(?:円)?/g);
      if (amountMatches && amountMatches.length > 0) {
        let nums = amountMatches.map(m => m.replace(/[^0-9]/g, '')).map(Number);
        let maxNum = Math.max(...nums);
        if (maxNum >= 1000) amount = maxNum;
      }

      // 4. 名義抽出（法人格や特徴的な文字列）
      let payerMatch = text.match(/(?:株式会社|有限会社|合同会社|カ\)|ｺ\)).{1,12}/);
      if (payerMatch) {
        payer = payerMatch[0];
      } else {
        // カタカナや人名っぽい部分の拾い上げ
        let kanaMatch = text.match(/[ァ-ンー]{3,10}/);
        if (kanaMatch) payer = kanaMatch[0];
      }

      return { date, staff, payer, amount };
    }

    function deleteDeposit(id) {
      if (confirm('この入金データを削除しますか？')) {
        deposits = deposits.filter(d => d.id !== id);
        saveAndRefresh();
      }
    }

    function saveAndRefresh() {
      localStorage.setItem('DAM_HQ_DEPOSITS', JSON.stringify(deposits));
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
        html += `
          <tr>
            <td>
              ${d.image ? `<div style="width:32px; height:32px; border-radius:4px; overflow:hidden; border:1px solid var(--border); cursor:pointer;" onclick="openImageModal('${d.image}')"><img src="${d.image}" style="width:100%; height:100%; object-fit:cover;"></div>` : '-'}
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
      downloadAnchor.setAttribute("download", `deposit_hq_data_${new Date().toISOString().split('T')[0]}.json`);
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

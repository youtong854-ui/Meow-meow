<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>律政風學習助手</title>
    <style id="dynamic-font">
        :root { --main-font: 'Noto Serif TC', serif; }
    </style>
    <style>
        :root { 
            --law-red: #800000; 
            --gold: #d4af37; 
            --bg: #0a0a0a; 
            --card: #151515; 
            --text: #ffffff; 
            --border: #2a2a2a;
        }
        
        body { 
            font-family: var(--main-font), 'Noto Sans TC', sans-serif; 
            background-color: var(--bg); 
            color: var(--text); 
            margin: 0; padding: 0; 
            display: flex; flex-direction: column; align-items: center; 
            min-height: 100vh; 
        }
        
        /* 導覽與經驗條 */
        nav { width: 100%; background: linear-gradient(to bottom, var(--law-red), #500000); padding: 15px; position: sticky; top: 0; z-index: 1000; border-bottom: 2px solid var(--gold); }
        .stats-grid { display: flex; justify-content: space-around; align-items: center; max-width: 500px; margin: 0 auto; }
        .exp-bar-bg { background: rgba(0,0,0,0.6); border-radius: 10px; height: 12px; width: 120px; overflow: hidden; border: 1px solid var(--gold); }
        .exp-bar-fill { background: linear-gradient(90deg, #d4af37, #fff, #d4af37); background-size: 200% 100%; animation: shine 2s infinite linear; height: 100%; width: 0%; transition: width 0.8s ease; }
        @keyframes shine { 0% { background-position: 200% 0; } 100% { background-position: -200% 0; } }

        /* 內容區控制 */
        .content { display: none; width: 95%; max-width: 500px; margin: 20px 0 120px 0; animation: fadeIn 0.4s; }
        .content.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        
        .card { background: var(--card); border-radius: 15px; padding: 20px; margin-bottom: 15px; border: 1px solid var(--border); }
        .btn { background: var(--law-red); color: white; border: 1px solid var(--gold); padding: 12px; border-radius: 10px; width: 100%; font-weight: bold; cursor: pointer; transition: 0.2s; font-family: inherit; margin-top: 10px; }
        .btn:active { transform: scale(0.98); }

        /* 課表樣式 */
        .table-container { overflow-x: auto; }
        #timetable-table { width: 100%; border-collapse: collapse; font-size: 0.75rem; min-width: 400px; }
        #timetable-table td, #timetable-table th { border: 1px solid #333; height: 45px; text-align: center; padding: 4px; }
        #timetable-table th { color: var(--gold); }

        /* 筆記樣式 */
        .note-card { background: #1a1a1a; border-left: 4px solid var(--law-red); padding: 15px; margin-bottom: 10px; border-radius: 0 10px 10px 0; }
        .note-card h4 { color: var(--gold); margin: 0 0 5px 0; }
        
        /* 計時器樣式 */
        .timer-display { font-size: 3rem; text-align: center; color: var(--gold); font-weight: bold; margin: 20px 0; font-family: monospace; }

        /* 選單欄 */
        .menu-bar { display: grid; grid-template-columns: repeat(5, 1fr); gap: 5px; width: 100%; max-width: 500px; padding: 12px; background: #111; position: fixed; bottom: 0; border-top: 1px solid var(--gold); z-index: 1000; }
        .menu-item { text-align: center; color: #666; font-size: 0.65rem; cursor: pointer; }
        .menu-item.active { color: var(--gold); font-weight: bold; }

        input, textarea, select { width: 100%; padding: 10px; background: #000; border: 1px solid #333; color: #fff; border-radius: 8px; box-sizing: border-box; margin-top: 5px; }
    </style>
</head>
<body>

<nav>
    <div class="stats-grid">
        <div>Lv. <span id="lv">1</span></div>
        <div class="exp-bar-bg"><div class="exp-bar-fill" id="exp-fill"></div></div>
        <div>EXP <span id="exp-val">0</span>/100</div>
    </div>
</nav>

<div id="home" class="content active">
    <div class="card">
        <h3 style="color:var(--gold); margin-top:0;">✨ 每日激勵</h3>
        <p id="daily-quote" style="font-style:italic; text-align:center; font-size:1.1rem;">「現在的努力，是為了以後有更多選擇。」</p>
        <p id="quote-author" style="text-align:right; color:#888; font-size:0.8rem;">— 佚名</p>
    </div>
    <div class="card">
        <h3 style="color:var(--gold); margin-top:0;">🏆 今日任務</h3>
        <div id="task-list">
            <label style="display:block; margin-bottom:10px;"><input type="checkbox" onchange="addExp(20)"> 專注學習 1 小時</label>
            <label style="display:block; margin-bottom:10px;"><input type="checkbox" onchange="addExp(15)"> 整理今日重點筆記</label>
            <label style="display:block; margin-bottom:10px;"><input type="checkbox" onchange="addExp(10)"> 閱讀 10 頁課外書籍</label>
        </div>
    </div>
</div>

<div id="timetable" class="content">
    <div class="card">
        <h3 style="color:var(--gold); margin-top:0;">📅 每週課表</h3>
        <div class="table-container">
            <table id="timetable-table">
                <thead><tr><th>節</th><th>一</th><th>二</th><th>三</th><th>四</th><th>五</th></tr></thead>
                <tbody id="timetable-body">
                    </tbody>
            </table>
        </div>
        <p style="font-size:0.7rem; color:#666; margin-top:10px;">* 點擊儲存格可快速編輯內容</p>
    </div>
</div>

<div id="notes" class="content">
    <div class="card">
        <h3 style="color:var(--gold); margin-top:0;">✍️ 新增筆記</h3>
        <input type="text" id="note-title" placeholder="標題...">
        <textarea id="note-content" rows="4" placeholder="輸入內容..."></textarea>
        <button class="btn" onclick="saveNote()">儲存筆記</button>
    </div>
    <div id="notes-list"></div>
</div>

<div id="timer" class="content">
    <div class="card">
        <h3 style="color:var(--gold); margin-top:0; text-align:center;">⌛ 專注番茄鐘</h3>
        <div class="timer-display" id="timer-run">25:00</div>
        <button class="btn" onclick="startTimer()">開始專注</button>
        <button class="btn" style="background:#333;" onclick="resetTimer()">重設</button>
    </div>
</div>

<div id="settings" class="content">
    <div class="card">
        <h3 style="color:var(--gold); margin-top:0;">⚙️ 個人設定</h3>
        <p>字體選擇</p>
        <select onchange="document.documentElement.style.setProperty('--main-font', this.value)">
            <option value="'Noto Serif TC', serif">精緻襯線 (預設)</option>
            <option value="'Noto Sans TC', sans-serif">簡潔黑體</option>
        </select>
        <button class="btn" onclick="alert('數據已同步至雲端（模擬）')">同步數據</button>
    </div>
</div>

<div class="menu-bar">
    <div class="menu-item active" onclick="switchTab('home')">🏠<br>首頁</div>
    <div class="menu-item" onclick="switchTab('timetable')">📅<br>課表</div>
    <div class="menu-item" onclick="switchTab('notes')">✍️<br>筆記</div>
    <div class="menu-item" onclick="switchTab('timer')">⏳<br>專注</div>
    <div class="menu-item" onclick="switchTab('settings')">⚙️<br>設定</div>
</div>

<script>
    // 1. 切換分頁
    function switchTab(tabId) {
        document.querySelectorAll('.content').forEach(c => c.classList.remove('active'));
        document.querySelectorAll('.menu-item').forEach(m => m.classList.remove('active'));
        document.getElementById(tabId).classList.add('active');
        event.currentTarget.classList.add('active');
    }

    // 2. 經驗值系統
    let exp = 0, lv = 1;
    function addExp(amount) {
        exp += amount;
        if (exp >= 100) { exp -= 100; lv++; alert("🎉 等級提升！目前 Lv." + lv); }
        document.getElementById('lv').innerText = lv;
        document.getElementById('exp-val').innerText = exp;
        document.getElementById('exp-fill').style.width = exp + '%';
    }

    // 3. 隨機激勵小語
    const quotes = [
        { t: "「越努力，越幸運。」", a: "民間諺語" },
        { t: "「卓越不是行為，而是習慣。」", a: "亞里斯多德" },
        { t: "「你的不甘，是因為你還不夠強。」", a: "勵志語錄" },
        { t: "「靜下心來，剩下的交給時間。」", a: "匿名" }
    ];
    const q = quotes[Math.floor(Math.random()*quotes.length)];
    document.getElementById('daily-quote').innerText = q.t;
    document.getElementById('quote-author').innerText = "— " + q.a;

    // 4. 生成課表
    const tbody = document.getElementById('timetable-body');
    for(let i=1; i<=8; i++) {
        let row = `<tr><td>${i}</td><td contenteditable="true"></td><td contenteditable="true"></td><td contenteditable="true"></td><td contenteditable="true"></td><td contenteditable="true"></td></tr>`;
        tbody.innerHTML += row;
    }

    // 5. 筆記功能
    function saveNote() {
        const t = document.getElementById('note-title').value;
        const c = document.getElementById('note-content').value;
        if(!t || !c) return;
        const div = document.createElement('div');
        div.className = 'card note-card';
        div.innerHTML = `<h4>${t}</h4><p>${c}</p>`;
        document.getElementById('notes-list').prepend(div);
        document.getElementById('note-title').value = '';
        document.getElementById('note-content').value = '';
        addExp(5);
    }

    // 6. 番茄鐘邏輯
    let timerIdx, timeLeft = 1500;
    function startTimer() {
        if(timerIdx) return;
        timerIdx = setInterval(() => {
            timeLeft--;
            let m = Math.floor(timeLeft/60), s = timeLeft%60;
            document.getElementById('timer-run').innerText = `${m}:${s<10?'0':''}${s}`;
            if(timeLeft <= 0) {
                clearInterval(timerIdx);
                alert("專注結束！獲得 30 經驗值");
                addExp(30);
                resetTimer();
            }
        }, 1000);
    }
    function resetTimer() {
        clearInterval(timerIdx);
        timerIdx = null; timeLeft = 1500;
        document.getElementById('timer-run').innerText = "25:00";
    }
</script>
</body>
</html>

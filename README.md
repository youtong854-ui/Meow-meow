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
    
    nav { width: 100%; background: linear-gradient(to bottom, var(--law-red), #500000); padding: 15px; position: sticky; top: 0; z-index: 1000; border-bottom: 2px solid var(--gold); }
    .stats-grid { display: flex; justify-content: space-around; align-items: center; max-width: 500px; margin: 0 auto; cursor: pointer; }
    .exp-bar-bg { background: rgba(0,0,0,0.6); border-radius: 10px; height: 12px; width: 120px; overflow: hidden; border: 1px solid var(--gold); }
    .exp-bar-fill { background: linear-gradient(90deg, #d4af37, #fff, #d4af37); background-size: 200% 100%; animation: shine 2s infinite linear; height: 100%; width: 0%; transition: width 0.8s ease; }
    @keyframes shine { 0% { background-position: 200% 0; } 100% { background-position: -200% 0; } }

    .content { display: none; width: 95%; max-width: 500px; margin: 20px 0 120px 0; animation: fadeIn 0.4s; }
    .content.active { display: block; }
    @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    
    .card { background: var(--card); border-radius: 15px; padding: 20px; margin-bottom: 15px; border: 1px solid var(--border); }
    .btn { background: var(--law-red); color: white; border: 1px solid var(--gold); padding: 12px; border-radius: 10px; width: 100%; font-weight: bold; cursor: pointer; transition: 0.2s; font-family: inherit; }
    
    .menu-bar { display: grid; grid-template-columns: repeat(5, 1fr); gap: 5px; width: 100%; max-width: 500px; padding: 12px; background: #111; position: fixed; bottom: 0; border-top: 1px solid var(--gold); z-index: 1000; }
    .menu-item { text-align: center; color: #666; font-size: 0.65rem; cursor: pointer; transition: 0.3s; }
    .menu-item.active { color: var(--gold); font-weight: bold; }

    .quote-text { font-style: italic; color: var(--gold); font-size: 1.1rem; line-height: 1.6; text-align: center; margin-bottom: 10px; }
    .quote-author { text-align: right; font-size: 0.85rem; color: #888; }
    
    .task-item { display: flex; align-items: center; background: #1a1a1a; padding: 12px; margin-bottom: 8px; border-radius: 8px; border-left: 3px solid var(--gold); }
    .task-checkbox { margin-right: 12px; transform: scale(1.2); }
</style>

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
        <h3 style="color:var(--gold); margin-top:0;">✨ 每日激勵小語</h3>
        <div class="quote-text" id="daily-quote">「卓越不是一種行為，而是一種習慣。」</div>
        <div class="quote-author" id="quote-author">— 亞里斯多德</div>
    </div>

    <div class="card">
        <h3 style="color:var(--gold); margin-top:0;">🏆 今日挑戰</h3>
        <div id="task-list">
            <div class="task-item">
                <input type="checkbox" class="task-checkbox" onchange="addExp(20)">
                <span>深度專注學習 50 分鐘</span>
            </div>
            <div class="task-item">
                <input type="checkbox" class="task-checkbox" onchange="addExp(15)">
                <span>整理今日學習筆記</span>
            </div>
            <div class="task-item">
                <input type="checkbox" class="task-checkbox" onchange="addExp(10)">
                <span>回顧一個過往的錯誤並訂正</span>
            </div>
        </div>
        <p style="font-size: 0.75rem; color: #666; margin-top: 10px;">* 每完成一項任務可獲得經驗值</p>
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
    // 基礎邏輯：切換頁籤
    function switchTab(tabId) {
        document.querySelectorAll('.content').forEach(c => c.classList.remove('active'));
        document.querySelectorAll('.menu-item').forEach(m => m.classList.remove('active'));
        document.getElementById(tabId).classList.add('active');
        event.currentTarget.classList.add('active');
    }

    // 經驗值系統
    let exp = 0;
    let lv = 1;
    function addExp(amount) {
        exp += amount;
        if (exp >= 100) {
            exp -= 100;
            lv++;
            alert("恭喜升級！目前等級：Lv." + lv);
        }
        updateStats();
    }

    function updateStats() {
        document.getElementById('lv').innerText = lv;
        document.getElementById('exp-val').innerText = exp;
        document.getElementById('exp-fill').style.width = exp + '%';
    }

    // 隨機激勵小語資料庫
    const quotes = [
        { text: "「卓越不是一種行為，而是一種習慣。」", author: "亞里斯多德" },
        { text: "「你的時間有限，所以不要為別人而活。」", author: "賈伯斯" },
        { text: "「成功的秘訣在於開始。」", author: "馬克·吐溫" },
        { text: "「不要等待機會，而要創造機會。」", author: "匿名" },
        { text: "「今天的努力，是為了明天更有選擇的權利。」", author: "網路語錄" }
    ];

    // 初始化隨機語錄
    const randomQuote = quotes[Math.floor(Math.random() * quotes.length)];
    document.getElementById('daily-quote').innerText = randomQuote.text;
    document.getElementById('quote-author').innerText = "— " + randomQuote.author;
</script>

</body>

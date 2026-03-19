<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>心靈成長學習助手</title>
    <style>
        :root { 
            --sage-green: #8DAA91; 
            --soft-white: #F8F9F5; 
            --warm-beige: #E8E2D6; 
            --text-gray: #4A4A4A; 
            --accent-orange: #E6A07E;
            --border: #D1D9CF;
        }
        
        body { 
            font-family: 'Noto Sans TC', sans-serif; 
            background-color: var(--soft-white); 
            color: var(--text-gray); 
            margin: 0; padding: 0; 
            display: flex; flex-direction: column; align-items: center; 
            min-height: 100vh; 
        }
        
        /* 導覽與療癒系經驗條 */
        nav { 
            width: 100%; 
            background: white; 
            padding: 15px; 
            position: sticky; 
            top: 0; z-index: 1000; 
            border-bottom: 1px solid var(--border);
            box-shadow: 0 2px 5px rgba(0,0,0,0.05);
        }
        .stats-grid { display: flex; justify-content: space-around; align-items: center; max-width: 500px; margin: 0 auto; font-weight: 500; }
        .exp-bar-bg { background: #eee; border-radius: 10px; height: 10px; width: 150px; overflow: hidden; }
        .exp-bar-fill { background: linear-gradient(90deg, var(--sage-green), #B8D8BA); height: 100%; width: 0%; transition: width 0.6s ease; }

        /* 內容區 */
        .content { display: none; width: 95%; max-width: 500px; margin: 20px 0 120px 0; animation: fadeIn 0.5s; }
        .content.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .card { background: white; border-radius: 20px; padding: 25px; margin-bottom: 20px; border: 1px solid var(--border); box-shadow: 0 4px 15px rgba(0,0,0,0.02); }
        .btn { background: var(--sage-green); color: white; border: none; padding: 12px; border-radius: 12px; width: 100%; font-weight: bold; cursor: pointer; transition: 0.3s; font-family: inherit; margin-top: 10px; }
        .btn:hover { background: #7A967E; }
        .btn:active { transform: scale(0.98); }

        /* 任務列表增強 */
        .task-input-group { display: flex; gap: 8px; margin-bottom: 15px; }
        .task-item { display: flex; align-items: center; background: var(--soft-white); padding: 12px; margin-bottom: 10px; border-radius: 12px; border: 1px solid #eee; }
        .task-item input[type="checkbox"] { margin-right: 12px; accent-color: var(--sage-green); width: 18px; height: 18px; }
        .task-item.completed { opacity: 0.6; text-decoration: line-through; }

        /* 課表與筆記優化 */
        #timetable-table { width: 100%; border-collapse: separate; border-spacing: 4px; font-size: 0.8rem; }
        #timetable-table td { border-radius: 6px; background: var(--soft-white); height: 45px; text-align: center; border: 1px solid #efefef; }
        #timetable-table th { color: var(--sage-green); padding-bottom: 8px; }

        /* 計時器 */
        .timer-display { font-size: 3.5rem; text-align: center; color: var(--sage-green); font-weight: 300; margin: 20px 0; letter-spacing: 2px; }

        /* 底部導覽列 */
        .menu-bar { display: grid; grid-template-columns: repeat(5, 1fr); gap: 5px; width: 100%; max-width: 500px; padding: 15px 10px; background: white; position: fixed; bottom: 0; border-top: 1px solid var(--border); z-index: 1000; }
        .menu-item { text-align: center; color: #A0A0A0; font-size: 0.7rem; cursor: pointer; transition: 0.3s; }
        .menu-item.active { color: var(--sage-green); }

        input, textarea, select { width: 100%; padding: 12px; background: #fff; border: 1px solid #ddd; border-radius: 10px; box-sizing: border-box; font-family: inherit; outline: none; }
        input:focus { border-color: var(--sage-green); }
    </style>
</head>
<body>

<nav>
    <div class="stats-grid">
        <div>等級 <span id="lv">1</span></div>
        <div class="exp-bar-bg"><div class="exp-bar-fill" id="exp-fill"></div></div>
        <div style="color: var(--sage-green);">能量 <span id="exp-val">0</span>%</div>
    </div>
</nav>

<div id="home" class="content active">
    <div class="card" style="border-left: 6px solid var(--accent-orange);">
        <h3 style="color:var(--accent-orange); margin:0 0 10px 0; font-size: 1rem;">🌱 今日心靈養分</h3>
        <p id="daily-quote" style="line-height: 1.6; font-size: 1.1rem; margin-bottom: 5px;"></p>
        <p id="quote-author" style="text-align:right; color:#AAA; font-size:0.8rem;"></p>
    </div>

    <div class="card">
        <h3 style="color:var(--sage-green); margin-top:0;">📝 自定義成長任務</h3>
        <div class="task-input-group">
            <input type="text" id="new-task-input" placeholder="想完成什麼小事？">
            <button class="btn" onclick="addNewTask()" style="width: 80px; margin-top:0;">增加</button>
        </div>
        <div id="dynamic-task-list">
            </div>
    </div>
</div>

<div id="timetable" class="content">
    <div class="card">
        <h3 style="color:var(--sage-green); margin-top:0;">📅 學習時光機</h3>
        <div style="overflow-x: auto;">
            <table id="timetable-table">
                <thead><tr><th>節</th><th>一</th><th>二</th><th>三</th><th>四</th><th>五</th></tr></thead>
                <tbody id="timetable-body"></tbody>
            </table>
        </div>
    </div>
</div>

<div id="notes" class="content">
    <div class="card">
        <h3 style="color:var(--sage-green); margin-top:0;">💭 隨手記下心情與重點</h3>
        <input type="text" id="note-title" placeholder="標題">
        <textarea id="note-content" rows="4" placeholder="寫下你的想法..."></textarea>
        <button class="btn" onclick="saveNote()">收藏這則筆記</button>
    </div>
    <div id="notes-list"></div>
</div>

<div id="timer" class="content">
    <div class="card" style="text-align: center;">
        <h3 style="color:var(--sage-green); margin-top:0;">🧘 靜心專注時刻</h3>
        <div class="timer-display" id="timer-run">25:00</div>
        <div style="display: flex; gap: 10px;">
            <button class="btn" onclick="startTimer()">開始</button>
            <button class="btn" style="background:#DDD; color:#666;" onclick="resetTimer()">重置</button>
        </div>
    </div>
</div>

<div id="settings" class="content">
    <div class="card">
        <h3 style="color:var(--sage-green); margin-top:0;">⚙️ 個人空間設定</h3>
        <p style="font-size: 0.9rem;">環境字體</p>
        <select onchange="document.body.style.fontFamily = this.value">
            <option value="'Noto Sans TC', sans-serif">現代黑體</option>
            <option value="'Noto Serif TC', serif">優雅襯線</option>
        </select>
        <button class="btn" style="background: var(--accent-orange);" onclick="confirm('是否要清空今日緩存？')">重置所有數據</button>
    </div>
</div>

<div class="menu-bar">
    <div class="menu-item active" onclick="switchTab('home')">🌿<br>首頁</div>
    <div class="menu-item" onclick="switchTab('timetable')">🗓️<br>課表</div>
    <div class="menu-item" onclick="switchTab('notes')">☁️<br>筆記</div>
    <div class="menu-item" onclick="switchTab('timer')">⏳<br>專注</div>
    <div class="menu-item" onclick="switchTab('settings')">⚙️<br>設定</div>
</div>

<script>
    // 基礎變數
    let exp = 0, lv = 1;
    let timerIdx, timeLeft = 1500;

    // 1. 切換分頁
    function switchTab(tabId) {
        document.querySelectorAll('.content').forEach(c => c.classList.remove('active'));
        document.querySelectorAll('.menu-item').forEach(m => m.classList.remove('active'));
        document.getElementById(tabId).classList.add('active');
        event.currentTarget.classList.add('active');
    }

    // 2. 經驗與升級
    function addExp(amount) {
        exp += amount;
        if (exp >= 100) { 
            exp -= 100; lv++; 
            alert("🌟 你的心靈能量提升了！目前等級：Lv." + lv); 
        }
        document.getElementById('lv').innerText = lv;
        document.getElementById('exp-val').innerText = exp;
        document.getElementById('exp-fill').style.width = exp + '%';
    }

    // 3. 增加自定義任務
    function addNewTask() {
        const input = document.getElementById('new-task-input');
        const text = input.value.trim();
        if(!text) return;

        const container = document.getElementById('dynamic-task-list');
        const taskDiv = document.createElement('div');
        taskDiv.className = 'task-item';
        taskDiv.innerHTML = `
            <input type="checkbox" onchange="completeTask(this)">
            <span>${text}</span>
        `;
        container.prepend(taskDiv);
        input.value = '';
    }

    function completeTask(checkbox) {
        if(checkbox.checked) {
            checkbox.parentElement.classList.add('completed');
            addExp(15);
            checkbox.disabled = true; // 完成後不可重複點擊
        }
    }

    // 4. 隨機小語 (心理系風格)
    const quotes = [
        { t: "「每個人都是自己生命的主宰，唯有愛與理解能治癒一切。」", a: "阿德勒" },
        { t: "「當我接受了真實的自己，改變才真正開始。」", a: "卡爾·羅傑斯" },
        { t: "「情緒如天氣，會來也會走，你只需要靜靜觀察。」", a: "正念引導" },
        { t: "「不完美的你，其實已經足夠好。」", a: "心靈慰藉" }
    ];
    const q = quotes[Math.floor(Math.random()*quotes.length)];
    document.getElementById('daily-quote').innerText = q.t;
    document.getElementById('quote-author').innerText = "— " + q.a;

    // 5. 課表生成
    const tb = document.getElementById('timetable-body');
    for(let i=1; i<=8; i++) {
        tb.innerHTML += `<tr><td>${i}</td><td contenteditable="true"></td><td contenteditable="true"></td><td contenteditable="true"></td><td contenteditable="true"></td><td contenteditable="true"></td></tr>`;
    }

    // 6. 筆記功能
    function saveNote() {
        const t = document.getElementById('note-title').value;
        const c = document.getElementById('note-content').value;
        if(!t || !c) return;
        const div = document.createElement('div');
        div.className = 'card';
        div.style.borderLeft = "4px solid var(--sage-green)";
        div.innerHTML = `<h4 style="margin:0; color:var(--sage-green);">${t}</h4><p style="font-size:0.9rem; color:#666;">${c}</p>`;
        document.getElementById('notes-list').prepend(div);
        document.getElementById('note-title').value = '';
        document.getElementById('note-content').value = '';
        addExp(10);
    }

    // 7. 番茄鐘
    function startTimer() {
        if(timerIdx) return;
        timerIdx = setInterval(() => {
            timeLeft--;
            let m = Math.floor(timeLeft/60), s = timeLeft%60;
            document.getElementById('timer-run').innerText = `${m}:${s<10?'0':''}${s}`;
            if(timeLeft <= 0) {
                clearInterval(timerIdx);
                alert("🍵 辛苦了，深呼吸一下，休息 5 分鐘吧！");
                addExp(30);
                resetTimer();
            }
        }, 1000);
    }
    function resetTimer() {
        clearInterval(timerIdx); timerIdx = null; timeLeft = 1500;
        document.getElementById('timer-run').innerText = "25:00";
    }
</script>

</body>
</html>

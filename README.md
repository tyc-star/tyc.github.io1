<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>小學數學怪獸大對決 Math Monster Arena</title>
    <style>
        :root {
            --primary: #4a90e2;
            --secondary: #f5a623;
            --success: #7ed321;
            --danger: #d0021b;
            --dark: #2c3e50;
            --light: #ecf0f1;
        }

        * { box-sizing: border-box; font-family: 'Segoe UI', Arial, "Microsoft JhengHei", sans-serif; margin: 0; padding: 0; }
        body { background: #1a1a2e; color: #fff; min-height: 100vh; display: flex; justify-content: center; align-items: center; }
        #app { width: 100%; max-width: 960px; min-height: 680px; background: #16213e; border-radius: 16px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); overflow: hidden; position: relative; display: flex; flex-direction: column; }

        header { background: #0f3460; padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid #e94560; }
        .user-info { display: flex; gap: 15px; font-size: 14px; align-items: center; }
        .gold-badge { background: var(--secondary); color: #000; padding: 4px 12px; border-radius: 20px; font-weight: bold; }

        .screen { display: none; padding: 20px; flex: 1; flex-direction: column; }
        .screen.active { display: flex; }

        /* Auth Screen */
        .auth-container { max-width: 400px; margin: auto; width: 100%; background: #0f3460; padding: 30px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.3); }
        .auth-tabs { display: flex; margin-bottom: 20px; border-bottom: 1px solid #333; }
        .tab-btn { flex: 1; padding: 10px; background: none; border: none; color: #888; cursor: pointer; font-size: 16px; font-weight: bold; }
        .tab-btn.active { color: var(--secondary); border-bottom: 3px solid var(--secondary); }
        .form-group { margin-bottom: 15px; }
        .form-group label { display: block; margin-bottom: 5px; font-size: 14px; color: #ccc; }
        .form-group input, .form-group select { width: 100%; padding: 10px; border-radius: 6px; border: 1px solid #333; background: #1a1a2e; color: #fff; }
        .btn { padding: 12px; background: var(--primary); color: white; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; width: 100%; font-size: 16px; transition: 0.2s; }
        .btn:hover { opacity: 0.9; transform: translateY(-2px); }
        .btn-secondary { background: var(--secondary); color: #000; }
        .btn-danger { background: var(--danger); }

        /* Main Menu */
        .grid-menu { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-top: 20px; }
        .menu-card { background: #0f3460; border-radius: 12px; padding: 25px; text-align: center; cursor: pointer; border: 2px solid transparent; transition: 0.3s; }
        .menu-card:hover { border-color: var(--secondary); transform: translateY(-5px); }
        .menu-card h3 { margin-bottom: 10px; color: var(--secondary); }

        /* Deck & Monsters */
        .monster-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 15px; overflow-y: auto; max-height: 500px; padding: 10px; }
        .monster-card { background: #0f3460; border-radius: 10px; padding: 12px; border: 2px solid #333; position: relative; display: flex; flex-direction: column; align-items: center; }
        .monster-card.god { border-color: #f1c40f; background: linear-gradient(135deg, #0f3460 0%, #3e2723 100%); }
        .monster-card.equipped { border-color: var(--success); }
        .monster-avatar { width: 80px; height: 80px; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 36px; margin-bottom: 10px; background: rgba(255,255,255,0.1); }
        .stat-badge { font-size: 11px; background: rgba(0,0,0,0.4); padding: 2px 6px; border-radius: 4px; margin: 2px; }

        /* Gacha */
        .gacha-stage { text-align: center; padding: 40px; }
        .egg { font-size: 100px; cursor: pointer; display: inline-block; animation: bounce 2s infinite; }
        @keyframes bounce { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-20px); } }

        /* Battle Arena */
        .battle-arena { display: flex; flex-direction: column; height: 100%; justify-content: space-between; }
        .battle-field { display: flex; justify-content: space-between; align-items: center; padding: 20px; min-height: 220px; background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"><rect width="100" height="100" fill="%231a1a2e"/><path d="M0 50 Q 50 0 100 50 T 200 50" stroke="%2316213e" fill="none" stroke-width="2"/></svg>'); position: relative; border-radius: 12px; }
        .fighter { text-align: center; width: 140px; position: relative; transition: transform 0.2s; }
        .fighter-avatar { font-size: 60px; }
        .hp-bar-bg { width: 100%; height: 12px; background: #333; border-radius: 6px; overflow: hidden; margin-top: 5px; }
        .hp-bar-fill { height: 100%; background: var(--success); width: 100%; transition: width 0.3s; }

        .quiz-box { background: #0f3460; padding: 20px; border-radius: 12px; margin-top: 15px; }
        .timer-bar { height: 6px; background: var(--secondary); width: 100%; border-radius: 3px; transition: width 1s linear; margin-bottom: 15px; }
        .options-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 15px; }

        /* FX Animations */
        .attack-fx { position: absolute; font-size: 40px; z-index: 99; pointer-events: none; transition: all 0.5s ease-in-out; }
        .shake { animation: shakeAnim 0.4s; }
        @keyframes shakeAnim { 0% { transform: translate(0); } 20% { transform: translate(-10px, 10px); } 40% { transform: translate(10px, -10px); } 60% { transform: translate(-10px, -10px); } 80% { transform: translate(10px, 10px); } 100% { transform: translate(0); } }

        /* Modals */
        .modal { display: none; position: absolute; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.8); justify-content: center; align-items: center; z-index: 1000; }
        .modal-content { background: #0f3460; padding: 30px; border-radius: 12px; max-width: 450px; width: 90%; text-align: center; }
    </style>
</head>
<body>

<div id="app">
    <header>
        <h2>⚔️ 數理怪獸擂台</h2>
        <div class="user-info" id="header-user" style="display:none;">
            <span id="user-name-display">校名 - 1A(01) 暱稱</span>
            <span class="gold-badge">🪙 <span id="user-gold">0</span></span>
            <button onclick="logout()" style="background:none; border:none; color:#aaa; cursor:pointer;">登出</button>
        </div>
    </header>

    <!-- 1. 登入/註冊 頁面 -->
    <div id="screen-auth" class="screen active">
        <div class="auth-container">
            <div class="auth-tabs">
                <button class="tab-btn active" onclick="switchAuthMode('login')">玩家登入</button>
                <button class="tab-btn" onclick="switchAuthMode('register')">新學生註冊</button>
            </div>
            
            <form id="auth-form" onsubmit="handleAuth(event)">
                <div id="register-fields">
                    <div class="form-group">
                        <label>學校名稱 (中文全名)</label>
                        <input type="text" id="reg-school" placeholder="例：香港培正小學">
                    </div>
                    <div class="form-group" style="display:flex; gap:10px;">
                        <div style="flex:1;">
                            <label>班別</label>
                            <input type="text" id="reg-class" placeholder="例：5A">
                        </div>
                        <div style="flex:1;">
                            <label>班號</label>
                            <input type="number" id="reg-classno" placeholder="例：12">
                        </div>
                    </div>
                </div>

                <div class="form-group">
                    <label>個人別號 / 遊戲暱稱</label>
                    <input type="text" id="auth-username" required placeholder="開發者輸入: MR TO">
                </div>
                <div class="form-group">
                    <label>密碼</label>
                    <input type="password" id="auth-password" required placeholder="開發者密碼: 3056">
                </div>

                <button type="submit" class="btn" id="auth-submit-btn">進入遊戲</button>
            </form>
        </div>
    </div>

    <!-- 2. 主功能選單 -->
    <div id="screen-menu" class="screen">
        <h2 style="text-align: center; margin-bottom: 20px;">請選擇遊戲模式</h2>
        <div class="grid-menu">
            <div class="menu-card" onclick="showScreen('screen-deck')">
                <h3>🐉 我的怪獸背包</h3>
                <p>檢視怪獸、升級、進化與能力值</p>
            </div>
            <div class="menu-card" onclick="showScreen('screen-gacha')">
                <h3>🥚 幸運抽蛋 / 經驗包</h3>
                <p>抽取新怪獸卡片或經驗提升道具</p>
            </div>
            <div class="menu-card" onclick="startSetupBattle('single')">
                <h3>🏰 單人劇情 / 打 Boss</h3>
                <p>挑戰強大 Boss 獲取大量經驗值</p>
            </div>
            <div class="menu-card" onclick="startSetupBattle('pvp_public')">
                <h3>🌐 公開隨機對戰</h3>
                <p>匹配線上同學，20秒無人自動配對 AI</p>
            </div>
            <div class="menu-card" onclick="startSetupBattle('pvp_private')">
                <h3>🔑 私人房間對戰</h3>
                <p>開房邀請好友，勝者奪取 50 金幣！</p>
            </div>
        </div>
    </div>

    <!-- 3. 怪獸背包頁面 -->
    <div id="screen-deck" class="screen">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
            <h3>我的怪獸陣容 (點擊選擇上陣)</h3>
            <div>
                <span style="margin-right:15px;">經驗包: 📦 <b id="exp-item-count">0</b></span>
                <button class="btn btn-secondary" style="width:auto; padding:6px 15px;" onclick="showScreen('screen-menu')">返回主頁</button>
            </div>
        </div>
        <div class="monster-grid" id="monster-list-container"></div>
    </div>

    <!-- 4. 抽蛋頁面 -->
    <div id="screen-gacha" class="screen">
        <div style="display:flex; justify-content:space-between; align-items:center;">
            <h3>怪獸扭蛋機</h3>
            <button class="btn btn-secondary" style="width:auto; padding:6px 15px;" onclick="showScreen('screen-menu')">返回主頁</button>
        </div>
        <div class="gacha-stage">
            <div class="egg" onclick="drawGacha()">🥚</div>
            <p style="margin-top:20px;">消耗 <b>100 金幣</b> 進行一次抽蛋</p>
            <p style="color:#aaa; font-size:14px; margin-top:5px;">有機率獲得一般怪獸、山海經神獸，或經驗包！<br>重複獲得怪獸將自動轉換為 <b>經驗包 × 2</b></p>
            <button class="btn btn-secondary" style="width:200px; margin:20px auto 0;" onclick="drawGacha()">花費 100 金幣抽卡</button>
        </div>
    </div>

    <!-- 5. 戰鬥範疇與年級選擇頁面 -->
    <div id="screen-battle-setup" class="screen">
        <h3 style="margin-bottom:15px;">對戰前設定：選擇數學挑戰範疇</h3>
        <div class="form-group">
            <label>選擇年級</label>
            <select id="setup-grade">
                <option value="1">小一</option><option value="2">小二</option>
                <option value="3">小三</option><option value="4">小四</option>
                <option value="5" selected>小五</option><option value="6">小六</option>
            </select>
        </div>
        <div class="form-group">
            <label>數學範疇</label>
            <select id="setup-category">
                <option value="number">數 (Number)</option>
                <option value="measure">度量 (Measurement)</option>
                <option value="shape">圖形與空間 (Shape & Space)</option>
                <option value="data">數據處理 (Data Handling)</option>
                <option value="algebra">代數初步 (Algebra)</option>
            </select>
        </div>
        <button class="btn" onclick="initiateBattleStart()">開始搜尋 / 對戰</button>
        <button class="btn btn-secondary" style="margin-top:10px;" onclick="showScreen('screen-menu')">取消</button>
    </div>

    <!-- 6. 戰鬥畫面 -->
    <div id="screen-battle" class="screen">
        <div class="battle-arena">
            <div class="battle-field" id="battle-field-elem">
                <!-- 玩家怪獸 -->
                <div class="fighter" id="player-fighter">
                    <div class="fighter-avatar" id="p-avatar">🐲</div>
                    <div id="p-name" style="font-weight:bold; font-size:14px;">我的怪獸</div>
                    <div style="font-size:12px; color:#aaa;">Lv.<span id="p-lv">1</span> (<span id="p-elem">火</span>)</div>
                    <div class="hp-bar-bg"><div class="hp-bar-fill" id="p-hp-fill"></div></div>
                    <div style="font-size:11px;" id="p-hp-text">100/100</div>
                </div>

                <div style="font-size:24px; font-weight:bold; color:var(--secondary);">VS</div>

                <!-- 對手 / Boss -->
                <div class="fighter" id="enemy-fighter">
                    <div class="fighter-avatar" id="e-avatar">👹</div>
                    <div id="e-name" style="font-weight:bold; font-size:14px;">敵方怪獸</div>
                    <div style="font-size:12px; color:#aaa;">Lv.<span id="e-lv">1</span> (<span id="e-elem">水</span>)</div>
                    <div class="hp-bar-bg"><div class="hp-bar-fill" id="e-hp-fill"></div></div>
                    <div style="font-size:11px;" id="e-hp-text">100/100</div>
                </div>
            </div>

            <!-- 答題與操作區 -->
            <div class="quiz-box">
                <div class="timer-bar" id="quiz-timer"></div>
                <div style="display:flex; justify-content:space-between; font-size:14px; margin-bottom:10px;">
                    <span id="quiz-cat-tag" style="color:var(--secondary);">範疇: 數</span>
                    <span>剩餘時間: <b id="timer-text" style="color:red;">40</b>s</span>
                </div>
                <h3 id="quiz-question" style="min-height:50px; font-size:18px;">題目載入中...</h3>
                <div class="options-grid" id="quiz-options"></div>
            </div>
        </div>
    </div>
</div>

<!-- 通用 Modal 彈窗 -->
<div class="modal" id="game-modal">
    <div class="modal-content">
        <h3 id="modal-title" style="margin-bottom:15px;">提示</h3>
        <p id="modal-msg" style="margin-bottom:20px; color:#ccc;"></p>
        <button class="btn" onclick="closeModal()">確定</button>
    </div>
</div>

<script>
// ==================== 1. 遊戲資料庫與全域變數 ====================

// 屬相相剋表
const ELEMENT_CHART = {
    '火': { strong: ['草', '冰', '蟲'], weak: ['水', '岩石', '龍'] },
    '水': { strong: ['火', '地面', '岩石'], weak: ['草', '電'] },
    '草': { strong: ['水', '地面', '岩石'], weak: ['火', '毒', '蟲', '飛行', '冰'] },
    '電': { strong: ['水', '飛行'], weak: ['地面', '草', '電'] },
    '格鬥': { strong: ['岩石', '冰'], weak: ['超能力', '飛行'] },
    '地面': { strong: ['火', '電', '毒', '岩石'], weak: ['草', '水', '冰'] },
    '冰': { strong: ['草', '地面', '飛龍', '飛行'], weak: ['火', '格鬥', '岩石'] }
};

// 基礎怪獸範本 (進化鏈)
const MONSTER_TEMPLATES = [
    {
        id: 'm1', isGod: false, elem: '火',
        stages: [
            { name: '小火苗', avatar: '🔥', atkP: 25, atkM: 35, defP: 20, defM: 20 },
            { name: '烈焰犬', avatar: '🐕', atkP: 45, atkM: 65, defP: 40, defM: 45 },
            { name: '爆炎虎', avatar: '🐅', atkP: 75, atkM: 105, defP: 70, defM: 75 },
            { name: '獄火朱雀', avatar: '🦅', atkP: 120, atkM: 160, defP: 100, defM: 110 }
        ]
    },
    {
        id: 'm2', isGod: false, elem: '水',
        stages: [
            { name: '水滴獸', avatar: '💧', atkP: 20, atkM: 30, defP: 30, defM: 30 },
            { name: '藍波海豹', avatar: '🦭', atkP: 40, atkM: 55, defP: 60, defM: 55 },
            { name: '深海巨鯊', avatar: '🦈', atkP: 70, atkM: 90, defP: 95, defM: 90 },
            { name: '怒海滄龍', avatar: '🐊', atkP: 105, atkM: 140, defP: 130, defM: 125 }
        ]
    },
    {
        id: 'm3', isGod: false, elem: '草',
        stages: [
            { name: '嫩芽仔', avatar: '🌱', atkP: 22, atkM: 28, defP: 25, defM: 25 },
            { name: '藤蔓鹿', avatar: '🦌', atkP: 42, atkM: 58, defP: 50, defM: 50 },
            { name: '森林巨熊', avatar: '🐻', atkP: 80, atkM: 95, defP: 85, defM: 80 },
            { name: '遠古樹皇', avatar: '🌲', atkP: 115, atkM: 145, defP: 120, defM: 120 }
        ]
    },
    // 8 隻山海經神獸 (無進化，40LV 四圍極高)
    { id: 'g1', isGod: true, name: '鳳凰', avatar: '🦚', elem: '火', atkP: 180, atkM: 220, defP: 150, defM: 170, desc: '出於丹穴山，象徵天下太平。' },
    { id: 'g2', isGod: true, name: '九尾狐', avatar: '🦊', elem: '超能力', atkP: 160, atkM: 230, defP: 140, defM: 180, desc: '青丘之山，其音如嬰兒，象徵帝王之兆。' },
    { id: 'g3', isGod: true, name: '九頭鳥', avatar: '🦅', elem: '飛行', atkP: 200, atkM: 190, defP: 150, defM: 150, desc: '源於九鳳，楚地崇拜的神鳥。' },
    { id: 'g4', isGod: true, name: '白鹿', avatar: '🦄', elem: '草', atkP: 140, atkM: 200, defP: 180, defM: 200, desc: '長壽與仙人座騎，象徵吉祥。' },
    { id: 'g5', isGod: true, name: '燭九陰', avatar: '🐉', elem: '龍', atkP: 230, atkM: 230, defP: 180, defM: 180, desc: '掌控日夜四季，身長千里的紅皮膚神龍。' },
    { id: 'g6', isGod: true, name: '饕餮', avatar: '👹', elem: '格鬥', atkP: 240, atkM: 150, defP: 190, defM: 140, desc: '羊身人面，眼在腋下，極度貪食之凶獸。' },
    { id: 'g7', isGod: true, name: '窮奇', avatar: '🐅', elem: '風', atkP: 220, atkM: 180, defP: 160, defM: 160, desc: '狀如牛，抑善揚惡之四大凶獸之一。' },
    { id: 'g8', isGod: true, name: '畢方', avatar: '🦜', elem: '火', atkP: 170, atkM: 240, defP: 140, defM: 160, desc: '一足神鳥，見則其邑有訛火。' }
];

// 當前登入用戶狀態
let currentUser = null;
let authMode = 'login';
let selectedMonsterIndex = 0;

// 戰鬥狀態全域變數
let battleState = {
    mode: '', // 'single', 'pvp_public', 'pvp_private'
    playerMon: null,
    enemyMon: null,
    pHp: 100, pMaxHp: 100,
    eHp: 100, eMaxHp: 100,
    timer: null,
    timeLeft: 40,
    currentQuiz: null
};

// ==================== 2. 初始化與帳號邏輯 ====================

function switchAuthMode(mode) {
    authMode = mode;
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    document.getElementById('register-fields').style.display = mode === 'register' ? 'block' : 'none';
    event.target.classList.add('active');
}

function handleAuth(e) {
    e.preventDefault();
    const uname = document.getElementById('auth-username').value.trim();
    const pass = document.getElementById('auth-password').value.trim();

    // 1. 開發者 MR TO 登入檢查
    if (uname === 'MR TO' && pass === '3056') {
        currentUser = {
            name: 'MR TO (開發者)',
            school: '系統管理', classInfo: 'DEV', gold: 999999, expItems: 99,
            monsters: []
        };
        // 解鎖所有怪獸且全滿級 40LV
        MONSTER_TEMPLATES.forEach(tmpl => {
            currentUser.monsters.push(createMonsterInstance(tmpl, 40));
        });
        saveAndLogin();
        return;
    }

    // 2. 一般學生帳號邏輯 (本地 LocalStorage 保存)
    const stored = localStorage.getItem('math_game_user_' + uname);
    if (authMode === 'login') {
        if (!stored) { closeModal('帳號不存在，請先選擇「新學生註冊」！'); return; }
        const uData = JSON.parse(stored);
        if (uData.pass !== pass) { closeModal('密碼錯誤！'); return; }
        currentUser = uData;
    } else {
        if (stored) { closeModal('此別號已被註冊，請直接登入！'); return; }
        const school = document.getElementById('reg-school').value.trim();
        const cls = document.getElementById('reg-class').value.trim();
        const cno = document.getElementById('reg-classno').value.trim();
        if(!school || !cls || !cno) { closeModal('請填妥學校名稱、班別及班號！'); return; }
        
        currentUser = {
            name: uname, pass: pass, school: school,
            classInfo: `${cls}(${cno})`, gold: 300, expItems: 3,
            monsters: [ createMonsterInstance(MONSTER_TEMPLATES[0], 1) ] // 初始贈送一隻小火苗
        };
    }
    saveAndLogin();
}

function createMonsterInstance(tmpl, level = 1) {
    return {
        templateId: tmpl.id,
        isGod: tmpl.isGod,
        level: level,
        exp: 0,
        customName: tmpl.isGod ? tmpl.name : tmpl.stages[0].name
    };
}

function saveAndLogin() {
    if(currentUser.pass) {
        localStorage.setItem('math_game_user_' + currentUser.name, JSON.stringify(currentUser));
    }
    updateUIHeader();
    showScreen('screen-menu');
}

function logout() {
    currentUser = null;
    document.getElementById('header-user').style.display = 'none';
    showScreen('screen-auth');
}

function updateUIHeader() {
    document.getElementById('header-user').style.display = 'flex';
    document.getElementById('user-name-display').innerText = `${currentUser.school} - ${currentUser.classInfo} ${currentUser.name}`;
    document.getElementById('user-gold').innerText = currentUser.gold;
    document.getElementById('exp-item-count').innerText = currentUser.expItems || 0;
}

// ==================== 3. 畫面切換與 UI 渲染 ====================

function showScreen(screenId) {
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    document.getElementById(screenId).classList.add('active');
    
    if (screenId === 'screen-deck') renderDeck();
}

function getMonsterStats(mon) {
    const tmpl = MONSTER_TEMPLATES.find(t => t.id === mon.templateId);
    let stageIdx = 0;
    if (!mon.isGod) {
        if (mon.level >= 30) stageIdx = 3;
        else if (mon.level >= 20) stageIdx = 2;
        else if (mon.level >= 10) stageIdx = 1;
    }
    
    const base = mon.isGod ? tmpl : tmpl.stages[stageIdx];
    const scale = 1 + (mon.level - 1) * 0.08; // 每級成長 8%
    
    return {
        name: mon.isGod ? tmpl.name : tmpl.stages[stageIdx].name,
        avatar: base.avatar,
        elem: tmpl.elem,
        atkP: Math.round(base.atkP * scale),
        atkM: Math.round(base.atkM * scale),
        defP: Math.round(base.defP * scale),
        defM: Math.round(base.defM * scale),
        stageName: mon.isGod ? '神獸' : ['幼年期','成長期','完全體','究極體'][stageIdx]
    };
}

function renderDeck() {
    const container = document.getElementById('monster-list-container');
    container.innerHTML = '';

    currentUser.monsters.forEach((mon, idx) => {
        const stats = getMonsterStats(mon);
        const card = document.createElement('div');
        card.className = `monster-card ${mon.isGod ? 'god' : ''} ${selectedMonsterIndex === idx ? 'equipped' : ''}`;
        
        // 解鎖技能列表
        let skillsText = '1.普通攻擊';
        if(mon.level >= 10) skillsText += ' | 2.屬性打擊';
        if(mon.level >= 20) skillsText += ' | 3.回血/暈眩';
        if(mon.level >= 30) skillsText += ' | 4.終極奧義';

        card.innerHTML = `
            <div class="monster-avatar">${stats.avatar}</div>
            <div style="font-weight:bold;">${stats.name} ${selectedMonsterIndex === idx ? '(已選擇)' : ''}</div>
            <div style="font-size:12px; color:var(--secondary);">Lv.${mon.level}/40 (${stats.stageName})</div>
            <div style="margin:5px 0;">
                <span class="stat-badge">物攻:${stats.atkP}</span><span class="stat-badge">魔攻:${stats.atkM}</span><br>
                <span class="stat-badge">物防:${stats.defP}</span><span class="stat-badge">魔防:${stats.defM}</span>
            </div>
            <div style="font-size:10px; color:#aaa; margin-bottom:8px;">技能: ${skillsText}</div>
            <button class="btn" style="padding:4px; font-size:12px;" onclick="selectMonster(${idx})">上陣對戰</button>
            <button class="btn btn-secondary" style="padding:4px; font-size:12px; margin-top:4px;" onclick="useExpPack(${idx})">使用經驗包</button>
        `;
        container.appendChild(card);
    });
}

function selectMonster(idx) {
    selectedMonsterIndex = idx;
    renderDeck();
    closeModal(`已成功選擇怪獸作為首發！`);
}

function useExpPack(idx) {
    if (!currentUser.expItems || currentUser.expItems <= 0) {
        closeModal('你沒有足夠的經驗包！可透過抽蛋或打 Boss 獲得。');
        return;
    }
    const mon = currentUser.monsters[idx];
    if (mon.level >= 40) { closeModal('怪獸已達最高等級 Lv.40！'); return; }
    
    currentUser.expItems--;
    mon.level = Math.min(40, mon.level + 2); // 一個經驗包升2級
    saveAndLogin();
    renderDeck();
}

// ==================== 4. 抽蛋與經驗包系統 ====================

function drawGacha() {
    if (currentUser.gold < 100) { closeModal('金幣不足 100，無法抽蛋！'); return; }
    currentUser.gold -= 100;

    // 20% 獲得經驗包，80% 獲得怪獸
    if (Math.random() < 0.2) {
        currentUser.expItems = (currentUser.expItems || 0) + 1;
        saveAndLogin();
        closeModal('🎉 恭喜抽中 <b>經驗包 × 1</b>！可在背包中給怪獸升級。');
        return;
    }

    // 隨機怪獸
    const randTmpl = MONSTER_TEMPLATES[Math.floor(Math.random() * MONSTER_TEMPLATES.length)];
    const hasMon = currentUser.monsters.find(m => m.templateId === randTmpl.id);

    if (hasMon) {
        // 重複怪獸轉化為經驗包 x 2
        currentUser.expItems = (currentUser.expItems || 0) + 2;
        saveAndLogin();
        closeModal(`📦 抽中了重複的怪獸【${randTmpl.isGod ? randTmpl.name : randTmpl.stages[0].name}】，已自動轉化為 <b>經驗包 × 2</b>！`);
    } else {
        const newMon = createMonsterInstance(randTmpl, 1);
        currentUser.monsters.push(newMon);
        saveAndLogin();
        closeModal(`🎉 超幸運！獲得全新怪獸卡片：<b>${newMon.customName}</b>！`);
    }
}

// ==================== 5. 數學題庫生成器 ====================

function generateMathQuestion(grade, cat) {
    grade = parseInt(grade);
    let q = '', ans = 0, opts = [];

    if (cat === 'number') {
        if (grade <= 2) {
            let a = Math.floor(Math.random()*50)+1, b = Math.floor(Math.random()*50)+1;
            q = `計算: ${a} + ${b} = ?`; ans = a + b;
        } else if (grade <= 4) {
            let a = Math.floor(Math.random()*12)+1, b = Math.floor(Math.random()*12)+1;
            q = `計算: ${a} × ${b} = ?`; ans = a * b;
        } else {
            let a = Math.floor(Math.random()*20)+10, b = Math.floor(Math.random()*10)+2;
            q = `求 ${a * b} 與 ${a} 的公因數/商數: (${a * b} ÷ ${a}) = ?`; ans = b;
        }
    } else if (cat === 'measure') {
        if (grade <= 2) {
            q = `1 米 (m) 等於多少 厘米 (cm)？`; ans = 100;
        } else if (grade <= 4) {
            let h = Math.floor(Math.random()*12)+1;
            q = `下午 ${h} 時換算成 24 小時報時制是幾時？`; ans = h + 12;
        } else {
            let w = Math.floor(Math.random()*8)+2, h = Math.floor(Math.random()*5)+2;
            q = `底長 ${w}cm，高 ${h}cm 的三角形面積是多少 cm²？`; ans = (w * h) / 2;
        }
    } else if (cat === 'algebra') {
        let x = Math.floor(Math.random()*10)+1, a = Math.floor(Math.random()*15)+1;
        q = `解方程: x + ${a} = ${x + a}，求解 x 的值？`; ans = x;
    } else {
        // 圖形/數據預設簡單數值題
        let a = Math.floor(Math.random()*30)+10, b = Math.floor(Math.random()*30)+10;
        q = `平均數計算: (${a} + ${b}) ÷ 2 = ?`; ans = (a + b) / 2;
    }

    opts = [ans, ans + 2, Math.max(1, ans - 3), ans + 5].sort(() => Math.random() - 0.5);
    return { q, ans, opts };
}

// ==================== 6. 對戰系統 (核心邏輯) ====================

function startSetupBattle(mode) {
    battleState.mode = mode;
    showScreen('screen-battle-setup');
}

function initiateBattleStart() {
    if(battleState.mode === 'pvp_public') {
        closeModal('正在匹配對手... (若20秒內未匹配到真人，將自動配對電腦)');
        setTimeout(() => { startBattle(); }, 2000); // 模擬搜尋延遲
    } else if(battleState.mode === 'pvp_private') {
        const roomCode = Math.floor(1000 + Math.random() * 9000);
        closeModal(`私人房間已建立！房間號碼: ${roomCode}。好友加入即可開始！`);
        setTimeout(() => { startBattle(); }, 1500);
    } else {
        startBattle();
    }
}

function startBattle() {
    closeModal();
    showScreen('screen-battle');

    // 玩家怪獸
    const pMonObj = currentUser.monsters[selectedMonsterIndex] || currentUser.monsters[0];
    battleState.playerMon = { ...getMonsterStats(pMonObj), level: pMonObj.level };
    battleState.pMaxHp = 100 + battleState.playerMon.level * 10;
    battleState.pHp = battleState.pMaxHp;

    // 敵方怪獸 (若為 single 則為 Boss，四圍較高且會主動攻擊)
    if (battleState.mode === 'single') {
        battleState.enemyMon = { name: '魔王 蚩尤', avatar: '👿', elem: '火', atkP: 80, atkM: 80, defP: 50, defM: 50, level: 25 };
        battleState.eMaxHp = 300;
    } else {
        const randTmpl = MONSTER_TEMPLATES[Math.floor(Math.random()*MONSTER_TEMPLATES.length)];
        const eLevel = Math.max(1, battleState.playerMon.level + Math.floor(Math.random()*3 - 1));
        battleState.enemyMon = { ...createMonsterInstance(randTmpl, eLevel), ...getMonsterStats(createMonsterInstance(randTmpl, eLevel)), level: eLevel };
        battleState.eMaxHp = 100 + eLevel * 10;
    }
    battleState.eHp = battleState.eMaxHp;

    updateBattleUI();
    nextQuizTurn();
}

function updateBattleUI() {
    const p = battleState.playerMon, e = battleState.enemyMon;
    document.getElementById('p-avatar').innerText = p.avatar;
    document.getElementById('p-name').innerText = p.name;
    document.getElementById('p-lv').innerText = p.level;
    document.getElementById('p-elem').innerText = p.elem;
    document.getElementById('p-hp-fill').style.width = `${Math.max(0, (battleState.pHp/battleState.pMaxHp)*100)}%`;
    document.getElementById('p-hp-text').innerText = `${battleState.pHp}/${battleState.pMaxHp}`;

    document.getElementById('e-avatar').innerText = e.avatar;
    document.getElementById('e-name').innerText = e.name;
    document.getElementById('e-lv').innerText = e.level;
    document.getElementById('e-elem').innerText = e.elem;
    document.getElementById('e-hp-fill').style.width = `${Math.max(0, (battleState.eHp/battleState.eMaxHp)*100)}%`;
    document.getElementById('e-hp-text').innerText = `${battleState.eHp}/${battleState.eMaxHp}`;
}

function nextQuizTurn() {
    if (battleState.pHp <= 0 || battleState.eHp <= 0) return;

    clearInterval(battleState.timer);
    battleState.timeLeft = 40; // 答題時間 40 秒

    const grade = document.getElementById('setup-grade').value;
    const cat = document.getElementById('setup-category').value;
    const quiz = generateMathQuestion(grade, cat);
    battleState.currentQuiz = quiz;

    document.getElementById('quiz-question').innerText = quiz.q;
    const optContainer = document.getElementById('quiz-options');
    optContainer.innerHTML = '';

    quiz.opts.forEach(opt => {
        const btn = document.createElement('button');
        btn.className = 'btn';
        btn.innerText = opt;
        btn.onclick = () => handleAnswer(opt === quiz.ans);
        optContainer.appendChild(btn);
    });

    // 倒數計時器
    document.getElementById('timer-text').innerText = battleState.timeLeft;
    document.getElementById('quiz-timer').style.width = '100%';

    battleState.timer = setInterval(() => {
        battleState.timeLeft--;
        document.getElementById('timer-text').innerText = battleState.timeLeft;
        document.getElementById('quiz-timer').style.width = `${(battleState.timeLeft/40)*100}%`;

        if (battleState.timeLeft <= 0) {
            clearInterval(battleState.timer);
            handleAnswer(false); // 超時視同答錯
        }
    }, 1000);
}

function handleAnswer(isCorrect) {
    clearInterval(battleState.timer);

    if (isCorrect) {
        // 玩家答對，進行攻擊
        triggerAttackFx('player', battleState.playerMon.elem);
        let dmg = Math.max(10, battleState.playerMon.atkP - battleState.enemyMon.defP / 2);
        
        // 屬性相剋加成
        if (ELEMENT_CHART[battleState.playerMon.elem]?.strong.includes(battleState.enemyMon.elem)) {
            dmg = Math.round(dmg * 1.5);
        }

        battleState.eHp -= dmg;
        updateBattleUI();

        if (battleState.eHp <= 0) {
            endBattle(true);
            return;
        }
    } else {
        closeModal('答錯了或超時！無法發動攻擊！');
    }

    // Boss / 對手攻擊回合 (Boss 不再站著不動)
    setTimeout(() => {
        if (battleState.eHp > 0) {
            triggerAttackFx('enemy', battleState.enemyMon.elem);
            let eDmg = Math.max(8, battleState.enemyMon.atkP - battleState.playerMon.defP / 2);
            battleState.pHp -= eDmg;
            updateBattleUI();

            if (battleState.pHp <= 0) {
                endBattle(false);
                return;
            }
        }
        nextQuizTurn();
    }, 1200);
}

function triggerAttackFx(attacker, elem) {
    const targetElem = attacker === 'player' ? document.getElementById('enemy-fighter') : document.getElementById('player-fighter');
    
    // 攻擊視覺動畫特效
    const fx = document.createElement('div');
    fx.className = 'attack-fx';
    fx.innerText = elem === '火' ? '🔥' : (elem === '水' ? '💧' : '⚔️');
    document.getElementById('battle-field-elem').appendChild(fx);

    targetElem.classList.add('shake');
    setTimeout(() => targetElem.classList.remove('shake'), 400);
}

function endBattle(isWin) {
    clearInterval(battleState.timer);

    if (isWin) {
        let goldReward = battleState.mode === 'pvp_private' ? 100 : 50;
        let expReward = battleState.mode === 'single' ? 2 : 1; // 打 Boss 給予經驗包獎勵
        
        currentUser.gold += goldReward;
        currentUser.expItems = (currentUser.expItems || 0) + expReward;
        saveAndLogin();

        closeModal(`🏆 戰鬥勝利！獲得 <b>${goldReward} 金幣</b> 與 <b>${expReward} 個經驗包</b>！`);
    } else {
        if(battleState.mode === 'pvp_private') currentUser.gold = Math.max(0, currentUser.gold - 50);
        saveAndLogin();
        closeModal(`❌ 戰鬥失敗！再接再厲！${battleState.mode === 'pvp_private' ? ' (扣除 50 金幣)' : ''}`);
    }

    showScreen('screen-menu');
}

function closeModal(msg) {
    const modal = document.getElementById('game-modal');
    if (msg) {
        document.getElementById('modal-msg').innerHTML = msg;
        modal.style.display = 'flex';
    } else {
        modal.style.display = 'none';
    }
}
</script>
</body>
</html>

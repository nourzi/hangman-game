<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Hangman</title>
<link href="https://fonts.googleapis.com/css2?family=Special+Elite&family=Courier+Prime:wght@400;700&display=swap" rel="stylesheet"/>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{background:#111;min-height:100vh;display:flex;align-items:center;justify-content:center;font-family:'Courier Prime',monospace;padding:2rem 1rem;overflow-x:hidden}
.screen{display:none;flex-direction:column;align-items:center;gap:1.25rem;width:100%;max-width:560px}
.screen.active{display:flex}
h1{font-family:'Special Elite',serif;font-size:28px;font-weight:400;color:#f0ece0;letter-spacing:6px;text-transform:uppercase}
h2{font-family:'Special Elite',serif;font-size:17px;font-weight:400;color:#f0ece0;letter-spacing:3px;text-transform:uppercase;align-self:flex-start}
.card{background:#1c1c1c;border:0.5px solid #333;border-radius:12px;padding:1.5rem;width:100%}
.btn{padding:10px 24px;background:transparent;border:0.5px solid #444;border-radius:8px;font-family:'Courier Prime',monospace;font-size:13px;font-weight:700;color:#f0ece0;cursor:pointer;text-transform:uppercase;letter-spacing:2px;transition:background .15s,border-color .15s,transform .1s}
.btn:hover{background:#2a2a2a;border-color:#666}
.btn:active{transform:scale(.97)}
.btn.accent{border-color:#5DCAA5;color:#5DCAA5}
.btn.accent:hover{background:#0a2e22}
.btn.gold{border-color:#EF9F27;color:#EF9F27}
.btn.gold:hover{background:#2a1f05}
.btn.danger{border-color:#D85A30;color:#D85A30}
.btn.danger:hover{background:#2e1209}
.btn.full{width:100%}
.btn-row{display:flex;gap:10px;width:100%}
input[type=text],input[type=password]{flex:1;background:#111;border:0.5px solid #333;border-radius:8px;color:#f0ece0;font-family:'Courier Prime',monospace;font-size:14px;padding:8px 12px;outline:none;transition:border-color .2s;width:100%}
input[type=text]:focus,input[type=password]:focus{border-color:#5DCAA5}
.field-error{font-size:12px;color:#D85A30;letter-spacing:1px;min-height:16px;margin-top:4px}
.no-msg{font-size:13px;color:#555;letter-spacing:1px;text-align:center;padding:1rem 0}
::-webkit-scrollbar{width:4px}::-webkit-scrollbar-track{background:#111}::-webkit-scrollbar-thumb{background:#333;border-radius:2px}

/* ── LANDING ── */
.landing-sub{font-size:12px;color:#555;letter-spacing:3px;text-transform:uppercase}
.landing-buttons{display:flex;flex-direction:column;gap:10px;width:100%;max-width:280px;margin-top:.5rem}

/* ── PLAYER CARD ── */
.player-card{width:100%;background:#1c1c1c;border:0.5px solid #333;border-radius:12px;padding:1rem 1.5rem;display:flex;align-items:center;gap:1rem}
.player-avatar{width:48px;height:48px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-family:'Special Elite',serif;font-size:20px;color:#f0ece0;flex-shrink:0;border:2px solid #333}
.player-info{flex:1}
.player-name-label{font-size:13px;font-weight:700;color:#f0ece0;text-transform:uppercase;letter-spacing:2px}
.player-level-label{font-size:11px;color:#888;letter-spacing:1px;margin-top:2px}
.xp-bar-wrap{margin-top:6px;height:4px;background:#222;border-radius:2px;overflow:hidden}
.xp-bar-fill{height:100%;border-radius:2px;transition:width .6s ease}

/* ── LEVEL BADGE ── */
.level-badge{display:inline-flex;align-items:center;gap:6px;padding:4px 12px;border-radius:20px;font-size:11px;font-weight:700;letter-spacing:2px;text-transform:uppercase}

/* ── CATEGORY GRID ── */
.cat-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(130px,1fr));gap:8px;width:100%}
.cat-card{background:#1c1c1c;border:0.5px solid #333;border-radius:10px;padding:10px 12px;cursor:pointer;transition:border-color .2s,background .2s;text-align:center;display:flex;flex-direction:column;align-items:center;gap:4px}
.cat-card:hover{background:#222}
.cat-card.selected{background:#0a2e22;border-color:#5DCAA5}
.cat-icon{font-size:22px}
.cat-name{font-size:11px;color:#f0ece0;letter-spacing:1px;text-transform:uppercase}

/* ── DIFF CARDS ── */
.diff-cards{display:flex;gap:8px;width:100%}
.diff-card{flex:1;background:#1c1c1c;border:0.5px solid #333;border-radius:12px;padding:1rem .75rem;text-align:center;cursor:pointer;transition:border-color .2s,background .2s;display:flex;flex-direction:column;gap:4px;align-items:center}
.diff-card .dc-label{font-family:'Special Elite',serif;font-size:14px;letter-spacing:2px;text-transform:uppercase}
.diff-card .dc-desc{font-size:10px;color:#555;letter-spacing:1px;line-height:1.6}
.diff-card .dc-lives{font-size:11px;color:#666;letter-spacing:1px}
.diff-card.easy.selected{border-color:#5DCAA5;background:#0a2e22}.diff-card.easy .dc-label{color:#5DCAA5}
.diff-card.medium.selected{border-color:#EF9F27;background:#2a1f05}.diff-card.medium .dc-label{color:#EF9F27}
.diff-card.hard.selected{border-color:#D85A30;background:#2e1209}.diff-card.hard .dc-label{color:#D85A30}

/* ── XP POPUP ── */
.xp-popup{position:fixed;top:40%;left:50%;transform:translate(-50%,-50%) scale(.5);opacity:0;pointer-events:none;font-family:'Special Elite',serif;font-size:32px;color:#EF9F27;letter-spacing:4px;text-shadow:0 0 20px rgba(239,159,39,.5);z-index:999;transition:none}
.xp-popup.burst{animation:xpburst .8s ease-out forwards}
@keyframes xpburst{0%{transform:translate(-50%,-50%) scale(.5);opacity:0}30%{transform:translate(-50%,-60%) scale(1.2);opacity:1}70%{transform:translate(-50%,-70%) scale(1);opacity:1}100%{transform:translate(-50%,-90%) scale(.9);opacity:0}}

/* ── LEVEL UP OVERLAY ── */
.lvlup-overlay{position:fixed;inset:0;background:rgba(0,0,0,.85);display:flex;align-items:center;justify-content:center;z-index:1000;opacity:0;pointer-events:none;transition:opacity .3s}
.lvlup-overlay.show{opacity:1;pointer-events:all}
.lvlup-box{background:#1c1c1c;border:0.5px solid #EF9F27;border-radius:16px;padding:2.5rem 2rem;text-align:center;display:flex;flex-direction:column;align-items:center;gap:1rem;animation:lvlpop .4s ease-out}
@keyframes lvlpop{from{transform:scale(.7)}to{transform:scale(1)}}
.lvlup-title{font-family:'Special Elite',serif;font-size:28px;color:#EF9F27;letter-spacing:4px;text-transform:uppercase}
.lvlup-sub{font-size:13px;color:#888;letter-spacing:2px;text-transform:uppercase}
.lvlup-num{font-family:'Special Elite',serif;font-size:64px;color:#EF9F27;line-height:1}
.lvlup-stars{font-size:28px;letter-spacing:8px}

/* ── LOSE ANIMATION OVERLAY ── */
.lose-overlay{position:fixed;inset:0;pointer-events:none;z-index:900;overflow:hidden}
.skull{position:absolute;font-size:28px;animation:skulldrop linear forwards}
@keyframes skulldrop{0%{transform:translateY(-60px) rotate(0deg);opacity:1}100%{transform:translateY(110vh) rotate(720deg);opacity:0}}

/* ── HINT ── */
.hint-box{width:100%;background:#1a1a0a;border:0.5px solid #3a3200;border-radius:10px;padding:10px 14px;font-size:13px;color:#EF9F27;letter-spacing:1px;line-height:1.6;display:none}
.hint-box.show{display:block}
.hint-cost{font-size:11px;color:#555;letter-spacing:1px}

/* ── GAME ── */
#hm-canvas-wrap{background:#1c1c1c;border:0.5px solid #333;border-radius:12px;padding:1.25rem;width:100%;display:flex;gap:1.5rem;align-items:flex-start}
#hm-svg line,#hm-svg circle{stroke:#f0ece0}
#hm-svg.shake{animation:shake .4s ease}
@keyframes shake{0%,100%{transform:translateX(0)}20%{transform:translateX(-6px)}40%{transform:translateX(6px)}60%{transform:translateX(-4px)}80%{transform:translateX(4px)}}
#hm-right{flex:1;display:flex;flex-direction:column;gap:1rem;padding-top:.5rem}
#hm-word-row{display:flex;flex-wrap:wrap;gap:6px;justify-content:center}
.hm-letter-box{width:28px;height:34px;border-bottom:2px solid #f0ece0;display:flex;align-items:center;justify-content:center;font-size:18px;font-weight:700;color:#f0ece0;text-transform:uppercase;transition:color .3s}
.hm-letter-box.revealed{color:#5DCAA5;animation:letterPop .25s ease}
.hm-letter-box.wrong-reveal{color:#D85A30}
@keyframes letterPop{0%{transform:scale(1.5)}100%{transform:scale(1)}}
#hm-wrong-label{font-size:11px;color:#666;text-transform:uppercase;letter-spacing:1.5px;margin-bottom:4px}
#hm-wrong-letters{font-size:14px;color:#D85A30;font-weight:700;letter-spacing:3px;min-height:20px;text-transform:uppercase}
#hm-lives{font-size:13px;color:#666;text-align:center}
#hm-lives span{font-weight:700;color:#f0ece0}
#hm-cat-badge{font-size:11px;letter-spacing:2px;text-transform:uppercase;text-align:center;color:#888}
#hm-diff-badge{font-size:11px;font-weight:700;letter-spacing:2px;text-transform:uppercase;text-align:center}
#hm-keyboard{display:flex;flex-wrap:wrap;gap:5px;justify-content:center;width:100%}
.hm-key{width:35px;height:35px;border:0.5px solid #333;border-radius:8px;background:#1c1c1c;color:#f0ece0;font-family:'Courier Prime',monospace;font-size:13px;font-weight:700;cursor:pointer;text-transform:uppercase;transition:background .15s,color .15s,transform .1s}
.hm-key:hover:not(:disabled){background:#2a2a2a;transform:scale(1.08)}
.hm-key:active:not(:disabled){transform:scale(.95)}
.hm-key.correct{background:#0a2e22;color:#5DCAA5;border-color:#1D9E75;cursor:default}
.hm-key.wrong{background:#111;color:#333;border-color:#1a1a1a;cursor:default;text-decoration:line-through}
#hm-status{font-family:'Special Elite',serif;font-size:18px;letter-spacing:2px;text-align:center;min-height:28px;color:#f0ece0;text-transform:uppercase}

/* ── VS ── */
.vs-score-row{display:flex;gap:1.5rem;justify-content:center;width:100%}
.vs-score-box{text-align:center}
.vs-score-name{font-size:11px;color:#555;text-transform:uppercase;letter-spacing:1px;margin-bottom:4px}
.vs-score-val{font-size:24px;font-weight:700;color:#f0ece0}
.vs-score-box.active .vs-score-name{color:#5DCAA5}
.vs-score-box.active .vs-score-val{color:#5DCAA5}
.vs-divider{font-size:20px;color:#333;align-self:center}
.vs-turn-banner{width:100%;background:#1c1c1c;border:0.5px solid #333;border-radius:12px;padding:10px 16px;display:flex;align-items:center;justify-content:space-between}
.vs-player-tag{font-size:13px;font-weight:700;text-transform:uppercase;letter-spacing:2px;color:#5DCAA5}

/* ── LEADERBOARD ── */
.lb-tabs{display:flex;gap:6px;width:100%;flex-wrap:wrap}
.lb-tab{flex:1;min-width:60px;padding:7px 4px;background:transparent;border:0.5px solid #333;border-radius:8px;font-family:'Courier Prime',monospace;font-size:10px;font-weight:700;color:#666;cursor:pointer;text-transform:uppercase;letter-spacing:1px;transition:all .15s}
.lb-tab.active{border-color:#5DCAA5;color:#5DCAA5;background:#0a2e22}
.lb-table{width:100%;border-collapse:collapse}
.lb-table th{font-size:10px;color:#555;text-transform:uppercase;letter-spacing:1px;padding:6px 8px;text-align:left;border-bottom:0.5px solid #2a2a2a}
.lb-table td{padding:9px 8px;border-bottom:0.5px solid #1e1e1e;font-size:13px;color:#f0ece0}
.lb-table tr:last-child td{border-bottom:none}
.lb-rank{font-weight:700;color:#555;width:28px}
.lb-rank.gold{color:#EF9F27}.lb-rank.silver{color:#aaa}.lb-rank.bronze{color:#D85A30}
.lb-name{font-weight:700;letter-spacing:1px;text-transform:uppercase}
.lb-val{text-align:right}

/* ── ADMIN TABS ── */
.admin-tabs{display:flex;gap:6px;width:100%}
.tab-btn{flex:1;padding:8px;background:transparent;border:0.5px solid #333;border-radius:8px;font-family:'Courier Prime',monospace;font-size:10px;font-weight:700;color:#666;cursor:pointer;text-transform:uppercase;letter-spacing:1px;transition:all .15s}
.tab-btn.active{border-color:#5DCAA5;color:#5DCAA5;background:#0a2e22}
.tab-panel{display:none;width:100%;flex-direction:column;gap:1rem}
.tab-panel.active{display:flex}
.admin-input-row{display:flex;gap:8px;margin-bottom:.75rem}
#word-list{display:flex;flex-direction:column;gap:6px;max-height:220px;overflow-y:auto}
.word-item{display:flex;align-items:center;gap:8px;background:#111;border:0.5px solid #2a2a2a;border-radius:8px;padding:8px 12px;font-size:13px;color:#f0ece0;text-transform:uppercase;letter-spacing:1px}
.word-item .wi-word{flex:1}
.wi-badge{font-size:9px;padding:2px 7px;border-radius:4px;font-weight:700;letter-spacing:1px;white-space:nowrap}
.wi-cat{background:#1a2a1a;color:#5DCAA5}.wi-diff-easy{background:#0a2e22;color:#5DCAA5}.wi-diff-medium{background:#2a1f05;color:#EF9F27}.wi-diff-hard{background:#2e1209;color:#D85A30}
.del-btn{background:none;border:none;color:#555;cursor:pointer;font-size:15px;padding:0 2px;transition:color .15s}
.del-btn:hover{color:#D85A30}
#history-list{display:flex;flex-direction:column;gap:8px;max-height:340px;overflow-y:auto}
.history-item{background:#111;border:0.5px solid #2a2a2a;border-radius:8px;padding:10px 14px;font-size:12px;display:flex;flex-direction:column;gap:3px}
.hi-top{display:flex;justify-content:space-between;align-items:center;gap:6px;flex-wrap:wrap}
.hi-name{color:#f0ece0;font-weight:700;letter-spacing:1px;text-transform:uppercase}
.hi-word{color:#5DCAA5;letter-spacing:2px;text-transform:uppercase;font-weight:700}
.hi-win{color:#5DCAA5;font-size:11px;letter-spacing:1px;text-transform:uppercase}
.hi-lose{color:#D85A30;font-size:11px;letter-spacing:1px;text-transform:uppercase}
.hi-meta{font-size:11px;color:#555;letter-spacing:1px}
.hi-guesses{font-size:11px;color:#777;letter-spacing:1px}

/* ── SELECT (category in admin) ── */
select{background:#111;border:0.5px solid #333;border-radius:8px;color:#f0ece0;font-family:'Courier Prime',monospace;font-size:13px;padding:8px 12px;outline:none;width:100%;transition:border-color .2s}
select:focus{border-color:#5DCAA5}
</style>
</head>
<body>

<div class="xp-popup" id="xp-popup"></div>
<div class="lvlup-overlay" id="lvlup-overlay">
  <div class="lvlup-box">
    <div class="lvlup-sub">Level up!</div>
    <div class="lvlup-num" id="lvlup-num">2</div>
    <div class="lvlup-stars">★ ★ ★</div>
    <div class="lvlup-title" id="lvlup-title">Apprentice</div>
    <button class="btn accent" onclick="closeLevelUp()">Continue</button>
  </div>
</div>
<div class="lose-overlay" id="lose-overlay"></div>

<!-- LANDING -->
<div id="screen-landing" class="screen active">
  <h1>Hangman</h1>
  <p class="landing-sub">word guessing game</p>
  <div id="landing-player-card" style="display:none;width:100%;max-width:340px"></div>
  <div class="landing-buttons">
    <button class="btn accent full" onclick="show('screen-name')">Play solo</button>
    <button class="btn gold full" onclick="show('screen-vs-setup')">VS mode</button>
    <button class="btn full" onclick="showLeaderboard('wins')">Leaderboard</button>
    <button class="btn full" style="margin-top:4px" onclick="goLogin()">Admin panel</button>
  </div>
</div>

<!-- NAME + CATEGORY + DIFF -->
<div id="screen-name" class="screen">
  <h1>New Game</h1>
  <div style="width:100%;display:flex;flex-direction:column;gap:8px">
    <input type="text" id="player-name-input" placeholder="Your name..." maxlength="16"/>
    <p class="field-error" id="name-error"></p>
  </div>
  <div style="width:100%">
    <h2 style="margin-bottom:.75rem">Category</h2>
    <div class="cat-grid" id="cat-grid-solo"></div>
  </div>
  <div style="width:100%">
    <h2 style="margin-bottom:.75rem">Difficulty</h2>
    <div class="diff-cards">
      <div class="diff-card easy selected" id="dc-easy" onclick="selectDiff('easy','solo')">
        <div class="dc-label">Easy</div><div class="dc-desc">Short words<br>≤5 letters</div><div class="dc-lives">8 lives</div>
      </div>
      <div class="diff-card medium" id="dc-medium" onclick="selectDiff('medium','solo')">
        <div class="dc-label">Medium</div><div class="dc-desc">6–8 letters</div><div class="dc-lives">6 lives</div>
      </div>
      <div class="diff-card hard" id="dc-hard" onclick="selectDiff('hard','solo')">
        <div class="dc-label">Hard</div><div class="dc-desc">9+ letters</div><div class="dc-lives">5 lives</div>
      </div>
    </div>
  </div>
  <div class="btn-row">
    <button class="btn full" onclick="show('screen-landing')">Back</button>
    <button class="btn accent full" onclick="startSolo()">Start</button>
  </div>
</div>

<!-- VS SETUP -->
<div id="screen-vs-setup" class="screen">
  <h1>VS Mode</h1>
  <div class="card" style="display:flex;flex-direction:column;gap:8px">
    <h2 style="margin-bottom:.25rem">Player 1</h2>
    <input type="text" id="vs-name-1" placeholder="Player 1 name..." maxlength="16"/>
    <h2 style="margin-top:.5rem;margin-bottom:.25rem">Player 2</h2>
    <input type="text" id="vs-name-2" placeholder="Player 2 name..." maxlength="16"/>
    <p class="field-error" id="vs-error"></p>
  </div>
  <div style="width:100%">
    <h2 style="margin-bottom:.75rem">Category</h2>
    <div class="cat-grid" id="cat-grid-vs"></div>
  </div>
  <div style="width:100%">
    <h2 style="margin-bottom:.75rem">Difficulty</h2>
    <div class="diff-cards">
      <div class="diff-card easy selected" id="vs-dc-easy" onclick="selectDiff('easy','vs')">
        <div class="dc-label">Easy</div><div class="dc-desc">≤5 letters</div><div class="dc-lives">8 lives</div>
      </div>
      <div class="diff-card medium" id="vs-dc-medium" onclick="selectDiff('medium','vs')">
        <div class="dc-label">Medium</div><div class="dc-desc">6–8 letters</div><div class="dc-lives">6 lives</div>
      </div>
      <div class="diff-card hard" id="vs-dc-hard" onclick="selectDiff('hard','vs')">
        <div class="dc-label">Hard</div><div class="dc-desc">9+ letters</div><div class="dc-lives">5 lives</div>
      </div>
    </div>
  </div>
  <div class="btn-row">
    <button class="btn full" onclick="show('screen-landing')">Back</button>
    <button class="btn gold full" onclick="startVS()">Start VS</button>
  </div>
</div>

<!-- LEADERBOARD -->
<div id="screen-leaderboard" class="screen">
  <h1>Leaderboard</h1>
  <div class="lb-tabs">
    <button class="lb-tab active" id="lb-tab-wins" onclick="showLeaderboard('wins')">Wins</button>
    <button class="lb-tab" id="lb-tab-xp" onclick="showLeaderboard('xp')">XP</button>
    <button class="lb-tab" id="lb-tab-level" onclick="showLeaderboard('level')">Level</button>
    <button class="lb-tab" id="lb-tab-pct" onclick="showLeaderboard('pct')">Win %</button>
  </div>
  <div class="card" style="width:100%;padding:.5rem 0"><div id="lb-content"></div></div>
  <button class="btn full" onclick="show('screen-landing')">Back</button>
</div>

<!-- LOGIN -->
<div id="screen-login" class="screen">
  <h1>Admin</h1>
  <div style="width:100%;max-width:320px;display:flex;flex-direction:column;gap:10px">
    <input type="password" id="login-pass" placeholder="Enter admin password" onkeydown="if(event.key==='Enter')doLogin()"/>
    <button class="btn accent full" onclick="doLogin()">Enter</button>
    <p class="field-error" id="login-error" style="text-align:center"></p>
    <button class="btn full" onclick="show('screen-landing')">Back</button>
  </div>
</div>

<!-- ADMIN -->
<div id="screen-admin" class="screen">
  <h1>Admin Panel</h1>
  <div class="admin-tabs">
    <button class="tab-btn active" onclick="switchTab('words')">Words</button>
    <button class="tab-btn" onclick="switchTab('history')">History</button>
    <button class="tab-btn" onclick="switchTab('settings')">Settings</button>
  </div>

  <div id="tab-words" class="tab-panel active">
    <div class="card">
      <h2 style="margin-bottom:.75rem">Add word</h2>
      <div class="admin-input-row">
        <input type="text" id="new-word-input" placeholder="Word..." maxlength="20" onkeydown="if(event.key==='Enter')addWord()"/>
        <select id="new-word-cat">
          <option value="mixed">Mixed</option>
          <option value="animals">Animals</option>
          <option value="food">Food & Drinks</option>
          <option value="countries">Countries & Cities</option>
          <option value="sports">Sports</option>
          <option value="movies">Movies & TV</option>
          <option value="tech">Technology</option>
        </select>
        <button class="btn accent" onclick="addWord()">Add</button>
      </div>
      <p class="field-error" id="admin-error"></p>
    </div>
    <div class="card">
      <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:.75rem">
        <h2>Word list</h2><span style="font-size:12px;color:#555" id="word-count">0 words</span>
      </div>
      <div id="word-list"></div>
      <p class="no-msg" id="no-words-msg" style="display:none">No words yet.</p>
    </div>
    <button class="btn danger full" onclick="clearWords()">Reset to defaults</button>
  </div>

  <div id="tab-history" class="tab-panel">
    <div class="card">
      <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:.75rem">
        <h2>Game history</h2>
        <button class="btn danger" style="padding:6px 12px;font-size:10px" onclick="clearHistory()">Clear</button>
      </div>
      <div id="history-list"></div>
      <p class="no-msg" id="no-history-msg" style="display:none">No games yet.</p>
    </div>
  </div>

  <div id="tab-settings" class="tab-panel">
    <div class="card">
      <h2 style="margin-bottom:.75rem">Change password</h2>
      <div style="display:flex;flex-direction:column;gap:8px">
        <input type="password" id="new-pass-1" placeholder="New password"/>
        <input type="password" id="new-pass-2" placeholder="Confirm new password"/>
        <button class="btn full" style="margin-top:4px" onclick="changePassword()">Update password</button>
        <p class="field-error" id="pass-error"></p>
      </div>
    </div>
  </div>

  <button class="btn full" onclick="show('screen-landing')">Back to menu</button>
</div>

<!-- GAME -->
<div id="screen-game" class="screen">
  <h1>Hangman</h1>

  <div id="vs-header" style="display:none;width:100%;flex-direction:column;gap:8px">
    <div class="vs-score-row">
      <div class="vs-score-box" id="vs-box-1">
        <div class="vs-score-name" id="vs-label-1">P1</div>
        <div class="vs-score-val" id="vs-val-1">0</div>
      </div>
      <div class="vs-divider">—</div>
      <div class="vs-score-box" id="vs-box-2">
        <div class="vs-score-name" id="vs-label-2">P2</div>
        <div class="vs-score-val" id="vs-val-2">0</div>
      </div>
    </div>
    <div class="vs-turn-banner">
      <span style="font-size:11px;color:#555;text-transform:uppercase;letter-spacing:1px">Turn</span>
      <span class="vs-player-tag" id="vs-turn-label">—</span>
      <span style="font-size:11px;color:#555;text-transform:uppercase;letter-spacing:1px" id="vs-round-label">Round 1</span>
    </div>
  </div>

  <div id="solo-player-card" style="display:none;width:100%"></div>

  <div style="display:flex;gap:8px;align-items:center;justify-content:center;width:100%">
    <div id="hm-cat-badge"></div>
    <div style="color:#333">·</div>
    <div id="hm-diff-badge"></div>
  </div>

  <div id="hm-canvas-wrap">
    <svg id="hm-svg" width="110" height="130" viewBox="0 0 110 130">
      <line x1="10" y1="125" x2="100" y2="125" stroke-width="2" stroke-linecap="round"/>
      <line x1="30" y1="125" x2="30" y2="10" stroke-width="2" stroke-linecap="round"/>
      <line x1="30" y1="10" x2="70" y2="10" stroke-width="2" stroke-linecap="round"/>
      <line x1="70" y1="10" x2="70" y2="25" stroke-width="2" stroke-linecap="round"/>
      <circle id="hm-head" cx="70" cy="35" r="10" fill="none" stroke-width="2" style="display:none"/>
      <line id="hm-body" x1="70" y1="45" x2="70" y2="80" stroke-width="2" stroke-linecap="round" style="display:none"/>
      <line id="hm-larm" x1="70" y1="55" x2="52" y2="70" stroke-width="2" stroke-linecap="round" style="display:none"/>
      <line id="hm-rarm" x1="70" y1="55" x2="88" y2="70" stroke-width="2" stroke-linecap="round" style="display:none"/>
      <line id="hm-lleg" x1="70" y1="80" x2="55" y2="100" stroke-width="2" stroke-linecap="round" style="display:none"/>
      <line id="hm-rleg" x1="70" y1="80" x2="85" y2="100" stroke-width="2" stroke-linecap="round" style="display:none"/>
    </svg>
    <div id="hm-right">
      <div id="hm-word-row"></div>
      <div>
        <div id="hm-wrong-label">wrong guesses</div>
        <div id="hm-wrong-letters">—</div>
      </div>
      <div id="hm-lives">Lives: <span id="hm-lives-count">6</span></div>
    </div>
  </div>

  <div class="hint-box" id="hint-box"></div>
  <button class="btn full" id="hint-btn" onclick="useHint()" style="font-size:11px;padding:7px"></button>

  <div id="hm-keyboard"></div>
  <div id="hm-status"></div>
  <div class="btn-row" style="justify-content:center">
    <button class="btn accent" id="hm-next-btn" style="display:none" onclick="handleNextBtn()"></button>
    <button class="btn" onclick="show('screen-landing')">Menu</button>
  </div>
</div>

<script>
// ════════════════════════════════════════
//  DATA
// ════════════════════════════════════════
const CATEGORIES = {
  mixed:    { label:'Mixed',           icon:'🎲' },
  animals:  { label:'Animals',         icon:'🦁' },
  food:     { label:'Food & Drinks',   icon:'🍕' },
  countries:{ label:'Countries & Cities', icon:'🌍' },
  sports:   { label:'Sports',          icon:'⚽' },
  movies:   { label:'Movies & TV',     icon:'🎬' },
  tech:     { label:'Technology',      icon:'💻' }
};

const WORD_DB = {
  animals:[
    {w:'cat',h:'A purring pet with whiskers'},{w:'fox',h:'A clever red-furred wild animal'},{w:'owl',h:'A nocturnal bird known for wisdom'},
    {w:'bear',h:'A large mammal that hibernates'},{w:'frog',h:'A green amphibian that jumps'},{w:'wolf',h:'A wild canine that howls at the moon'},
    {w:'shark',h:'A feared ocean predator'},{w:'eagle',h:'A bird of prey with sharp talons'},{w:'horse',h:'A mammal used for riding'},
    {w:'zebra',h:'A striped African animal'},{w:'tiger',h:'A striped big cat from Asia'},{w:'panda',h:'A black and white bear from China'},
    {w:'parrot',h:'A colorful bird that can talk'},{w:'rabbit',h:'A furry animal with long ears'},{w:'jaguar',h:'A spotted big cat from the Americas'},
    {w:'dolphin',h:'A highly intelligent sea mammal'},{w:'giraffe',h:'The tallest living animal'},{w:'penguin',h:'A flightless bird from Antarctica'},
    {w:'elephant',h:'The largest land animal with a trunk'},{w:'crocodile',h:'A large reptile lurking in rivers'},{w:'chimpanzee',h:'A great ape closely related to humans'}
  ],
  food:[
    {w:'pie',h:'A baked dish with a pastry crust'},{w:'jam',h:'A sweet fruit spread'},{w:'rice',h:'A grain eaten across Asia'},
    {w:'taco',h:'A Mexican tortilla filled with meat'},{w:'sushi',h:'Japanese rice rolls with fish'},{w:'pasta',h:'An Italian wheat-based dish'},
    {w:'curry',h:'A spiced dish popular in South Asia'},{w:'salad',h:'A cold dish of mixed vegetables'},{w:'bread',h:'A staple food baked from dough'},
    {w:'pizza',h:'An Italian dish with tomato and cheese'},{w:'burger',h:'A sandwich with a meat patty'},{w:'waffle',h:'A crispy battered breakfast treat'},
    {w:'brownie',h:'A dense chocolate baked square'},{w:'noodles',h:'Long thin strips of cooked dough'},{w:'popcorn',h:'Corn kernels that pop when heated'},
    {w:'chocolate',h:'A sweet treat made from cacao'},{w:'croissant',h:'A buttery French pastry'},{w:'strawberry',h:'A small red heart-shaped fruit'},
    {w:'cheesecake',h:'A creamy dessert with a biscuit base'},{w:'cappuccino',h:'An Italian coffee with foamed milk'}
  ],
  countries:[
    {w:'cuba',h:'A Caribbean island nation'},{w:'peru',h:'Home to Machu Picchu'},{w:'iran',h:'An ancient Middle Eastern nation'},
    {w:'egypt',h:'Land of the Pharaohs and pyramids'},{w:'japan',h:'The Land of the Rising Sun'},{w:'india',h:'The most populous democracy on Earth'},
    {w:'brazil',h:'The largest country in South America'},{w:'france',h:'Home to the Eiffel Tower'},{w:'canada',h:'The second largest country by area'},
    {w:'greece',h:'Birthplace of the Olympics'},{w:'mexico',h:'A country known for tacos and Mariachi'},{w:'turkey',h:'A country straddling Europe and Asia'},
    {w:'lebanon',h:'A small Middle Eastern country on the Mediterranean'},{w:'iceland',h:'A Nordic island nation of fire and ice'},
    {w:'portugal',h:'A country on the Iberian Peninsula'},{w:'argentina',h:'Home of tango and Messi'},{w:'australia',h:'A country that is also a continent'},
    {w:'barcelona',h:'A Spanish city famous for its architecture'},{w:'istanbul',h:'A city on two continents'},{w:'tokyo',h:'The most populous city in the world'}
  ],
  sports:[
    {w:'ski',h:'A winter sport on snow slopes'},{w:'golf',h:'A sport played with clubs and holes'},{w:'surf',h:'Riding waves on a board'},
    {w:'tennis',h:'A sport played with a racket and net'},{w:'boxing',h:'A combat sport with gloves'},{w:'rugby',h:'A contact team sport with an oval ball'},
    {w:'soccer',h:'The most popular sport in the world'},{w:'hockey',h:'A sport played with a stick and puck'},{w:'cycling',h:'Racing on two wheels'},
    {w:'archery',h:'Shooting arrows at a target'},{w:'fencing',h:'A sword-based Olympic sport'},{w:'bowling',h:'Rolling a ball to knock down pins'},
    {w:'marathon',h:'A 42km running race'},{w:'swimming',h:'Moving through water competitively'},{w:'baseball',h:'A bat and ball game with four bases'},
    {w:'volleyball',h:'A sport played over a net with a ball'},{w:'gymnastics',h:'An Olympic sport of acrobatic skill'},{w:'basketball',h:'A sport with hoops and a bouncing ball'}
  ],
  movies:[
    {w:'avatar',h:'A sci-fi film set on the planet Pandora'},{w:'frozen',h:'An animated film about a queen of ice'},
    {w:'alien',h:'A sci-fi horror film set in space'},{w:'jaws',h:'A thriller about a great white shark'},{w:'shrek',h:'An animated film about a green ogre'},
    {w:'matrix',h:'A sci-fi film where reality is a simulation'},{w:'titanic',h:'A romance set on a doomed ocean liner'},
    {w:'batman',h:'A superhero who fights crime in Gotham'},{w:'minions',h:'Small yellow animated movie characters'},
    {w:'inception',h:'A film about entering peoples dreams'},{w:'interstellar',h:'A space film about wormholes and time'},
    {w:'gladiator',h:'A Roman epic about a betrayed general'},{w:'parasite',h:'An Oscar winning Korean thriller'},
    {w:'avengers',h:'A team of Marvel superheroes'},{w:'casablanca',h:'A classic romantic wartime drama'},
    {w:'godfather',h:'A classic Mafia family drama'},{w:'joker',h:'A film about a troubled man becoming a villain'}
  ],
  tech:[
    {w:'app',h:'A software program on your phone'},{w:'wifi',h:'Wireless internet connection'},{w:'robot',h:'A machine that can perform automated tasks'},
    {w:'pixel',h:'The smallest unit of a digital image'},{w:'cloud',h:'Remote storage and computing over the internet'},{w:'virus',h:'Malicious software that infects computers'},
    {w:'server',h:'A computer that provides data to other computers'},{w:'cursor',h:'The arrow you move on screen'},{w:'laptop',h:'A portable personal computer'},
    {w:'browser',h:'A program used to access the internet'},{w:'podcast',h:'An audio show you subscribe to online'},{w:'battery',h:'A device that stores electrical energy'},
    {w:'keyboard',h:'An input device with letter keys'},{w:'software',h:'Programs that run on a computer'},{w:'database',h:'An organized collection of stored data'},
    {w:'algorithm',h:'A step-by-step set of instructions for a computer'},{w:'bluetooth',h:'A wireless technology for short-range data exchange'},
    {w:'encryption',h:'Encoding data so only authorized users can read it'},{w:'javascript',h:'A popular programming language for websites'}
  ],
  mixed:[
    {w:'sun',h:'The star at the center of our solar system'},{w:'map',h:'A diagram showing a geographical area'},
    {w:'gem',h:'A precious or semi-precious stone'},{w:'book',h:'Sheets of text bound together'},{w:'fire',h:'The rapid oxidation of material'},
    {w:'clock',h:'A device for measuring time'},{w:'music',h:'Organized sound for artistic purposes'},{w:'ocean',h:'A vast body of salt water'},
    {w:'bridge',h:'A structure built to cross a gap'},{w:'castle',h:'A large medieval fortified building'},
    {w:'mirror',h:'A reflective surface showing your image'},{w:'candle',h:'A wax stick with a wick that burns'},
    {w:'compass',h:'A navigation tool that points north'},{w:'lantern',h:'A portable light in a protective case'},
    {w:'volcano',h:'A mountain that erupts with lava'},{w:'diamond',h:'The hardest natural substance on Earth'},
    {w:'labyrinth',h:'A complicated maze'},{w:'adventure',h:'An exciting and unusual experience'},
    {w:'telescope',h:'A tool for viewing distant objects in space'},{w:'universe',h:'All of space and everything in it'}
  ]
};

// XP per level boundary
const LEVEL_XP = [0,100,250,450,700,1000,1350,1750,2200,2700,3250];
const LEVEL_TITLES = ['Novice','Apprentice','Player','Thinker','Guesser','Wordsmith','Scholar','Expert','Master','Legend','Grandmaster'];
const DIFF_CONFIG = { easy:{maxLen:5,lives:8,xp:10,color:'#5DCAA5',label:'Easy'}, medium:{maxLen:8,lives:6,xp:20,color:'#EF9F27',label:'Medium'}, hard:{maxLen:99,lives:5,xp:35,color:'#D85A30',label:'Hard'} };

const HINTS_WRONG = [
  "Hmm... not quite.",
  "Nope! Keep thinking.",
  "Wrong! But don't give up.",
  "That's not it...",
  "Almost... just kidding.",
  "Oof, not that one!"
];

// ════════════════════════════════════════
//  STORAGE
// ════════════════════════════════════════
function loadWords() {
  try {
    const s=JSON.parse(localStorage.getItem('hm_words'));
    if(!s||!s.length) return buildDefaultWords();
    if(typeof s[0]==='string'){ localStorage.removeItem('hm_words'); return buildDefaultWords(); }
    return s;
  } catch { return buildDefaultWords(); }
}
function buildDefaultWords() {
  const arr=[];
  Object.entries(WORD_DB).forEach(([cat,words])=>words.forEach(({w,h})=>arr.push({w,h,cat})));
  return arr;
}
function saveWords(a){ localStorage.setItem('hm_words',JSON.stringify(a)); }
function loadPass(){ return localStorage.getItem('hm_pass')||'n1o2u3r4z5i6'; }
function savePass(p){ localStorage.setItem('hm_pass',p); }
function loadHistory(){ try{return JSON.parse(localStorage.getItem('hm_history'))||[];}catch{return[];} }
function saveHistory(h){ localStorage.setItem('hm_history',JSON.stringify(h)); }
function loadPlayers(){ try{return JSON.parse(localStorage.getItem('hm_players'))||{};}catch{return {};} }
function savePlayers(p){ localStorage.setItem('hm_players',JSON.stringify(p)); }

function getPlayer(name){
  const ps=loadPlayers();
  if(!ps[name]) ps[name]={name,xp:0,level:1,wins:0,games:0};
  return ps[name];
}
function savePlayer(p){
  const ps=loadPlayers(); ps[p.name]=p; savePlayers(ps);
}

function addXP(playerName, amount) {
  const p=getPlayer(playerName);
  const oldLevel=p.level;
  p.xp+=amount; p.wins++;
  // level up check
  while(p.level<LEVEL_TITLES.length-1 && p.xp>=(LEVEL_XP[p.level]||9999)){
    p.level++;
  }
  savePlayer(p);
  showXPPopup('+'+amount+' XP');
  if(p.level>oldLevel) setTimeout(()=>showLevelUp(p.level),900);
  return p;
}
function addGame(playerName){
  const p=getPlayer(playerName); p.games++; savePlayer(p);
}

function getLevelProgress(p){
  const cur=LEVEL_XP[p.level-1]||0;
  const next=LEVEL_XP[p.level]||LEVEL_XP[LEVEL_XP.length-1];
  return Math.min(1,(p.xp-cur)/Math.max(1,next-cur));
}
function getLevelColor(level){
  const cols=['#888','#5DCAA5','#5DCAA5','#EF9F27','#EF9F27','#D85A30','#D85A30','#B97AFF','#B97AFF','#EF9F27','#EF9F27'];
  return cols[Math.min(level-1,cols.length-1)];
}

// ════════════════════════════════════════
//  NAVIGATION
// ════════════════════════════════════════
function show(id){
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
  if(id==='screen-landing') renderLandingCard();
}
function goLogin(){ document.getElementById('login-pass').value=''; document.getElementById('login-error').textContent=''; show('screen-login'); }
function doLogin(){
  if(document.getElementById('login-pass').value===loadPass()){ renderAdmin(); show('screen-admin'); }
  else document.getElementById('login-error').textContent='Incorrect password.';
}

// ════════════════════════════════════════
//  PLAYER CARD RENDER
// ════════════════════════════════════════
function playerCardHTML(p){
  const prog=Math.round(getLevelProgress(p)*100);
  const col=getLevelColor(p.level);
  const initials=p.name.slice(0,2).toUpperCase();
  return `<div class="player-card">
    <div class="player-avatar" style="border-color:${col};color:${col}">${initials}</div>
    <div class="player-info">
      <div class="player-name-label">${p.name}</div>
      <div class="player-level-label">Level ${p.level} — ${LEVEL_TITLES[p.level-1]} · ${p.wins}W / ${p.games}G · ${p.xp} XP</div>
      <div class="xp-bar-wrap"><div class="xp-bar-fill" style="width:${prog}%;background:${col}"></div></div>
    </div>
  </div>`;
}

function renderLandingCard(){
  const last=localStorage.getItem('hm_last_player');
  const el=document.getElementById('landing-player-card');
  if(last){ const p=getPlayer(last); el.innerHTML=playerCardHTML(p); el.style.display='block'; }
  else el.style.display='none';
}

// ════════════════════════════════════════
//  CATEGORY GRID
// ════════════════════════════════════════
let selectedCat='mixed', selectedVSCat='mixed';
let selectedDiff='easy', selectedVSDiff='easy';

function buildCatGrids(){
  ['solo','vs'].forEach(mode=>{
    const grid=document.getElementById('cat-grid-'+mode);
    grid.innerHTML='';
    Object.entries(CATEGORIES).forEach(([key,cat])=>{
      const d=document.createElement('div');
      d.className='cat-card'+(((mode==='solo'&&key===selectedCat)||(mode==='vs'&&key===selectedVSCat))?' selected':'');
      d.id=`cat-${mode}-${key}`;
      d.onclick=()=>selectCat(key,mode);
      d.innerHTML=`<div class="cat-icon">${cat.icon}</div><div class="cat-name">${cat.label}</div>`;
      grid.appendChild(d);
    });
  });
}

function selectCat(key,mode){
  if(mode==='solo'){ selectedCat=key; document.querySelectorAll('#cat-grid-solo .cat-card').forEach(c=>c.classList.remove('selected')); }
  else { selectedVSCat=key; document.querySelectorAll('#cat-grid-vs .cat-card').forEach(c=>c.classList.remove('selected')); }
  document.getElementById(`cat-${mode}-${key}`).classList.add('selected');
}

function selectDiff(d,mode){
  if(mode==='solo'){ selectedDiff=d; ['easy','medium','hard'].forEach(x=>document.getElementById('dc-'+x).classList.toggle('selected',x===d)); }
  else { selectedVSDiff=d; ['easy','medium','hard'].forEach(x=>document.getElementById('vs-dc-'+x).classList.toggle('selected',x===d)); }
}

// ════════════════════════════════════════
//  LEADERBOARD
// ════════════════════════════════════════
function showLeaderboard(sortBy){
  ['wins','xp','level','pct'].forEach(t=>{ const el=document.getElementById('lb-tab-'+t); if(el) el.classList.toggle('active',t===sortBy); });
  const ps=Object.values(loadPlayers());
  const content=document.getElementById('lb-content');
  if(!ps.length){ content.innerHTML='<p class="no-msg">No players yet.</p>'; show('screen-leaderboard'); return; }
  const sorted=ps.sort((a,b)=>{
    if(sortBy==='wins') return b.wins-a.wins;
    if(sortBy==='xp') return b.xp-a.xp;
    if(sortBy==='level') return b.level-a.level||b.xp-a.xp;
    const pa=a.games?a.wins/a.games:0, pb=b.games?b.wins/b.games:0;
    return pb-pa;
  });
  const rankCls=['gold','silver','bronze'];
  const rows=sorted.map((p,i)=>{
    const col=getLevelColor(p.level);
    const pct=p.games?Math.round(p.wins/p.games*100):0;
    return `<tr>
      <td class="lb-rank ${i<3?rankCls[i]:''}">${i<3?['#1','#2','#3'][i]:'#'+(i+1)}</td>
      <td class="lb-name" style="color:${col}">${p.name}</td>
      <td class="lb-val" style="color:${col}">Lv.${p.level}</td>
      <td class="lb-val">${p.wins}W</td>
      <td class="lb-val" style="color:#888">${p.xp}xp</td>
      <td class="lb-val" style="color:#666">${pct}%</td>
    </tr>`;
  }).join('');
  content.innerHTML=`<table class="lb-table"><thead><tr><th></th><th>Player</th><th>Level</th><th style="text-align:right">Wins</th><th style="text-align:right">XP</th><th style="text-align:right">Win%</th></tr></thead><tbody>${rows}</tbody></table>`;
  show('screen-leaderboard');
}

// ════════════════════════════════════════
//  ANIMATIONS
// ════════════════════════════════════════
function showXPPopup(text){
  const el=document.getElementById('xp-popup');
  el.textContent=text; el.className='xp-popup';
  void el.offsetWidth;
  el.classList.add('burst');
}

function showLevelUp(level){
  document.getElementById('lvlup-num').textContent=level;
  document.getElementById('lvlup-title').textContent=LEVEL_TITLES[level-1]||'Legend';
  document.getElementById('lvlup-overlay').classList.add('show');
}
function closeLevelUp(){ document.getElementById('lvlup-overlay').classList.remove('show'); }

function triggerLoseAnimation(){
  const overlay=document.getElementById('lose-overlay');
  overlay.innerHTML='';
  for(let i=0;i<18;i++){
    const s=document.createElement('div');
    s.className='skull'; s.textContent='💀';
    s.style.left=Math.random()*100+'%';
    s.style.animationDuration=(1+Math.random()*1.5)+'s';
    s.style.animationDelay=(Math.random()*.6)+'s';
    overlay.appendChild(s);
  }
  setTimeout(()=>{ overlay.innerHTML=''; },2500);
}

function shakeGallows(){
  const svg=document.getElementById('hm-svg');
  svg.classList.remove('shake'); void svg.offsetWidth; svg.classList.add('shake');
}

// ════════════════════════════════════════
//  ADMIN
// ════════════════════════════════════════
function switchTab(name){
  document.querySelectorAll('.tab-btn').forEach(b=>b.classList.toggle('active',b.textContent.toLowerCase().trim()===name));
  document.querySelectorAll('.tab-panel').forEach(p=>p.classList.remove('active'));
  document.getElementById('tab-'+name).classList.add('active');
  if(name==='history') renderHistory();
}
function renderAdmin(){ renderWordList(); }

let words=loadWords();
function renderWordList(){
  words=loadWords();
  const list=document.getElementById('word-list'), noMsg=document.getElementById('no-words-msg');
  document.getElementById('word-count').textContent=words.length+' word'+(words.length!==1?'s':'');
  list.innerHTML='';
  if(!words.length){ noMsg.style.display='block'; return; }
  noMsg.style.display='none';
  const sorted=[...words].sort((a,b)=>a.w.length-b.w.length);
  sorted.forEach(entry=>{
    const idx=words.indexOf(entry);
    const diff=getDifficulty(entry.w);
    const cat=CATEGORIES[entry.cat]||CATEGORIES.mixed;
    const item=document.createElement('div'); item.className='word-item';
    item.innerHTML=`<span class="wi-word">${entry.w}</span><span class="wi-badge wi-cat">${cat.icon} ${cat.label}</span><span class="wi-badge wi-diff-${diff}">${DIFF_CONFIG[diff].label}</span><button class="del-btn" onclick="removeWord(${idx})">✕</button>`;
    list.appendChild(item);
  });
}
function addWord(){
  const input=document.getElementById('new-word-input'), err=document.getElementById('admin-error');
  const val=input.value.trim().toLowerCase().replace(/[^a-z]/g,'');
  const cat=document.getElementById('new-word-cat').value;
  if(!val){ err.textContent='Please enter a word.'; return; }
  if(val.length<2){ err.textContent='Word must be at least 2 letters.'; return; }
  if(words.find(e=>e.w===val)){ err.textContent=`"${val}" already exists.`; return; }
  err.textContent=''; words.push({w:val,h:'',cat}); saveWords(words); input.value=''; renderWordList();
}
function removeWord(i){ words.splice(i,1); saveWords(words); renderWordList(); }
function clearWords(){ if(confirm('Reset to defaults?')){ words=buildDefaultWords(); saveWords(words); renderWordList(); } }
function changePassword(){
  const p1=document.getElementById('new-pass-1').value, p2=document.getElementById('new-pass-2').value, err=document.getElementById('pass-error');
  if(!p1){ err.textContent='Password cannot be empty.'; return; }
  if(p1!==p2){ err.textContent='Passwords do not match.'; return; }
  savePass(p1); err.style.color='#5DCAA5'; err.textContent='Password updated!';
  document.getElementById('new-pass-1').value=''; document.getElementById('new-pass-2').value='';
  setTimeout(()=>{ err.textContent=''; err.style.color='#D85A30'; },2500);
}
function renderHistory(){
  const h=loadHistory(), list=document.getElementById('history-list'), noMsg=document.getElementById('no-history-msg');
  list.innerHTML='';
  if(!h.length){ noMsg.style.display='block'; return; }
  noMsg.style.display='none';
  h.forEach(entry=>{
    const item=document.createElement('div'); item.className='history-item';
    const wrong=entry.guesses?entry.guesses.filter(g=>!entry.word.includes(g)).join(', '):'—';
    const right=entry.guesses?entry.guesses.filter(g=>entry.word.includes(g)).join(', '):'—';
    const cat=CATEGORIES[entry.cat]||CATEGORIES.mixed;
    item.innerHTML=`<div class="hi-top"><span class="hi-name">${entry.name}</span><span class="hi-word">${entry.word}</span><span style="font-size:11px">${cat.icon}</span><span class="${entry.won?'hi-win':'hi-lose'}">${entry.won?'WIN':'LOST'}</span></div>
    <div class="hi-meta">${entry.date} · ${entry.diff||''} · ${entry.wrong||0} wrong</div>
    <div class="hi-guesses">Correct: ${right}</div><div class="hi-guesses">Wrong: <span style="color:#D85A30">${wrong}</span></div>`;
    list.appendChild(item);
  });
}
function clearHistory(){ if(confirm('Clear all history?')){ saveHistory([]); renderHistory(); } }

// ════════════════════════════════════════
//  GAME ENGINE
// ════════════════════════════════════════
function getDifficulty(w){ return w.length<=5?'easy':w.length<=8?'medium':'hard'; }

let currentPlayer='', currentDiff='easy', currentCat='mixed';
let vsMode=false, vsPlayers=['',''], vsScores=[0,0], vsTurn=0, vsRound=1, vsGlobalDiff='easy', vsGlobalCat='mixed';
let secret='', secretHint='', secretCat='', guessed, wrong, maxWrong, gameOver, allGuesses, hintUsed;

function pickWord(cat,diff){
  const pool=loadWords().filter(e=>{ const dc=getDifficulty(e.w); return (cat==='mixed'||e.cat===cat)&&dc===diff; });
  if(!pool.length){ const fallback=loadWords().filter(e=>getDifficulty(e.w)===diff); return fallback.length?fallback[Math.floor(Math.random()*fallback.length)]:loadWords()[0]; }
  return pool[Math.floor(Math.random()*pool.length)];
}

function startSolo(){
  const val=document.getElementById('player-name-input').value.trim();
  if(!val){ document.getElementById('name-error').textContent='Please enter your name.'; return; }
  document.getElementById('name-error').textContent='';
  currentPlayer=val; currentDiff=selectedDiff; currentCat=selectedCat;
  localStorage.setItem('hm_last_player',val);
  addGame(val);
  vsMode=false;
  document.getElementById('vs-header').style.display='none';
  const p=getPlayer(val);
  document.getElementById('solo-player-card').innerHTML=playerCardHTML(p);
  document.getElementById('solo-player-card').style.display='block';
  document.getElementById('player-name-input').value='';
  newGame(); show('screen-game');
}

function startVS(){
  const n1=document.getElementById('vs-name-1').value.trim(), n2=document.getElementById('vs-name-2').value.trim(), err=document.getElementById('vs-error');
  if(!n1||!n2){ err.textContent='Both players need a name.'; return; }
  if(n1.toLowerCase()===n2.toLowerCase()){ err.textContent='Players must have different names.'; return; }
  err.textContent='';
  vsMode=true; vsPlayers=[n1,n2]; vsScores=[0,0]; vsTurn=0; vsRound=1;
  vsGlobalDiff=selectedVSDiff; vsGlobalCat=selectedVSCat;
  currentDiff=vsGlobalDiff; currentCat=vsGlobalCat; currentPlayer=n1;
  addGame(n1); addGame(n2);
  document.getElementById('vs-name-1').value=''; document.getElementById('vs-name-2').value='';
  document.getElementById('solo-player-card').style.display='none';
  updateVSHeader();
  document.getElementById('vs-header').style.display='flex';
  newGame(); show('screen-game');
}

function updateVSHeader(){
  document.getElementById('vs-label-1').textContent=vsPlayers[0];
  document.getElementById('vs-label-2').textContent=vsPlayers[1];
  document.getElementById('vs-val-1').textContent=vsScores[0];
  document.getElementById('vs-val-2').textContent=vsScores[1];
  document.getElementById('vs-turn-label').textContent=vsPlayers[vsTurn];
  document.getElementById('vs-round-label').textContent='Round '+vsRound;
  document.getElementById('vs-box-1').classList.toggle('active',vsTurn===0);
  document.getElementById('vs-box-2').classList.toggle('active',vsTurn===1);
}

const bodyParts=["hm-head","hm-body","hm-larm","hm-rarm","hm-lleg","hm-rleg"];

function newGame(){
  const entry=pickWord(currentCat,currentDiff);
  secret=entry.w; secretHint=entry.h||''; secretCat=entry.cat||'mixed';
  maxWrong=DIFF_CONFIG[currentDiff].lives;
  guessed=new Set(); wrong=0; gameOver=false; allGuesses=[]; hintUsed=false;
  bodyParts.forEach(id=>document.getElementById(id).style.display='none');
  document.getElementById('hm-status').textContent='';
  document.getElementById('hm-next-btn').style.display='none';
  document.getElementById('hm-wrong-letters').textContent='—';
  document.getElementById('hm-lives-count').textContent=maxWrong;
  document.getElementById('hm-svg').classList.remove('shake');
  const catInfo=CATEGORIES[secretCat]||CATEGORIES.mixed;
  document.getElementById('hm-cat-badge').textContent=catInfo.icon+' '+catInfo.label;
  const dc=DIFF_CONFIG[currentDiff];
  document.getElementById('hm-diff-badge').style.color=dc.color;
  document.getElementById('hm-diff-badge').textContent=dc.label;
  // hint button
  const hintBtn=document.getElementById('hint-btn');
  if(secretHint){ hintBtn.style.display='block'; hintBtn.textContent='Show hint (costs 5 XP)'; }
  else hintBtn.style.display='none';
  document.getElementById('hint-box').classList.remove('show');
  if(vsMode) updateVSHeader();
  else {
    const p=getPlayer(currentPlayer);
    document.getElementById('solo-player-card').innerHTML=playerCardHTML(p);
  }
  renderWord(); renderKeyboard();
}

function useHint(){
  if(hintUsed||!secretHint) return;
  hintUsed=true;
  document.getElementById('hint-box').textContent='Hint: '+secretHint;
  document.getElementById('hint-box').classList.add('show');
  document.getElementById('hint-btn').style.display='none';
}

function renderWord(){
  const row=document.getElementById('hm-word-row'); row.innerHTML='';
  for(const ch of secret){
    const box=document.createElement('div');
    const isRevealed=guessed.has(ch);
    box.className='hm-letter-box'+(isRevealed?' revealed':'');
    box.textContent=isRevealed?ch.toUpperCase():'';
    row.appendChild(box);
  }
}

function renderKeyboard(){
  const kb=document.getElementById('hm-keyboard'); kb.innerHTML='';
  for(let i=0;i<26;i++){
    const ch=String.fromCharCode(97+i);
    const btn=document.createElement('button');
    btn.className='hm-key'; btn.textContent=ch; btn.dataset.ch=ch;
    if(guessed.has(ch)){ btn.classList.add(secret.includes(ch)?'correct':'wrong'); btn.disabled=true; }
    btn.addEventListener('click',()=>guess(ch));
    kb.appendChild(btn);
  }
}

function guess(ch){
  if(gameOver||guessed.has(ch)) return;
  guessed.add(ch); allGuesses.push(ch);
  const btn=document.querySelector(`.hm-key[data-ch="${ch}"]`);
  if(secret.includes(ch)){
    btn.classList.add('correct'); btn.disabled=true;
    renderWord();
    if([...secret].every(l=>guessed.has(l))){ document.getElementById('hm-status').textContent='you win!'; finishRound(true); }
  } else {
    btn.classList.add('wrong'); btn.disabled=true;
    shakeGallows();
    const partIdx=Math.floor(wrong*bodyParts.length/maxWrong);
    if(partIdx<bodyParts.length) document.getElementById(bodyParts[partIdx]).style.display='block';
    wrong++;
    document.getElementById('hm-lives-count').textContent=maxWrong-wrong;
    const wl=[...guessed].filter(l=>!secret.includes(l)).join('  ');
    document.getElementById('hm-wrong-letters').textContent=wl||'—';
    // taunting hint sentence
    if(wrong<maxWrong){
      const taunt=HINTS_WRONG[Math.floor(Math.random()*HINTS_WRONG.length)];
      document.getElementById('hm-status').textContent=taunt;
      setTimeout(()=>{ if(!gameOver) document.getElementById('hm-status').textContent=''; },1200);
    }
    if(wrong>=maxWrong){
      bodyParts.forEach(id=>document.getElementById(id).style.display='block');
      document.getElementById('hm-status').textContent='the word was: '+secret;
      finishRound(false);
    }
  }
}

function finishRound(won){
  gameOver=true;
  const pName=vsMode?vsPlayers[vsTurn]:currentPlayer;
  if(!won){ [...secret].forEach(ch=>guessed.add(ch)); renderWord(); triggerLoseAnimation(); }
  else {
    const xpEarned=DIFF_CONFIG[currentDiff].xp-(hintUsed?5:0);
    const p=addXP(pName,Math.max(1,xpEarned));
    if(!vsMode){ document.getElementById('solo-player-card').innerHTML=playerCardHTML(p); }
  }
  // games already counted at game start via addGame()

  const h=loadHistory();
  h.unshift({name:pName,word:secret,cat:secretCat,diff:currentDiff,won,wrong,guesses:allGuesses,date:new Date().toLocaleString()});
  if(h.length>300) h.pop(); saveHistory(h);

  const nextBtn=document.getElementById('hm-next-btn');
  if(vsMode){
    if(won) vsScores[vsTurn]++;
    vsTurn=vsTurn===0?1:0;
    if(vsTurn===0) vsRound++;
    nextBtn.textContent='Next turn ('+vsPlayers[vsTurn]+')';
  } else { nextBtn.textContent='Play again'; }
  nextBtn.style.display='inline-block';
}

function handleNextBtn(){ newGame(); }

document.addEventListener('keydown',e=>{
  if(document.getElementById('screen-game').classList.contains('active'))
    if(e.key.length===1&&/[a-zA-Z]/.test(e.key)) guess(e.key.toLowerCase());
});

// ── INIT ──
buildCatGrids();
renderLandingCard();
</script>
</body>
</html>

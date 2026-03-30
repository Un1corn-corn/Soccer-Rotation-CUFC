# Soccer-Rotation-CUFC
Tracks players time on, off, injuries and totals
<index.html>
<!DOCTYPE html>

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="MatchDay">
<title>MatchDay — Rotation Manager</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Sans:wght@400;500;600&display=swap" rel="stylesheet">
<style>
/* =============================================
   CSS VARIABLES & RESET
   ============================================= */
:root {
  --bg:        #0d0f14;
  --surface:   #161a23;
  --card:      #1e2330;
  --border:    #2a3045;
  --accent:    #00e5a0;
  --warn:      #f5c518;
  --danger:    #ff4560;
  --field:     #00e5a0;
  --bench:     #4f8ef7;
  --injured:   #ff4560;
  --text:      #e8ecf4;
  --muted:     #6b7591;
  --radius:    14px;
  --font-head: 'Bebas Neue', sans-serif;
  --font-body: 'DM Sans', sans-serif;
}

*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

html, body {
width: 100%; height: 100%;
overflow: hidden;
background: var(–bg);
color: var(–text);
font-family: var(–font-body);
-webkit-tap-highlight-color: transparent;
touch-action: manipulation;
}

/* =============================================
SCREEN MANAGEMENT
============================================= */
.screen { display: none; width: 100%; height: 100%; }
.screen.active { display: flex; }

/* =============================================
SETUP SCREEN
============================================= */
#setup-screen {
flex-direction: column;
overflow-y: auto;
padding: 24px 20px;
gap: 20px;
}

.setup-header {
text-align: center;
padding: 8px 0 4px;
}

.setup-header h1 {
font-family: var(–font-head);
font-size: 52px;
letter-spacing: 3px;
color: var(–accent);
line-height: 1;
}

.setup-header p {
font-size: 14px;
color: var(–muted);
margin-top: 4px;
letter-spacing: 1px;
text-transform: uppercase;
}

.setup-grid {
display: grid;
grid-template-columns: 1fr 1fr;
gap: 12px;
}

.field-group {
background: var(–surface);
border: 1px solid var(–border);
border-radius: var(–radius);
padding: 14px 16px;
}

.field-group.full { grid-column: 1 / -1; }

.field-group label {
display: block;
font-size: 11px;
font-weight: 600;
text-transform: uppercase;
letter-spacing: 1.2px;
color: var(–muted);
margin-bottom: 8px;
}

.field-group input[type=“number”],
.field-group textarea {
width: 100%;
background: var(–card);
border: 1px solid var(–border);
border-radius: 8px;
color: var(–text);
font-family: var(–font-body);
font-size: 22px;
font-weight: 600;
padding: 10px 12px;
outline: none;
-webkit-appearance: none;
}

.field-group textarea {
font-size: 16px;
font-weight: 400;
min-height: 140px;
resize: vertical;
line-height: 1.6;
}

.field-group input:focus,
.field-group textarea:focus {
border-color: var(–accent);
}

/* Toggle switch */
.toggle-row {
display: flex;
align-items: center;
justify-content: space-between;
}

.toggle {
position: relative;
width: 56px;
height: 30px;
}

.toggle input { opacity: 0; width: 0; height: 0; }

.toggle-slider {
position: absolute;
inset: 0;
background: var(–card);
border: 1px solid var(–border);
border-radius: 30px;
cursor: pointer;
transition: background 0.2s;
}

.toggle-slider::before {
content: ‘’;
position: absolute;
height: 22px; width: 22px;
left: 3px; top: 3px;
border-radius: 50%;
background: var(–muted);
transition: transform 0.2s, background 0.2s;
}

.toggle input:checked + .toggle-slider { background: var(–accent); border-color: var(–accent); }
.toggle input:checked + .toggle-slider::before {
transform: translateX(26px);
background: #0d0f14;
}

#apply-btn {
background: var(–accent);
color: #0d0f14;
border: none;
border-radius: var(–radius);
font-family: var(–font-head);
font-size: 28px;
letter-spacing: 2px;
padding: 18px;
width: 100%;
cursor: pointer;
transition: opacity 0.15s, transform 0.1s;
-webkit-tap-highlight-color: transparent;
}

#apply-btn:active { opacity: 0.8; transform: scale(0.98); }

/* =============================================
GAME SCREEN LAYOUT
============================================= */
#game-screen {
flex-direction: column;
height: 100%;
overflow: hidden;
}

/* –– TOP BAR –– */
#top-bar {
display: flex;
align-items: center;
justify-content: space-between;
background: var(–surface);
border-bottom: 1px solid var(–border);
padding: 0 16px;
height: 52px;
flex-shrink: 0;
gap: 10px;
}

#top-bar .brand {
font-family: var(–font-head);
font-size: 22px;
letter-spacing: 2px;
color: var(–accent);
}

.half-block-info {
display: flex;
gap: 8px;
font-size: 13px;
font-weight: 600;
color: var(–muted);
text-transform: uppercase;
letter-spacing: 0.8px;
}

.half-block-info span { color: var(–text); }

#field-count-badge {
font-size: 13px;
font-weight: 700;
padding: 4px 12px;
border-radius: 20px;
background: var(–card);
border: 1.5px solid var(–border);
transition: all 0.2s;
}

#field-count-badge.ok     { color: var(–accent); border-color: var(–accent); }
#field-count-badge.low    { color: var(–warn);   border-color: var(–warn); }
#field-count-badge.over   { color: var(–danger); border-color: var(–danger); }

#settings-btn {
background: var(–card);
border: 1px solid var(–border);
color: var(–muted);
border-radius: 8px;
padding: 6px 12px;
font-size: 13px;
font-weight: 600;
cursor: pointer;
white-space: nowrap;
}

/* –– TIMER PANEL –– */
#timer-panel {
background: var(–surface);
border-bottom: 1px solid var(–border);
display: flex;
align-items: center;
justify-content: space-between;
padding: 10px 16px;
flex-shrink: 0;
gap: 12px;
}

#timer-display {
font-family: var(–font-head);
font-size: 72px;
line-height: 1;
color: var(–text);
letter-spacing: 3px;
min-width: 180px;
text-align: center;
}

#timer-display.warning { color: var(–warn); }
#timer-display.critical { color: var(–danger); animation: pulse 0.5s ease-in-out infinite alternate; }

@keyframes pulse { from { opacity: 1; } to { opacity: 0.5; } }

.timer-controls {
display: flex;
flex-direction: column;
gap: 8px;
flex: 1;
}

.timer-row {
display: flex;
gap: 8px;
}

/* Control buttons */
.ctrl-btn {
flex: 1;
padding: 12px 8px;
border: none;
border-radius: 10px;
font-family: var(–font-head);
font-size: 18px;
letter-spacing: 1.5px;
cursor: pointer;
transition: opacity 0.15s, transform 0.1s;
}

.ctrl-btn:active { opacity: 0.75; transform: scale(0.96); }

#start-btn  { background: var(–accent);  color: #0d0f14; }
#pause-btn  { background: var(–warn);    color: #0d0f14; }
#block-btn  { background: var(–bench);   color: #fff; }
#reset-btn  { background: var(–card);    color: var(–danger); border: 1px solid var(–danger); }
#save-btn   { background: var(–card);    color: var(–muted); border: 1px solid var(–border); }
#load-btn   { background: var(–card);    color: var(–muted); border: 1px solid var(–border); }

/* –– PLAYER COLUMNS AREA –– */
#columns-area {
display: grid;
grid-template-columns: 1fr 1fr 1fr;
flex: 1;
overflow: hidden;
gap: 0;
}

.col {
display: flex;
flex-direction: column;
overflow: hidden;
border-right: 1px solid var(–border);
}

.col:last-child { border-right: none; }

.col-header {
display: flex;
align-items: center;
justify-content: center;
gap: 8px;
padding: 9px 10px;
border-bottom: 2px solid transparent;
flex-shrink: 0;
}

.col[data-col=“field”] .col-header  { border-color: var(–field); }
.col[data-col=“bench”] .col-header  { border-color: var(–bench); }
.col[data-col=“injured”] .col-header { border-color: var(–injured); }

.col-label {
font-family: var(–font-head);
font-size: 18px;
letter-spacing: 1.5px;
}

.col[data-col=“field”]   .col-label { color: var(–field); }
.col[data-col=“bench”]   .col-label { color: var(–bench); }
.col[data-col=“injured”] .col-label { color: var(–injured); }

.col-count {
font-size: 12px;
font-weight: 700;
color: var(–muted);
}

.col-players {
flex: 1;
overflow-y: auto;
padding: 6px;
display: flex;
flex-direction: column;
gap: 6px;
-webkit-overflow-scrolling: touch;
}

/* –– PLAYER CARD –– */
.player-card {
background: var(–card);
border: 1.5px solid var(–border);
border-radius: 10px;
padding: 8px 10px;
transition: border-color 0.15s, transform 0.1s;
user-select: none;
}

.player-card.just-moved {
animation: flashIn 0.4s ease-out;
}

@keyframes flashIn {
from { background: #2a3a50; transform: scale(1.03); }
to   { background: var(–card); transform: scale(1); }
}

.card-name {
font-size: 15px;
font-weight: 700;
color: var(–text);
white-space: nowrap;
overflow: hidden;
text-overflow: ellipsis;
margin-bottom: 4px;
}

.card-times {
display: flex;
gap: 6px;
font-size: 10px;
font-weight: 600;
color: var(–muted);
margin-bottom: 8px;
}

.card-times .t-field   { color: var(–field); }
.card-times .t-bench   { color: var(–bench); }
.card-times .t-injured { color: var(–injured); }

.card-actions {
display: grid;
grid-template-columns: 1fr 1fr 1fr;
gap: 4px;
}

.move-btn {
border: none;
border-radius: 6px;
padding: 6px 2px;
font-size: 9px;
font-weight: 700;
text-transform: uppercase;
letter-spacing: 0.5px;
cursor: pointer;
transition: opacity 0.15s, transform 0.1s;
}

.move-btn:active { opacity: 0.7; transform: scale(0.95); }

.move-btn.to-field   { background: rgba(0,229,160,0.15); color: var(–field);   border: 1px solid rgba(0,229,160,0.3); }
.move-btn.to-bench   { background: rgba(79,142,247,0.15); color: var(–bench);  border: 1px solid rgba(79,142,247,0.3); }
.move-btn.to-injured { background: rgba(255,69,96,0.15);  color: var(–injured);border: 1px solid rgba(255,69,96,0.3); }

.move-btn.active-loc { opacity: 0.3; cursor: default; pointer-events: none; }

/* –– TOTALS DRAWER –– */
#totals-panel {
background: var(–surface);
border-top: 1px solid var(–border);
padding: 0;
flex-shrink: 0;
max-height: 200px;
overflow-y: auto;
-webkit-overflow-scrolling: touch;
}

#totals-toggle {
width: 100%;
background: var(–surface);
border: none;
border-bottom: 1px solid var(–border);
color: var(–muted);
font-family: var(–font-head);
font-size: 14px;
letter-spacing: 1.5px;
padding: 8px;
cursor: pointer;
display: flex;
align-items: center;
justify-content: center;
gap: 6px;
position: sticky;
top: 0;
z-index: 1;
}

#totals-table-wrap { padding: 0 8px 8px; }

#totals-table {
width: 100%;
border-collapse: collapse;
font-size: 13px;
}

#totals-table th {
font-size: 10px;
font-weight: 700;
text-transform: uppercase;
letter-spacing: 1px;
color: var(–muted);
padding: 6px 8px;
text-align: left;
border-bottom: 1px solid var(–border);
}

#totals-table td {
padding: 6px 8px;
border-bottom: 1px solid rgba(42,48,69,0.5);
font-size: 13px;
}

#totals-table td:first-child { font-weight: 700; }
#totals-table .td-field   { color: var(–field); font-weight: 600; }
#totals-table .td-bench   { color: var(–bench); font-weight: 600; }
#totals-table .td-injured { color: var(–injured); font-weight: 600; }

/* =============================================
ROTATION TOAST
============================================= */
#toast {
position: fixed;
bottom: 220px;
left: 50%;
transform: translateX(-50%) translateY(20px);
background: var(–card);
border: 1px solid var(–accent);
color: var(–accent);
font-size: 13px;
font-weight: 600;
padding: 10px 20px;
border-radius: 30px;
opacity: 0;
transition: opacity 0.3s, transform 0.3s;
pointer-events: none;
white-space: nowrap;
z-index: 999;
}

#toast.show {
opacity: 1;
transform: translateX(-50%) translateY(0);
}

/* Scrollbar styling */
::-webkit-scrollbar { width: 4px; }
::-webkit-scrollbar-track { background: transparent; }
::-webkit-scrollbar-thumb { background: var(–border); border-radius: 4px; }
</style>

</head>
<body>

<!-- ============================================================
     SETUP SCREEN
     ============================================================ -->

<div id="setup-screen" class="screen active">
  <div class="setup-header">
    <h1>MATCHDAY</h1>
    <p>Rotation Manager</p>
  </div>

  <div class="setup-grid">
    <div class="field-group">
      <label>Half Length (min)</label>
      <input type="number" id="s-half-len" value="20" min="1" max="60">
    </div>

```
<div class="field-group">
  <label>Block Length (min)</label>
  <input type="number" id="s-block-len" value="4" min="1" max="30">
</div>

<div class="field-group">
  <label>Players on Field</label>
  <input type="number" id="s-field-count" value="7" min="1" max="14">
</div>

<div class="field-group">
  <label>Rotate Count</label>
  <input type="number" id="s-rotate-count" value="3" min="1" max="7">
</div>

<div class="field-group full">
  <label>Auto Rotation</label>
  <div class="toggle-row">
    <span style="font-size:15px;font-weight:600;color:var(--text)">Auto-rotate players each block</span>
    <label class="toggle">
      <input type="checkbox" id="s-auto-rotate" checked>
      <span class="toggle-slider"></span>
    </label>
  </div>
</div>

<div class="field-group full">
  <label>Player List (one per line, 10–14 players)</label>
  <textarea id="s-players" spellcheck="false">Alice
```

Bob
Charlie
Diana
Ethan
Fiona
George
Hannah
Ivan
Julia
Kevin
Lena</textarea>
</div>

  </div>

<button id="apply-btn" onclick="applySetup()">START GAME</button>

</div>

<!-- ============================================================
     GAME SCREEN
     ============================================================ -->

<div id="game-screen" class="screen">

  <!-- TOP BAR -->

  <div id="top-bar">
    <span class="brand">MD</span>
    <div class="half-block-info">
      <span>HALF <span id="cur-half">1</span></span>
      <span style="color:var(--border)">|</span>
      <span>BLOCK <span id="cur-block">1</span></span>
    </div>
    <div id="field-count-badge">— on field</div>
    <button id="settings-btn" onclick="goSetup()">⚙ SETUP</button>
  </div>

  <!-- TIMER PANEL -->

  <div id="timer-panel">
    <div id="timer-display">20:00</div>
    <div class="timer-controls">
      <div class="timer-row">
        <button class="ctrl-btn" id="start-btn" onclick="startTimer()">START</button>
        <button class="ctrl-btn" id="pause-btn" onclick="pauseTimer()">PAUSE</button>
        <button class="ctrl-btn" id="block-btn" onclick="nextBlock()">NEXT BLOCK</button>
      </div>
      <div class="timer-row">
        <button class="ctrl-btn" id="reset-btn" onclick="resetGame()">RESET</button>
        <button class="ctrl-btn" id="save-btn" onclick="saveGame()">SAVE</button>
        <button class="ctrl-btn" id="load-btn" onclick="loadGame()">LOAD</button>
      </div>
    </div>
  </div>

  <!-- THREE PLAYER COLUMNS -->

  <div id="columns-area">
    <div class="col" data-col="field">
      <div class="col-header">
        <span class="col-label">FIELD</span>
        <span class="col-count" id="count-field">0</span>
      </div>
      <div class="col-players" id="players-field"></div>
    </div>
    <div class="col" data-col="bench">
      <div class="col-header">
        <span class="col-label">BENCH</span>
        <span class="col-count" id="count-bench">0</span>
      </div>
      <div class="col-players" id="players-bench"></div>
    </div>
    <div class="col" data-col="injured">
      <div class="col-header">
        <span class="col-label">INJURED</span>
        <span class="col-count" id="count-injured">0</span>
      </div>
      <div class="col-players" id="players-injured"></div>
    </div>
  </div>

  <!-- TOTALS PANEL (collapsible) -->

  <div id="totals-panel">
    <button id="totals-toggle" onclick="toggleTotals()">
      ▲ TOTALS
    </button>
    <div id="totals-table-wrap" style="display:none">
      <table id="totals-table">
        <thead>
          <tr>
            <th>Player</th>
            <th class="td-field">Field</th>
            <th class="td-bench">Bench</th>
            <th class="td-injured">Inj</th>
            <th>Total</th>
          </tr>
        </thead>
        <tbody id="totals-body"></tbody>
      </table>
    </div>
  </div>
</div>

<!-- TOAST NOTIFICATION -->

<div id="toast"></div>

<script>
/* =============================================
   STATE
   ============================================= */
let state = {
  // Config
  halfLen:       20,    // minutes
  blockLen:      4,     // minutes
  fieldCount:    7,     // required players on field
  rotateCount:   3,     // players to swap per block
  autoRotate:    true,

  // Game
  half:          1,
  block:         1,
  running:       false,
  halfSecsLeft:  0,     // seconds remaining in this half
  blockSecsLeft: 0,     // seconds remaining in this block

  // Players: { name, loc: 'field'|'bench'|'injured', fieldSecs, benchSecs, injuredSecs }
  players:       [],

  // Timestamps for background-aware timing
  lastTick:      null,  // Date.now() when timer last polled
};

let tickInterval = null;

/* =============================================
   SETUP → GAME
   ============================================= */
function applySetup() {
  const halfLen     = parseInt(document.getElementById('s-half-len').value)     || 20;
  const blockLen    = parseInt(document.getElementById('s-block-len').value)    || 4;
  const fieldCount  = parseInt(document.getElementById('s-field-count').value)  || 7;
  const rotateCount = parseInt(document.getElementById('s-rotate-count').value) || 3;
  const autoRotate  = document.getElementById('s-auto-rotate').checked;
  const playerLines = document.getElementById('s-players').value
    .split('\n')
    .map(l => l.trim())
    .filter(l => l.length > 0);

  if (playerLines.length < 2) {
    alert('Please enter at least 2 players.');
    return;
  }

  // Build players: first N on field, rest on bench
  const players = playerLines.map((name, i) => ({
    name,
    loc: i < fieldCount ? 'field' : 'bench',
    fieldSecs:   0,
    benchSecs:   0,
    injuredSecs: 0,
  }));

  state = {
    halfLen, blockLen, fieldCount, rotateCount, autoRotate,
    half: 1, block: 1,
    running: false,
    halfSecsLeft:  halfLen * 60,
    blockSecsLeft: blockLen * 60,
    lastTick: null,
    players,
  };

  saveToStorage();
  showScreen('game-screen');
  renderAll();
}

/* =============================================
   TIMER CONTROLS
   ============================================= */
function startTimer() {
  if (state.running) return;
  if (state.halfSecsLeft <= 0) {
    // Auto-advance half if possible
    if (state.half < 2) {
      state.half++;
      state.halfSecsLeft  = state.halfLen * 60;
      state.blockSecsLeft = state.blockLen * 60;
      state.block = 1;
    } else {
      showToast('Game Over');
      return;
    }
  }
  state.running = true;
  state.lastTick = Date.now();
  tickInterval = setInterval(tick, 1000);
  renderTimerDisplay();
}

function pauseTimer() {
  if (!state.running) return;
  clearInterval(tickInterval);
  state.running = false;
  saveToStorage();
  renderTimerDisplay();
}

function nextBlock() {
  /* Force-advance to next block */
  advanceBlock(true);
  renderAll();
  saveToStorage();
}

function resetGame() {
  if (!confirm('Reset game? All time data will be cleared.')) return;
  clearInterval(tickInterval);
  state.running = false;
  state.half = 1;
  state.block = 1;
  state.halfSecsLeft  = state.halfLen * 60;
  state.blockSecsLeft = state.blockLen * 60;
  state.lastTick = null;
  state.players.forEach(p => {
    p.fieldSecs = 0;
    p.benchSecs = 0;
    p.injuredSecs = 0;
    // re-assign positions: first N on field
    const idx = state.players.indexOf(p);
    p.loc = idx < state.fieldCount ? 'field' : 'bench';
  });
  renderAll();
  saveToStorage();
}

/* =============================================
   TICK — called every second
   ============================================= */
function tick() {
  const now     = Date.now();
  const elapsed = state.lastTick ? Math.round((now - state.lastTick) / 1000) : 1;
  state.lastTick = now;

  /* Accumulate time per player by status */
  state.players.forEach(p => {
    if      (p.loc === 'field')   p.fieldSecs   += elapsed;
    else if (p.loc === 'bench')   p.benchSecs   += elapsed;
    else if (p.loc === 'injured') p.injuredSecs += elapsed;
  });

  state.halfSecsLeft  = Math.max(0, state.halfSecsLeft  - elapsed);
  state.blockSecsLeft = Math.max(0, state.blockSecsLeft - elapsed);

  /* Block expired? */
  if (state.blockSecsLeft === 0) {
    advanceBlock(false);
  }

  /* Half expired? */
  if (state.halfSecsLeft === 0) {
    clearInterval(tickInterval);
    state.running = false;
    state.lastTick = null;
    showToast(state.half < 2 ? 'Half Time! Press START for 2nd half.' : 'Full Time!');
  }

  renderAll();

  // Auto-save every tick (handles sleep/minimize)
  saveToStorage();
}

/* =============================================
   BLOCK ADVANCEMENT
   ============================================= */
function advanceBlock(forced) {
  if (forced) {
    // Reset block timer
    state.blockSecsLeft = state.blockLen * 60;
  } else {
    // Natural rollover
    state.blockSecsLeft = state.blockLen * 60;
  }

  state.block++;

  /* Auto-rotation */
  if (state.autoRotate) {
    doAutoRotation();
  }
}

/* =============================================
   AUTO-ROTATION LOGIC
   ============================================= */
function doAutoRotation() {
  const onField  = state.players.filter(p => p.loc === 'field');
  const onBench  = state.players.filter(p => p.loc === 'bench');

  if (onBench.length === 0 || onField.length === 0) return;

  // Sort field by most field time (highest out first)
  const fieldSorted = [...onField].sort((a, b) => b.fieldSecs - a.fieldSecs);
  // Sort bench by least field time (lowest in first)
  const benchSorted = [...onBench].sort((a, b) => a.fieldSecs - b.fieldSecs);

  const swapCount = Math.min(state.rotateCount, onField.length, onBench.length);

  const outPlayers = fieldSorted.slice(0, swapCount);
  const inPlayers  = benchSorted.slice(0, swapCount);

  outPlayers.forEach(p => p.loc = 'bench');
  inPlayers.forEach(p => p.loc = 'field');

  const names = inPlayers.map(p => p.name.split(' ')[0]).join(', ');
  showToast(`▶ IN: ${names}`);
}

/* =============================================
   PLAYER MOVEMENT
   ============================================= */
function movePlayer(name, newLoc) {
  const p = state.players.find(p => p.name === name);
  if (!p || p.loc === newLoc) return;
  p.loc = newLoc;
  saveToStorage();
  renderAll();

  // Flash the moved card
  const card = document.querySelector(`[data-player="${CSS.escape(name)}"]`);
  if (card) {
    card.classList.remove('just-moved');
    void card.offsetWidth; // reflow
    card.classList.add('just-moved');
  }
}

/* =============================================
   RENDER
   ============================================= */
function renderAll() {
  renderTimerDisplay();
  renderColumns();
  renderFieldBadge();
  renderTotals();
  renderHalfBlock();
}

function renderHalfBlock() {
  document.getElementById('cur-half').textContent  = state.half;
  document.getElementById('cur-block').textContent = state.block;
}

/* Format seconds → MM:SS */
function fmt(secs) {
  const m = Math.floor(secs / 60);
  const s = secs % 60;
  return `${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
}

/* Format seconds → Xm display */
function fmtMin(secs) {
  const m = Math.floor(secs / 60);
  const s = secs % 60;
  return s > 0 ? `${m}m${s}s` : `${m}m`;
}

function renderTimerDisplay() {
  const el = document.getElementById('timer-display');
  el.textContent = fmt(state.halfSecsLeft);
  el.className = '';
  if (state.halfSecsLeft <= 60)  el.className = 'critical';
  else if (state.halfSecsLeft <= 180) el.className = 'warning';
}

function renderColumns() {
  const locs = ['field', 'bench', 'injured'];
  locs.forEach(loc => {
    const container = document.getElementById(`players-${loc}`);
    const players   = state.players.filter(p => p.loc === loc);
    document.getElementById(`count-${loc}`).textContent = players.length;

    container.innerHTML = '';
    players.forEach(p => {
      container.appendChild(buildCard(p));
    });
  });
}

function buildCard(p) {
  const card = document.createElement('div');
  card.className = 'player-card';
  card.dataset.player = p.name;

  const isField   = p.loc === 'field';
  const isBench   = p.loc === 'bench';
  const isInjured = p.loc === 'injured';

  card.innerHTML = `
    <div class="card-name">${p.name}</div>
    <div class="card-times">
      <span class="t-field">⬢ ${fmtMin(p.fieldSecs)}</span>
      <span class="t-bench">◈ ${fmtMin(p.benchSecs)}</span>
      <span class="t-injured">✦ ${fmtMin(p.injuredSecs)}</span>
    </div>
    <div class="card-actions">
      <button class="move-btn to-field   ${isField   ? 'active-loc' : ''}" onclick="movePlayer('${p.name}','field')">Field</button>
      <button class="move-btn to-bench   ${isBench   ? 'active-loc' : ''}" onclick="movePlayer('${p.name}','bench')">Bench</button>
      <button class="move-btn to-injured ${isInjured ? 'active-loc' : ''}" onclick="movePlayer('${p.name}','injured')">Inj</button>
    </div>`;

  return card;
}

function renderFieldBadge() {
  const count    = state.players.filter(p => p.loc === 'field').length;
  const required = state.fieldCount;
  const badge    = document.getElementById('field-count-badge');

  if (count === required) {
    badge.textContent = `✅ ${count} on field`;
    badge.className   = 'ok';
  } else if (count < required) {
    badge.textContent = `⚠️ ${count}/${required}`;
    badge.className   = 'low';
  } else {
    badge.textContent = `⛔ ${count}/${required}`;
    badge.className   = 'over';
  }
}

function renderTotals() {
  const tbody = document.getElementById('totals-body');
  tbody.innerHTML = '';
  [...state.players]
    .sort((a, b) => b.fieldSecs - a.fieldSecs)
    .forEach(p => {
      const total = p.fieldSecs + p.benchSecs + p.injuredSecs;
      const tr = document.createElement('tr');
      tr.innerHTML = `
        <td>${p.name}</td>
        <td class="td-field">${fmtMin(p.fieldSecs)}</td>
        <td class="td-bench">${fmtMin(p.benchSecs)}</td>
        <td class="td-injured">${fmtMin(p.injuredSecs)}</td>
        <td>${fmtMin(total)}</td>`;
      tbody.appendChild(tr);
    });
}

/* =============================================
   TOTALS TOGGLE
   ============================================= */
let totalsOpen = false;
function toggleTotals() {
  totalsOpen = !totalsOpen;
  document.getElementById('totals-table-wrap').style.display = totalsOpen ? 'block' : 'none';
  document.getElementById('totals-toggle').textContent = (totalsOpen ? '▼' : '▲') + ' TOTALS';
}

/* =============================================
   SAVE / LOAD (localStorage)
   ============================================= */
const SAVE_KEY = 'matchday_state';

function saveToStorage() {
  try {
    localStorage.setItem(SAVE_KEY, JSON.stringify(state));
  } catch(e) { /* ignore quota errors */ }
}

function saveGame() {
  saveToStorage();
  showToast('Game saved ✓');
}

function loadGame() {
  const raw = localStorage.getItem(SAVE_KEY);
  if (!raw) { showToast('No saved game found'); return; }
  try {
    const loaded = JSON.parse(raw);
    // Restore (timer won't auto-resume after load)
    clearInterval(tickInterval);
    loaded.running   = false;
    loaded.lastTick  = null;
    state = loaded;
    showScreen('game-screen');
    renderAll();
    showToast('Game loaded ✓');
  } catch(e) {
    showToast('Failed to load save');
  }
}

/* =============================================
   TOAST NOTIFICATION
   ============================================= */
let toastTimer = null;
function showToast(msg) {
  const el = document.getElementById('toast');
  el.textContent = msg;
  el.classList.add('show');
  if (toastTimer) clearTimeout(toastTimer);
  toastTimer = setTimeout(() => el.classList.remove('show'), 2800);
}

/* =============================================
   SCREEN SWITCHING
   ============================================= */
function showScreen(id) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}

function goSetup() {
  pauseTimer();
  showScreen('setup-screen');
}

/* =============================================
   VISIBILITY API — handle iPad sleep/bg
   ============================================= */
document.addEventListener('visibilitychange', () => {
  if (document.hidden) {
    // App going to background: record when we paused
    if (state.running) {
      state._bgAt = Date.now();
      saveToStorage();
    }
  } else {
    // App coming back to foreground
    if (state.running && state._bgAt) {
      const elapsed = Math.round((Date.now() - state._bgAt) / 1000);
      state._bgAt = null;

      // Apply elapsed time
      state.players.forEach(p => {
        if      (p.loc === 'field')   p.fieldSecs   += elapsed;
        else if (p.loc === 'bench')   p.benchSecs   += elapsed;
        else if (p.loc === 'injured') p.injuredSecs += elapsed;
      });

      state.halfSecsLeft  = Math.max(0, state.halfSecsLeft  - elapsed);
      state.blockSecsLeft = Math.max(0, state.blockSecsLeft - elapsed);

      // Check if multiple blocks expired while in background
      if (state.autoRotate && elapsed > state.blockLen * 60) {
        doAutoRotation();
      }

      if (state.halfSecsLeft === 0) {
        clearInterval(tickInterval);
        state.running = false;
        showToast(state.half < 2 ? 'Half Time!' : 'Full Time!');
      }

      state.lastTick = Date.now();
      renderAll();
      saveToStorage();
    }
  }
});

/* =============================================
   INIT — try to restore saved state on load
   ============================================= */
(function init() {
  const raw = localStorage.getItem(SAVE_KEY);
  if (raw) {
    try {
      const loaded = JSON.parse(raw);
      // Only auto-restore if there's a game in progress
      if (loaded.players && loaded.players.length > 0) {
        loaded.running  = false;
        loaded.lastTick = null;
        state = loaded;
        showScreen('game-screen');
        renderAll();
        showToast('Session restored');
        return;
      }
    } catch(e) { /* fall through to setup */ }
  }
  showScreen('setup-screen');
})();
</script>

</body>
</html>

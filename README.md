<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NEXUS-7 Cargo Sorting System</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;500;700;900&family=Rajdhani:wght@300;400;500;600&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
*{margin:0;padding:0;box-sizing:border-box}
:root{
  --cyan:#00f5ff;--blue:#0080ff;--purple:#8b00ff;--magenta:#ff00aa;
  --green:#00ff88;--orange:#ff6600;--red:#ff2244;--gold:#ffd700;
  --bg:#030610;--panel:#0a0f1e;--glass:rgba(0,200,255,0.04);
  --border:rgba(0,200,255,0.15);--text:#c8e8ff;--dim:#4a7a9b;
  --font-display:'Orbitron',monospace;--font-body:'Rajdhani',sans-serif;--font-mono:'Share Tech Mono',monospace;
}
html,body{height:100%;overflow:hidden;background:var(--bg);font-family:var(--font-body);color:var(--text)}

/* ── CANVAS STARFIELD ── */
#starfield{position:fixed;inset:0;z-index:0}

/* ── NEBULA LAYERS ── */
.nebula{position:fixed;border-radius:50%;filter:blur(80px);pointer-events:none;z-index:0;animation:nebula-drift 20s ease-in-out infinite alternate}
.n1{width:600px;height:400px;background:radial-gradient(ellipse,rgba(100,0,200,0.18),transparent 70%);top:-10%;left:-5%;animation-duration:18s}
.n2{width:500px;height:500px;background:radial-gradient(ellipse,rgba(0,100,255,0.14),transparent 70%);bottom:-15%;right:-5%;animation-duration:22s;animation-delay:-8s}
.n3{width:350px;height:250px;background:radial-gradient(ellipse,rgba(0,200,255,0.1),transparent 70%);top:40%;left:30%;animation-duration:15s;animation-delay:-4s}
@keyframes nebula-drift{0%{transform:translate(0,0) scale(1)}100%{transform:translate(30px,-20px) scale(1.1)}}

/* ── LAYOUT ── */
#app{position:relative;z-index:1;height:100vh;display:grid;grid-template-rows:auto 1fr auto;grid-template-columns:260px 1fr 240px;grid-template-areas:"hdr hdr hdr""sidebar stage panel""footer footer footer";gap:0}

/* ── HEADER ── */
header{grid-area:hdr;display:flex;align-items:center;gap:20px;padding:12px 24px;background:rgba(3,6,16,0.85);border-bottom:1px solid var(--border);backdrop-filter:blur(20px)}
.logo{display:flex;align-items:center;gap:12px}
.logo-icon{width:36px;height:36px;position:relative}
.logo-icon svg{width:100%;height:100%}
.logo-text{font-family:var(--font-display);font-size:15px;font-weight:700;letter-spacing:3px;color:var(--cyan);text-shadow:0 0 20px rgba(0,245,255,0.5)}
.logo-sub{font-size:10px;color:var(--dim);letter-spacing:2px;font-family:var(--font-mono)}
.header-center{flex:1;display:flex;justify-content:center;gap:8px}
.algo-btn{font-family:var(--font-display);font-size:9px;letter-spacing:1px;padding:6px 14px;background:transparent;border:1px solid var(--border);color:var(--dim);cursor:pointer;border-radius:2px;transition:all .2s;position:relative;overflow:hidden}
.algo-btn::before{content:'';position:absolute;inset:0;background:var(--cyan);opacity:0;transition:opacity .2s}
.algo-btn:hover{color:var(--cyan);border-color:var(--cyan);text-shadow:0 0 8px var(--cyan)}
.algo-btn.active{color:var(--bg);border-color:var(--cyan);background:var(--cyan);text-shadow:none;box-shadow:0 0 20px rgba(0,245,255,0.4)}
.header-right{display:flex;align-items:center;gap:12px}
.env-btn{font-size:9px;font-family:var(--font-mono);padding:4px 10px;border:1px solid rgba(255,255,255,0.1);background:transparent;color:var(--dim);cursor:pointer;border-radius:2px;transition:all .2s}
.env-btn.active,.env-btn:hover{border-color:var(--purple);color:var(--purple);box-shadow:0 0 10px rgba(139,0,255,0.3)}

/* ── SIDEBAR ── */
aside{grid-area:sidebar;background:rgba(3,6,16,0.7);border-right:1px solid var(--border);backdrop-filter:blur(20px);overflow-y:auto;padding:16px;display:flex;flex-direction:column;gap:14px}
.panel-card{background:var(--glass);border:1px solid var(--border);border-radius:4px;padding:14px;position:relative;overflow:hidden}
.panel-card::before{content:'';position:absolute;top:0;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,var(--cyan),transparent);opacity:0.5}
.panel-title{font-family:var(--font-display);font-size:9px;letter-spacing:3px;color:var(--cyan);margin-bottom:10px;display:flex;align-items:center;gap:6px}
.panel-title svg{width:10px;height:10px;opacity:0.8}
.stat-row{display:flex;justify-content:space-between;align-items:baseline;margin-bottom:6px}
.stat-label{font-size:11px;color:var(--dim);letter-spacing:1px}
.stat-value{font-family:var(--font-mono);font-size:18px;color:var(--cyan);text-shadow:0 0 10px rgba(0,245,255,0.4)}
.stat-value.swaps{color:var(--magenta);text-shadow:0 0 10px rgba(255,0,170,0.4)}
.stat-value.time{color:var(--green);text-shadow:0 0 10px rgba(0,255,136,0.4);font-size:12px}
.algo-desc{font-size:12px;line-height:1.7;color:var(--text);opacity:0.8}
.complexity-grid{display:grid;grid-template-columns:1fr 1fr;gap:6px;margin-top:8px}
.cx-item{background:rgba(0,0,0,0.3);border:1px solid rgba(255,255,255,0.05);border-radius:3px;padding:6px}
.cx-label{font-size:9px;color:var(--dim);letter-spacing:1px;margin-bottom:2px}
.cx-val{font-family:var(--font-mono);font-size:11px}
.cx-val.good{color:var(--green)}
.cx-val.ok{color:var(--gold)}
.cx-val.bad{color:var(--red)}
.step-log{font-family:var(--font-mono);font-size:10px;line-height:1.8;color:var(--dim);max-height:80px;overflow:hidden}
.step-log .current{color:var(--cyan);animation:blink .8s infinite}
@keyframes blink{50%{opacity:0.5}}
.ai-bubble{background:linear-gradient(135deg,rgba(139,0,255,0.1),rgba(0,128,255,0.08));border:1px solid rgba(139,0,255,0.3);border-radius:4px;padding:10px}
.ai-name{font-family:var(--font-display);font-size:8px;letter-spacing:2px;color:var(--purple);margin-bottom:6px}
.ai-text{font-size:11px;line-height:1.6;color:var(--text);opacity:0.85;font-style:italic}

/* ── MAIN STAGE ── */
.stage{grid-area:stage;display:flex;flex-direction:column;padding:16px 8px;overflow:hidden;position:relative}
.stage-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px;padding:0 8px}
.algo-display{font-family:var(--font-display);font-size:20px;font-weight:700;letter-spacing:4px;color:transparent;background:linear-gradient(90deg,var(--cyan),var(--blue),var(--purple));-webkit-background-clip:text;background-clip:text}
.mission-tag{font-family:var(--font-mono);font-size:10px;padding:3px 10px;border:1px solid var(--green);color:var(--green);border-radius:2px}
.visualizer{flex:1;display:flex;align-items:flex-end;justify-content:center;gap:3px;padding:0 8px 0;position:relative;min-height:0}
.bar-wrap{display:flex;flex-direction:column;align-items:center;flex:1;max-width:60px;position:relative;transition:transform .15s ease}
.bar{width:100%;border-radius:3px 3px 0 0;position:relative;overflow:hidden;transition:height .15s ease,background .1s ease;cursor:default;min-width:8px}
.bar::after{content:'';position:absolute;top:0;left:0;right:0;height:40%;background:linear-gradient(rgba(255,255,255,0.12),transparent);border-radius:3px 3px 0 0}
.bar-val{font-family:var(--font-mono);font-size:8px;color:rgba(255,255,255,0.6);text-align:center;margin-top:2px;position:absolute;bottom:-18px;width:100%;text-align:center}
.bar.comparing{animation:bar-compare .25s ease infinite alternate}
.bar.swapping{animation:bar-swap .2s ease}
.bar.sorted{animation:none}
.bar.pivot-bar{animation:bar-pivot .5s ease infinite alternate}
@keyframes bar-compare{0%{filter:brightness(1);box-shadow:none}100%{filter:brightness(2.2);box-shadow:0 0 18px var(--gold),0 -6px 30px var(--gold)}}
@keyframes bar-swap{0%{transform:scaleX(1)}40%{transform:scaleX(1.12) translateY(-6px);filter:brightness(2.5)}100%{transform:scaleX(1)}}
@keyframes bar-pivot{0%{filter:brightness(1.4);box-shadow:0 0 14px var(--purple)}100%{filter:brightness(1.8);box-shadow:0 0 28px var(--purple),0 -4px 20px var(--magenta)}}
.floor-line{height:2px;background:linear-gradient(90deg,transparent,var(--blue),var(--cyan),var(--blue),transparent);margin:0 8px;border-radius:1px;box-shadow:0 0 10px var(--cyan)}
.legend{display:flex;gap:16px;justify-content:center;margin-top:8px}
.leg-item{display:flex;align-items:center;gap:5px;font-size:10px;color:var(--dim)}
.leg-dot{width:8px;height:8px;border-radius:2px}

/* ── RIGHT PANEL ── */
.right-panel{grid-area:panel;background:rgba(3,6,16,0.7);border-left:1px solid var(--border);backdrop-filter:blur(20px);padding:16px;display:flex;flex-direction:column;gap:14px;overflow-y:auto}

/* ── FOOTER CONTROLS ── */
footer{grid-area:footer;background:rgba(3,6,16,0.9);border-top:1px solid var(--border);backdrop-filter:blur(20px);padding:12px 24px;display:flex;align-items:center;gap:16px;flex-wrap:wrap}
.ctrl-btn{font-family:var(--font-display);font-size:10px;letter-spacing:2px;padding:8px 20px;border:1px solid;cursor:pointer;border-radius:2px;transition:all .2s;position:relative;overflow:hidden;display:flex;align-items:center;gap:6px}
.ctrl-btn svg{width:12px;height:12px;flex-shrink:0}
.btn-start{border-color:var(--cyan);color:var(--cyan);background:rgba(0,245,255,0.05)}
.btn-start:hover,.btn-start.active-btn{background:rgba(0,245,255,0.15);box-shadow:0 0 20px rgba(0,245,255,0.3)}
.btn-pause{border-color:var(--gold);color:var(--gold);background:rgba(255,215,0,0.05)}
.btn-pause:hover{background:rgba(255,215,0,0.12);box-shadow:0 0 16px rgba(255,215,0,0.3)}
.btn-reset{border-color:var(--red);color:var(--red);background:rgba(255,34,68,0.05)}
.btn-reset:hover{background:rgba(255,34,68,0.12);box-shadow:0 0 16px rgba(255,34,68,0.3)}
.btn-random{border-color:var(--magenta);color:var(--magenta);background:rgba(255,0,170,0.05)}
.btn-random:hover{background:rgba(255,0,170,0.12);box-shadow:0 0 16px rgba(255,0,170,0.3)}
.slider-group{display:flex;align-items:center;gap:8px;margin-left:auto}
.slider-label{font-family:var(--font-mono);font-size:10px;color:var(--dim);white-space:nowrap}
input[type=range]{-webkit-appearance:none;appearance:none;width:90px;height:2px;border-radius:1px;background:rgba(255,255,255,0.1);outline:none}
input[type=range]::-webkit-slider-thumb{-webkit-appearance:none;appearance:none;width:12px;height:12px;border-radius:50%;background:var(--cyan);cursor:pointer;box-shadow:0 0 8px var(--cyan)}
.progress-wrap{flex:1;min-width:100px}
.progress-track{height:3px;background:rgba(255,255,255,0.06);border-radius:2px;overflow:hidden}
.progress-fill{height:100%;background:linear-gradient(90deg,var(--blue),var(--cyan));width:0%;transition:width .3s;box-shadow:0 0 8px var(--cyan)}

/* ── MISSION PANEL ── */
.mission-item{display:flex;justify-content:space-between;align-items:center;padding:6px 0;border-bottom:1px solid rgba(255,255,255,0.04)}
.mission-name{font-size:11px;color:var(--text)}
.mission-status{font-family:var(--font-mono);font-size:9px}
.mission-status.done{color:var(--green)}
.mission-status.pending{color:var(--dim)}
.mission-status.active{color:var(--cyan);animation:blink .8s infinite}
.score-big{font-family:var(--font-display);font-size:32px;font-weight:900;color:transparent;background:linear-gradient(135deg,var(--cyan),var(--purple));-webkit-background-clip:text;background-clip:text;text-align:center;text-shadow:none;margin:6px 0}
.rating-bar{height:4px;background:rgba(255,255,255,0.06);border-radius:2px;overflow:hidden;margin-top:4px}
.rating-fill{height:100%;border-radius:2px;transition:width .5s ease}

/* ── COMPLETION OVERLAY ── */
#complete-overlay{position:fixed;inset:0;background:rgba(3,6,16,0.85);z-index:1000;display:none;align-items:center;justify-content:center;backdrop-filter:blur(10px)}
#complete-overlay.show{display:flex}
.complete-box{text-align:center;max-width:420px;padding:40px}
.complete-box h2{font-family:var(--font-display);font-size:28px;font-weight:900;color:var(--cyan);letter-spacing:4px;text-shadow:0 0 40px rgba(0,245,255,0.7);margin-bottom:8px}
.complete-box p{color:var(--text);opacity:0.7;margin-bottom:24px}
.complete-stats{display:grid;grid-template-columns:1fr 1fr 1fr;gap:12px;margin-bottom:28px}
.cs-item{background:rgba(0,245,255,0.05);border:1px solid var(--border);border-radius:4px;padding:12px}
.cs-num{font-family:var(--font-display);font-size:22px;font-weight:700}
.cs-lbl{font-size:10px;color:var(--dim);letter-spacing:1px}

/* ── PARTICLE CANVAS ── */
#particles{position:fixed;inset:0;pointer-events:none;z-index:2}

/* ── FLOATING OBJECTS ── */
.float-obj{position:fixed;pointer-events:none;animation:float-anim var(--fd,12s) ease-in-out infinite alternate;opacity:0.15;z-index:0}
@keyframes float-anim{0%{transform:translate(0,0) rotate(0deg)}100%{transform:translate(var(--tx,20px),var(--ty,-30px)) rotate(var(--tr,45deg))}}

/* ── SCAN LINE ── */
.scanlines{position:fixed;inset:0;background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,0,0,0.04) 2px,rgba(0,0,0,0.04) 4px);pointer-events:none;z-index:3}

/* ── SCROLLBAR ── */
::-webkit-scrollbar{width:3px}::-webkit-scrollbar-track{background:transparent}::-webkit-scrollbar-thumb{background:rgba(0,245,255,0.2);border-radius:2px}
</style>
</head>
<body>

<canvas id="starfield"></canvas>
<div class="nebula n1"></div>
<div class="nebula n2"></div>
<div class="nebula n3"></div>
<div class="scanlines"></div>
<canvas id="particles"></canvas>

<div id="complete-overlay">
  <div class="complete-box">
    <h2>MISSION COMPLETE</h2>
    <p>Cargo sorted successfully. All systems nominal.</p>
    <div class="complete-stats">
      <div class="cs-item"><div class="cs-num" id="fin-comp" style="color:var(--cyan)">0</div><div class="cs-lbl">COMPARISONS</div></div>
      <div class="cs-item"><div class="cs-num" id="fin-swap" style="color:var(--magenta)">0</div><div class="cs-lbl">SWAPS</div></div>
      <div class="cs-item"><div class="cs-num" id="fin-time" style="color:var(--green)">0s</div><div class="cs-lbl">ELAPSED</div></div>
    </div>
    <button class="ctrl-btn btn-start" onclick="closeComplete()" style="margin:auto">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M4 4h16v16H4z"/></svg>
      NEW MISSION
    </button>
  </div>
</div>

<div id="app">
  <header>
    <div class="logo">
      <div class="logo-icon">
        <svg viewBox="0 0 36 36" fill="none">
          <circle cx="18" cy="18" r="16" stroke="rgba(0,245,255,0.5)" stroke-width="1.5"/>
          <circle cx="18" cy="18" r="9" stroke="rgba(0,245,255,0.8)" stroke-width="1"/>
          <circle cx="18" cy="18" r="3" fill="var(--cyan)"/>
          <line x1="18" y1="2" x2="18" y2="8" stroke="var(--cyan)" stroke-width="1.5"/>
          <line x1="18" y1="28" x2="18" y2="34" stroke="var(--cyan)" stroke-width="1.5"/>
          <line x1="2" y1="18" x2="8" y2="18" stroke="var(--cyan)" stroke-width="1.5"/>
          <line x1="28" y1="18" x2="34" y2="18" stroke="var(--cyan)" stroke-width="1.5"/>
        </svg>
      </div>
      <div>
        <div class="logo-text">NEXUS-7</div>
        <div class="logo-sub">CARGO SORTING SYSTEM</div>
      </div>
    </div>

    <div class="header-center">
      <button class="algo-btn active" onclick="setAlgo('bubble')">BUBBLE</button>
      <button class="algo-btn" onclick="setAlgo('selection')">SELECTION</button>
      <button class="algo-btn" onclick="setAlgo('insertion')">INSERTION</button>
      <button class="algo-btn" onclick="setAlgo('merge')">MERGE</button>
      <button class="algo-btn" onclick="setAlgo('quick')">QUICK</button>
    </div>

    <div class="header-right">
      <button class="env-btn active" onclick="setEnv('deep')" id="env-deep">DEEP SPACE</button>
      <button class="env-btn" onclick="setEnv('nebula')" id="env-nebula">NEBULA</button>
      <button class="env-btn" onclick="setEnv('asteroid')" id="env-asteroid">ASTEROID</button>
    </div>
  </header>

  <aside>
    <div class="panel-card">
      <div class="panel-title">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/></svg>
        MISSION STATS
      </div>
      <div class="stat-row"><span class="stat-label">COMPARISONS</span><span class="stat-value" id="stat-comp">0</span></div>
      <div class="stat-row"><span class="stat-label">SWAPS</span><span class="stat-value swaps" id="stat-swap">0</span></div>
      <div class="stat-row"><span class="stat-label">COMPLEXITY</span><span class="stat-value time" id="stat-time">O(n²)</span></div>
      <div class="stat-row"><span class="stat-label">STATUS</span><span class="stat-value" id="stat-status" style="font-size:10px;letter-spacing:1px">STANDBY</span></div>
      <div class="progress-wrap" style="margin-top:8px">
        <div class="rating-bar"><div class="rating-fill" id="efficiency-fill" style="width:0%;background:linear-gradient(90deg,var(--cyan),var(--green))"></div></div>
        <div style="display:flex;justify-content:space-between;margin-top:3px"><span style="font-size:9px;color:var(--dim)">PROGRESS</span><span style="font-size:9px;font-family:var(--font-mono);color:var(--cyan)" id="progress-pct">0%</span></div>
      </div>
    </div>

    <div class="panel-card">
      <div class="panel-title">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z"/><path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z"/></svg>
        ALGORITHM DATABASE
      </div>
      <div class="algo-desc" id="algo-desc">Bubble Sort repeatedly steps through the list, compares adjacent elements, and swaps them if they're in the wrong order. Cargo pods bubble upward like gas in liquid.</div>
      <div class="complexity-grid">
        <div class="cx-item"><div class="cx-label">BEST CASE</div><div class="cx-val good" id="cx-best">O(n)</div></div>
        <div class="cx-item"><div class="cx-label">AVERAGE</div><div class="cx-val ok" id="cx-avg">O(n²)</div></div>
        <div class="cx-item"><div class="cx-label">WORST CASE</div><div class="cx-val bad" id="cx-worst">O(n²)</div></div>
        <div class="cx-item"><div class="cx-label">SPACE</div><div class="cx-val good" id="cx-space">O(1)</div></div>
      </div>
    </div>

    <div class="panel-card">
      <div class="panel-title">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/></svg>
        STEP LOG
      </div>
      <div class="step-log" id="step-log">
        <div>▸ System initialized</div>
        <div>▸ Awaiting sort command</div>
        <div class="current">▸ _</div>
      </div>
    </div>

    <div class="ai-bubble">
      <div class="ai-name">◈ ARIA — AI SYSTEMS</div>
      <div class="ai-text" id="ai-text">All cargo pods loaded and ready. Select an algorithm to begin the sorting sequence, Commander.</div>
    </div>
  </aside>

  <div class="stage">
    <div class="stage-header">
      <div class="algo-display" id="algo-display">BUBBLE SORT</div>
      <div class="mission-tag" id="mission-tag">STANDBY</div>
    </div>
    <div class="visualizer" id="visualizer"></div>
    <div class="floor-line"></div>
    <div class="legend">
      <div class="leg-item"><div class="leg-dot" style="background:var(--gold);box-shadow:0 0 6px var(--gold)"></div>Comparing</div>
      <div class="leg-item"><div class="leg-dot" style="background:var(--magenta);box-shadow:0 0 6px var(--magenta)"></div>Swapping</div>
      <div class="leg-item"><div class="leg-dot" style="background:var(--green);box-shadow:0 0 6px var(--green)"></div>Sorted</div>
      <div class="leg-item"><div class="leg-dot" style="background:var(--purple);box-shadow:0 0 6px var(--purple)"></div>Pivot</div>
    </div>
  </div>

  <div class="right-panel">
    <div class="panel-card">
      <div class="panel-title">◈ SCORE</div>
      <div class="score-big" id="score">0</div>
      <div style="text-align:center;font-size:9px;color:var(--dim);letter-spacing:2px">EFFICIENCY RATING</div>
      <div class="rating-bar" style="margin-top:6px"><div class="rating-fill" id="score-fill" style="width:0%;background:linear-gradient(90deg,var(--purple),var(--cyan))"></div></div>
    </div>

    <div class="panel-card">
      <div class="panel-title">◈ MISSIONS</div>
      <div id="missions-list">
        <div class="mission-item"><span class="mission-name">Sort 10 pods</span><span class="mission-status active" id="m1">ACTIVE</span></div>
        <div class="mission-item"><span class="mission-name">Try 3 algorithms</span><span class="mission-status pending" id="m2">PENDING</span></div>
        <div class="mission-item"><span class="mission-name">Under 50 swaps</span><span class="mission-status pending" id="m3">PENDING</span></div>
        <div class="mission-item"><span class="mission-name">Use Quick Sort</span><span class="mission-status pending" id="m4">PENDING</span></div>
        <div class="mission-item" style="border:none"><span class="mission-name">Sort 30+ pods</span><span class="mission-status pending" id="m5">PENDING</span></div>
      </div>
    </div>

    <div class="panel-card">
      <div class="panel-title">◈ CARGO MANIFEST</div>
      <div id="cargo-info" style="font-family:var(--font-mono);font-size:10px;line-height:2;color:var(--dim)">
        <div>PODS: <span style="color:var(--cyan)" id="pod-count">20</span></div>
        <div>MIN VAL: <span style="color:var(--green)" id="min-val">—</span></div>
        <div>MAX VAL: <span style="color:var(--red)" id="max-val">—</span></div>
        <div>ALGORITHM: <span style="color:var(--purple)" id="algo-short">BUBBLE</span></div>
      </div>
    </div>

    <div class="panel-card">
      <div class="panel-title">◈ USE CASES</div>
      <div class="algo-desc" id="algo-usecase" style="font-size:11px">Good for small datasets and educational purposes. Simple implementation for basic cargo sorting tasks.</div>
    </div>
  </div>

  <footer>
    <button class="ctrl-btn btn-start" id="btn-start" onclick="handleStart()">
      <svg viewBox="0 0 24 24" fill="currentColor"><polygon points="5 3 19 12 5 21 5 3"/></svg>
      INITIATE
    </button>
    <button class="ctrl-btn btn-pause" id="btn-pause" onclick="handlePause()">
      <svg viewBox="0 0 24 24" fill="currentColor"><rect x="6" y="4" width="4" height="16"/><rect x="14" y="4" width="4" height="16"/></svg>
      PAUSE
    </button>
    <button class="ctrl-btn btn-reset" onclick="handleReset()">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="1 4 1 10 7 10"/><path d="M3.51 15a9 9 0 1 0 .49-4.5"/></svg>
      RESET
    </button>
    <button class="ctrl-btn btn-random" onclick="handleRandomize()">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><polyline points="16 3 21 3 21 8"/><line x1="4" y1="20" x2="21" y2="3"/><polyline points="21 16 21 21 16 21"/><line x1="15" y1="15" x2="21" y2="21"/></svg>
      RANDOMIZE
    </button>
    <div class="slider-group">
      <span class="slider-label">SPEED</span>
      <input type="range" min="1" max="5" value="3" step="1" id="speed-slider" oninput="updateSpeed(this.value)">
      <span class="slider-label" id="speed-label" style="color:var(--cyan)">×3</span>
    </div>
    <div class="slider-group">
      <span class="slider-label">PODS</span>
      <input type="range" min="5" max="60" value="20" step="1" id="size-slider" oninput="updateSize(this.value)">
      <span class="slider-label" id="size-label" style="color:var(--magenta)">20</span>
    </div>
    <div class="progress-wrap" style="min-width:120px">
      <div class="progress-track"><div class="progress-fill" id="progress-fill"></div></div>
      <div style="font-size:9px;color:var(--dim);margin-top:2px;font-family:var(--font-mono)" id="progress-label">SORTED 0 / 0</div>
    </div>
  </footer>
</div>

<script>
// ── STARFIELD ──
(function(){
  const c=document.getElementById('starfield'),ctx=c.getContext('2d');
  let stars=[];
  function init(){
    c.width=window.innerWidth;c.height=window.innerHeight;stars=[];
    for(let i=0;i<200;i++)stars.push({x:Math.random()*c.width,y:Math.random()*c.height,r:Math.random()*1.5+0.2,twinkle:Math.random()*Math.PI*2,speed:0.02+Math.random()*0.03});
  }
  function draw(){
    ctx.clearRect(0,0,c.width,c.height);
    stars.forEach(s=>{
      s.twinkle+=s.speed;
      const a=0.3+0.5*Math.abs(Math.sin(s.twinkle));
      ctx.beginPath();ctx.arc(s.x,s.y,s.r,0,Math.PI*2);
      ctx.fillStyle=`rgba(200,232,255,${a})`;ctx.fill();
    });
    requestAnimationFrame(draw);
  }
  init();draw();
  window.addEventListener('resize',init);
})();

// ── PARTICLES ──
(function(){
  const c=document.getElementById('particles'),ctx=c.getContext('2d');
  c.width=window.innerWidth;c.height=window.innerHeight;
  const particles=[];
  window.spawnParticles=function(x,y,color='#00f5ff',count=8){
    for(let i=0;i<count;i++){
      const angle=Math.random()*Math.PI*2,speed=1+Math.random()*3;
      particles.push({x,y,vx:Math.cos(angle)*speed,vy:Math.sin(angle)*speed-1,life:1,color,size:2+Math.random()*3});
    }
  };
  function animate(){
    ctx.clearRect(0,0,c.width,c.height);
    for(let i=particles.length-1;i>=0;i--){
      const p=particles[i];
      p.x+=p.vx;p.y+=p.vy;p.vy+=0.05;p.life-=0.025;
      if(p.life<=0){particles.splice(i,1);continue;}
      ctx.save();ctx.globalAlpha=p.life;
      ctx.shadowBlur=6;ctx.shadowColor=p.color;
      ctx.fillStyle=p.color;
      ctx.beginPath();ctx.arc(p.x,p.y,p.size*p.life,0,Math.PI*2);ctx.fill();
      ctx.restore();
    }
    requestAnimationFrame(animate);
  }
  animate();
  window.addEventListener('resize',()=>{c.width=window.innerWidth;c.height=window.innerHeight});
})();

// ── AUDIO ──
const AudioCtx=window.AudioContext||window.webkitAudioContext;
let audioCtx=null;
function getAudio(){if(!audioCtx)audioCtx=new AudioCtx();return audioCtx;}
function playTone(freq,dur,type='sine',vol=0.08){
  try{const a=getAudio();const o=a.createOscillator();const g=a.createGain();
  o.connect(g);g.connect(a.destination);o.type=type;o.frequency.value=freq;
  g.gain.setValueAtTime(vol,a.currentTime);g.gain.exponentialRampToValueAtTime(0.001,a.currentTime+dur);
  o.start(a.currentTime);o.stop(a.currentTime+dur);}catch(e){}
}
function sfxCompare(){playTone(440,0.05,'square',0.04);}
function sfxSwap(){playTone(660,0.08,'sawtooth',0.06);playTone(880,0.06,'sine',0.04);}
function sfxComplete(){
  [523,659,784,1047].forEach((f,i)=>setTimeout(()=>playTone(f,0.2,'sine',0.1),i*80));
}
function sfxClick(){playTone(330,0.04,'square',0.05);}

// ── ALGORITHM DATA ──
const algoData={
  bubble:{name:'BUBBLE SORT',display:'BUBBLE SORT',desc:'Bubble Sort repeatedly steps through the list, compares adjacent elements, and swaps them if they\'re in the wrong order. Cargo pods bubble upward like gas in liquid.',best:'O(n)',avg:'O(n²)',worst:'O(n²)',space:'O(1)',usecase:'Good for small datasets and educational purposes. Simple implementation for basic cargo sorting tasks.',ai:'Bubble Sort detected. Running adjacent comparisons. Heavy cargo may take many passes, Commander.'},
  selection:{name:'SELECTION SORT',display:'SELECTION SORT',desc:'Selection Sort finds the minimum element in the unsorted region and moves it to the beginning. Like picking the lightest cargo pod each pass.',best:'O(n²)',avg:'O(n²)',worst:'O(n²)',space:'O(1)',usecase:'Useful when memory writes are expensive. Makes the minimum number of swaps — ideal for fragile cargo.',ai:'Selection Sort engaged. Scanning for minimum-mass cargo pod each cycle. Efficient swaps, steady Commander.'},
  insertion:{name:'INSERTION SORT',display:'INSERTION SORT',desc:'Insertion Sort builds a sorted array one element at a time by inserting each element into its correct position, like sorting playing cards or cargo manifests by hand.',best:'O(n)',avg:'O(n²)',worst:'O(n²)',space:'O(1)',usecase:'Excellent for nearly-sorted data and small arrays. Online algorithm — sorts as cargo arrives in real time.',ai:'Insertion Sort protocol active. Building sorted manifest incrementally. Efficient for pre-sorted cargo runs.'},
  merge:{name:'MERGE SORT',display:'MERGE SORT',desc:'Merge Sort divides the array in half, recursively sorts both halves, then merges them into a sorted result. Divide and conquer — like splitting cargo into bays then recombining.',best:'O(n log n)',avg:'O(n log n)',worst:'O(n log n)',space:'O(n)',usecase:'Best for large datasets requiring guaranteed O(n log n). Stable sort — critical for preserving cargo load order.',ai:'Merge Sort initialized. Dividing cargo into sectors for parallel processing. Optimal for large manifests.'},
  quick:{name:'QUICK SORT',display:'QUICK SORT',desc:'Quick Sort selects a pivot element and partitions the array so smaller elements are left and larger are right, then recurses on each partition. Fast, in-place divide and conquer.',best:'O(n log n)',avg:'O(n log n)',worst:'O(n²)',space:'O(log n)',usecase:'Fastest general-purpose algorithm in practice. Industry standard for large unsorted datasets and real-time sorting.',ai:'Quick Sort protocol — MAXIMUM EFFICIENCY mode. Pivot selected. All systems go, Commander!'}
};

// ── STATE ──
let arr=[],n=20,algo='bubble',sorting=false,paused=false,sortedSet=new Set();
let comparisons=0,swaps=0,startTime=0,animSpeed=3,stepCount=0;
let algosUsed=new Set(),completedSorts=0;
const speedMap={1:500,2:250,3:100,4:30,5:5};
const colors={default:'var(--blue)',compare:'var(--gold)',swap:'var(--magenta)',sorted:'var(--green)',pivot:'var(--purple)'};

function delay(ms){return new Promise(r=>setTimeout(r,ms));}

function randArr(count){
  return Array.from({length:count},()=>Math.floor(Math.random()*95)+5);
}

function getBarColor(i,hi={}){
  if(sortedSet.has(i))return colors.sorted;
  if(hi[i]==='pivot')return colors.pivot;
  if(hi[i]==='swap')return colors.swap;
  if(hi[i]==='compare')return colors.compare;
  const t=i/arr.length;
  return `hsl(${200+t*80},80%,${40+t*20}%)`;
}

function render(hi={}){
  const vis=document.getElementById('visualizer');
  vis.innerHTML='';
  const maxV=Math.max(...arr,1);
  const maxH=vis.clientHeight-28;
  arr.forEach((v,i)=>{
    const wrap=document.createElement('div');wrap.className='bar-wrap';
    const bar=document.createElement('div');bar.className='bar';
    const h=Math.max((v/maxV)*maxH,4);
    bar.style.height=h+'px';
    const col=getBarColor(i,hi);
    bar.style.background=col;
    bar.style.boxShadow=`0 0 ${sortedSet.has(i)||hi[i]?12:4}px ${col}`;
    if(hi[i]==='compare')bar.classList.add('comparing');
    else if(hi[i]==='swap')bar.classList.add('swapping');
    else if(sortedSet.has(i))bar.classList.add('sorted');
    else if(hi[i]==='pivot')bar.classList.add('pivot-bar');
    if(n<=35){const lbl=document.createElement('div');lbl.className='bar-val';lbl.textContent=v;bar.appendChild(lbl);}
    wrap.appendChild(bar);vis.appendChild(wrap);
  });
  updateProgress();
  updateCargoInfo();
}

function updateStats(){
  document.getElementById('stat-comp').textContent=comparisons;
  document.getElementById('stat-swap').textContent=swaps;
  const sc=Math.max(0,Math.floor(10000-comparisons*2-swaps*5));
  document.getElementById('score').textContent=sc;
  const pct=Math.min(100,Math.floor(sc/100));
  document.getElementById('score-fill').style.width=pct+'%';
}

function updateProgress(){
  const pct=Math.floor((sortedSet.size/arr.length)*100);
  document.getElementById('progress-fill').style.width=pct+'%';
  document.getElementById('efficiency-fill').style.width=pct+'%';
  document.getElementById('progress-pct').textContent=pct+'%';
  document.getElementById('progress-label').textContent=`SORTED ${sortedSet.size} / ${arr.length}`;
}

function updateCargoInfo(){
  document.getElementById('pod-count').textContent=arr.length;
  document.getElementById('min-val').textContent=arr.length?Math.min(...arr):'—';
  document.getElementById('max-val').textContent=arr.length?Math.max(...arr):'—';
  document.getElementById('algo-short').textContent=algo.toUpperCase();
}

function setStep(msg){
  const log=document.getElementById('step-log');
  const lines=log.querySelectorAll('div');
  lines.forEach(l=>l.classList.remove('current'));
  if(lines.length>=4)lines[0].remove();
  const d=document.createElement('div');d.textContent='▸ '+msg;d.classList.add('current');log.appendChild(d);
}

function setAI(msg){document.getElementById('ai-text').textContent=msg;}
function setStatus(s,c='var(--cyan)'){const el=document.getElementById('stat-status');el.textContent=s;el.style.color=c;}
function setMissionTag(t,c='var(--cyan)'){const el=document.getElementById('mission-tag');el.textContent=t;el.style.borderColor=c;el.style.color=c;}

function getBarRect(i){
  const bars=document.getElementById('visualizer').querySelectorAll('.bar-wrap');
  if(bars[i])return bars[i].getBoundingClientRect();
  return null;
}

// ── ALGORITHMS ──
async function* bubbleSort(a){
  const total=a.length*(a.length-1)/2;let done=0;
  for(let i=0;i<a.length-1;i++){
    for(let j=0;j<a.length-i-1;j++){
      comparisons++;updateStats();
      setStep(`Comparing pod[${j}]=${a[j]} vs pod[${j+1}]=${a[j+1]}`);
      render({[j]:'compare',[j+1]:'compare'});sfxCompare();
      yield;
      if(a[j]>a[j+1]){
        swaps++;[a[j],a[j+1]]=[a[j+1],a[j]];
        setStep(`Swapping ${a[j+1]} ↔ ${a[j]}`);
        const r1=getBarRect(j),r2=getBarRect(j+1);
        if(r1&&r2)spawnParticles((r1.left+r1.right)/2,r1.top,'#ff00aa',6);
        render({[j]:'swap',[j+1]:'swap'});sfxSwap();
        updateStats();yield;
      }
      done++;
    }
    sortedSet.add(a.length-1-i);
    render();
  }
  sortedSet.add(0);render();
}

async function* selectionSort(a){
  for(let i=0;i<a.length-1;i++){
    let minIdx=i;
    setStep(`Pass ${i+1}: seeking minimum from index ${i}`);
    for(let j=i+1;j<a.length;j++){
      comparisons++;updateStats();
      setStep(`Is pod[${j}]=${a[j]} < min=${a[minIdx]}?`);
      render({[j]:'compare',[minIdx]:'pivot',[i]:'swap'});sfxCompare();
      yield;
      if(a[j]<a[minIdx])minIdx=j;
    }
    if(minIdx!==i){
      swaps++;[a[i],a[minIdx]]=[a[minIdx],a[i]];
      const r=getBarRect(i);if(r)spawnParticles((r.left+r.right)/2,r.top,'#ff00aa',6);
      setStep(`Placing minimum ${a[i]} at index ${i}`);
      render({[i]:'swap',[minIdx]:'swap'});sfxSwap();updateStats();yield;
    }
    sortedSet.add(i);render();
  }
  sortedSet.add(a.length-1);render();
}

async function* insertionSort(a){
  sortedSet.add(0);
  for(let i=1;i<a.length;i++){
    const key=a[i];let j=i-1;
    setStep(`Inserting pod[${i}]=${key} into sorted region`);
    render({[i]:'compare'});yield;
    while(j>=0&&a[j]>key){
      comparisons++;swaps++;updateStats();
      a[j+1]=a[j];
      setStep(`Shifting ${a[j]} right to make room`);
      const r=getBarRect(j);if(r)spawnParticles((r.left+r.right)/2,r.top,'#8b00ff',4);
      render({[j]:'swap',[j+1]:'swap'});sfxSwap();yield;
      j--;
    }
    a[j+1]=key;sortedSet.add(i);render({[j+1]:'sorted'});yield;
  }
}

async function* mergeSort(a){
  async function* mergeFn(l,m,r){
    const L=a.slice(l,m+1),R=a.slice(m+1,r+1);
    let i=0,j=0,k=l;
    while(i<L.length&&j<R.length){
      comparisons++;updateStats();
      setStep(`Merging: L[${i}]=${L[i]} vs R[${j}]=${R[j]}`);
      render({[l+i]:'compare',[m+1+j]:'compare'});sfxCompare();yield;
      if(L[i]<=R[j])a[k++]=L[i++];
      else{a[k++]=R[j++];swaps++;updateStats();}
      render();yield;
    }
    while(i<L.length){a[k++]=L[i++];render();yield;}
    while(j<R.length){a[k++]=R[j++];render();yield;}
    for(let x=l;x<=r;x++)sortedSet.add(x);
    render();
  }
  async function* ms(l,r){
    if(l<r){
      const m=Math.floor((l+r)/2);
      setStep(`Dividing sector [${l}..${r}] at midpoint ${m}`);
      yield* ms(l,m);yield* ms(m+1,r);yield* mergeFn(l,m,r);
    }
  }
  yield* ms(0,a.length-1);
  for(let i=0;i<a.length;i++)sortedSet.add(i);render();
}

async function* quickSort(a){
  async function* partition(l,r){
    const pivot=a[r];let i=l-1;
    setStep(`Pivot selected: ${pivot} at index ${r}`);
    render({[r]:'pivot'});yield;
    for(let j=l;j<r;j++){
      comparisons++;updateStats();
      render({[j]:'compare',[r]:'pivot'});sfxCompare();yield;
      if(a[j]<=pivot){
        i++;swaps++;[a[i],a[j]]=[a[j],a[i]];
        setStep(`Moving ${a[i]} left of pivot`);
        const rct=getBarRect(j);if(rct)spawnParticles((rct.left+rct.right)/2,rct.top,'#8b00ff',5);
        render({[i]:'swap',[j]:'swap',[r]:'pivot'});sfxSwap();updateStats();yield;
      }
    }
    swaps++;[a[i+1],a[r]]=[a[r],a[i+1]];updateStats();
    sortedSet.add(i+1);render({[i+1]:'pivot'});yield;
    return i+1;
  }
  async function* qs(l,r){
    if(l<r){
      let pi;const gen=partition(l,r);
      while(true){const {value,done}=await gen.next();if(done){pi=value;break;}yield;}
      yield* qs(l,pi-1);yield* qs(pi+1,r);
    }else if(l===r&&l>=0&&l<a.length)sortedSet.add(l);
  }
  yield* qs(0,a.length-1);
  for(let i=0;i<a.length;i++)sortedSet.add(i);render();
}

const algoFns={bubble:bubbleSort,selection:selectionSort,insertion:insertionSort,merge:mergeSort,quick:quickSort};

// ── CONTROL ──
let currentGen=null;
let animFrame=null;
let runInterval=null;

async function runSort(){
  const a=[...arr];
  const gen=algoFns[algo](a);
  sorting=true;paused=false;
  setStatus('SORTING','var(--cyan)');
  setMissionTag('SORTING','var(--cyan)');
  setAI(algoData[algo].ai);
  startTime=Date.now();
  while(sorting){
    if(paused){await delay(50);continue;}
    const {done}=await gen.next();
    arr=[...a];
    if(done){
      for(let i=0;i<arr.length;i++)sortedSet.add(i);
      render();
      sorting=false;
      const elapsed=((Date.now()-startTime)/1000).toFixed(1);
      setStatus('COMPLETE','var(--green)');
      setMissionTag('COMPLETE','var(--green)');
      setStep('Sorting complete! All pods arranged.');
      setAI('Mission accomplished, Commander. All cargo pods are perfectly sorted. Efficiency rating calculated.');
      sfxComplete();
      const vis=document.getElementById('visualizer');
      const rect=vis.getBoundingClientRect();
      for(let i=0;i<15;i++)setTimeout(()=>spawnParticles(rect.left+Math.random()*rect.width,rect.top+Math.random()*rect.height,'#00f5ff',8),i*60);
      completedSorts++;
      checkMissions();
      document.getElementById('btn-start').textContent='';
      document.getElementById('btn-start').innerHTML='<svg viewBox="0 0 24 24" fill="currentColor" style="width:12px;height:12px"><polygon points="5 3 19 12 5 21 5 3"/></svg> INITIATE';
      setTimeout(()=>{
        document.getElementById('fin-comp').textContent=comparisons;
        document.getElementById('fin-swap').textContent=swaps;
        document.getElementById('fin-time').textContent=elapsed+'s';
        document.getElementById('complete-overlay').classList.add('show');
      },800);
      break;
    }
    await delay(speedMap[animSpeed]);
  }
}

function handleStart(){
  sfxClick();
  if(!sorting){
    comparisons=0;swaps=0;sortedSet=new Set();updateStats();render();
    algosUsed.add(algo);
    runSort();
  }
}

function handlePause(){
  sfxClick();
  if(!sorting)return;
  paused=!paused;
  const btn=document.getElementById('btn-pause');
  if(paused){
    btn.innerHTML='<svg viewBox="0 0 24 24" fill="currentColor" style="width:12px;height:12px"><polygon points="5 3 19 12 5 21 5 3"/></svg> RESUME';
    setStatus('PAUSED','var(--gold)');setMissionTag('PAUSED','var(--gold)');
  }else{
    btn.innerHTML='<svg viewBox="0 0 24 24" fill="currentColor" style="width:12px;height:12px"><rect x="6" y="4" width="4" height="16"/><rect x="14" y="4" width="4" height="16"/></svg> PAUSE';
    setStatus('SORTING','var(--cyan)');setMissionTag('SORTING','var(--cyan)');
  }
}

function handleReset(){
  sfxClick();
  sorting=false;paused=false;comparisons=0;swaps=0;sortedSet=new Set();
  updateStats();render();
  setStatus('STANDBY','var(--cyan)');setMissionTag('STANDBY','var(--cyan)');
  setStep('System reset. Ready for new mission.');
  setAI('Cargo reset complete. Ready for your command, Commander.');
  document.getElementById('btn-pause').innerHTML='<svg viewBox="0 0 24 24" fill="currentColor" style="width:12px;height:12px"><rect x="6" y="4" width="4" height="16"/><rect x="14" y="4" width="4" height="16"/></svg> PAUSE';
  document.getElementById('complete-overlay').classList.remove('show');
}

function handleRandomize(){
  sfxClick();
  if(sorting)return;
  arr=randArr(n);sortedSet=new Set();comparisons=0;swaps=0;
  updateStats();render();
  setStep('New cargo manifest loaded.');
  setAI('Fresh cargo pods loaded into the sorting bay, Commander.');
  checkMissions();
}

function updateSpeed(v){animSpeed=parseInt(v);document.getElementById('speed-label').textContent='×'+v;}
function updateSize(v){
  n=parseInt(v);
  document.getElementById('size-label').textContent=v;
  if(!sorting){arr=randArr(n);sortedSet=new Set();render();checkMissions();}
}

function setAlgo(a){
  sfxClick();
  algo=a;
  if(sorting)return;
  const d=algoData[a];
  document.getElementById('algo-display').textContent=d.display;
  document.getElementById('algo-desc').textContent=d.desc;
  document.getElementById('cx-best').textContent=d.best;
  document.getElementById('cx-avg').textContent=d.avg;
  document.getElementById('cx-worst').textContent=d.worst;
  document.getElementById('cx-space').textContent=d.space;
  document.getElementById('algo-usecase').textContent=d.usecase;
  document.getElementById('stat-time').textContent=d.avg;
  document.getElementById('ai-text').textContent=d.ai;
  document.querySelectorAll('.algo-btn').forEach(b=>{b.classList.toggle('active',b.textContent===a.toUpperCase()||(a==='bubble'&&b.textContent==='BUBBLE')||(a==='selection'&&b.textContent==='SELECTION')||(a==='insertion'&&b.textContent==='INSERTION')||(a==='merge'&&b.textContent==='MERGE')||(a==='quick'&&b.textContent==='QUICK'));});
  algosUsed.add(a);checkMissions();
  updateCargoInfo();
}

const envSettings={
  deep:{n1:'rgba(100,0,200,0.18)',n2:'rgba(0,100,255,0.14)',n3:'rgba(0,200,255,0.1)'},
  nebula:{n1:'rgba(200,0,100,0.2)',n2:'rgba(100,0,200,0.18)',n3:'rgba(255,100,0,0.08)'},
  asteroid:{n1:'rgba(150,100,0,0.15)',n2:'rgba(100,50,0,0.12)',n3:'rgba(200,150,50,0.08)'}
};
function setEnv(e){
  sfxClick();
  document.querySelectorAll('.env-btn').forEach(b=>{b.classList.toggle('active',b.id==='env-'+e);});
  const s=envSettings[e];
  document.querySelector('.n1').style.background=`radial-gradient(ellipse,${s.n1},transparent 70%)`;
  document.querySelector('.n2').style.background=`radial-gradient(ellipse,${s.n2},transparent 70%)`;
  document.querySelector('.n3').style.background=`radial-gradient(ellipse,${s.n3},transparent 70%)`;
}

function checkMissions(){
  // M1: sort 10 pods
  if(n>=10&&completedSorts>0){document.getElementById('m1').textContent='DONE';document.getElementById('m1').className='mission-status done';}
  // M2: 3 algos
  if(algosUsed.size>=3){document.getElementById('m2').textContent='DONE';document.getElementById('m2').className='mission-status done';}
  // M3: under 50 swaps
  if(completedSorts>0&&swaps<50&&swaps>0){document.getElementById('m3').textContent='DONE';document.getElementById('m3').className='mission-status done';}
  // M4: quick sort
  if(algosUsed.has('quick')&&completedSorts>0){document.getElementById('m4').textContent='DONE';document.getElementById('m4').className='mission-status done';}
  // M5: 30+ pods
  if(n>=30&&completedSorts>0){document.getElementById('m5').textContent='DONE';document.getElementById('m5').className='mission-status done';}
}

function closeComplete(){
  sfxClick();
  document.getElementById('complete-overlay').classList.remove('show');
  handleReset();
  arr=randArr(n);render();
}

// ── INIT ──
arr=randArr(n);
setAlgo('bubble');
render();
setStep('System initialized');
setStep('Awaiting sort command');
</script>
</body>
</html>

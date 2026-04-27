<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Hogwarts Sorting Spells — Algorithm Simulator</title>
<link href="https://fonts.googleapis.com/css2?family=MedievalSharp&family=Cinzel:wght@400;600;700;900&family=Cinzel+Decorative:wght@400;700&family=IM+Fell+English:ital@0;1&family=Uncial+Antiqua&display=swap" rel="stylesheet">
<style>
:root {
  --gold: #f0c040;
  --gold-light: #ffe082;
  --gold-dark: #b8860b;
  --maroon: #6b0f1a;
  --maroon-dark: #3d0008;
  --maroon-light: #8b1a2a;
  --parchment: #f5e6c8;
  --parchment-dark: #d4b483;
  --ink: #1a0a00;
  --blue-deep: #0a0f2e;
  --blue-mid: #1a2050;
  --emerald: #1a4a2e;
  --emerald-light: #2d7a4f;
  --glow-gold: rgba(240,192,64,0.6);
  --glow-maroon: rgba(107,15,26,0.6);
  --glow-blue: rgba(100,149,237,0.5);
  --shadow: rgba(0,0,0,0.8);
}

* { margin: 0; padding: 0; box-sizing: border-box; }

@keyframes float { 0%,100%{transform:translateY(0) rotate(-2deg)} 50%{transform:translateY(-18px) rotate(2deg)} }
@keyframes floatB { 0%,100%{transform:translateY(0) rotate(3deg)} 50%{transform:translateY(-12px) rotate(-1deg)} }
@keyframes flicker { 0%,100%{opacity:1;transform:scaleY(1)} 20%{opacity:.9;transform:scaleY(1.05)} 50%{opacity:.95;transform:scaleY(.95)} 80%{opacity:1;transform:scaleY(1.02)} }
@keyframes candleGlow { 0%,100%{box-shadow:0 0 8px 2px rgba(255,200,50,.5),0 0 20px 6px rgba(255,150,0,.2)} 50%{box-shadow:0 0 14px 4px rgba(255,220,80,.7),0 0 30px 10px rgba(255,150,0,.35)} }
@keyframes sparkle { 0%{transform:scale(0) rotate(0deg);opacity:1} 100%{transform:scale(1.5) rotate(180deg);opacity:0} }
@keyframes pulse { 0%,100%{box-shadow:0 0 10px var(--glow-gold)} 50%{box-shadow:0 0 25px var(--glow-gold),0 0 50px rgba(240,192,64,.3)} }
@keyframes swapAnim { 0%{transform:translateY(0)} 30%{transform:translateY(-60px)} 70%{transform:translateY(-60px)} 100%{transform:translateY(0)} }
@keyframes compareAnim { 0%,100%{filter:brightness(1)} 50%{filter:brightness(1.8) saturate(2)} }
@keyframes celebrationBurst { 0%{transform:scale(0);opacity:1} 100%{transform:scale(4);opacity:0} }
@keyframes textGlow { 0%,100%{text-shadow:0 0 10px var(--gold)} 50%{text-shadow:0 0 25px var(--gold),0 0 50px rgba(240,192,64,.5)} }
@keyframes particleDrift { 0%{transform:translateY(0) translateX(0);opacity:.8} 100%{transform:translateY(-120px) translateX(var(--drift));opacity:0} }
@keyframes slideIn { from{transform:translateX(100%);opacity:0} to{transform:translateX(0);opacity:1} }
@keyframes shimmer { 0%{background-position:-200% 0} 100%{background-position:200% 0} }
@keyframes runeRotate { 0%{transform:rotate(0deg)} 100%{transform:rotate(360deg)} }
@keyframes hatBob { 0%,100%{transform:rotate(-5deg) translateY(0)} 50%{transform:rotate(5deg) translateY(-8px)} }
@keyframes orbPulse { 0%,100%{box-shadow:0 0 15px var(--color),0 0 30px rgba(255,255,255,.1)} 50%{box-shadow:0 0 30px var(--color),0 0 60px rgba(255,255,255,.2)} }
@keyframes starTwinkle { 0%,100%{opacity:.2;transform:scale(.8)} 50%{opacity:1;transform:scale(1.2)} }

body {
  min-height: 100vh;
  font-family: 'IM Fell English', Georgia, serif;
  background: var(--blue-deep);
  overflow-x: hidden;
  color: var(--parchment);
  cursor: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='24' height='24'%3E%3Cpath d='M4 20 L20 4' stroke='%23f0c040' stroke-width='2'/%3E%3Ccircle cx='20' cy='4' r='3' fill='%23f0c040'/%3E%3C/svg%3E") 4 20, auto;
}

/* ═══ BACKGROUND ═══ */
.bg-scene {
  position: fixed; inset: 0; z-index: 0;
  background:
    radial-gradient(ellipse 80% 50% at 50% 100%, #0a0f2e 0%, transparent 70%),
    linear-gradient(180deg, #060b20 0%, #0d1535 30%, #1a0a0f 70%, #0d0008 100%);
}

.bg-stars {
  position: fixed; inset: 0; z-index: 1;
}
.star {
  position: absolute; border-radius: 50%; background: #fff;
  animation: starTwinkle var(--dur, 3s) ease-in-out infinite;
  animation-delay: var(--delay, 0s);
}

.bg-castle {
  position: fixed; bottom: 0; left: 0; right: 0; z-index: 2;
  height: 220px;
  background:
    linear-gradient(transparent 0%, rgba(5,8,20,.95) 100%);
}
.castle-silhouette {
  position: absolute; bottom: 0; width: 100%; height: 200px;
  background: #060a1a;
  clip-path: polygon(
    0% 100%, 0% 70%, 2% 70%, 2% 40%, 3% 40%, 3% 30%, 4% 30%, 4% 40%, 5% 40%, 5% 70%,
    6% 70%, 6% 50%, 7% 50%, 7% 20%, 7.5% 20%, 7.5% 10%, 8% 10%, 8% 20%, 8.5% 20%, 8.5% 50%,
    9% 50%, 9% 70%, 10% 70%, 10% 55%, 11% 55%, 11% 25%, 11.5% 25%, 11.5% 15%, 12% 15%, 12% 25%,
    12.5% 25%, 12.5% 55%, 13% 55%, 14% 55%, 14% 70%,
    20% 70%, 20% 45%, 22% 45%, 22% 70%,
    30% 70%, 30% 30%, 31% 30%, 31% 15%, 31.5% 5%, 32% 15%, 32% 30%, 33% 30%, 33% 70%,
    40% 70%, 40% 50%, 41% 50%, 41% 70%,
    50% 70%, 50% 40%, 51% 40%, 51% 20%, 51.5% 10%, 52% 20%, 52% 40%, 53% 40%, 53% 70%,
    58% 70%, 58% 50%, 59% 50%, 59% 70%,
    67% 70%, 67% 30%, 68% 30%, 68% 15%, 68.5% 5%, 69% 15%, 69% 30%, 70% 30%, 70% 70%,
    78% 70%, 78% 45%, 80% 45%, 80% 70%,
    85% 70%, 85% 55%, 86.5% 55%, 86.5% 25%, 87% 15%, 87.5% 25%, 87.5% 55%, 89% 55%, 89% 70%,
    91% 70%, 91% 50%, 92% 50%, 92% 20%, 92.5% 10%, 93% 20%, 93% 50%, 94% 50%, 94% 70%,
    95% 70%, 95% 40%, 96% 40%, 96% 30%, 97% 30%, 97% 40%, 98% 40%, 98% 70%,
    100% 70%, 100% 100%
  );
}
.castle-windows {
  position: absolute; bottom: 0; width: 100%; height: 200px;
}
.castle-window {
  position: absolute; background: rgba(240,192,64,.15);
  border-radius: 50% 50% 0 0; animation: flicker 3s ease-in-out infinite;
}

/* ═══ FLOATING CANDLES ═══ */
.candles-container { position: fixed; inset: 0; z-index: 3; pointer-events: none; }
.candle-group {
  position: absolute;
  animation: float var(--dur, 5s) ease-in-out infinite;
  animation-delay: var(--delay, 0s);
}
.candle {
  width: 8px; height: 40px;
  background: linear-gradient(180deg, #fff8dc 0%, #f5deb3 60%, #daa520 100%);
  border-radius: 3px 3px 1px 1px;
  position: relative; margin: 0 auto;
}
.candle::after {
  content: ''; position: absolute; top: -12px; left: 50%; transform: translateX(-50%);
  width: 6px; height: 14px;
  background: linear-gradient(180deg, #fff 0%, #ffe066 30%, #ff8c00 60%, transparent 100%);
  border-radius: 50% 50% 30% 30%;
  animation: flicker 1.5s ease-in-out infinite;
  filter: blur(.5px);
}
.candle-glow {
  position: absolute; top: -18px; left: 50%; transform: translateX(-50%);
  width: 20px; height: 20px; border-radius: 50%;
  background: radial-gradient(circle, rgba(255,200,50,.8) 0%, transparent 70%);
  animation: candleGlow 1.5s ease-in-out infinite;
}
.candle-drip {
  position: absolute; bottom: -4px; left: 2px;
  width: 4px; height: 8px;
  background: rgba(255,248,220,.7); border-radius: 0 0 3px 3px;
}

/* ═══ PARTICLES ═══ */
.particles-container { position: fixed; inset: 0; z-index: 4; pointer-events: none; overflow: hidden; }
.magic-particle {
  position: absolute; border-radius: 50%;
  animation: particleDrift var(--dur, 4s) ease-out infinite;
  animation-delay: var(--delay, 0s);
}

/* ═══ LAYOUT ═══ */
.app-wrapper {
  position: relative; z-index: 10;
  min-height: 100vh;
  display: flex; flex-direction: column;
  padding: 20px;
  gap: 16px;
}

/* ═══ HEADER ═══ */
header {
  text-align: center; padding: 24px 20px 16px;
  position: relative;
}
.header-rune-left, .header-rune-right {
  position: absolute; top: 50%; transform: translateY(-50%);
  font-size: 2rem; color: var(--gold); opacity: .4;
  animation: runeRotate 8s linear infinite;
}
.header-rune-left { left: 20px; }
.header-rune-right { right: 20px; animation-direction: reverse; }

.main-title {
  font-family: 'Cinzel Decorative', serif;
  font-size: clamp(1.4rem, 4vw, 2.8rem);
  color: var(--gold);
  animation: textGlow 3s ease-in-out infinite;
  line-height: 1.2;
  letter-spacing: .05em;
}
.sub-title {
  font-family: 'Cinzel', serif;
  font-size: clamp(.7rem, 1.8vw, 1rem);
  color: var(--parchment-dark);
  letter-spacing: .2em;
  margin-top: 4px;
  text-transform: uppercase;
}
.golden-divider {
  width: 60%; margin: 12px auto 0;
  height: 2px;
  background: linear-gradient(90deg, transparent, var(--gold), var(--gold-dark), var(--gold), transparent);
  position: relative;
}
.golden-divider::before, .golden-divider::after {
  content: '✦'; position: absolute; top: 50%; transform: translateY(-50%);
  color: var(--gold); font-size: .8rem;
}
.golden-divider::before { left: -12px; }
.golden-divider::after { right: -12px; }

/* ═══ HOUSE SELECTOR ═══ */
.house-selector {
  display: flex; justify-content: center; gap: 12px; flex-wrap: wrap;
  margin: 8px 0;
}
.house-btn {
  padding: 6px 18px;
  border: 2px solid;
  border-radius: 20px;
  font-family: 'Cinzel', serif;
  font-size: .75rem;
  cursor: pointer;
  transition: all .3s;
  background: rgba(0,0,0,.4);
  letter-spacing: .1em;
}
.house-btn[data-house="gryffindor"] { border-color: #740001; color: #ffd700; }
.house-btn[data-house="ravenclaw"]  { border-color: #0e1a40; color: #946b2d; }
.house-btn[data-house="slytherin"]  { border-color: #1a472a; color: #aaaaaa; }
.house-btn[data-house="hufflepuff"] { border-color: #ecb939; color: #372e29; }
.house-btn.active[data-house="gryffindor"] { background: linear-gradient(135deg,#740001,#ae0001); box-shadow: 0 0 20px rgba(174,0,1,.5); }
.house-btn.active[data-house="ravenclaw"]  { background: linear-gradient(135deg,#0e1a40,#222f5b); box-shadow: 0 0 20px rgba(14,26,64,.8); }
.house-btn.active[data-house="slytherin"]  { background: linear-gradient(135deg,#1a472a,#2a623d); box-shadow: 0 0 20px rgba(26,71,42,.8); }
.house-btn.active[data-house="hufflepuff"] { background: linear-gradient(135deg,#ecb939,#f0c75e); color: #372e29; box-shadow: 0 0 20px rgba(236,185,57,.5); }
.house-btn:hover { transform: scale(1.05); }

/* ═══ MAIN GRID ═══ */
.main-grid {
  display: grid;
  grid-template-columns: 280px 1fr 280px;
  gap: 16px;
  flex: 1;
}
@media (max-width: 1100px) {
  .main-grid { grid-template-columns: 1fr; }
}

/* ═══ PARCHMENT PANEL ═══ */
.parchment-panel {
  background:
    linear-gradient(135deg, rgba(30,15,5,.92) 0%, rgba(20,10,5,.95) 100%);
  border: 2px solid var(--gold-dark);
  border-radius: 4px;
  padding: 20px;
  position: relative;
  box-shadow:
    inset 0 0 30px rgba(0,0,0,.5),
    0 0 20px rgba(0,0,0,.8),
    0 0 0 1px rgba(240,192,64,.1);
}
.parchment-panel::before {
  content: ''; position: absolute; inset: 4px;
  border: 1px solid rgba(240,192,64,.2);
  border-radius: 2px; pointer-events: none;
}
.panel-corner {
  position: absolute; color: var(--gold); font-size: 1rem; opacity: .6;
  line-height: 1;
}
.panel-corner.tl { top: 6px; left: 8px; }
.panel-corner.tr { top: 6px; right: 8px; }
.panel-corner.bl { bottom: 6px; left: 8px; }
.panel-corner.br { bottom: 6px; right: 8px; }

.panel-title {
  font-family: 'Cinzel', serif;
  font-size: .85rem;
  color: var(--gold);
  text-align: center;
  letter-spacing: .15em;
  text-transform: uppercase;
  margin-bottom: 14px;
  padding-bottom: 8px;
  border-bottom: 1px solid rgba(240,192,64,.3);
}

/* ═══ LEFT PANEL — CONTROLS ═══ */
.control-section { margin-bottom: 16px; }
.control-label {
  font-family: 'Cinzel', serif;
  font-size: .7rem;
  color: var(--parchment-dark);
  letter-spacing: .12em;
  text-transform: uppercase;
  margin-bottom: 6px;
  display: flex; align-items: center; gap: 6px;
}
.control-label::before { content: '✦'; color: var(--gold); font-size: .5rem; }

.algo-grid {
  display: grid; grid-template-columns: 1fr 1fr; gap: 6px;
}
.algo-btn {
  padding: 8px 6px;
  background: rgba(240,192,64,.05);
  border: 1px solid rgba(240,192,64,.25);
  border-radius: 3px;
  color: var(--parchment-dark);
  font-family: 'IM Fell English', serif;
  font-size: .75rem;
  cursor: pointer;
  transition: all .25s;
  text-align: center;
  position: relative; overflow: hidden;
}
.algo-btn:hover { border-color: var(--gold); color: var(--gold); background: rgba(240,192,64,.1); }
.algo-btn.active {
  background: linear-gradient(135deg, rgba(240,192,64,.2), rgba(240,192,64,.05));
  border-color: var(--gold);
  color: var(--gold);
  box-shadow: 0 0 12px rgba(240,192,64,.3);
}
.algo-btn .algo-icon { display: block; font-size: 1.2rem; margin-bottom: 2px; }
.algo-btn .algo-name { font-size: .65rem; }

.magic-range {
  -webkit-appearance: none; width: 100%; height: 4px;
  background: linear-gradient(90deg, var(--gold-dark), var(--gold));
  border-radius: 2px; outline: none; cursor: pointer;
}
.magic-range::-webkit-slider-thumb {
  -webkit-appearance: none; width: 16px; height: 16px;
  border-radius: 50%; background: var(--gold);
  box-shadow: 0 0 8px var(--glow-gold); cursor: pointer;
}

.range-labels { display: flex; justify-content: space-between; font-size: .65rem; color: var(--parchment-dark); margin-top: 4px; }

.action-btn {
  width: 100%; padding: 10px;
  border: none; border-radius: 3px;
  font-family: 'Cinzel', serif;
  font-size: .8rem;
  letter-spacing: .1em;
  cursor: pointer;
  transition: all .25s;
  position: relative; overflow: hidden;
  display: flex; align-items: center; justify-content: center; gap: 6px;
}
.action-btn::after {
  content: ''; position: absolute; inset: 0;
  background: linear-gradient(45deg, transparent 30%, rgba(255,255,255,.1) 50%, transparent 70%);
  transform: translateX(-100%); transition: transform .4s;
}
.action-btn:hover::after { transform: translateX(100%); }
.action-btn:hover { transform: translateY(-2px); }
.action-btn:active { transform: translateY(0); }

.btn-cast {
  background: linear-gradient(135deg, #5a0010, #8b0020, #5a0010);
  color: var(--gold); border: 1px solid var(--gold-dark);
  box-shadow: 0 0 15px rgba(139,0,32,.4);
  margin-bottom: 6px;
}
.btn-cast:hover { box-shadow: 0 0 25px rgba(240,192,64,.4); }
.btn-pause {
  background: linear-gradient(135deg, #1a2050, #2a3080, #1a2050);
  color: var(--gold-light); border: 1px solid rgba(100,149,237,.4);
  margin-bottom: 6px;
}
.btn-reset {
  background: linear-gradient(135deg, #1a4a2e, #2d7a4f, #1a4a2e);
  color: var(--gold-light); border: 1px solid rgba(45,122,79,.5);
  margin-bottom: 6px;
}
.btn-random {
  background: linear-gradient(135deg, #3a2000, #6b3d00, #3a2000);
  color: var(--gold); border: 1px solid rgba(240,192,64,.3);
}
.btn-hat {
  background: linear-gradient(135deg, #2a0040, #4a0070, #2a0040);
  color: var(--gold-light); border: 1px solid rgba(150,100,240,.4);
  margin-top: 6px; width: 100%; font-size: .75rem;
}
.btn-hat:hover { box-shadow: 0 0 20px rgba(150,100,240,.5); }

/* ═══ STATS ═══ */
.stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-top: 12px; }
.stat-box {
  background: rgba(0,0,0,.4); border: 1px solid rgba(240,192,64,.2);
  border-radius: 3px; padding: 8px; text-align: center;
}
.stat-value {
  font-family: 'Cinzel', serif;
  font-size: 1.3rem; color: var(--gold);
  line-height: 1;
}
.stat-label { font-size: .6rem; color: var(--parchment-dark); letter-spacing: .1em; margin-top: 2px; }

/* ═══ CENTER — VISUALIZATION ═══ */
.viz-panel {
  display: flex; flex-direction: column; gap: 12px;
}

.sorting-stage {
  background: linear-gradient(180deg, rgba(10,5,0,.95) 0%, rgba(5,3,0,.98) 100%);
  border: 2px solid var(--gold-dark);
  border-radius: 4px;
  position: relative;
  overflow: hidden;
  flex: 1;
  min-height: 320px;
}
.sorting-stage::before {
  content: ''; position: absolute; inset: 0;
  background:
    linear-gradient(transparent 60%, rgba(240,192,64,.03) 100%),
    repeating-linear-gradient(0deg, transparent, transparent 39px, rgba(240,192,64,.05) 40px);
  pointer-events: none;
}

/* Stage decorations */
.stage-torches {
  position: absolute; top: 0; left: 0; right: 0;
  display: flex; justify-content: space-between; padding: 0 20px;
  pointer-events: none;
}
.stage-torch {
  width: 12px; display: flex; flex-direction: column; align-items: center;
}
.torch-flame {
  width: 10px; height: 18px;
  background: linear-gradient(180deg, #fff8dc 0%, #ffa500 40%, transparent 100%);
  border-radius: 50% 50% 30% 30%;
  animation: flicker 1.2s ease-in-out infinite;
  filter: blur(.5px);
}
.torch-body { width: 6px; height: 20px; background: linear-gradient(180deg, #8b4513, #5c2d0a); border-radius: 2px; }

.bars-container {
  position: absolute; bottom: 20px; left: 0; right: 0;
  display: flex; align-items: flex-end; justify-content: center;
  gap: 3px; padding: 0 20px;
  height: calc(100% - 60px);
}

.magic-bar {
  flex: 1; max-width: 60px; min-width: 8px;
  border-radius: 4px 4px 0 0;
  position: relative;
  transition: height .3s ease, background .3s ease;
  cursor: pointer;
  display: flex; align-items: flex-start; justify-content: center;
  padding-top: 4px;
}
.magic-bar.comparing {
  animation: compareAnim .4s ease-in-out;
  z-index: 5;
}
.magic-bar.swapping { animation: swapAnim .5s ease-in-out; z-index: 10; }
.magic-bar.sorted {
  animation: orbPulse 2s ease-in-out infinite;
}
.bar-label {
  font-family: 'Cinzel', serif;
  font-size: .55rem; color: rgba(255,255,255,.8);
  writing-mode: horizontal-tb;
  margin-top: 2px;
}
.bar-icon {
  position: absolute; top: -22px; left: 50%; transform: translateX(-50%);
  font-size: 1rem; filter: drop-shadow(0 0 4px var(--gold));
}

.stage-shelf {
  position: absolute; bottom: 16px; left: 10px; right: 10px;
  height: 4px;
  background: linear-gradient(90deg, transparent, var(--gold-dark), var(--gold), var(--gold-dark), transparent);
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(240,192,64,.3);
}

/* Step description */
.step-description {
  position: absolute; top: 8px; left: 50%; transform: translateX(-50%);
  font-family: 'IM Fell English', serif; font-style: italic;
  font-size: .75rem; color: var(--gold-light);
  background: rgba(0,0,0,.7); border: 1px solid rgba(240,192,64,.3);
  padding: 4px 12px; border-radius: 20px;
  white-space: nowrap; max-width: 90%;
  overflow: hidden; text-overflow: ellipsis;
  z-index: 20;
}

/* Completion effect */
.completion-burst {
  position: absolute; inset: 0; z-index: 50;
  display: none; align-items: center; justify-content: center;
  flex-direction: column; gap: 12px;
  background: rgba(0,0,0,.7);
}
.completion-burst.active { display: flex; }
.burst-circle {
  width: 100px; height: 100px; border-radius: 50%;
  background: radial-gradient(circle, var(--gold) 0%, transparent 70%);
  animation: celebrationBurst 1s ease-out forwards;
}
.burst-text {
  font-family: 'Cinzel Decorative', serif;
  font-size: 1.5rem; color: var(--gold);
  animation: textGlow 1s ease-in-out infinite;
  text-align: center;
}

/* ═══ SPARKLE OVERLAY ═══ */
.sparkle-container {
  position: fixed; inset: 0; z-index: 100; pointer-events: none;
}
.sparkle {
  position: absolute; pointer-events: none;
  animation: sparkle .6s ease-out forwards;
}

/* ═══ RIGHT PANEL — SPELLBOOK ═══ */
.spellbook-content { font-size: .8rem; line-height: 1.6; }
.spell-name {
  font-family: 'Cinzel', serif;
  font-size: 1rem; color: var(--gold);
  text-align: center; margin-bottom: 8px;
  font-style: italic;
}
.spell-desc { color: var(--parchment-dark); margin-bottom: 12px; font-style: italic; }
.complexity-table { width: 100%; border-collapse: collapse; margin-bottom: 12px; }
.complexity-table th {
  font-family: 'Cinzel', serif; font-size: .65rem;
  color: var(--gold); letter-spacing: .1em;
  border-bottom: 1px solid rgba(240,192,64,.3);
  padding: 4px 6px; text-align: left;
}
.complexity-table td {
  padding: 4px 6px; font-size: .7rem; color: var(--parchment-dark);
  border-bottom: 1px solid rgba(240,192,64,.1);
}
.complexity-table td.good { color: #4caf50; }
.complexity-table td.mid  { color: var(--gold); }
.complexity-table td.bad  { color: #ef5350; }

.spell-steps { list-style: none; }
.spell-steps li {
  display: flex; gap: 8px; margin-bottom: 6px;
  font-size: .72rem; color: var(--parchment-dark);
}
.spell-steps li::before { content: '⚡'; flex-shrink: 0; }
.step-highlight { background: rgba(240,192,64,.15); border-radius: 2px; padding: 0 3px; color: var(--gold-light); }

/* Wizard level */
.wizard-level {
  margin-top: 14px; padding-top: 10px;
  border-top: 1px solid rgba(240,192,64,.2);
  text-align: center;
}
.level-label { font-family: 'Cinzel', serif; font-size: .7rem; color: var(--gold); margin-bottom: 6px; }
.level-bar-bg {
  height: 8px; background: rgba(0,0,0,.5);
  border: 1px solid rgba(240,192,64,.3); border-radius: 4px; overflow: hidden;
}
.level-bar-fill {
  height: 100%; border-radius: 4px;
  background: linear-gradient(90deg, var(--gold-dark), var(--gold));
  transition: width 1s ease;
  box-shadow: 0 0 8px var(--glow-gold);
}
.level-text { font-size: .65rem; color: var(--parchment-dark); margin-top: 4px; }

/* ═══ OWL ASSISTANT ═══ */
.owl-panel {
  display: flex; align-items: flex-start; gap: 10px;
  background: rgba(240,192,64,.05); border: 1px solid rgba(240,192,64,.2);
  border-radius: 4px; padding: 10px; margin-top: 12px;
}
.owl-icon { font-size: 1.8rem; animation: hatBob 3s ease-in-out infinite; flex-shrink: 0; }
.owl-message { font-size: .72rem; color: var(--parchment-dark); font-style: italic; line-height: 1.5; }

/* ═══ SORTING HAT ═══ */
.hat-animation {
  text-align: center; font-size: 4rem;
  animation: hatBob 1s ease-in-out infinite;
  display: none; margin: 10px 0;
}
.hat-animation.visible { display: block; }

/* ═══ STATUS BAR ═══ */
.status-bar {
  background: rgba(0,0,0,.6); border: 1px solid rgba(240,192,64,.2);
  border-radius: 3px; padding: 8px 16px;
  display: flex; justify-content: center; align-items: center; gap: 20px;
  flex-wrap: wrap;
}
.status-item { font-family: 'Cinzel', serif; font-size: .7rem; color: var(--parchment-dark); display: flex; align-items: center; gap: 5px; }
.status-value { color: var(--gold); font-size: .85rem; }

/* ═══ WAND CURSOR TRAIL ═══ */
.wand-trail {
  position: fixed; pointer-events: none; z-index: 9999;
  width: 6px; height: 6px; border-radius: 50%;
  background: var(--gold); opacity: .8;
  transform: translate(-50%, -50%);
  transition: opacity .5s;
}

/* ═══ RESPONSIVE ═══ */
@media (max-width: 1100px) {
  .main-grid { grid-template-columns: 1fr; }
  .sorting-stage { min-height: 280px; }
}
@media (max-width: 600px) {
  .app-wrapper { padding: 10px; }
  .algo-grid { grid-template-columns: 1fr 1fr 1fr; }
}

/* ═══ SCROLLBAR ═══ */
::-webkit-scrollbar { width: 6px; }
::-webkit-scrollbar-track { background: var(--maroon-dark); }
::-webkit-scrollbar-thumb { background: var(--gold-dark); border-radius: 3px; }
</style>
</head>
<body>

<!-- Background -->
<div class="bg-scene"></div>
<div class="bg-stars" id="bgStars"></div>
<div class="bg-castle">
  <div class="castle-silhouette"></div>
  <div class="castle-windows" id="castleWindows"></div>
</div>
<div class="candles-container" id="candlesContainer"></div>
<div class="particles-container" id="particlesContainer"></div>
<div class="sparkle-container" id="sparkleContainer"></div>

<!-- Wand trail -->
<div class="wand-trail" id="wandTrail"></div>

<div class="app-wrapper">

  <!-- Header -->
  <header>
    <div class="header-rune-left">⚝</div>
    <div class="header-rune-right">⚝</div>
    <div class="main-title">✦ Hogwarts Sorting Spells ✦</div>
    <div class="sub-title">The Enchanted Algorithm Classroom</div>
    <div class="golden-divider"></div>
  </header>

  <!-- House Selector -->
  <div class="house-selector">
    <button class="house-btn active" data-house="gryffindor" onclick="selectHouse('gryffindor')">🦁 Gryffindor</button>
    <button class="house-btn" data-house="ravenclaw" onclick="selectHouse('ravenclaw')">🦅 Ravenclaw</button>
    <button class="house-btn" data-house="slytherin" onclick="selectHouse('slytherin')">🐍 Slytherin</button>
    <button class="house-btn" data-house="hufflepuff" onclick="selectHouse('hufflepuff')">🦡 Hufflepuff</button>
  </div>

  <!-- Main Grid -->
  <div class="main-grid">

    <!-- LEFT: Controls -->
    <div class="parchment-panel">
      <span class="panel-corner tl">✦</span>
      <span class="panel-corner tr">✦</span>
      <span class="panel-corner bl">✦</span>
      <span class="panel-corner br">✦</span>
      <div class="panel-title">📜 Spell Selection</div>

      <div class="control-section">
        <div class="control-label">Choose Your Sorting Spell</div>
        <div class="algo-grid">
          <button class="algo-btn active" data-algo="bubble" onclick="selectAlgo('bubble')">
            <span class="algo-icon">🫧</span><span class="algo-name">Bubble</span>
          </button>
          <button class="algo-btn" data-algo="selection" onclick="selectAlgo('selection')">
            <span class="algo-icon">🔮</span><span class="algo-name">Selection</span>
          </button>
          <button class="algo-btn" data-algo="insertion" onclick="selectAlgo('insertion')">
            <span class="algo-icon">📖</span><span class="algo-name">Insertion</span>
          </button>
          <button class="algo-btn" data-algo="merge" onclick="selectAlgo('merge')">
            <span class="algo-icon">⚗️</span><span class="algo-name">Merge</span>
          </button>
          <button class="algo-btn" data-algo="quick" onclick="selectAlgo('quick')">
            <span class="algo-icon">⚡</span><span class="algo-name">Quick</span>
          </button>
          <button class="algo-btn" data-algo="shell" onclick="selectAlgo('shell')">
            <span class="algo-icon">🐚</span><span class="algo-name">Shell</span>
          </button>
        </div>
      </div>

      <div class="control-section">
        <div class="control-label">Magical Items Count</div>
        <input type="range" class="magic-range" id="itemCount" min="5" max="50" value="20" oninput="updateItemCount(this.value)">
        <div class="range-labels"><span>5 items</span><span id="itemCountVal">20 items</span><span>50 items</span></div>
      </div>

      <div class="control-section">
        <div class="control-label">Spell Casting Speed</div>
        <input type="range" class="magic-range" id="speedRange" min="1" max="10" value="5" oninput="updateSpeed(this.value)">
        <div class="range-labels"><span>🐢 Slow</span><span>⚡ Fast</span></div>
      </div>

      <div class="control-section">
        <button class="action-btn btn-cast" onclick="startSorting()">⚡ Cast Sorting Spell</button>
        <button class="action-btn btn-pause" onclick="togglePause()" id="pauseBtn">⏸ Pause Spell</button>
        <button class="action-btn btn-reset" onclick="resetAll()">🔄 Vanish & Reset</button>
        <button class="action-btn btn-random" onclick="randomize()">🎲 Accio Random Items</button>
        <button class="action-btn btn-hat" onclick="sortingHatMode()">🎩 Sorting Hat Mode!</button>
      </div>

      <div class="stats-grid">
        <div class="stat-box">
          <div class="stat-value" id="statComparisons">0</div>
          <div class="stat-label">⚖ Comparisons</div>
        </div>
        <div class="stat-box">
          <div class="stat-value" id="statSwaps">0</div>
          <div class="stat-label">✨ Swaps</div>
        </div>
        <div class="stat-box">
          <div class="stat-value" id="statTime">0ms</div>
          <div class="stat-label">⏱ Duration</div>
        </div>
        <div class="stat-box">
          <div class="stat-value" id="statItems">20</div>
          <div class="stat-label">📦 Items</div>
        </div>
      </div>
    </div>

    <!-- CENTER: Visualization -->
    <div class="viz-panel">
      <div class="sorting-stage" id="sortingStage">
        <div class="stage-torches">
          <div class="stage-torch"><div class="torch-flame"></div><div class="torch-body"></div></div>
          <div class="stage-torch"><div class="torch-flame"></div><div class="torch-body"></div></div>
          <div class="stage-torch"><div class="torch-flame"></div><div class="torch-body"></div></div>
        </div>
        <div class="step-description" id="stepDesc">✨ Choose a sorting spell and cast it upon the magical items...</div>
        <div class="bars-container" id="barsContainer"></div>
        <div class="stage-shelf"></div>
        <div class="completion-burst" id="completionBurst">
          <div class="burst-circle"></div>
          <div class="burst-text">🎉 Sorting Complete!<br><span style="font-size:.9rem">The Spell Succeeded!</span></div>
        </div>
      </div>

      <div class="status-bar">
        <div class="status-item">Spell: <span class="status-value" id="currentAlgoName">Bubble Sortium</span></div>
        <div class="status-item">House: <span class="status-value" id="currentHouseName">Gryffindor</span></div>
        <div class="status-item">Status: <span class="status-value" id="sortStatus">Ready</span></div>
        <div class="status-item">Progress: <span class="status-value" id="sortProgress">0%</span></div>
      </div>
    </div>

    <!-- RIGHT: Spellbook -->
    <div class="parchment-panel">
      <span class="panel-corner tl">✦</span>
      <span class="panel-corner tr">✦</span>
      <span class="panel-corner bl">✦</span>
      <span class="panel-corner br">✦</span>
      <div class="panel-title">📚 The Sorting Spellbook</div>

      <div class="hat-animation" id="hatAnimation">🎩</div>

      <div class="spellbook-content" id="spellbookContent">
        <!-- Filled by JS -->
      </div>

      <div class="owl-panel">
        <div class="owl-icon">🦉</div>
        <div class="owl-message" id="owlMessage">
          "Welcome, young wizard. Select an enchanted sorting spell from the panel on your left, then cast it upon the magical artefacts. I shall guide your studies..."
        </div>
      </div>

      <div class="wizard-level">
        <div class="level-label">🧙 Wizard Rank: <span id="wizardTitle">Apprentice</span></div>
        <div class="level-bar-bg"><div class="level-bar-fill" id="levelBar" style="width:10%"></div></div>
        <div class="level-text">Complete sorts to advance your rank</div>
      </div>
    </div>

  </div>
</div>

<script>
// ═══════════════════════════════════════════
// STATE
// ═══════════════════════════════════════════
let state = {
  array: [],
  algo: 'bubble',
  house: 'gryffindor',
  speed: 5,
  itemCount: 20,
  comparisons: 0,
  swaps: 0,
  isSorting: false,
  isPaused: false,
  startTime: null,
  sortsDone: 0,
  wizardXP: 10,
};

let animFrameId = null;
let pauseResolve = null;

// ═══════════════════════════════════════════
// HOUSE THEMES
// ═══════════════════════════════════════════
const houseThemes = {
  gryffindor: {
    name: 'Gryffindor',
    barColor: (pct) => `hsl(${0 + pct*20}, 85%, ${25 + pct*35}%)`,
    compareColor: '#ffd700',
    swapColor: '#ff4444',
    sortedColor: '#ff8c00',
  },
  ravenclaw: {
    name: 'Ravenclaw',
    barColor: (pct) => `hsl(${210 + pct*30}, 70%, ${20 + pct*40}%)`,
    compareColor: '#946b2d',
    swapColor: '#6699ff',
    sortedColor: '#4488ff',
  },
  slytherin: {
    name: 'Slytherin',
    barColor: (pct) => `hsl(${130 + pct*20}, 60%, ${15 + pct*35}%)`,
    compareColor: '#aaaaaa',
    swapColor: '#66ff99',
    sortedColor: '#2d7a4f',
  },
  hufflepuff: {
    name: 'Hufflepuff',
    barColor: (pct) => `hsl(${40 + pct*10}, 80%, ${25 + pct*40}%)`,
    compareColor: '#ffdd44',
    swapColor: '#ff9900',
    sortedColor: '#ecb939',
  },
};

// ═══════════════════════════════════════════
// ALGORITHM DATA
// ═══════════════════════════════════════════
const algoData = {
  bubble: {
    spellName: 'Bullio Sortium',
    icon: '🫧',
    desc: 'The gentle floating spell. Like soap bubbles rising through water, larger values float upward to their rightful place.',
    best: 'O(n)', avg: 'O(n²)', worst: 'O(n²)', space: 'O(1)',
    bestClass: 'good', avgClass: 'bad', worstClass: 'bad',
    steps: [
      'Begin at the <span class="step-highlight">leftmost item</span>',
      'Compare each adjacent pair of magical objects',
      'Swap if the left is greater than the right',
      'The largest item <span class="step-highlight">"bubbles"</span> to the end',
      'Repeat, ignoring the already-sorted tail',
      'Continue until no swaps occur',
    ],
    useCase: 'Teaching & detecting nearly-sorted lists.',
    owlMsg: '"Ahh, the Bullio Sortium! A classic beginner spell. Watch how the heaviest objects bubble upward like cauldron gases!"',
  },
  selection: {
    spellName: 'Selectus Maximus',
    icon: '🔮',
    desc: 'The seeking spell. Scans the entire enchanted shelf to find the smallest item, then places it with a decisive swap.',
    best: 'O(n²)', avg: 'O(n²)', worst: 'O(n²)', space: 'O(1)',
    bestClass: 'bad', avgClass: 'bad', worstClass: 'bad',
    steps: [
      'Mark the <span class="step-highlight">first unsorted</span> position',
      'Scan entire remaining array for the minimum',
      'Swap the minimum into the marked position',
      'The sorted section grows by one from the left',
      'Repeat until all items are placed',
    ],
    useCase: 'When minimizing swaps is crucial.',
    owlMsg: '"The Selectus Maximus! A methodical spell - it always knows exactly what it seeks. Fewer swaps, but no shortcuts!"',
  },
  insertion: {
    spellName: 'Insertus Ordinis',
    icon: '📖',
    desc: 'The bookshelf spell. Takes each new magical tome and slides it precisely into the already-sorted section.',
    best: 'O(n)', avg: 'O(n²)', worst: 'O(n²)', space: 'O(1)',
    bestClass: 'good', avgClass: 'bad', worstClass: 'bad',
    steps: [
      'The first item is trivially sorted',
      'Take the <span class="step-highlight">next item</span> as the "key"',
      'Shift larger sorted items one position right',
      'Insert the key in its correct position',
      'The sorted section grows rightward',
      'Efficient when data is <span class="step-highlight">nearly sorted</span>',
    ],
    useCase: 'Small datasets & nearly-sorted collections.',
    owlMsg: '"Insertus Ordinis - the sorting hat\'s own method! It reads each item and places it perfectly among the sorted ones."',
  },
  merge: {
    spellName: 'Mergius Divinitus',
    icon: '⚗️',
    desc: 'The splitting potion. Divides the array into halves, sorts each recursively, then merges them into perfect order.',
    best: 'O(n log n)', avg: 'O(n log n)', worst: 'O(n log n)', space: 'O(n)',
    bestClass: 'good', avgClass: 'good', worstClass: 'good',
    steps: [
      '<span class="step-highlight">Divide</span> the array into two halves',
      'Recursively sort each half with the same spell',
      'Base case: arrays of length 1 are sorted',
      '<span class="step-highlight">Merge</span> two sorted halves by comparing front elements',
      'The smaller element is placed into result',
      'Guarantees O(n log n) — always reliable!',
    ],
    useCase: 'Large datasets & guaranteed performance.',
    owlMsg: '"The Mergius Divinitus - a masterwork of divide and conquer! The Dark Lord himself fears its consistency."',
  },
  quick: {
    spellName: 'Quickus Pivotum',
    icon: '⚡',
    desc: 'The lightning pivot spell. Chooses a magical pivot stone, then banishes smaller items left and larger items right.',
    best: 'O(n log n)', avg: 'O(n log n)', worst: 'O(n²)', space: 'O(log n)',
    bestClass: 'good', avgClass: 'good', worstClass: 'bad',
    steps: [
      'Choose a <span class="step-highlight">pivot element</span> (typically last)',
      'Partition: items < pivot go left, > pivot go right',
      'The pivot is now in its final sorted position',
      'Recursively apply to both partitions',
      'Very fast in practice — a favorite of Aurors!',
      'Worst case when pivot is always min/max',
    ],
    useCase: 'General purpose, fastest in practice.',
    owlMsg: '"Quickus Pivotum! The preferred spell of advanced Aurors. Lightning fast, but choose your pivot wisely, young one."',
  },
  shell: {
    spellName: 'Shellus Gapicus',
    icon: '🐚',
    desc: 'The ancient shell spell. Uses diminishing gap sequences to pre-sort distant elements before final insertion sort.',
    best: 'O(n log n)', avg: 'O(n log² n)', worst: 'O(n²)', space: 'O(1)',
    bestClass: 'good', avgClass: 'mid', worstClass: 'bad',
    steps: [
      'Start with a large <span class="step-highlight">gap</span> (half array size)',
      'Perform insertion sort across elements gap apart',
      'Reduce gap (usually halve it)',
      'Large elements move toward right quickly',
      'When gap = 1, it\'s a standard insertion sort',
      'Much faster than pure insertion sort!',
    ],
    useCase: 'Medium datasets needing no extra memory.',
    owlMsg: '"Shellus Gapicus — a forgotten ancient spell! It reaches across the array like Dumbledore\'s long arm of wisdom."',
  },
};

// ═══════════════════════════════════════════
// BACKGROUND CREATION
// ═══════════════════════════════════════════
function createStars() {
  const c = document.getElementById('bgStars');
  for (let i = 0; i < 180; i++) {
    const s = document.createElement('div');
    s.className = 'star';
    const size = Math.random() * 2.5 + .5;
    s.style.cssText = `width:${size}px;height:${size}px;top:${Math.random()*70}%;left:${Math.random()*100}%;--dur:${2+Math.random()*4}s;--delay:${-Math.random()*4}s;`;
    c.appendChild(s);
  }
}

function createCandles() {
  const c = document.getElementById('candlesContainer');
  const positions = [
    [8,15],[15,25],[25,10],[40,20],[55,8],[68,22],[80,12],[90,18],
    [5,40],[20,55],[35,45],[50,50],[65,38],[78,48],[95,42],
  ];
  positions.forEach(([left, top], i) => {
    const g = document.createElement('div');
    g.className = 'candle-group';
    g.style.cssText = `left:${left}%;top:${top}%;--dur:${4+Math.random()*3}s;--delay:${-Math.random()*4}s;`;
    g.innerHTML = `<div class="candle-glow"></div><div class="candle"><div class="candle-drip"></div></div>`;
    c.appendChild(g);
  });
}

function createParticles() {
  const c = document.getElementById('particlesContainer');
  const colors = ['#f0c040','#ffe082','#ff8c00','#6699ff','#66ff99','#ff6699'];
  for (let i = 0; i < 40; i++) {
    const p = document.createElement('div');
    p.className = 'magic-particle';
    const color = colors[Math.floor(Math.random()*colors.length)];
    const size = Math.random() * 4 + 1;
    p.style.cssText = `
      width:${size}px;height:${size}px;
      background:${color};
      left:${Math.random()*100}%;
      top:${40+Math.random()*60}%;
      --dur:${3+Math.random()*5}s;
      --delay:${-Math.random()*8}s;
      --drift:${(Math.random()-0.5)*80}px;
      box-shadow:0 0 ${size*2}px ${color};
    `;
    c.appendChild(p);
  }
}

function createCastleWindows() {
  const c = document.getElementById('castleWindows');
  for (let i = 0; i < 12; i++) {
    const w = document.createElement('div');
    w.className = 'castle-window';
    w.style.cssText = `
      width:${8+Math.random()*10}px;height:${12+Math.random()*16}px;
      left:${5+Math.random()*90}%;bottom:${10+Math.random()*60}px;
      animation-delay:${-Math.random()*3}s;
    `;
    c.appendChild(w);
  }
}

// ═══════════════════════════════════════════
// SPARKLE EFFECTS
// ═══════════════════════════════════════════
function spawnSparkle(x, y, color = '#f0c040') {
  const c = document.getElementById('sparkleContainer');
  const sparkleChars = ['✦','✧','⋆','★','✨','⚡','💫'];
  for (let i = 0; i < 5; i++) {
    const s = document.createElement('div');
    s.className = 'sparkle';
    s.textContent = sparkleChars[Math.floor(Math.random()*sparkleChars.length)];
    const angle = (i/5)*360;
    const dist = 20 + Math.random()*40;
    const dx = Math.cos(angle*Math.PI/180)*dist;
    const dy = Math.sin(angle*Math.PI/180)*dist;
    s.style.cssText = `
      left:${x}px;top:${y}px;
      color:${color};font-size:${.6+Math.random()*.8}rem;
      transform:translate(${dx}px,${dy}px);
    `;
    c.appendChild(s);
    setTimeout(() => s.remove(), 700);
  }
}

// Wand cursor trail
document.addEventListener('mousemove', (e) => {
  const trail = document.getElementById('wandTrail');
  trail.style.left = e.clientX + 'px';
  trail.style.top = e.clientY + 'px';
  if (Math.random() < 0.15) {
    spawnSparkle(e.clientX, e.clientY, '#f0c040');
  }
});

// ═══════════════════════════════════════════
// ARRAY MANAGEMENT
// ═══════════════════════════════════════════
function generateArray(n) {
  state.array = Array.from({length: n}, () => Math.floor(Math.random()*90)+10);
  state.comparisons = 0;
  state.swaps = 0;
  updateStats();
  renderBars();
}

function getBarColor(index, total) {
  const theme = houseThemes[state.house];
  const pct = index / (total - 1);
  return theme.barColor(pct);
}

const ICONS = ['🧪','📖','🔮','⚗️','🪄','🐸','📜','🦋','💎','🌙','⭐','🌿'];

function renderBars(highlights = {}) {
  const container = document.getElementById('barsContainer');
  const stage = document.getElementById('sortingStage');
  const stageH = stage.offsetHeight - 80;
  const n = state.array.length;
  const maxVal = Math.max(...state.array);

  container.innerHTML = '';

  state.array.forEach((val, i) => {
    const barH = Math.max(20, (val / maxVal) * stageH);
    const pct = i / Math.max(n - 1, 1);
    const color = getBarColor(i, n);

    const bar = document.createElement('div');
    bar.className = 'magic-bar';
    bar.id = `bar-${i}`;
    bar.style.cssText = `
      height:${barH}px;
      background:linear-gradient(180deg, ${color} 0%, ${color}88 100%);
      box-shadow:0 0 8px ${color}66,inset 0 0 6px rgba(255,255,255,.1);
      --color:${color};
      border:1px solid ${color}44;
    `;

    if (highlights.comparing && highlights.comparing.includes(i)) {
      bar.classList.add('comparing');
      bar.style.background = `linear-gradient(180deg, ${houseThemes[state.house].compareColor}, ${houseThemes[state.house].compareColor}88)`;
      bar.style.boxShadow = `0 0 20px ${houseThemes[state.house].compareColor}`;
    }
    if (highlights.swapping && highlights.swapping.includes(i)) {
      bar.classList.add('swapping');
      bar.style.background = `linear-gradient(180deg, ${houseThemes[state.house].swapColor}, ${houseThemes[state.house].swapColor}88)`;
      bar.style.boxShadow = `0 0 25px ${houseThemes[state.house].swapColor}`;
    }
    if (highlights.sorted && highlights.sorted.includes(i)) {
      bar.classList.add('sorted');
      bar.style.background = `linear-gradient(180deg, ${houseThemes[state.house].sortedColor}, ${houseThemes[state.house].sortedColor}88)`;
    }
    if (highlights.pivot === i) {
      bar.style.background = 'linear-gradient(180deg, #ff99ff, #cc66cc88)';
      bar.style.boxShadow = '0 0 20px #ff99ff';
    }

    // Show value label if not too many bars
    if (n <= 30) {
      const label = document.createElement('div');
      label.className = 'bar-label';
      label.textContent = val;
      bar.appendChild(label);
    }

    // Show icon on top for fewer bars
    if (n <= 20) {
      const icon = document.createElement('div');
      icon.className = 'bar-icon';
      icon.textContent = ICONS[i % ICONS.length];
      bar.appendChild(icon);
    }

    bar.addEventListener('click', (e) => {
      const rect = bar.getBoundingClientRect();
      spawnSparkle(rect.left + rect.width/2, rect.top, houseThemes[state.house].compareColor);
    });

    container.appendChild(bar);
  });
}

function updateStats() {
  document.getElementById('statComparisons').textContent = state.comparisons;
  document.getElementById('statSwaps').textContent = state.swaps;
  document.getElementById('statItems').textContent = state.array.length;
  if (state.startTime) {
    document.getElementById('statTime').textContent = (Date.now() - state.startTime) + 'ms';
  }
}

// ═══════════════════════════════════════════
// DELAY / PAUSE
// ═══════════════════════════════════════════
function getDelay() {
  const s = state.speed;
  const base = [800, 600, 400, 250, 150, 100, 60, 30, 15, 5];
  return base[Math.max(0, Math.min(9, s-1))];
}

function sleep(ms) {
  return new Promise(resolve => {
    if (state.isPaused) {
      pauseResolve = resolve;
    } else {
      setTimeout(resolve, ms);
    }
  });
}

function checkPause() {
  if (state.isPaused) {
    return new Promise(r => { pauseResolve = r; });
  }
  return Promise.resolve();
}

// ═══════════════════════════════════════════
// SORTING ALGORITHMS
// ═══════════════════════════════════════════
async function bubbleSort() {
  const arr = state.array;
  const n = arr.length;
  const sorted = [];
  for (let i = 0; i < n-1; i++) {
    let swapped = false;
    for (let j = 0; j < n-i-1; j++) {
      if (!state.isSorting) return;
      await checkPause();
      state.comparisons++;
      updateDesc(`⚖ Comparing positions ${j} and ${j+1}: ${arr[j]} vs ${arr[j+1]}`);
      renderBars({ comparing: [j, j+1], sorted });
      await sleep(getDelay());
      if (arr[j] > arr[j+1]) {
        [arr[j], arr[j+1]] = [arr[j+1], arr[j]];
        state.swaps++;
        swapped = true;
        renderBars({ swapping: [j, j+1], sorted });
        updateDesc(`✨ Swapping ${arr[j+1]} and ${arr[j]}!`);
        await sleep(getDelay());
      }
      updateStats();
      updateProgress(Math.round(((i*(n-1)+j)/(n*(n-1)/2))*100));
    }
    sorted.push(n-i-1);
    if (!swapped) break;
  }
  sorted.push(0);
  renderBars({ sorted: Array.from({length:n},(_,i)=>i) });
}

async function selectionSort() {
  const arr = state.array;
  const n = arr.length;
  const sorted = [];
  for (let i = 0; i < n-1; i++) {
    if (!state.isSorting) return;
    let minIdx = i;
    for (let j = i+1; j < n; j++) {
      if (!state.isSorting) return;
      await checkPause();
      state.comparisons++;
      updateDesc(`🔮 Seeking minimum... comparing ${arr[j]} with current min ${arr[minIdx]}`);
      renderBars({ comparing: [j, minIdx], sorted });
      await sleep(getDelay());
      if (arr[j] < arr[minIdx]) minIdx = j;
      updateStats();
    }
    if (minIdx !== i) {
      [arr[i], arr[minIdx]] = [arr[minIdx], arr[i]];
      state.swaps++;
      renderBars({ swapping: [i, minIdx], sorted });
      updateDesc(`✨ Placed minimum (${arr[i]}) at position ${i}!`);
      await sleep(getDelay());
    }
    sorted.push(i);
    updateProgress(Math.round((i/(n-1))*100));
  }
  sorted.push(n-1);
  renderBars({ sorted: Array.from({length:n},(_,i)=>i) });
}

async function insertionSort() {
  const arr = state.array;
  const n = arr.length;
  const sorted = [0];
  for (let i = 1; i < n; i++) {
    if (!state.isSorting) return;
    const key = arr[i];
    let j = i-1;
    updateDesc(`📖 Inserting ${key} into sorted section...`);
    while (j >= 0 && arr[j] > key) {
      if (!state.isSorting) return;
      await checkPause();
      state.comparisons++;
      arr[j+1] = arr[j];
      state.swaps++;
      renderBars({ comparing: [j, j+1], sorted });
      updateDesc(`📖 Shifting ${arr[j+1]} right to make room for ${key}`);
      await sleep(getDelay());
      j--;
      updateStats();
    }
    arr[j+1] = key;
    sorted.push(i);
    renderBars({ sorted });
    updateDesc(`📖 Placed ${key} at position ${j+1}`);
    await sleep(getDelay());
    updateProgress(Math.round((i/n)*100));
  }
  renderBars({ sorted: Array.from({length:n},(_,i)=>i) });
}

async function mergeSort() {
  const arr = state.array;
  await mergeSortHelper(arr, 0, arr.length-1, []);
  renderBars({ sorted: Array.from({length:arr.length},(_,i)=>i) });
}

async function mergeSortHelper(arr, l, r, sorted) {
  if (l >= r || !state.isSorting) return;
  const mid = Math.floor((l+r)/2);
  await mergeSortHelper(arr, l, mid, sorted);
  await mergeSortHelper(arr, mid+1, r, sorted);
  await merge(arr, l, mid, r, sorted);
}

async function merge(arr, l, mid, r, sorted) {
  const left = arr.slice(l, mid+1);
  const right = arr.slice(mid+1, r+1);
  let i=0,j=0,k=l;
  while (i<left.length && j<right.length) {
    if (!state.isSorting) return;
    await checkPause();
    state.comparisons++;
    updateDesc(`⚗️ Merging: comparing ${left[i]} and ${right[j]}`);
    renderBars({ comparing: [l+i, mid+1+j] });
    await sleep(getDelay());
    if (left[i] <= right[j]) { arr[k++] = left[i++]; }
    else { arr[k++] = right[j++]; state.swaps++; }
    updateStats();
  }
  while (i<left.length) arr[k++]=left[i++];
  while (j<right.length) arr[k++]=right[j++];
  const partSorted = Array.from({length:r-l+1},(_,x)=>l+x);
  renderBars({ sorted: partSorted });
  await sleep(getDelay()/2);
  updateProgress(Math.round((r/arr.length)*100));
}

async function quickSort() {
  const arr = state.array;
  await quickSortHelper(arr, 0, arr.length-1, []);
  renderBars({ sorted: Array.from({length:arr.length},(_,i)=>i) });
}

async function quickSortHelper(arr, low, high, sorted) {
  if (low < high && state.isSorting) {
    const pi = await partition(arr, low, high, sorted);
    await quickSortHelper(arr, low, pi-1, sorted);
    await quickSortHelper(arr, pi+1, high, sorted);
  }
}

async function partition(arr, low, high, sorted) {
  const pivot = arr[high];
  updateDesc(`⚡ Pivot chosen: ${pivot} at position ${high}`);
  let i = low-1;
  for (let j=low; j<high; j++) {
    if (!state.isSorting) return i+1;
    await checkPause();
    state.comparisons++;
    renderBars({ comparing: [j, high], pivot: high });
    updateDesc(`⚡ Comparing ${arr[j]} with pivot ${pivot}`);
    await sleep(getDelay());
    if (arr[j] < pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]];
      state.swaps++;
      renderBars({ swapping: [i, j], pivot: high });
      await sleep(getDelay());
    }
    updateStats();
  }
  [arr[i+1], arr[high]] = [arr[high], arr[i+1]];
  state.swaps++;
  sorted.push(i+1);
  renderBars({ sorted, pivot: i+1 });
  await sleep(getDelay());
  return i+1;
}

async function shellSort() {
  const arr = state.array;
  const n = arr.length;
  let gap = Math.floor(n/2);
  const sorted = [];
  while (gap > 0 && state.isSorting) {
    for (let i=gap; i<n; i++) {
      if (!state.isSorting) return;
      await checkPause();
      const temp = arr[i];
      let j = i;
      updateDesc(`🐚 Gap=${gap}: Inserting ${temp} into gap-sorted section`);
      while (j>=gap && arr[j-gap]>temp) {
        state.comparisons++;
        arr[j] = arr[j-gap];
        state.swaps++;
        renderBars({ comparing: [j, j-gap] });
        await sleep(getDelay());
        j -= gap;
        updateStats();
      }
      arr[j] = temp;
    }
    gap = Math.floor(gap/2);
    updateProgress(Math.round((1-gap/n)*100));
  }
  renderBars({ sorted: Array.from({length:n},(_,i)=>i) });
}

// ═══════════════════════════════════════════
// UI CONTROLS
// ═══════════════════════════════════════════
function updateDesc(text) {
  document.getElementById('stepDesc').textContent = text;
}

function updateProgress(pct) {
  document.getElementById('sortProgress').textContent = Math.min(100,pct) + '%';
}

async function startSorting() {
  if (state.isSorting) return;
  state.isSorting = true;
  state.isPaused = false;
  state.comparisons = 0;
  state.swaps = 0;
  state.startTime = Date.now();

  document.getElementById('completionBurst').classList.remove('active');
  document.getElementById('sortStatus').textContent = 'Casting...';
  document.getElementById('pauseBtn').textContent = '⏸ Pause Spell';
  document.getElementById('pauseBtn').style.opacity = '1';

  updateDesc(`✨ Casting ${algoData[state.algo].spellName}...`);

  const sorters = { bubble: bubbleSort, selection: selectionSort, insertion: insertionSort, merge: mergeSort, quick: quickSort, shell: shellSort };

  try {
    await sorters[state.algo]();
  } catch(e) {}

  if (state.isSorting) {
    state.isSorting = false;
    const totalTime = Date.now() - state.startTime;
    document.getElementById('statTime').textContent = totalTime + 'ms';
    document.getElementById('sortStatus').textContent = '✅ Complete!';
    updateProgress(100);
    updateDesc(`🎉 ${algoData[state.algo].spellName} complete! ${state.comparisons} comparisons, ${state.swaps} swaps`);

    // Celebration
    document.getElementById('completionBurst').classList.add('active');
    setTimeout(() => document.getElementById('completionBurst').classList.remove('active'), 3000);

    // Spawn sparkles
    const stage = document.getElementById('sortingStage');
    const rect = stage.getBoundingClientRect();
    for (let i=0; i<8; i++) {
      setTimeout(() => {
        spawnSparkle(
          rect.left + Math.random()*rect.width,
          rect.top + Math.random()*rect.height,
          houseThemes[state.house].compareColor
        );
      }, i*200);
    }

    // Update wizard level
    state.sortsDone++;
    state.wizardXP = Math.min(100, state.wizardXP + 15);
    updateWizardLevel();

    const owlMessages = [
      '"Magnificent! Your spell was flawless, young wizard!"',
      '"Most impressive! Even Professor McGonagall would be proud."',
      '"Excellent work! You show great promise in the sorting arts."',
      '"Splendid! Ten points to your house for that perfect sort!"',
      '"Remarkable! Your magical sorting powers grow stronger!"',
    ];
    document.getElementById('owlMessage').textContent = owlMessages[state.sortsDone % owlMessages.length];
  }
}

function togglePause() {
  if (!state.isSorting) return;
  state.isPaused = !state.isPaused;
  const btn = document.getElementById('pauseBtn');
  if (state.isPaused) {
    btn.textContent = '▶ Resume Spell';
    document.getElementById('sortStatus').textContent = '⏸ Paused';
    updateDesc('⏸ The spell is paused... resume when ready.');
  } else {
    btn.textContent = '⏸ Pause Spell';
    document.getElementById('sortStatus').textContent = 'Casting...';
    if (pauseResolve) { pauseResolve(); pauseResolve = null; }
  }
}

function resetAll() {
  state.isSorting = false;
  state.isPaused = false;
  if (pauseResolve) { pauseResolve(); pauseResolve = null; }
  document.getElementById('completionBurst').classList.remove('active');
  document.getElementById('sortStatus').textContent = 'Ready';
  document.getElementById('pauseBtn').textContent = '⏸ Pause Spell';
  updateDesc('✨ Choose a sorting spell and cast it upon the magical items...');
  updateProgress(0);
  generateArray(state.itemCount);
}

function randomize() {
  if (state.isSorting) return;
  generateArray(state.itemCount);
  updateDesc('🎲 New magical items have appeared on the enchanted shelf!');
  document.getElementById('owlMessage').textContent = '"Ooh, fresh magical items! Now choose your sorting spell wisely!"';
}

function selectAlgo(algo) {
  state.algo = algo;
  document.querySelectorAll('.algo-btn').forEach(b => b.classList.remove('active'));
  document.querySelector(`[data-algo="${algo}"]`).classList.add('active');
  document.getElementById('currentAlgoName').textContent = algoData[algo].spellName;
  updateSpellbook();
  if (!state.isSorting) updateDesc(`✨ ${algoData[algo].spellName} selected. Cast when ready!`);
}

function selectHouse(house) {
  state.house = house;
  document.querySelectorAll('.house-btn').forEach(b => b.classList.remove('active'));
  document.querySelector(`[data-house="${house}"]`).classList.add('active');
  document.getElementById('currentHouseName').textContent = houseThemes[house].name;
  if (!state.isSorting) renderBars();
}

function updateItemCount(val) {
  state.itemCount = parseInt(val);
  document.getElementById('itemCountVal').textContent = val + ' items';
  if (!state.isSorting) generateArray(state.itemCount);
}

function updateSpeed(val) {
  state.speed = parseInt(val);
}

function sortingHatMode() {
  if (state.isSorting) return;
  const algos = Object.keys(algoData);
  const chosen = algos[Math.floor(Math.random()*algos.length)];
  const hat = document.getElementById('hatAnimation');
  hat.classList.add('visible');
  updateDesc('🎩 The Sorting Hat is deliberating...');
  document.getElementById('owlMessage').textContent = '"Hmm... difficult, very difficult... what spell shall it be?"';

  let count = 0;
  const interval = setInterval(() => {
    const random = algos[Math.floor(Math.random()*algos.length)];
    selectAlgo(random);
    count++;
    if (count > 15) {
      clearInterval(interval);
      selectAlgo(chosen);
      hat.classList.remove('visible');
      updateDesc(`🎩 The Sorting Hat has chosen: ${algoData[chosen].spellName}!`);
      document.getElementById('owlMessage').textContent = `"The hat has spoken! ${algoData[chosen].spellName} is your destined spell!"`;
    }
  }, 120);
}

function updateSpellbook() {
  const d = algoData[state.algo];
  document.getElementById('spellbookContent').innerHTML = `
    <div class="spell-name">✦ ${d.spellName} ✦</div>
    <div class="spell-desc">${d.desc}</div>
    <table class="complexity-table">
      <tr><th>Enchantment</th><th>Complexity</th></tr>
      <tr><td>Best Case</td><td class="${d.bestClass}">${d.best}</td></tr>
      <tr><td>Average</td><td class="${d.avgClass}">${d.avg}</td></tr>
      <tr><td>Worst Case</td><td class="${d.worstClass}">${d.worst}</td></tr>
      <tr><td>Spell Memory</td><td class="mid">${d.space}</td></tr>
    </table>
    <div class="control-label" style="margin-bottom:8px;">Incantation Steps</div>
    <ul class="spell-steps">
      ${d.steps.map(s=>`<li>${s}</li>`).join('')}
    </ul>
    <div style="margin-top:10px;font-size:.7rem;color:var(--parchment-dark);font-style:italic;border-top:1px solid rgba(240,192,64,.2);padding-top:8px;">
      📌 <strong style="color:var(--gold)">Practical Use:</strong> ${d.useCase}
    </div>
  `;
  document.getElementById('owlMessage').textContent = d.owlMsg;
}

function updateWizardLevel() {
  const titles = ['Apprentice','Scholar','Adept','Enchanter','Sorcerer','Archmage','Grand Sorcerer'];
  const idx = Math.min(titles.length-1, Math.floor(state.sortsDone/2));
  document.getElementById('wizardTitle').textContent = titles[idx];
  document.getElementById('levelBar').style.width = state.wizardXP + '%';
}

// ═══════════════════════════════════════════
// INIT
// ═══════════════════════════════════════════
function init() {
  createStars();
  createCandles();
  createParticles();
  createCastleWindows();
  selectAlgo('bubble');
  selectHouse('gryffindor');
  generateArray(20);
  updateWizardLevel();
}

init();
</script>
</body>
</html>

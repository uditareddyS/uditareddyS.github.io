<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NEXUS-7 Cargo Sorting Simulator</title>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;500;700;900&family=Rajdhani:wght@300;400;500;600&display=swap" rel="stylesheet">

<style>
*{
  margin:0;
  padding:0;
  box-sizing:border-box;
}

:root{
  --cyan:#00f5ff;
  --blue:#0080ff;
  --purple:#8b00ff;
  --green:#00ff88;
  --magenta:#ff00aa;
  --red:#ff2244;
  --gold:#ffd700;
  --text:#d8f3ff;
  --dim:#7aa8c7;
}

body{
  font-family:'Rajdhani',sans-serif;
  color:var(--text);
  background:
    radial-gradient(circle at top,#08111f 0%,#030610 45%,#01040a 100%);
  min-height:100vh;
  overflow-x:hidden;
}

/* HERO */
.hero{
  min-height:100vh;
  display:flex;
  align-items:center;
  justify-content:center;
  text-align:center;
  padding:40px 20px;
}

.hero-content{
  max-width:900px;
}

.hero h1{
  font-family:'Orbitron',sans-serif;
  font-size:clamp(2.5rem,6vw,5rem);
  color:var(--cyan);
  letter-spacing:4px;
  margin-bottom:20px;
  text-shadow:0 0 25px rgba(0,245,255,0.35);
}

.hero p{
  font-size:1.2rem;
  line-height:1.8;
  color:var(--text);
  opacity:0.9;
  max-width:700px;
  margin:0 auto 30px;
}

.hero-btn{
  display:inline-block;
  padding:14px 30px;
  border:1px solid var(--cyan);
  color:var(--cyan);
  text-decoration:none;
  font-family:'Orbitron',sans-serif;
  letter-spacing:2px;
  transition:all 0.3s ease;
}

.hero-btn:hover{
  background:rgba(0,245,255,0.1);
  box-shadow:0 0 18px rgba(0,245,255,0.25);
}

/* APP SHELL */
#app{
  width:95%;
  max-width:1500px;
  margin:40px auto;
  display:grid;
  grid-template-columns:240px 1fr 240px;
  grid-template-rows:auto 1fr auto;
  grid-template-areas:
    "hdr hdr hdr"
    "sidebar stage panel"
    "footer footer footer";
  border:1px solid rgba(0,245,255,0.08);
  border-radius:14px;
  overflow:hidden;
  box-shadow:0 0 35px rgba(0,245,255,0.08);
  backdrop-filter:blur(14px);
}

header{
  grid-area:hdr;
  padding:18px 24px;
  background:rgba(8,16,32,0.92);
  border-bottom:1px solid rgba(0,245,255,0.08);
}

header h2{
  font-family:'Orbitron',sans-serif;
  color:var(--cyan);
  letter-spacing:3px;
}

aside,.right-panel{
  padding:18px;
  background:rgba(255,255,255,0.02);
}

aside{
  grid-area:sidebar;
  border-right:1px solid rgba(255,255,255,0.05);
}

.right-panel{
  grid-area:panel;
  border-left:1px solid rgba(255,255,255,0.05);
}

.stage{
  grid-area:stage;
  padding:24px;
  min-height:550px;
}

footer{
  grid-area:footer;
  padding:18px;
  background:rgba(8,16,32,0.92);
  border-top:1px solid rgba(0,245,255,0.08);
}

.panel-card{
  background:rgba(255,255,255,0.03);
  border:1px solid rgba(255,255,255,0.06);
  border-radius:10px;
  padding:16px;
  margin-bottom:16px;
  box-shadow:0 8px 24px rgba(0,0,0,0.25);
}

.panel-title{
  font-family:'Orbitron',sans-serif;
  color:var(--cyan);
  font-size:0.8rem;
  margin-bottom:12px;
  letter-spacing:2px;
}

.visualizer{
  display:flex;
  align-items:flex-end;
  justify-content:center;
  gap:4px;
  height:420px;
  margin-top:20px;
}

.bar{
  width:20px;
  background:linear-gradient(to top,var(--blue),var(--cyan));
  border-radius:4px 4px 0 0;
  box-shadow:0 0 10px rgba(0,245,255,0.3);
}

.controls{
  display:flex;
  gap:10px;
  flex-wrap:wrap;
}

button{
  padding:10px 18px;
  background:transparent;
  color:var(--cyan);
  border:1px solid var(--cyan);
  cursor:pointer;
  font-family:'Orbitron',sans-serif;
}

button:hover{
  background:rgba(0,245,255,0.1);
}

.about-project{
  max-width:1000px;
  margin:60px auto;
  text-align:center;
  padding:20px;
}

.about-project h2{
  font-family:'Orbitron',sans-serif;
  color:var(--cyan);
  margin-bottom:14px;
}

.about-project p{
  line-height:1.8;
  color:var(--text);
  opacity:0.9;
}

@media(max-width:1100px){
  #app{
    grid-template-columns:1fr;
    grid-template-areas:
      "hdr"
      "stage"
      "sidebar"
      "panel"
      "footer";
  }

  aside,.right-panel{
    border:none;
  }
}
</style>
</head>
<body>

<section class="hero">
  <div class="hero-content">
    <h1>NEXUS-7 Cargo Sorting Simulator</h1>
    <p>
      Explore classic sorting algorithms through an immersive sci-fi cargo sorting simulation.
      Visualize Bubble, Selection, Insertion, Merge, and Quick Sort in an interactive futuristic environment.
    </p>
    <a href="#app" class="hero-btn">Launch Simulator</a>
  </div>
</section>

<div id="app">

<header>
  <h2>NEXUS-7 SORTING CONTROL SYSTEM</h2>
</header>

<aside>
  <div class="panel-card">
    <div class="panel-title">MISSION STATS</div>
    <p>Comparisons: <span id="comparisons">0</span></p>
    <p>Swaps: <span id="swaps">0</span></p>
  </div>

  <div class="panel-card">
    <div class="panel-title">ALGORITHM</div>
    <p id="algo-name">Bubble Sort</p>
  </div>
</aside>

<div class="stage">
  <div class="panel-card">
    <div class="panel-title">SORT VISUALIZER</div>
    <div class="visualizer" id="visualizer"></div>
  </div>
</div>

<div class="right-panel">
  <div class="panel-card">
    <div class="panel-title">PERFORMANCE</div>
    <p>Real-time sorting animation</p>
  </div>
</div>

<footer>
  <div class="controls">
    <button onclick="generateBars()">Randomize</button>
    <button onclick="bubbleSort()">Bubble Sort</button>
  </div>
</footer>

</div>

<section class="about-project">
  <h2>About This Project</h2>
  <p>
    NEXUS-7 is a futuristic sorting visualizer that demonstrates how sorting algorithms
    work through interactive cargo management animations in a sci-fi inspired control system.
  </p>
</section>

<script>
let arr = [];

function generateBars(){
  arr = [];
  const visualizer = document.getElementById("visualizer");
  visualizer.innerHTML = "";

  for(let i=0;i<30;i++){
    const val = Math.floor(Math.random()*300)+20;
    arr.push(val);

    const bar = document.createElement("div");
    bar.classList.add("bar");
    bar.style.height = val + "px";
    visualizer.appendChild(bar);
  }

  document.getElementById("comparisons").innerText = 0;
  document.getElementById("swaps").innerText = 0;
}

async function bubbleSort(){
  let bars = document.getElementsByClassName("bar");
  let comparisons = 0;
  let swaps = 0;

  for(let i=0;i<arr.length;i++){
    for(let j=0;j<arr.length-i-1;j++){
      comparisons++;
      document.getElementById("comparisons").innerText = comparisons;

      bars[j].style.background = "var(--gold)";
      bars[j+1].style.background = "var(--gold)";

      await new Promise(resolve => setTimeout(resolve, 50));

      if(arr[j] > arr[j+1]){
        swaps++;
        document.getElementById("swaps").innerText = swaps;

        [arr[j],arr[j+1]] = [arr[j+1],arr[j]];
        bars[j].style.height = arr[j] + "px";
        bars[j+1].style.height = arr[j+1] + "px";
      }

      bars[j].style.background = "";
      bars[j+1].style.background = "";
    }
  }
}

generateBars();
</script>

</body>
</html>

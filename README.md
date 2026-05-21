<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Something I Need to Tell You</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;1,300;1,400&family=Cinzel:wght@300;400&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }

  :root {
    --crimson: #8b0000;
    --crimson-glow: #c0392b;
    --rose: #e8354a;
    --gold: #c9a96e;
    --black: #000000;
    --near-black: #060608;
    --glass: rgba(255,255,255,0.03);
    --glass-border: rgba(200,60,60,0.15);
  }

  html { scroll-behavior: smooth; }

  body {
    background: var(--black);
    color: #fff;
    font-family: 'Cormorant Garamond', serif;
    overflow-x: hidden;
    cursor: default;
  }

  #canvas-bg {
    position: fixed;
    top: 0; left: 0;
    width: 100%; height: 100%;
    z-index: 0;
    pointer-events: none;
  }

  /* ── OPENING SCENE ── */
  #opening {
    position: fixed;
    inset: 0;
    z-index: 100;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #000;
    flex-direction: column;
    gap: 2rem;
    transition: opacity 1.5s ease;
  }

  #opening.fade-out {
    opacity: 0;
    pointer-events: none;
  }

  #typewriter {
    font-family: 'Cormorant Garamond', serif;
    font-size: clamp(1.2rem, 3vw, 2rem);
    font-style: italic;
    font-weight: 300;
    color: rgba(255,255,255,0.85);
    letter-spacing: 0.12em;
    text-align: center;
    padding: 0 2rem;
    min-height: 2.5em;
  }

  #cursor-blink {
    display: inline-block;
    width: 2px;
    height: 1.1em;
    background: var(--rose);
    margin-left: 3px;
    vertical-align: middle;
    animation: blink 1s step-end infinite;
  }

  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0} }

  #enter-btn {
    font-family: 'Cinzel', serif;
    font-size: 0.75rem;
    letter-spacing: 0.3em;
    color: var(--gold);
    border: 1px solid rgba(201,169,110,0.3);
    background: transparent;
    padding: 0.75rem 2.5rem;
    cursor: pointer;
    opacity: 0;
    transition: all 0.5s ease;
    text-transform: uppercase;
  }

  #enter-btn:hover {
    background: rgba(201,169,110,0.08);
    border-color: var(--gold);
    box-shadow: 0 0 30px rgba(201,169,110,0.2);
  }

  #enter-btn.visible { opacity: 1; }

  /* ── MUSIC TOGGLE ── */
  #music-btn {
    position: fixed;
    top: 1.5rem; right: 1.5rem;
    z-index: 999;
    background: var(--glass);
    border: 1px solid var(--glass-border);
    backdrop-filter: blur(10px);
    color: rgba(255,255,255,0.6);
    font-family: 'Cinzel', serif;
    font-size: 0.6rem;
    letter-spacing: 0.2em;
    padding: 0.5rem 1rem;
    cursor: pointer;
    border-radius: 2px;
    transition: all 0.3s;
    display: none;
  }

  #music-btn:hover { color: var(--gold); border-color: var(--gold); }
  #music-btn.show { display: block; }

  /* ── MAIN SCROLL CONTENT ── */
  #main {
    position: relative;
    z-index: 10;
    opacity: 0;
    pointer-events: none;
    transition: opacity 1.5s ease 0.5s;
  }

  #main.visible {
    opacity: 1;
    pointer-events: all;
  }

  .section {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 4rem 2rem;
    position: relative;
  }

  .glass-card {
    background: var(--glass);
    border: 1px solid var(--glass-border);
    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    padding: clamp(2rem, 5vw, 4rem) clamp(2rem, 6vw, 5rem);
    max-width: 700px;
    width: 100%;
    text-align: center;
    position: relative;
    overflow: hidden;
    border-radius: 2px;
    box-shadow: 0 0 60px rgba(139,0,0,0.15), inset 0 0 60px rgba(0,0,0,0.3);
    transform: translateY(60px);
    opacity: 0;
    transition: transform 1.2s cubic-bezier(0.16,1,0.3,1), opacity 1.2s ease;
  }

  .glass-card.in-view {
    transform: translateY(0);
    opacity: 1;
  }

  .glass-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 1px;
    background: linear-gradient(90deg, transparent, rgba(200,60,60,0.5), transparent);
  }

  .section-number {
    font-family: 'Cinzel', serif;
    font-size: 0.6rem;
    letter-spacing: 0.4em;
    color: var(--crimson-glow);
    text-transform: uppercase;
    margin-bottom: 1.5rem;
    opacity: 0.7;
  }

  .section-text {
    font-family: 'Cormorant Garamond', serif;
    font-size: clamp(1.4rem, 3.5vw, 2.2rem);
    font-style: italic;
    font-weight: 300;
    line-height: 1.7;
    color: rgba(255,255,255,0.9);
    letter-spacing: 0.02em;
  }

  .section-text em {
    color: var(--rose);
    font-style: normal;
  }

  .ornament {
    display: block;
    margin: 1.5rem auto;
    width: 60px;
    height: 1px;
    background: linear-gradient(90deg, transparent, var(--crimson-glow), transparent);
  }

  /* ── HEARTBEAT ── */
  .heartbeat-dot {
    width: 6px;
    height: 6px;
    background: var(--rose);
    border-radius: 50%;
    margin: 1.5rem auto 0;
    animation: hb 1.2s ease-in-out infinite;
    box-shadow: 0 0 12px var(--rose);
  }

  @keyframes hb {
    0%,100%{transform:scale(1);opacity:0.6}
    14%{transform:scale(1.5);opacity:1}
    28%{transform:scale(1.1);opacity:0.8}
    42%{transform:scale(1.4);opacity:1}
  }

  /* ── PROPOSAL SECTION ── */
  #proposal {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 4rem 2rem;
    position: relative;
    z-index: 10;
  }

  #heart-canvas {
    width: min(500px, 90vw);
    height: min(500px, 90vw);
    display: block;
    margin: 0 auto 1rem;
  }

  #proposal-text {
    font-family: 'Cinzel', serif;
    font-size: clamp(1.6rem, 5vw, 3rem);
    font-weight: 300;
    letter-spacing: 0.08em;
    text-align: center;
    color: #fff;
    text-shadow: 0 0 40px rgba(232,53,74,0.6), 0 0 80px rgba(139,0,0,0.4);
    opacity: 0;
    transform: translateY(30px);
    transition: all 1.5s cubic-bezier(0.16,1,0.3,1);
    margin-bottom: 3rem;
  }

  #proposal-text.visible { opacity: 1; transform: translateY(0); }

  .btn-row {
    display: flex;
    gap: 1.5rem;
    justify-content: center;
    flex-wrap: wrap;
    opacity: 0;
    transform: translateY(20px);
    transition: all 1s ease 0.5s;
  }

  .btn-row.visible { opacity: 1; transform: translateY(0); }

  .btn-yes {
    font-family: 'Cinzel', serif;
    font-size: 0.85rem;
    letter-spacing: 0.25em;
    text-transform: uppercase;
    padding: 1rem 3rem;
    background: linear-gradient(135deg, var(--crimson), var(--rose));
    border: none;
    color: #fff;
    cursor: pointer;
    position: relative;
    overflow: hidden;
    border-radius: 2px;
    box-shadow: 0 0 30px rgba(232,53,74,0.4);
    transition: all 0.4s ease;
  }

  .btn-yes::before {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(135deg, var(--rose), #ff6b6b);
    opacity: 0;
    transition: opacity 0.4s;
  }

  .btn-yes:hover::before { opacity: 1; }
  .btn-yes:hover { box-shadow: 0 0 60px rgba(232,53,74,0.7); transform: translateY(-2px); }
  .btn-yes span { position: relative; z-index: 1; }

  /* ── YES CELEBRATION ── */
  #celebration {
    position: fixed;
    inset: 0;
    z-index: 200;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background: rgba(0,0,0,0.95);
    opacity: 0;
    pointer-events: none;
    transition: opacity 1s ease;
    text-align: center;
    padding: 2rem;
  }

  #celebration.visible { opacity: 1; pointer-events: all; }

  #celebration h1 {
    font-family: 'Cormorant Garamond', serif;
    font-size: clamp(2rem, 6vw, 4rem);
    font-style: italic;
    font-weight: 300;
    color: #fff;
    line-height: 1.4;
    text-shadow: 0 0 60px rgba(232,53,74,0.8);
    margin-bottom: 1rem;
  }

  #celebration p {
    font-family: 'Cinzel', serif;
    font-size: 0.75rem;
    letter-spacing: 0.3em;
    color: var(--gold);
    opacity: 0.8;
  }

  #celebration-canvas {
    position: absolute;
    inset: 0;
    z-index: -1;
  }

  /* ── SCROLL HINT ── */
  #scroll-hint {
    position: fixed;
    bottom: 2rem;
    left: 50%;
    transform: translateX(-50%);
    z-index: 50;
    opacity: 0;
    transition: opacity 1s ease;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.5rem;
  }

  #scroll-hint.show { opacity: 1; }

  #scroll-hint span {
    font-family: 'Cinzel', serif;
    font-size: 0.55rem;
    letter-spacing: 0.3em;
    color: rgba(255,255,255,0.3);
    text-transform: uppercase;
  }

  .scroll-line {
    width: 1px;
    height: 40px;
    background: linear-gradient(to bottom, rgba(200,60,60,0.5), transparent);
    animation: scrollPulse 2s ease-in-out infinite;
  }

  @keyframes scrollPulse { 0%,100%{opacity:0.3;transform:scaleY(1)} 50%{opacity:1;transform:scaleY(1.1)} }

  /* ── VIGNETTE ── */
  .vignette {
    position: fixed;
    inset: 0;
    z-index: 5;
    pointer-events: none;
    background: radial-gradient(ellipse at center, transparent 40%, rgba(0,0,0,0.7) 100%);
  }

  /* ── AMBIENT GLOW ── */
  .ambient-glow {
    position: fixed;
    border-radius: 50%;
    filter: blur(100px);
    pointer-events: none;
    z-index: 1;
    animation: drift 8s ease-in-out infinite alternate;
  }

  .glow-1 {
    width: 400px; height: 400px;
    background: rgba(139,0,0,0.12);
    top: -100px; left: -100px;
    animation-delay: 0s;
  }

  .glow-2 {
    width: 350px; height: 350px;
    background: rgba(139,0,0,0.08);
    bottom: -80px; right: -80px;
    animation-delay: -4s;
  }

  @keyframes drift {
    0%{transform:translate(0,0) scale(1)}
    100%{transform:translate(30px,20px) scale(1.05)}
  }
</style>
</head>
<body>

<div class="ambient-glow glow-1"></div>
<div class="ambient-glow glow-2"></div>
<div class="vignette"></div>

<canvas id="canvas-bg"></canvas>

<!-- Added a default royalty-free instrumental melody link so you can immediately see it work -->
<audio id="bgm" loop>
  <source src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" type="audio/mpeg">
</audio>
<button id="music-btn">♪ MUSIC OFF</button>

<div id="opening">
  <div id="typewriter"><span id="typed-text"></span><span id="cursor-blink"></span></div>
  <button id="enter-btn">Continue →</button>
</div>

<div id="scroll-hint">
  <span>Scroll</span>
  <div class="scroll-line"></div>
</div>

<div id="main">

  <div class="section">
    <div class="glass-card" data-reveal>
      <p class="section-number">I —</p>
      <p class="section-text">Talking to you became <em>the best part of my day.</em></p>
      <span class="ornament"></span>
      <div class="heartbeat-dot"></div>
    </div>
  </div>

  <div class="section">
    <div class="glass-card" data-reveal>
      <p class="section-number">II —</p>
      <p class="section-text">You turned <em>ordinary moments</em> into something I never wanted to end.</p>
      <span class="ornament"></span>
      <div class="heartbeat-dot"></div>
    </div>
  </div>

  <div class="section">
    <div class="glass-card" data-reveal>
      <p class="section-number">III —</p>
      <p class="section-text">And honestly... I don't want this feeling to stay <em>unspoken</em> anymore.</p>
      <span class="ornament"></span>
      <div class="heartbeat-dot"></div>
    </div>
  </div>

  <div id="proposal">
    <canvas id="heart-canvas"></canvas>
    <div id="proposal-text">Will you be my girlfriend?</div>
    <div class="btn-row" id="btn-row">
      <button class="btn-yes" id="btn-yes"><span>Yes ❤️</span></button>
    </div>
  </div>

</div>

<div id="celebration">
  <canvas id="celebration-canvas"></canvas>
  <h1>You just made me<br>the happiest person alive.</h1>
  <p>Forever yours ❤</p>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
const rand = (a,b) => Math.random()*(b-a)+a;
const lerp = (a,b,t) => a+(b-a)*t;

(function initBG() {
  const canvas = document.getElementById('canvas-bg');
  const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setClearColor(0x000000, 0);

  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(60, window.innerWidth/window.innerHeight, 0.1, 200);
  camera.position.set(0, 0, 30);

  const PARTICLE_COUNT = 800;
  const pGeo = new THREE.BufferGeometry();
  const pPos = new Float32Array(PARTICLE_COUNT * 3);
  const pSpeeds = [];
  for (let i = 0; i < PARTICLE_COUNT; i++) {
    pPos[i*3]   = rand(-40, 40);
    pPos[i*3+1] = rand(-40, 40);
    pPos[i*3+2] = rand(-20, 10);
    pSpeeds.push({ vx: rand(-0.01,0.01), vy: rand(0.005,0.02), drift: rand(-0.005,0.005) });
  }
  pGeo.setAttribute('position', new THREE.BufferAttribute(pPos, 3));
  const pMat = new THREE.PointsMaterial({
    color: 0x8b0000, size: 0.12, transparent: true, opacity: 0.6, sizeAttenuation: true
  });
  const particles = new THREE.Points(pGeo, pMat);
  scene.add(particles);

  const PETAL_COUNT = 120;
  const petalMeshes = [];
  const petalGeo = new THREE.PlaneGeometry(0.6, 0.8);

  function makePetal() {
    const hue = Math.random() > 0.5 ? 0x8b0000 : 0xc0392b;
    const mat = new THREE.MeshBasicMaterial({
      color: hue, transparent: true, opacity: rand(0.3, 0.7), side: THREE.DoubleSide
    });
    const mesh = new THREE.Mesh(petalGeo, mat);
    resetPetal(mesh);
    scene.add(mesh);
    return mesh;
  }

  function resetPetal(m) {
    m.position.set(rand(-35,35), rand(30, 55), rand(-15, 5));
    m.rotation.set(rand(0,Math.PI*2), rand(0,Math.PI*2), rand(0,Math.PI*2));
    m.userData = {
      vy: rand(-0.04, -0.12),
      vx: rand(-0.02, 0.02),
      vz: rand(-0.01, 0.01),
      rx: rand(0.005, 0.02),
      ry: rand(0.003, 0.015),
      rz: rand(0.002, 0.01),
    };
  }

  for (let i = 0; i < PETAL_COUNT; i++) petalMeshes.push(makePetal());

  const FLOWER_COUNT = 30;
  const flowerMeshes = [];
  const flowerGeo = new THREE.CircleGeometry(0.5, 8);
  for (let i = 0; i < FLOWER_COUNT; i++) {
    const mat = new THREE.MeshBasicMaterial({
      color: 0xe8354a, transparent: true, opacity: rand(0.2, 0.5), side: THREE.DoubleSide
    });
    const m = new THREE.Mesh(flowerGeo, mat);
    m.position.set(rand(-35,35), rand(30, 60), rand(-15, 5));
    m.userData = { vy: rand(-0.03,-0.08), vx: rand(-0.015,0.015), rx: rand(0.003,0.012), ry: rand(0.002,0.01) };
    scene.add(m);
    flowerMeshes.push(m);
  }

  let mouseX = 0, mouseY = 0;
  document.addEventListener('mousemove', e => {
    mouseX = (e.clientX/window.innerWidth - 0.5) * 2;
    mouseY = (e.clientY/window.innerHeight - 0.5) * 2;
  });

  let t = 0;
  function animate() {
    requestAnimationFrame(animate);
    t += 0.005;

    camera.position.x += (mouseX * 2 - camera.position.x) * 0.02;
    camera.position.y += (-mouseY * 1.5 - camera.position.y) * 0.02;
    camera.position.z = 30 + Math.sin(t * 0.3) * 1;
    camera.lookAt(0, 0, 0);

    const pos = particles.geometry.attributes.position.array;
    for (let i = 0; i < PARTICLE_COUNT; i++) {
      const s = pSpeeds[i];
      pos[i*3]   += s.vx + Math.sin(t + i) * 0.002;
      pos[i*3+1] += s.vy;
      pos[i*3+2] += s.drift;
      if (pos[i*3+1] > 45) { pos[i*3+1] = -45; pos[i*3] = rand(-40,40); }
      if (pos[i*3+1] < -45) pos[i*3+1] = 45;
    }
    particles.geometry.attributes.position.needsUpdate = true;

    petalMeshes.forEach(m => {
      const d = m.userData;
      m.position.y += d.vy;
      m.position.x += d.vx + Math.sin(t + m.position.z) * 0.01;
      m.position.z += d.vz;
      m.rotation.x += d.rx;
      m.rotation.y += d.ry;
      m.rotation.z += d.rz;
      if (m.position.y < -35) resetPetal(m);
    });

    flowerMeshes.forEach(m => {
      m.position.y += m.userData.vy;
      m.position.x += m.userData.vx + Math.cos(t * 0.7 + m.position.z) * 0.008;
      m.rotation.x += m.userData.rx;
      m.rotation.y += m.userData.ry;
      if (m.position.y < -35) {
        m.position.y = rand(30, 60);
        m.position.x = rand(-35, 35);
      }
    });

    renderer.render(scene, camera);
  }
  animate();

  window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth/window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });
})();

(function initOpening() {
  const texts = [
    "There's something I've been wanting to tell you...",
  ];
  const el = document.getElementById('typed-text');
  const enterBtn = document.getElementById('enter-btn');
  let charIdx = 0;
  const full = texts[0];

  function type() {
    if (charIdx < full.length) {
      el.textContent += full[charIdx++];
      setTimeout(type, rand(40, 90));
    } else {
      setTimeout(() => enterBtn.classList.add('visible'), 600);
    }
  }

  setTimeout(type, 1200);

  enterBtn.addEventListener('click', () => {
    document.getElementById('opening').classList.add('fade-out');
    
    // Fixed: Try to play music automatically once user interacts by clicking "Continue"
    const audio = document.getElementById('bgm');
    const musicBtn = document.getElementById('music-btn');
    audio.play().then(() => {
      musicBtn.textContent = '♪ MUSIC ON';
    }).catch((err) => {
      console.log("Autoplay blocked, waiting for manual toggle.");
      musicBtn.textContent = '♪ MUSIC OFF';
    });

    setTimeout(() => {
      document.getElementById('opening').style.display = 'none';
      document.getElementById('main').classList.add('visible');
      document.getElementById('scroll-hint').classList.add('show');
      musicBtn.classList.add('show');
    }, 1600);
  });
})();

(function initReveal() {
  const cards = document.querySelectorAll('[data-reveal]');
  const obs = new IntersectionObserver((entries) => {
    entries.forEach(e => {
      if (e.isIntersecting) e.target.classList.add('in-view');
    });
  }, { threshold: 0.2 });
  cards.forEach(c => obs.observe(c));

  const proposalObs = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting) {
      setTimeout(() => {
        document.getElementById('proposal-text').classList.add('visible');
        document.getElementById('btn-row').classList.add('visible');
        startHeartCanvas();
      }, 400);
    }
  }, { threshold: 0.3 });
  proposalObs.observe(document.getElementById('proposal'));

  window.addEventListener('scroll', () => {
    if (window.scrollY > 100) document.getElementById('scroll-hint').classList.remove('show');
  }, { passive: true });
})();

let heartStarted = false;
function startHeartCanvas() {
  if (heartStarted) return;
  heartStarted = true;

  const canvas = document.getElementById('heart-canvas');
  const ctx = canvas.getContext('2d');
  
  // Fixed dynamic sizing for high DPI screens
  const dpr = window.devicePixelRatio || 1;
  const rect = canvas.getBoundingClientRect();
  canvas.width = rect.width * dpr;
  canvas.height = rect.height * dpr;
  ctx.scale(dpr, dpr);

  const W = rect.width;
  const H = rect.height;
  const cx = W/2, cy = H/2;

  function heartX(t) { return 16*Math.pow(Math.sin(t),3); }
  function heartY(t) { return -(13*Math.cos(t)-5*Math.cos(2*t)-2*Math.cos(3*t)-Math.cos(4*t)); }

  const scale = W * 0.024;

  const DOTS = 600;
  const dots = [];
  for (let i = 0; i < DOTS; i++) {
    const t = rand(0, Math.PI*2);
    const r = rand(0.7, 1.0);
    const tx = heartX(t)*scale*r + cx;
    const ty = heartY(t)*scale*r + cy;
    dots.push({
      x: cx + rand(-W/2, W/2),
      y: cy + rand(-H/2, H/2),
      tx, ty,
      vx: 0, vy: 0,
      size: rand(1.5, 3.5),
      hue: Math.random() > 0.6 ? '#e8354a' : '#8b0000',
      alpha: 0,
      delay: rand(0, 1500),
      arrived: false
    });
  }

  const flPetals = Array.from({length:40}

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>ASCII Spinning Donut 🍩</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Fira+Code:wght@400;700&display=swap');

    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --bg: #0a0a0f;
      --panel: #10101a;
      --border: #1e1e2e;
      --accent: #7c3aed;
      --accent2: #06b6d4;
      --text: #e2e8f0;
      --muted: #64748b;
      --glow: rgba(124,58,237,0.3);
    }

    html, body {
      height: 100%;
      background: var(--bg);
      color: var(--text);
      font-family: 'Fira Code', monospace;
      overflow: hidden;
    }

    /* Animated background grid */
    body::before {
      content: '';
      position: fixed;
      inset: 0;
      background-image:
        linear-gradient(rgba(124,58,237,0.03) 1px, transparent 1px),
        linear-gradient(90deg, rgba(124,58,237,0.03) 1px, transparent 1px);
      background-size: 40px 40px;
      pointer-events: none;
      z-index: 0;
    }

    .wrapper {
      position: relative;
      z-index: 1;
      height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 16px;
      padding: 16px;
    }

    /* ── Header ── */
    .header {
      display: flex;
      align-items: center;
      gap: 14px;
    }
    .header-icon {
      font-size: 2rem;
      filter: drop-shadow(0 0 12px #f97316);
      animation: pulse-icon 2s ease-in-out infinite;
    }
    @keyframes pulse-icon {
      0%,100%{ transform: scale(1); }
      50%{ transform: scale(1.15) rotate(5deg); }
    }
    .header-title {
      font-size: clamp(1.1rem, 2.5vw, 1.7rem);
      font-weight: 700;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      background: linear-gradient(90deg, #a78bfa, #38bdf8, #34d399, #fb923c, #f472b6, #a78bfa);
      background-size: 300% 100%;
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      animation: rainbow-title 4s linear infinite;
    }
    @keyframes rainbow-title {
      0%{ background-position: 0% 50%; }
      100%{ background-position: 300% 50%; }
    }
    .header-sub {
      font-size: 0.7rem;
      color: var(--muted);
      letter-spacing: 0.2em;
      text-transform: uppercase;
      margin-top: 2px;
    }

    /* ── Terminal Container ── */
    .terminal-shell {
      position: relative;
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: 14px;
      overflow: hidden;
      box-shadow:
        0 0 0 1px rgba(124,58,237,0.15),
        0 0 40px rgba(124,58,237,0.15),
        0 0 80px rgba(6,182,212,0.07),
        0 25px 60px rgba(0,0,0,0.6);
    }

    /* Animated border glow */
    .terminal-shell::before {
      content:'';
      position:absolute;
      inset:-2px;
      border-radius:16px;
      background: conic-gradient(
        from var(--angle,0deg),
        #7c3aed, #06b6d4, #34d399, #fb923c, #f472b6, #7c3aed
      );
      z-index:-1;
      animation: spin-border 4s linear infinite;
      opacity: 0.5;
    }
    @property --angle { syntax:'<angle>'; initial-value:0deg; inherits:false; }
    @keyframes spin-border {
      to{ --angle: 360deg; }
    }

    /* Title bar */
    .titlebar {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 10px 16px;
      background: rgba(255,255,255,0.03);
      border-bottom: 1px solid var(--border);
    }
    .dot { width:12px;height:12px;border-radius:50%; flex-shrink:0; }
    .dot-r{ background:#ff5f57; box-shadow:0 0 6px #ff5f57; }
    .dot-y{ background:#febc2e; box-shadow:0 0 6px #febc2e; }
    .dot-g{ background:#28c840; box-shadow:0 0 6px #28c840; }
    .titlebar-label {
      flex: 1;
      text-align: center;
      font-size: 0.72rem;
      color: var(--muted);
      letter-spacing: 0.1em;
      margin-left: -60px;
    }

    /* Canvas */
    #canvas {
      display: block;
      padding: 8px 12px 12px;
      font-family: 'Share Tech Mono', 'Courier New', monospace;
      font-size: clamp(7px, 1.1vw, 13px);
      line-height: 1.18;
      letter-spacing: 0.06em;
      white-space: pre;
      user-select: none;
      min-width: 200px;
    }

    /* Scanline overlay */
    .scanlines {
      position: absolute;
      inset: 0;
      background: repeating-linear-gradient(
        0deg,
        transparent,
        transparent 2px,
        rgba(0,0,0,0.04) 2px,
        rgba(0,0,0,0.04) 4px
      );
      pointer-events: none;
      border-radius: 14px;
    }

    /* Corner decorations */
    .corner {
      position: absolute;
      width: 16px; height: 16px;
      border-color: var(--accent);
      border-style: solid;
      opacity: 0.5;
    }
    .corner-tl { top:6px;left:6px; border-width:2px 0 0 2px; }
    .corner-tr { top:6px;right:6px; border-width:2px 2px 0 0; }
    .corner-bl { bottom:6px;left:6px; border-width:0 0 2px 2px; }
    .corner-br { bottom:6px;right:6px; border-width:0 2px 2px 0; }

    /* ── Controls ── */
    .controls {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 10px;
    }

    .ctrl-group {
      display: flex;
      align-items: center;
      gap: 8px;
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: 10px;
      padding: 8px 14px;
    }
    .ctrl-label {
      font-size: 0.65rem;
      color: var(--muted);
      text-transform: uppercase;
      letter-spacing: 0.12em;
      white-space: nowrap;
    }

    input[type=range] {
      -webkit-appearance: none;
      width: 90px;
      height: 4px;
      border-radius: 2px;
      background: var(--border);
      outline: none;
      cursor: pointer;
    }
    input[type=range]::-webkit-slider-thumb {
      -webkit-appearance: none;
      width: 14px; height: 14px;
      border-radius: 50%;
      background: var(--accent);
      box-shadow: 0 0 8px var(--glow);
      cursor: pointer;
    }
    input[type=range]:focus::-webkit-slider-thumb {
      box-shadow: 0 0 0 3px rgba(124,58,237,0.3);
    }

    .btn {
      font-family: inherit;
      font-size: 0.68rem;
      letter-spacing: 0.08em;
      text-transform: uppercase;
      padding: 6px 14px;
      border-radius: 7px;
      border: 1px solid var(--border);
      background: rgba(255,255,255,0.04);
      color: var(--text);
      cursor: pointer;
      transition: all 0.2s;
      white-space: nowrap;
    }
    .btn:hover { background: rgba(124,58,237,0.15); border-color: var(--accent); color: #a78bfa; box-shadow: 0 0 10px var(--glow); }
    .btn.active { background: rgba(124,58,237,0.25); border-color: var(--accent); color: #c4b5fd; box-shadow: 0 0 14px var(--glow); }

    /* ── Stats bar ── */
    .stats {
      display: flex;
      gap: 20px;
      font-size: 0.64rem;
      color: var(--muted);
      letter-spacing: 0.1em;
    }
    .stat { display:flex; gap:6px; align-items:center; }
    .stat-val { color: var(--accent2); font-weight:700; min-width:36px; }

    /* ── Responsive ── */
    @media (max-width: 600px) {
      .controls { gap: 6px; }
      .ctrl-group { padding: 6px 10px; }
      input[type=range] { width: 65px; }
    }
  </style>
</head>
<body>
<div class="wrapper">

  <!-- Header -->
  <div class="header">
    <span class="header-icon">🍩</span>
    <div>
      <div class="header-title">ASCII Spinning Donut</div>
      <div class="header-sub">Real-time 3D renderer · ASCII shading · ANSI colors</div>
    </div>
  </div>

  <!-- Terminal -->
  <div class="terminal-shell">
    <div class="titlebar">
      <span class="dot dot-r"></span>
      <span class="dot dot-y"></span>
      <span class="dot dot-g"></span>
      <span class="titlebar-label">donut.js — bash — 80×40</span>
    </div>
    <div id="canvas" aria-label="ASCII Donut Animation"></div>
    <div class="scanlines"></div>
    <div class="corner corner-tl"></div>
    <div class="corner corner-tr"></div>
    <div class="corner corner-bl"></div>
    <div class="corner corner-br"></div>
  </div>

  <!-- Controls -->
  <div class="controls">

    <div class="ctrl-group">
      <span class="ctrl-label">Speed</span>
      <input type="range" id="speedSlider" min="0.2" max="4" step="0.1" value="1.0">
    </div>

    <div class="ctrl-group">
      <span class="ctrl-label">Size</span>
      <input type="range" id="sizeSlider" min="0.5" max="1.8" step="0.05" value="1.0">
    </div>

    <div class="ctrl-group">
      <span class="ctrl-label">Color Mode</span>
      <button class="btn active" id="btnRainbow">Rainbow</button>
      <button class="btn" id="btnMono">Mono</button>
      <button class="btn" id="btnFire">Fire</button>
      <button class="btn" id="btnMatrix">Matrix</button>
      <button class="btn" id="btnIce">Ice</button>
    </div>

    <div class="ctrl-group">
      <span class="ctrl-label">FPS</span>
      <input type="range" id="fpsSlider" min="10" max="60" step="1" value="30">
      <span class="stat-val" id="fpsDisplay" style="min-width:30px">30</span>
    </div>

    <button class="btn" id="btnPause">⏸ Pause</button>
    <button class="btn" id="btnReset">↺ Reset</button>

  </div>

  <!-- Stats -->
  <div class="stats">
    <div class="stat"><span>FRAME</span><span class="stat-val" id="statFrame">0</span></div>
    <div class="stat"><span>A</span><span class="stat-val" id="statA">0.00</span></div>
    <div class="stat"><span>B</span><span class="stat-val" id="statB">0.00</span></div>
    <div class="stat"><span>FPS</span><span class="stat-val" id="statFps">—</span></div>
  </div>

</div>

<script>
(() => {
  'use strict';

  /* ── Config ── */
  const COLS   = 80;
  const ROWS   = 40;
  const R1     = 1.0;   // tube radius
  const R2     = 2.0;   // distance from centre
  const K2     = 5;     // distance to projection plane
  const CHARS  = '.,-~:;=!*#$@';   // brightness chars (darkest → brightest)

  /* ── State ── */
  let A = 0, B = 0;
  let frameCount = 0;
  let paused = false;
  let colorMode = 'rainbow';
  let speedMult = 1.0;
  let sizeMult  = 1.0;
  let targetFps = 30;
  let lastTime  = 0;
  let lastFpsTime = 0;
  let fpsFrames = 0;
  let displayedFps = 0;
  let animId = null;
  let hueOffset = 0;

  /* ── DOM ── */
  const canvas      = document.getElementById('canvas');
  const speedSlider = document.getElementById('speedSlider');
  const sizeSlider  = document.getElementById('sizeSlider');
  const fpsSlider   = document.getElementById('fpsSlider');
  const fpsDisplay  = document.getElementById('fpsDisplay');
  const btnPause    = document.getElementById('btnPause');
  const btnReset    = document.getElementById('btnReset');
  const statFrame   = document.getElementById('statFrame');
  const statA       = document.getElementById('statA');
  const statB       = document.getElementById('statB');
  const statFps     = document.getElementById('statFps');

  /* Color palette functions */
  const colorModes = {
    rainbow: (lum, x, y, t) => {
      // Hue cycles around the torus based on angle & time
      const h = ((x / COLS) * 360 + (y / ROWS) * 180 + hueOffset) % 360;
      const s = 90 + lum * 10;
      const l = 25 + lum * 50;
      return `hsl(${h},${s}%,${l}%)`;
    },
    mono: (lum) => {
      const v = Math.round(40 + lum * 215);
      return `rgb(${v},${v},${v})`;
    },
    fire: (lum) => {
      // dark → red → orange → yellow → white
      const r = Math.min(255, Math.round(lum * 510));
      const g = Math.min(255, Math.max(0, Math.round(lum * 510 - 255)));
      const b = Math.min(255, Math.max(0, Math.round(lum * 510 - 460)));
      return `rgb(${r},${g},${b})`;
    },
    matrix: (lum) => {
      const v = Math.round(40 + lum * 215);
      return `rgb(0,${v},${Math.round(v * 0.3)})`;
    },
    ice: (lum, x, y, t) => {
      const h = 185 + Math.sin(t * 0.001 + x * 0.1) * 25;
      const l = 25 + lum * 55;
      return `hsl(${h},80%,${l}%)`;
    }
  };

  /* ── Core Renderer ── */
  function renderFrame() {
    const K1 = (COLS * K2 * 3) / (8 * (R1 + R2)) * sizeMult;

    // Buffers
    const output = new Array(COLS * ROWS).fill(' ');
    const zbuf   = new Float32Array(COLS * ROWS).fill(0);
    const lbuf   = new Float32Array(COLS * ROWS).fill(-1);

    // Precompute rotation matrices
    const cosA = Math.cos(A), sinA = Math.sin(A);
    const cosB = Math.cos(B), sinB = Math.sin(B);

    // Sample the torus surface
    for (let theta = 0; theta < Math.PI * 2; theta += 0.04) {
      const cosTheta = Math.cos(theta), sinTheta = Math.sin(theta);

      for (let phi = 0; phi < Math.PI * 2; phi += 0.015) {
        const cosPhi = Math.cos(phi), sinPhi = Math.sin(phi);

        // Circle of the torus
        const cx = R2 + R1 * cosTheta;
        const cy = R1 * sinTheta;

        // 3D point on torus after rotation
        // Rotate around Y-axis by B, then around X-axis by A
        const x = cx * (cosB * cosPhi + sinA * sinB * sinPhi) - cy * cosA * sinB;
        const y = cx * (sinB * cosPhi - sinA * cosB * sinPhi) + cy * cosA * cosB;
        const z = K2 + cosA * cx * sinPhi + cy * sinA;
        const ooz = 1 / z; // one over z

        // Project onto screen
        const xp = Math.round(COLS / 2 + K1 * ooz * x);
        const yp = Math.round(ROWS / 2 - K1 * ooz * y * 0.55);

        if (xp < 0 || xp >= COLS || yp < 0 || yp >= ROWS) continue;
        const idx = yp * COLS + xp;

        // Surface normal (for lighting)
        const nx = cosTheta * (cosB * cosPhi + sinA * sinB * sinPhi) - sinTheta * cosA * sinB;
        const ny = cosTheta * (sinB * cosPhi - sinA * cosB * sinPhi) + sinTheta * cosA * cosB;
        const nz = cosA * cosTheta * sinPhi + sinTheta * sinA;

        // Light direction (slightly above and to the right)
        const lx = 0.6, ly = 0.6, lz = -0.5;
        const lMag = Math.sqrt(lx*lx+ly*ly+lz*lz);
        const nMag = Math.sqrt(nx*nx+ny*ny+nz*nz);
        const luminance = (nx*lx + ny*ly + nz*lz) / (lMag * nMag);

        // Z-buffering
        if (ooz > zbuf[idx]) {
          zbuf[idx] = ooz;
          lbuf[idx] = luminance;
          // Pick ASCII char
          const ci = Math.max(0, Math.floor(luminance * CHARS.length));
          output[idx] = CHARS[Math.min(ci, CHARS.length - 1)];
        }
      }
    }

    return { output, zbuf, lbuf };
  }

  /* ── Build HTML ── */
  function buildHtml(output, lbuf, t) {
    let html = '';
    for (let row = 0; row < ROWS; row++) {
      for (let col = 0; col < COLS; col++) {
        const idx = row * COLS + col;
        const ch  = output[idx];
        if (ch === ' ') {
          html += '<span> </span>';
        } else {
          const lum = Math.max(0, lbuf[idx]);
          const color = colorModes[colorMode](lum, col, row, t);
          // Extra glow on bright chars
          const bright = lum > 0.7;
          html += `<span style="color:${color};${bright ? `text-shadow:0 0 4px ${color}` : ''}">${ch}</span>`;
        }
      }
      if (row < ROWS - 1) html += '\n';
    }
    return html;
  }

  /* ── Animation loop ── */
  function loop(ts) {
    const interval = 1000 / targetFps;
    const delta = ts - lastTime;

    if (delta >= interval) {
      lastTime = ts - (delta % interval);

      if (!paused) {
        const speed = 0.04 * speedMult;
        A += speed * 0.7;
        B += speed;
        hueOffset = (hueOffset + 1.5 * speedMult) % 360;
        frameCount++;

        const { output, zbuf, lbuf } = renderFrame();
        canvas.innerHTML = buildHtml(output, lbuf, ts);

        // Stats
        statFrame.textContent = frameCount;
        statA.textContent = (A % (Math.PI * 2)).toFixed(2);
        statB.textContent = (B % (Math.PI * 2)).toFixed(2);

        // FPS counter
        fpsFrames++;
        if (ts - lastFpsTime >= 600) {
          displayedFps = Math.round(fpsFrames / ((ts - lastFpsTime) / 1000));
          statFps.textContent = displayedFps;
          fpsFrames = 0;
          lastFpsTime = ts;
        }
      }
    }

    animId = requestAnimationFrame(loop);
  }

  /* ── Controls ── */
  speedSlider.addEventListener('input', () => {
    speedMult = parseFloat(speedSlider.value);
  });

  sizeSlider.addEventListener('input', () => {
    sizeMult = parseFloat(sizeSlider.value);
  });

  fpsSlider.addEventListener('input', () => {
    targetFps = parseInt(fpsSlider.value);
    fpsDisplay.textContent = targetFps;
  });

  btnPause.addEventListener('click', () => {
    paused = !paused;
    btnPause.textContent = paused ? '▶ Resume' : '⏸ Pause';
    btnPause.classList.toggle('active', paused);
    if (!paused) lastFpsTime = performance.now(); // reset fps timer
  });

  btnReset.addEventListener('click', () => {
    A = 0; B = 0; hueOffset = 0; frameCount = 0;
    statFrame.textContent = 0;
  });

  /* Color mode buttons */
  document.querySelectorAll('[id^=btn]').forEach(btn => {
    const modes = ['Rainbow','Mono','Fire','Matrix','Ice'];
    modes.forEach(m => {
      const b = document.getElementById(`btn${m}`);
      if (!b) return;
      b.addEventListener('click', () => {
        colorMode = m.toLowerCase();
        document.querySelectorAll('[id^=btn]:not(#btnPause):not(#btnReset)').forEach(x => x.classList.remove('active'));
        b.classList.add('active');
      });
    });
  });

  /* Keyboard shortcuts */
  window.addEventListener('keydown', e => {
    switch (e.key.toLowerCase()) {
      case ' ': e.preventDefault(); btnPause.click(); break;
      case 'r': btnReset.click(); break;
      case 'arrowup':
        speedSlider.value = Math.min(4, parseFloat(speedSlider.value) + 0.1);
        speedMult = parseFloat(speedSlider.value);
        break;
      case 'arrowdown':
        speedSlider.value = Math.max(0.2, parseFloat(speedSlider.value) - 0.1);
        speedMult = parseFloat(speedSlider.value);
        break;
      case '1': document.getElementById('btnRainbow').click(); break;
      case '2': document.getElementById('btnMono').click(); break;
      case '3': document.getElementById('btnFire').click(); break;
      case '4': document.getElementById('btnMatrix').click(); break;
      case '5': document.getElementById('btnIce').click(); break;
    }
  });

  /* Start */
  lastFpsTime = performance.now();
  animId = requestAnimationFrame(loop);
})();
</script>
</body>
</html>

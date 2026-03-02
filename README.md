# Trianguinity
A cartesian map (x and y axis) that helps users point and find triangles
[index.html](https://github.com/user-attachments/files/25682160/index.html)
[index.html](https://github.com/user-attachments/files/25682160/index.html)
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Trianguinity — Interactive Geometry</title>
<style>
  :root {
    --bg: #0e1116;
    --panel: #151a23;
    --text: #e7edf3;
    --muted: #9fb0c6;
    --accent: #4cc9f0;
    --accent-2: #ffd166;
    --danger: #ff6b6b;
    --ok: #06d6a0;
    --card: #0f1420cc; /* translucent overlay */
    --border: #273246;
  }
  * { box-sizing: border-box; }
  body {
    margin: 0; font-family: system-ui, Segoe UI, Roboto, Arial, sans-serif;
    color: var(--text); background: var(--bg); display: grid;
    grid-template-columns: 1fr 360px; min-height: 100vh;
  }
  @media (max-width: 900px) { body { grid-template-columns: 1fr; } }
  .board-wrap { position: relative; display: grid; place-items: center; padding: 16px; }
  #board {
    background: #0c0f14; border: 1px solid #1e2633; width: 100%; max-width: 900px;
    aspect-ratio: 16/10; border-radius: 10px; touch-action: none;
  }
  /* ===== Branding (Top Left) ===== */
  .brand {
    position: absolute; left: 28px; top: 24px; z-index: 10;
    font-weight: 800; letter-spacing: 1px;
    background: linear-gradient(90deg, #a0c4ff, #4cc9f0, #ffd166);
    -webkit-background-clip: text; background-clip: text; color: transparent;
    text-shadow: 0 2px 16px rgba(76, 201, 240, 0.25);
    font-size: clamp(18px, 2.2vw, 24px);
  }

  aside {
    background: var(--panel); padding: 16px 16px 20px; border-left: 1px solid #1e2633;
  }
  h1 { font-size: 20px; margin: 0 0 8px; }
  p, li { color: var(--muted); line-height: 1.5; }
  .controls { display: flex; gap: 8px; flex-wrap: wrap; margin: 10px 0 12px; }
  .btn {
    background: #1c2431; border: 1px solid var(--border); color: var(--text);
    padding: 8px 12px; border-radius: 8px; cursor: pointer; font-weight: 600;
  }
  .btn:hover { border-color: var(--accent); color: var(--accent); }
  .btn-danger:hover { border-color: var(--danger); color: var(--danger); }
  .toggle { display: inline-flex; align-items: center; gap: 8px; cursor: pointer; }
  .pill {
    display: inline-block; padding: 2px 8px; border-radius: 999px; font-size: 12px;
    border: 1px solid var(--border); background: #121722; color: var(--muted);
  }
  .results {
    margin-top: 10px; padding: 12px; border-radius: 10px; background: #101520; border: 1px solid #1e2633;
  }
  .results .ok { color: var(--ok); font-weight: 700; }
  .results .no { color: var(--danger); font-weight: 700; }
  .metric { display: grid; grid-template-columns: 120px 1fr; gap: 8px; margin: 6px 0; }
  code { color: var(--accent-2); }
  .legend { display: flex; gap: 10px; margin-top: 10px; flex-wrap: wrap; }
  .dot { width: 10px; height: 10px; border-radius: 50%; display: inline-block; }
  .A { background: #ff595e; } .B { background: #8ac926; } .C { background: #1982c4; }
  .line { height: 10px; width: 20px; border-top: 3px solid var(--muted); align-self: center; }
  footer { margin-top: 14px; font-size: 12px; color: var(--muted); }

  /* ===== Overlay cards (Bottom Left) ===== */
  .overlay {
    position: absolute; left: 24px; bottom: 24px; z-index: 9;
    display: grid; gap: 10px; width: min(420px, 45vw);
  }
  .card {
    background: var(--card); border: 1px solid var(--border);
    padding: 12px; border-radius: 12px; backdrop-filter: blur(6px);
    box-shadow: 0 6px 24px rgba(0,0,0,0.25);
  }
  .card h3 { margin: 0 0 8px; font-size: 16px; }
  .row { display: flex; gap: 8px; align-items: center; flex-wrap: wrap; }
  .card input[type="text"], .card input[type="number"] {
    flex: 1; min-width: 180px; padding: 8px 10px; border-radius: 8px;
    border: 1px solid var(--border); background: #0e1320; color: var(--text);
  }
  .small { font-size: 12px; color: var(--muted); }
  .badge { display:inline-block; padding: 2px 8px; border-radius: 999px; background:#121722; border:1px solid var(--border); font-size:12px; color:var(--muted); }
  .mc { display: grid; gap: 6px; margin: 8px 0; }
  .mc label { display: flex; gap: 8px; align-items: center; cursor: pointer; }
  .feedback { margin-top: 6px; font-weight: 700; }
  .feedback.ok { color: var(--ok); }
  .feedback.no { color: var(--danger); }
  .hr { border:0; border-top:1px solid var(--border); margin:10px 0; }
  .right-actions { margin-left: auto; display: inline-flex; gap: 8px; }
</style>
</head>
<body>
  <div class="board-wrap">
    <!-- Brand -->
    <div class="brand">[ Trianguinity ]</div>

    <!-- Canvas -->
    <canvas id="board" width="1280" height="800" aria-label="Interactive coordinate grid"></canvas>

    <!-- Bottom-left overlays -->
    <div class="overlay">
      <!-- Quiz Card -->
      <section class="card" id="quizCard">
        <div class="row" style="justify-content: space-between; align-items: baseline;">
          <h3>Quiz (10 items)</h3>
          <span class="badge" id="quizProgress">0 / 10 · Score: 0</span>
        </div>
        <div id="quizQ" class="small" style="margin-bottom:8px;">Click <b>Start</b> to begin a randomized set.</div>
        <div id="quizInputArea"></div>
        <div class="row" style="margin-top:8px; gap:8px;">
          <button class="btn" id="quizStartBtn">Start</button>
          <button class="btn" id="quizSubmitBtn" disabled>Submit</button>
          <button class="btn" id="quizNextBtn" disabled>Next</button>
          <div class="small" id="quizFeedback"></div>
        </div>
      </section>

      <!-- Calculator Card -->
      <section class="card" id="calcCard">
        <h3>Equation Solver</h3>
        <div class="small">
          • Solve linear/quadratic in <b>x</b> (e.g., <code>2x+3=11</code>, <code>x^2-9=0</code>)<br/>
          • Or evaluate numeric expressions (e.g., <code>3*(2+5)/7 + 1.25</code>)
        </div>
        <div class="row" style="margin-top:8px;">
          <input type="text" id="calcInput" placeholder="Enter equation or expression…" />
          <button class="btn" id="calcSolveBtn">Solve</button>
        </div>
        <div class="small" style="margin-top:6px;">
          Allowed ops (numeric): <code>+ - * / ^ ( )</code>. Equation parser supports <code>x</code>, <code>x^2</code>, numeric coefficients.
        </div>
        <div id="calcOutput" class="feedback" style="min-height: 20px;"></div>
      </section>
    </div>
  </div>

  <aside>
    <h1>Geometry Playground</h1>
    <p>Click on the grid to place up to <b>3 points</b>: <b>A</b>, <b>B</b>, <b>C</b>. Toggle <i>Snap</i> for exact integer coordinates.</p>
    <div class="controls">
      <button id="undoBtn" class="btn" title="Remove the last point">Undo</button>
      <button id="clearBtn" class="btn btn-danger" title="Remove all points">Clear</button>
      <label class="toggle pill" title="Snap to integer grid">
        <input type="checkbox" id="snapChk" />
        <span>Snap</span>
      </label>
    </div>

    <div class="legend">
      <span><span class="dot A"></span> A</span>
      <span><span class="dot B"></span> B</span>
      <span><span class="dot C"></span> C</span>
      <span class="line" title="Edges"></span> Edges
    </div>

    <div class="results" id="results">
      <div class="metric"><div><b>A</b>:</div><div>(—, —)</div></div>
      <div class="metric"><div><b>B</b>:</div><div>(—, —)</div></div>
      <div class="metric"><div><b>C</b>:</div><div>(—, —)</div></div>
      <hr class="hr" />
      <div class="metric"><div>Triangle?</div><div>—</div></div>
      <div class="metric"><div>Type</div><div>—</div></div>
      <div class="metric"><div>Side lengths</div><div>—</div></div>
      <div class="metric"><div>Area</div><div>—</div></div>
    </div>

    <footer>
      Tips: drag near a point to move it (desktop); use <b>Undo</b>/<b>Clear</b> to reset.  
      Coordinates use a standard Cartesian plane with the origin at the center.
    </footer>
  </aside>

<script>
(function() {
  // ======== Configuration ========
  const canvas = document.getElementById('board');
  const ctx = canvas.getContext('2d');

  // Math viewport ↔ canvas mapping
  const scale = 40;         // pixels per 1 unit on the grid
  let origin = {            // origin in canvas pixels (center the axes)
    x: canvas.width / 2,
    y: canvas.height / 2
  };

  const grid = {
    minor: scale,           // minor grid spacing (1 unit)
    majorEvery: 5,          // every N minors draw a thicker major grid line
    colorMinor: '#132032',
    colorMajor: '#1c2a40',
    colorAxes:  '#2e3e5a',
  };

  const colors = {
    A: '#ff595e',
    B: '#8ac926',
    C: '#1982c4',
    edge: '#a7b4c7',
    text: '#e7edf3',
    shadow: 'rgba(0,0,0,0.35)',
  };

  const pointRadius = 6;
  const hitRadiusPx = 12; // for dragging

  const state = {
    points: [], // {x, y} in math units; length 0..3
    draggingIdx: -1,
    snap: false,
  };

  // ======== Utilities: coordinates ========
  const mathToCanvas = ({x, y}) => ({ x: origin.x + x * scale, y: origin.y - y * scale });
  const canvasToMath = ({x, y}) => ({ x: (x - origin.x) / scale, y: (origin.y - y) / scale });

  // ======== Drawing grid & axes ========
  function drawGrid() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Fill background
    ctx.fillStyle = '#0c0f14';
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Minor and major grid lines
    ctx.lineWidth = 1;
    for (let dir of ['x', 'y']) {
      const max = dir === 'x' ? canvas.width : canvas.height;
      const originPx = dir === 'x' ? origin.x : origin.y;
      const count = Math.ceil(max / grid.minor) + 2;

      for (let i = -count; i <= count; i++) {
        const offset = i * grid.minor + (dir === 'x' ? 0 : 0);
        const pos = Math.round(originPx + offset);

        const isMajor = (i % grid.majorEvery === 0);
        ctx.strokeStyle = isMajor ? grid.colorMajor : grid.colorMinor;

        ctx.beginPath();
        if (dir === 'x') {
          ctx.moveTo(pos, 0); ctx.lineTo(pos, canvas.height);
        } else {
          ctx.moveTo(0, pos); ctx.lineTo(canvas.width, pos);
        }
        ctx.stroke();
      }
    }

    // Axes
    ctx.strokeStyle = grid.colorAxes;
    ctx.lineWidth = 2;
    // y-axis
    ctx.beginPath(); ctx.moveTo(origin.x, 0); ctx.lineTo(origin.x, canvas.height); ctx.stroke();
    // x-axis
    ctx.beginPath(); ctx.moveTo(0, origin.y); ctx.lineTo(canvas.width, origin.y); ctx.stroke();

    // Axis ticks/labels (every major)
    ctx.fillStyle = colors.text;
    ctx.font = '12px system-ui, Segoe UI, Roboto, Arial, sans-serif';
    ctx.textAlign = 'center'; ctx.textBaseline = 'top';

    const unitsX = Math.floor(canvas.width / scale / 2);
    for (let ux = -unitsX; ux <= unitsX; ux++) {
      if (ux === 0) continue;
      const px = mathToCanvas({x: ux, y: 0}).x;
      if (ux % grid.majorEvery === 0) {
        ctx.fillText(String(ux), px, origin.y + 4);
      }
    }
    ctx.textAlign = 'right'; ctx.textBaseline = 'middle';
    const unitsY = Math.floor(canvas.height / scale / 2);
    for (let uy = -unitsY; uy <= unitsY; uy++) {
      if (uy === 0) continue;
      const py = mathToCanvas({x: 0, y: uy}).y;
      if (uy % grid.majorEvery === 0) {
        ctx.fillText(String(uy), origin.x - 4, py);
      }
    }
  }

  // ======== Drawing points and triangle ========
  function drawPointsAndEdges() {
    const pts = state.points;

    // Edges (if >= 2 points)
    if (pts.length >= 2) {
      ctx.strokeStyle = colors.edge;
      ctx.lineWidth = 2.5;
      ctx.beginPath();
      const p0 = mathToCanvas(pts[0]);
      ctx.moveTo(p0.x, p0.y);
      for (let i = 1; i < pts.length; i++) {
        const pi = mathToCanvas(pts[i]);
        ctx.lineTo(pi.x, pi.y);
      }
      if (pts.length === 3) {
        const p0again = mathToCanvas(pts[0]);
        ctx.lineTo(p0again.x, p0again.y);
      }
      ctx.stroke();
    }

    // Points
    const labels = ['A', 'B', 'C'];
    const labelColors = { A: colors.A, B: colors.B, C: colors.C };

    for (let i = 0; i < pts.length; i++) {
      const p = mathToCanvas(pts[i]);
      const label = labels[i];
      const fill = labelColors[label];

      // Glow shadow
      ctx.save();
      ctx.shadowColor = colors.shadow; ctx.shadowBlur = 8;
      ctx.beginPath();
      ctx.arc(p.x, p.y, pointRadius, 0, Math.PI * 2);
      ctx.fillStyle = fill; ctx.fill();
      ctx.restore();

      // White inner ring
      ctx.lineWidth = 2;
      ctx.strokeStyle = '#ffffff';
      ctx.beginPath();
      ctx.arc(p.x, p.y, pointRadius, 0, Math.PI * 2);
      ctx.stroke();

      // Label above the point
      ctx.fillStyle = fill;
      ctx.font = 'bold 14px system-ui, Segoe UI, Roboto, Arial, sans-serif';
      ctx.textAlign = 'center'; ctx.textBaseline = 'bottom';
      ctx.fillText(label, p.x, p.y - 8);
    }
  }

  // ======== Geometry helpers ========
  const dist = (p, q) => Math.hypot(p.x - q.x, p.y - q.y);

  function areaTriangle(A, B, C) {
    return 0.5 * Math.abs(
      A.x * (B.y - C.y) +
      B.x * (C.y - A.y) +
      C.x * (A.y - B.y)
    );
  }

  function classifyTriangle(A, B, C) {
    const AB = dist(A, B), BC = dist(B, C), CA = dist(C, A);
    const sides = [AB, BC, CA].sort((a, b) => a - b);
    const [a, b, c] = sides; // a ≤ b ≤ c

    const area = areaTriangle(A, B, C);
    const epsLen = Math.max(1e-6, 1e-6 * c);     // length tolerance
    const epsArea = Math.max(1e-9, 1e-6 * a * b); // area tolerance proportional to sides

    const isTriangle = area > epsArea;
    if (!isTriangle) {
      return {
        isTriangle: false,
        type: 'Not a triangle (points are collinear)',
        sides: { AB, BC, CA },
        area: 0
      };
    }

    const eq = (x, y) => Math.abs(x - y) <= epsLen;
    const isRight = Math.abs(a*a + b*b - c*c) <= Math.max(1e-6, 1e-6 * c*c);
    const isEquilateral = eq(AB, BC) && eq(BC, CA);
    const isIsosceles = isEquilateral || eq(AB, BC) || eq(BC, CA) || eq(CA, AB);

    let type = 'Scalene';
    if (isEquilateral) type = 'Equilateral';
    else if (isIsosceles) type = 'Isosceles';
    if (isRight && !isEquilateral) type = (type === 'Isosceles') ? 'Right Isosceles' : 'Right';

    return {
      isTriangle: true,
      type,
      sides: { AB, BC, CA },
      area
    };
  }

  // ======== Results panel ========
  const resultsEl = document.getElementById('results');
  function fmt(n) { return Number.isFinite(n) ? n.toFixed(3) : '—'; }

  function updateResults() {
    const labels = ['A', 'B', 'C'];
    const coords = labels.map((lab, i) => {
      const p = state.points[i];
      return `<div class="metric"><div><b>${lab}</b>:</div><div>${p ? `(${fmt(p.x)}, ${fmt(p.y)})` : '(—, —)'}</div></div>`;
    }).join('');

    let tri = '<div class="metric"><div>Triangle?</div><div>—</div></div>';
    let typ = '<div class="metric"><div>Type</div><div>—</div></div>';
    let lens = '<div class="metric"><div>Side lengths</div><div>—</div></div>';
    let area = '<div class="metric"><div>Area</div><div>—</div></div>';

    if (state.points.length === 3) {
      const [A, B, C] = state.points;
      const res = classifyTriangle(A, B, C);
      tri = `<div class="metric"><div>Triangle?</div><div>${res.isTriangle ? '<span class="ok">Yes</span>' : '<span class="no">No</span>'}</div></div>`;
      typ = `<div class="metric"><div>Type</div><div>${res.type}</div></div>`;
      lens = `<div class="metric"><div>Side lengths</div><div>AB=${fmt(res.sides.AB)}, BC=${fmt(res.sides.BC)}, CA=${fmt(res.sides.CA)}</div></div>`;
      area = `<div class="metric"><div>Area</div><div>${fmt(res.area)}</div></div>`;
    }

    resultsEl.innerHTML = `
      ${coords}
      <hr class="hr" />
      ${tri}
      ${typ}
      ${lens}
      ${area}
    `;
  }

  // ======== Interaction: click, drag, snap, buttons ========
  function getMousePos(evt) {
    const rect = canvas.getBoundingClientRect();
    return {
      x: (evt.clientX - rect.left) * (canvas.width / rect.width),
      y: (evt.clientY - rect.top)  * (canvas.height / rect.height)
    };
  }

  function nearestPointIndex(mousePx) {
    for (let i = 0; i < state.points.length; i++) {
      const p = mathToCanvas(state.points[i]);
      const d = Math.hypot(p.x - mousePx.x, p.y - mousePx.y);
      if (d <= hitRadiusPx) return i;
    }
    return -1;
  }

  function placeOrStartDrag(evt) {
    const mouse = getMousePos(evt);
    const dragIdx = nearestPointIndex(mouse);
    if (dragIdx >= 0) {
      state.draggingIdx = dragIdx;
      return;
    }
    if (state.points.length >= 3) return;

    let m = canvasToMath(mouse);
    if (state.snap) {
      m = { x: Math.round(m.x), y: Math.round(m.y) };
    } else {
      // Keep 3 decimals for neatness
      m = { x: Math.round(m.x * 1000) / 1000, y: Math.round(m.y * 1000) / 1000 };
    }
    state.points.push(m);
    redraw();
  }

  function drag(evt) {
    if (state.draggingIdx < 0) return;
    const mouse = getMousePos(evt);
    let m = canvasToMath(mouse);
    if (state.snap) {
      m = { x: Math.round(m.x), y: Math.round(m.y) };
    } else {
      m = { x: Math.round(m.x * 1000) / 1000, y: Math.round(m.y * 1000) / 1000 };
    }
    state.points[state.draggingIdx] = m;
    redraw();
  }

  function endDrag() { state.draggingIdx = -1; }

  function redraw() {
    drawGrid();
    drawPointsAndEdges();
    updateResults();
  }

  // Buttons and toggle
  document.getElementById('undoBtn').addEventListener('click', () => {
    state.points.pop();
    redraw();
  });

  document.getElementById('clearBtn').addEventListener('click', () => {
    state.points = [];
    redraw();
  });

  document.getElementById('snapChk').addEventListener('change', (e) => {
    state.snap = e.target.checked;
  });

  // Mouse / touch events
  canvas.addEventListener('mousedown', placeOrStartDrag);
  window.addEventListener('mousemove', drag);
  window.addEventListener('mouseup', endDrag);

  // Touch support
  canvas.addEventListener('touchstart', (e) => {
    e.preventDefault();
    const t = e.changedTouches[0];
    placeOrStartDrag(t);
  }, { passive: false });

  window.addEventListener('touchmove', (e) => {
    e.preventDefault();
    const t = e.changedTouches[0];
    drag(t);
  }, { passive: false });

  window.addEventListener('touchend', (e) => {
    e.preventDefault();
    endDrag();
  }, { passive: false });

  // Initial draw
  redraw();

  // Handle resize: keep crisp canvas and re-center origin
  function resizeCanvasToCSS() {
    const rect = canvas.getBoundingClientRect();
    const dpr = window.devicePixelRatio || 1;
    const needW = Math.round(rect.width * dpr);
    const needH = Math.round(rect.height * dpr);
    if (canvas.width !== needW || canvas.height !== needH) {
      canvas.width = needW; canvas.height = needH;
      origin = { x: canvas.width / 2, y: canvas.height / 2 };
      redraw();
    }
  }
  const ro = new ResizeObserver(resizeCanvasToCSS);
  ro.observe(canvas);

  // ============================================================
  //                       QUIZ MODULE
  // ============================================================
  const quizQEl = document.getElementById('quizQ');
  const quizInputArea = document.getElementById('quizInputArea');
  const quizStartBtn = document.getElementById('quizStartBtn');
  const quizSubmitBtn = document.getElementById('quizSubmitBtn');
  const quizNextBtn = document.getElementById('quizNextBtn');
  const quizFeedbackEl = document.getElementById('quizFeedback');
  const quizProgressEl = document.getElementById('quizProgress');

  const Q_TYPES = ['distance','area','triangle','isosceles','classify']; // variety

  const quizState = {
    idx: 0,
    total: 10,
    score: 0,
    current: null, // {type, data, answer, answer2?, choiceOptions?}
    answered: false
  };

  function randInt(a, b) { // inclusive
    return Math.floor(Math.random() * (b - a + 1)) + a;
  }
  function randPoint() {
    return { x: randInt(-5, 5), y: randInt(-5, 5) };
  }
  function nonCollinearTri() {
    // ensure area > 0
    let A,B,C, area;
    do {
      A = randPoint(); B = randPoint(); C = randPoint();
      area = areaTriangle(A,B,C);
    } while (area < 0.5); // avoid near-collinear small area
    return [A,B,C];
  }
  function maybeCollinearTri() {
    // sometimes force collinear: pick two points and align third on the line
    if (Math.random() < 0.45) {
      const A = randPoint(); const B = randPoint();
      // pick t in [-2,2] integer to place C on line AB: C = A + t(B-A)
      const t = randInt(-2,2);
      const C = { x: A.x + t*(B.x - A.x), y: A.y + t*(B.y - A.y) };
      return [A,B,C];
    } else {
      return nonCollinearTri();
    }
  }

  function genDistanceQ() {
    const P = randPoint(), Q = randPoint();
    const d = Math.hypot(P.x - Q.x, P.y - Q.y);
    return {
      type: 'distance',
      data: { P, Q },
      answer: d
    };
  }

  function genAreaQ() {
    const [A,B,C] = nonCollinearTri();
    return {
      type: 'area',
      data: { A,B,C },
      answer: areaTriangle(A,B,C)
    };
  }

  function genTriangleQ() {
    const [A,B,C] = maybeCollinearTri();
    const isTri = areaTriangle(A,B,C) > 1e-6;
    return {
      type: 'triangle',
      data: { A,B,C },
      answer: isTri ? 'Yes' : 'No'
    };
  }

  function genIsoscelesQ() {
    const [A,B,C] = nonCollinearTri(); // ensure real triangle
    const c = classifyTriangle(A,B,C);
    const isIso = c.type.includes('Isosceles') || c.type === 'Equilateral';
    return {
      type: 'isosceles',
      data: { A,B,C },
      answer: isIso ? 'Yes' : 'No'
    };
  }

  function genClassifyQ() {
    const [A,B,C] = nonCollinearTri();
    const c = classifyTriangle(A,B,C);
    // possible options
    const opts = ['Equilateral','Isosceles','Right','Right Isosceles','Scalene'];
    // Map our returned type into one of the options
    let correct = 'Scalene';
    if (c.type === 'Equilateral') correct = 'Equilateral';
    else if (c.type === 'Right Isosceles') correct = 'Right Isosceles';
    else if (c.type === 'Right') correct = 'Right';
    else if (c.type === 'Isosceles') correct = 'Isosceles';
    return {
      type: 'classify',
      data: { A,B,C },
      answer: correct,
      options: opts
    };
  }

  function newQuestion() {
    quizState.answered = false;
    quizFeedbackEl.textContent = '';
    quizSubmitBtn.disabled = false;
    quizNextBtn.disabled = true;

    const t = Q_TYPES[randInt(0, Q_TYPES.length - 1)];
    let q;
    if (t === 'distance') q = genDistanceQ();
    else if (t === 'area') q = genAreaQ();
    else if (t === 'triangle') q = genTriangleQ();
    else if (t === 'isosceles') q = genIsoscelesQ();
    else q = genClassifyQ();

    quizState.current = q;
    renderQuestion(q);
    updateQuizProgress();
  }

  function coordStr(p) { return `(${p.x}, ${p.y})`; }

  function renderQuestion(q) {
    const d = q.data;
    if (q.type === 'distance') {
      quizQEl.innerHTML = `Find the distance between <b>P</b>${coordStr(d.P)} and <b>Q</b>${coordStr(d.Q)}. <span class="badge">Answer to 2 decimals</span>`;
      quizInputArea.innerHTML = `
        <div class="row">
          <input type="number" step="0.01" id="quizNum" placeholder="e.g., 5.66" />
        </div>
      `;
    } else if (q.type === 'area') {
      quizQEl.innerHTML = `Compute the area of △<b>ABC</b> with A${coordStr(d.A)}, B${coordStr(d.B)}, C${coordStr(d.C)}. <span class="badge">Answer to 2 decimals</span>`;
      quizInputArea.innerHTML = `
        <div class="row">
          <input type="number" step="0.01" id="quizNum" placeholder="e.g., 7.50" />
        </div>
      `;
    } else if (q.type === 'triangle') {
      quizQEl.innerHTML = `Do points <b>A</b>${coordStr(d.A)}, <b>B</b>${coordStr(d.B)}, <b>C</b>${coordStr(d.C)} form a triangle?`;
      quizInputArea.innerHTML = `
        <div class="row">
          <label class="pill"><input type="radio" name="yn" value="Yes"> Yes</label>
          <label class="pill"><input type="radio" name="yn" value="No"> No</label>
        </div>
      `;
    } else if (q.type === 'isosceles') {
      quizQEl.innerHTML = `Is △<b>ABC</b> with A${coordStr(d.A)}, B${coordStr(d.B)}, C${coordStr(d.C)} an <b>isosceles</b> triangle?`;
      quizInputArea.innerHTML = `
        <div class="row">
          <label class="pill"><input type="radio" name="yn" value="Yes"> Yes</label>
          <label class="pill"><input type="radio" name="yn" value="No"> No</label>
        </div>
      `;
    } else if (q.type === 'classify') {
      quizQEl.innerHTML = `Classify △<b>ABC</b> with A${coordStr(d.A)}, B${coordStr(d.B)}, C${coordStr(d.C)}.`;
      const opts = q.options.map(o => `
        <label class="pill"><input type="radio" name="mc" value="${o}"> ${o}</label>
      `).join('');
      quizInputArea.innerHTML = `<div class="mc">${opts}</div>`;
    }
  }

  function getUserAnswer(q) {
    if (q.type === 'distance' || q.type === 'area') {
      const v = parseFloat(document.getElementById('quizNum').value);
      return Number.isFinite(v) ? v : null;
    } else if (q.type === 'triangle' || q.type === 'isosceles') {
      const el = document.querySelector('input[name="yn"]:checked');
      return el ? el.value : null;
    } else {
      const el = document.querySelector('input[name="mc"]:checked');
      return el ? el.value : null;
    }
  }

  function checkAnswer(q, user) {
    if (user == null) return { correct: false, msg: 'Please enter/select an answer.' };

    if (q.type === 'distance' || q.type === 'area') {
      const tol = 0.02;
      const correct = Math.abs(user - q.answer) <= tol;
      return { correct, msg: `Correct answer: ${q.answer.toFixed(2)}` };
    } else {
      const correct = String(user) === String(q.answer);
      return { correct, msg: `Correct answer: ${q.answer}` };
    }
  }

  function updateQuizProgress() {
    quizProgressEl.textContent = `${quizState.idx} / ${quizState.total} · Score: ${quizState.score}`;
  }

  quizStartBtn.addEventListener('click', () => {
    quizState.idx = 0; quizState.score = 0;
    quizStartBtn.textContent = 'Restart';
    newQuestion();
  });

  quizSubmitBtn.addEventListener('click', () => {
    if (!quizState.current || quizState.answered) return;
    const user = getUserAnswer(quizState.current);
    const res = checkAnswer(quizState.current, user);
    quizState.answered = true;

    if (res.correct) {
      quizState.score += 1;
      quizFeedbackEl.className = 'feedback ok';
      quizFeedbackEl.textContent = '✅ Correct! ' + res.msg;
    } else {
      quizFeedbackEl.className = 'feedback no';
      quizFeedbackEl.textContent = '❌ Not quite. ' + res.msg;
    }
    quizSubmitBtn.disabled = true;
    quizNextBtn.disabled = false;

    // increment progress index
    quizState.idx += 1;
    updateQuizProgress();
  });

  quizNextBtn.addEventListener('click', () => {
    if (quizState.idx >= quizState.total) {
      quizQEl.innerHTML = `<b>Finished!</b> You scored <b>${quizState.score} / ${quizState.total}</b>. Click <b>Restart</b> to play again.`;
      quizInputArea.innerHTML = '';
      quizSubmitBtn.disabled = true;
      quizNextBtn.disabled = true;
      quizFeedbackEl.textContent = '';
      return;
    }
    newQuestion();
  });

  // ============================================================
  //                      CALCULATOR MODULE
  // ============================================================
  const calcInput = document.getElementById('calcInput');
  const calcSolveBtn = document.getElementById('calcSolveBtn');
  const calcOutput = document.getElementById('calcOutput');

  function parsePolySide(str) {
    // Accepts tokens like: 2x^2, -x^2, 3.5x, -x, 7, -4.2
    const s = str.replace(/\s+/g,''); // remove spaces
    // Split by + while preserving minus as sign: replace - with +-
    const norm = s.replace(/-/g, '+-');
    const parts = norm.split('+').filter(Boolean);
    let a2 = 0, a1 = 0, a0 = 0;
    for (let raw of parts) {
      let t = raw;
      if (t.includes('x^2')) {
        let k = t.replace('x^2','');
        if (k === '' || k === '+') k = '1';
        if (k === '-') k = '-1';
        const v = parseFloat(k);
        if (!isNaN(v)) a2 += v;
        else return null;
      } else if (t.includes('x')) {
        let k = t.replace('x','');
        if (k === '' || k === '+') k = '1';
        if (k === '-') k = '-1';
        const v = parseFloat(k);
        if (!isNaN(v)) a1 += v;
        else return null;
      } else {
        const v = parseFloat(t);
        if (isNaN(v)) return null;
        a0 += v;
      }
    }
    return { a2, a1, a0 };
  }

  function solveEquation(input) {
    const s = input.trim();
    if (!s.includes('=')) return { mode: 'numeric' };

    const [L, R] = s.split('=');
    if (L == null || R == null) return { error: 'Invalid equation. Use a single "=".' };

    const left = parsePolySide(L);
    const right = parsePolySide(R);
    if (!left || !right) return { error: 'Unsupported format. Use x, x^2, and numeric coefficients only (e.g., 2x+3, x^2-4x+4).' };

    const a = (left.a2 - right.a2);
    const b = (left.a1 - right.a1);
    const c = (left.a0 - right.a0);
    const eps = 1e-12;

    if (Math.abs(a) > eps) {
      const D = b*b - 4*a*c;
      if (D > eps) {
        const r1 = (-b + Math.sqrt(D)) / (2*a);
        const r2 = (-b - Math.sqrt(D)) / (2*a);
        return { mode: 'equation', type: 'quadratic', roots: [r1, r2] };
      } else if (Math.abs(D) <= eps) {
        const r = (-b) / (2*a);
        return { mode: 'equation', type: 'quadratic', roots: [r] };
      } else {
        return { mode: 'equation', type: 'quadratic', roots: [], complex: true };
      }
    } else if (Math.abs(b) > eps) {
      const r = -c / b;
      return { mode: 'equation', type: 'linear', roots: [r] };
    } else {
      if (Math.abs(c) <= eps) {
        return { mode: 'equation', type: 'identity', infinite: true };
      } else {
        return { mode: 'equation', type: 'contradiction', none: true };
      }
    }
  }

  function safeEvalNumeric(expr) {
    // Only digits, operators, parentheses, spaces, decimal points and ^ allowed
    const allowed = /^[0-9+\-*/().\s^]+$/;
    if (!allowed.test(expr)) return { error: 'Only numbers and + - * / ^ ( ) are allowed.' };
    // Convert ^ to ** for exponentiation
    const jsExpr = expr.replace(/\^/g, '**');
    try {
      // eslint-disable-next-line no-new-func
      const val = Function('"use strict";return (' + jsExpr + ')')();
      if (typeof val !== 'number' || !isFinite(val)) return { error: 'Expression did not evaluate to a finite number.' };
      return { value: val };
    } catch {
      return { error: 'Invalid numeric expression.' };
    }
  }

  calcSolveBtn.addEventListener('click', () => {
    const raw = calcInput.value.trim();
    if (!raw) { calcOutput.className = 'feedback no'; calcOutput.textContent = 'Enter an equation or expression.'; return; }

    const res = solveEquation(raw);
    if (res.error) {
      calcOutput.className = 'feedback no';
      calcOutput.textContent = '⚠ ' + res.error;
      return;
    }

    if (res.mode === 'numeric') {
      const v = safeEvalNumeric(raw);
      if (v.error) {
        calcOutput.className = 'feedback no';
        calcOutput.textContent = '⚠ ' + v.error;
      } else {
        calcOutput.className = 'feedback ok';
        calcOutput.textContent = `= ${v.value}`;
      }
      return;
    }

    // Equation mode
    calcOutput.className = 'feedback ok';
    if (res.type === 'quadratic') {
      if (res.complex) {
        calcOutput.textContent = 'No real roots (discriminant < 0).';
      } else if (res.roots.length === 1) {
        calcOutput.textContent = `x = ${res.roots[0]}`;
      } else {
        calcOutput.textContent = `x₁ = ${res.roots[0]},  x₂ = ${res.roots[1]}`;
      }
    } else if (res.type === 'linear') {
      calcOutput.textContent = `x = ${res.roots[0]}`;
    } else if (res.infinite) {
      calcOutput.textContent = 'Identity: infinitely many solutions.';
    } else if (res.none) {
      calcOutput.textContent = 'Contradiction: no solution.';
    } else {
      calcOutput.textContent = 'Solved.';
    }
  });

})();
</script>
</body>
</html>

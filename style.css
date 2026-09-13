(() => {
  'use strict';
  const cv = document.getElementById('game'); const ctx = cv.getContext('2d');
  const W = cv.width, H = cv.height;
  const dpr = Math.max(1, Math.min(3, window.devicePixelRatio || 1));
  cv.width = W * dpr; cv.height = H * dpr; ctx.scale(dpr, dpr);
  const scoreEl = document.getElementById('score'), timeEl = document.getElementById('time'), leftEl = document.getElementById('left');
  const overlay = document.getElementById('overlay'), ovTitle = document.getElementById('ov-title'), ovSub = document.getElementById('ov-sub');
  const COLS = 12, ROWS = 10, CELL = 40;
  const ox = (W - COLS * CELL) / 2, oy = (H - ROWS * CELL) / 2;
  const COLORS = ['#ff5c7a', '#4fd1ff', '#43d97a', '#ffd23f', '#b15cff', '#ff9f43', '#ff7eb3', '#7afcff', '#a0ff7a', '#ffe17a'];
  const SYM = ['🍎', '🍌', '🍇', '🍓', '🍑', '🍒', '🥝', '🍍', '🥥', '🍉'];
  const DIRS = [[0, -1], [0, 1], [-1, 0], [1, 0]];
  let board, pad, score, timeLeft, over, sel, timer;

  function shuffle(a) { for (let i = a.length - 1; i > 0; i--) { const j = Math.floor(Math.random() * (i + 1)); [a[i], a[j]] = [a[j], a[i]]; } return a; }
  function reset() {
    const total = COLS * ROWS; const arr = [];
    for (let i = 0; i < total / 2; i++) { const s = 1 + (i % COLORS.length); arr.push(s, s); }
    shuffle(arr);
    board = []; let k = 0;
    for (let r = 0; r < ROWS; r++) { board[r] = []; for (let c = 0; c < COLS; c++) board[r][c] = arr[k++]; }
    pad = Array.from({ length: ROWS + 2 }, () => Array(COLS + 2).fill(0));
    for (let r = 0; r < ROWS; r++) for (let c = 0; c < COLS; c++) pad[r + 1][c + 1] = board[r][c];
    score = 0; timeLeft = 90; over = false; sel = null;
    scoreEl.textContent = '0'; timeEl.textContent = '90'; leftEl.textContent = total;
    overlay.classList.add('hidden');
    if (timer) clearInterval(timer);
    timer = setInterval(() => { if (!over) { timeLeft--; timeEl.textContent = timeLeft; if (timeLeft <= 0) { over = true; ovTitle.textContent = '时间到'; ovSub.textContent = '剩余 ' + leftEl.textContent + ' 张'; overlay.classList.remove('hidden'); } } }, 1000);
  }
  function canLink(a, b) {
    const q = [{ r: a.r + 1, c: a.c + 1, dir: -1, turns: 0 }]; const seen = new Set();
    while (q.length) {
      const cur = q.shift();
      for (let d = 0; d < 4; d++) {
        const nr = cur.r + DIRS[d][0], nc = cur.c + DIRS[d][1];
        const turns = cur.turns + (cur.dir !== -1 && cur.dir !== d ? 1 : 0);
        if (turns > 2) continue;
        if (nr < 0 || nr >= ROWS + 2 || nc < 0 || nc >= COLS + 2) continue;
        if (nr === b.r + 1 && nc === b.c + 1) return true;
        if (pad[nr][nc] !== 0) continue;
        const key = nr + ',' + nc + ',' + d; if (seen.has(key)) continue; seen.add(key);
        q.push({ r: nr, c: nc, dir: d, turns });
      }
    }
    return false;
  }
  function clickHandler(e) {
    if (over) return;
    const rect = cv.getBoundingClientRect(); const px = (e.clientX - rect.left) / rect.width * W, py = (e.clientY - rect.top) / rect.height * H;
    const c = Math.floor((px - ox) / CELL), r = Math.floor((py - oy) / CELL);
    if (r < 0 || r >= ROWS || c < 0 || c >= COLS || board[r][c] === 0) return;
    if (!sel) { sel = { r, c }; return; }
    if (sel.r === r && sel.c === c) { sel = null; return; }
    if (board[sel.r][sel.c] === board[r][c] && canLink(sel, { r, c })) {
      board[sel.r][sel.c] = 0; board[r][c] = 0; pad[sel.r + 1][sel.c + 1] = 0; pad[r + 1][c + 1] = 0;
      score += 10; scoreEl.textContent = score;
      const left = leftEl.textContent - 2; leftEl.textContent = left; sel = null;
      if (left === 0) { over = true; ovTitle.textContent = '通关！'; ovSub.textContent = '得分 ' + score; overlay.classList.remove('hidden'); }
    } else sel = { r, c };
  }
  function draw() {
    ctx.fillStyle = '#1a1c3a'; ctx.fillRect(0, 0, W, H);
    for (let r = 0; r < ROWS; r++) for (let c = 0; c < COLS; c++) {
      const x = ox + c * CELL, y = oy + r * CELL;
      if (board[r][c] === 0) continue;
      ctx.fillStyle = COLORS[board[r][c] - 1];
      ctx.fillRect(x + 2, y + 2, CELL - 4, CELL - 4);
      ctx.font = (CELL * 0.5) + 'px serif'; ctx.textAlign = 'center'; ctx.textBaseline = 'middle';
      ctx.fillText(SYM[board[r][c] - 1], x + CELL / 2, y + CELL / 2);
      if (sel && sel.r === r && sel.c === c) { ctx.strokeStyle = '#fff'; ctx.lineWidth = 3; ctx.strokeRect(x + 2, y + 2, CELL - 4, CELL - 4); }
    }
  }
  cv.addEventListener('click', clickHandler);
  cv.addEventListener('touchend', e => { const t = e.changedTouches[0]; clickHandler({ clientX: t.clientX, clientY: t.clientY }); }, { passive: true });
  document.getElementById('new').addEventListener('click', reset);
  document.getElementById('ov-btn').addEventListener('click', reset);
  function loop() { draw(); requestAnimationFrame(loop); }
  reset(); requestAnimationFrame(loop);
})();

(() => {
  'use strict';
  const cv = document.getElementById('game'); const ctx = cv.getContext('2d');
  const W = cv.width, H = cv.height;
  const dpr = Math.max(1, Math.min(3, window.devicePixelRatio || 1));
  cv.width = W * dpr; cv.height = H * dpr; ctx.scale(dpr, dpr);
  const movesEl = document.getElementById('moves'), pairsEl = document.getElementById('pairs'), timeEl = document.getElementById('time');
  const overlay = document.getElementById('overlay'), ovTitle = document.getElementById('ov-title'), ovSub = document.getElementById('ov-sub');
  const N = 4, CELL = W / N;
  const SYM = ['🐶', '🐱', '🐭', '🐹', '🐰', '🦊', '🐻', '🐼'];
  let cards, first, lock, moves, pairs, over, timer, secs;

  function shuffle(a) { for (let i = a.length - 1; i > 0; i--) { const j = Math.floor(Math.random() * (i + 1)); [a[i], a[j]] = [a[j], a[i]]; } return a; }
  function reset() {
    const syms = []; for (const s of SYM) syms.push(s, s); shuffle(syms);
    cards = []; let k = 0;
    for (let r = 0; r < N; r++) for (let c = 0; c < N; c++) cards.push({ sym: syms[k++], up: false, done: false });
    first = null; lock = false; moves = 0; pairs = 0; over = false; secs = 0;
    movesEl.textContent = '0'; pairsEl.textContent = '0'; timeEl.textContent = '0';
    if (timer) clearInterval(timer);
    timer = setInterval(() => { if (!over) { secs++; timeEl.textContent = secs; } }, 1000);
    overlay.classList.add('hidden');
  }
  function flip(idx) {
    if (lock || over || cards[idx].up || cards[idx].done) return;
    cards[idx].up = true;
    if (!first) { first = idx; return; }
    moves++; movesEl.textContent = moves;
    if (cards[first].sym === cards[idx].sym) {
      cards[first].done = true; cards[idx].done = true; first = null; pairs++;
      pairsEl.textContent = pairs;
      if (pairs === SYM.length) { over = true; if (timer) clearInterval(timer); ovTitle.textContent = '全部配对！'; ovSub.textContent = '步数 ' + moves + ' · 用时 ' + secs + ' 秒'; overlay.classList.remove('hidden'); }
    } else {
      lock = true; const a = first; first = null;
      setTimeout(() => { cards[a].up = false; cards[idx].up = false; lock = false; }, 700);
    }
  }
  function draw() {
    ctx.fillStyle = '#1a1c3a'; ctx.fillRect(0, 0, W, H);
    for (let i = 0; i < N * N; i++) {
      const r = Math.floor(i / N), c = i % N, x = c * CELL, y = r * CELL, card = cards[i];
      if (card.done) { ctx.fillStyle = '#26304f'; ctx.fillRect(x + 4, y + 4, CELL - 8, CELL - 8); ctx.font = (CELL * 0.5) + 'px serif'; ctx.textAlign = 'center'; ctx.textBaseline = 'middle'; ctx.fillStyle = '#43d97a'; ctx.fillText(card.sym, x + CELL / 2, y + CELL / 2); }
      else if (card.up) { ctx.fillStyle = '#eef0ff'; ctx.fillRect(x + 4, y + 4, CELL - 8, CELL - 8); ctx.font = (CELL * 0.5) + 'px serif'; ctx.textAlign = 'center'; ctx.textBaseline = 'middle'; ctx.fillText(card.sym, x + CELL / 2, y + CELL / 2); }
      else { ctx.fillStyle = '#34386e'; ctx.fillRect(x + 4, y + 4, CELL - 8, CELL - 8); ctx.fillStyle = 'rgba(255,255,255,0.25)'; ctx.font = 'bold ' + (CELL * 0.4) + 'px sans-serif'; ctx.textAlign = 'center'; ctx.textBaseline = 'middle'; ctx.fillText('?', x + CELL / 2, y + CELL / 2); }
    }
  }
  cv.addEventListener('click', e => { const rect = cv.getBoundingClientRect(); const px = (e.clientX - rect.left) / rect.width * W, py = (e.clientY - rect.top) / rect.height * H; flip(Math.floor(py / CELL) * N + Math.floor(px / CELL)); });
  cv.addEventListener('touchend', e => { const t = e.changedTouches[0]; const rect = cv.getBoundingClientRect(); const px = (t.clientX - rect.left) / rect.width * W, py = (t.clientY - rect.top) / rect.height * H; flip(Math.floor(py / CELL) * N + Math.floor(px / CELL)); }, { passive: true });
  document.getElementById('new').addEventListener('click', reset);
  document.getElementById('ov-btn').addEventListener('click', reset);
  function loop() { draw(); requestAnimationFrame(loop); }
  reset(); requestAnimationFrame(loop);
})();

<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PANEN PINTAR — Game Kelas</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  html, body { height: 100%; }
  body {
    font-family: 'Times New Roman', Times, serif;
    font-weight: bold;
    background: linear-gradient(#7ec8f7 0%, #bfe8ff 45%, #a8e6a1 45%, #7ed07a 100%);
    color: #1d4d1a;
    overflow-x: hidden;
    user-select: none;
  }
  h1 { font-size: 52px; color: #c0392b; text-shadow: 2px 2px 0 #fff, 4px 4px 0 rgba(0,0,0,.15); letter-spacing: 2px; }
  h2 { font-size: 30px; }
  p, label, button, .q-text { font-size: 24px; }
  .screen { display: none; min-height: 100vh; padding: 16px; text-align: center; }
  .screen.active { display: block; }
  .card {
    background: rgba(255,255,255,.92);
    border: 4px solid #2e8b57;
    border-radius: 24px;
    max-width: 760px;
    margin: 20px auto;
    padding: 24px;
    box-shadow: 0 8px 20px rgba(0,0,0,.2);
  }
  .animals { font-size: 44px; letter-spacing: 8px; margin: 10px 0; }
  .team-row { display: flex; gap: 12px; justify-content: center; flex-wrap: wrap; margin: 14px 0; }
  .team-pick {
    border: 4px solid #ccc; border-radius: 16px; padding: 12px 18px;
    cursor: pointer; background: #fff; min-width: 150px;
  }
  .team-pick.sel { border-color: #222; transform: scale(1.06); box-shadow: 0 4px 12px rgba(0,0,0,.3); }
  .team-pick input {
    font-family: 'Times New Roman', Times, serif; font-weight: bold; font-size: 22px;
    border: none; border-bottom: 2px dashed #999; text-align: center; width: 130px; background: transparent;
  }
  .team-pick .dot { font-size: 34px; display: block; }
  .btn {
    font-family: 'Times New Roman', Times, serif; font-weight: bold;
    font-size: 28px; padding: 14px 34px; border-radius: 40px; border: 3px solid #1d4d1a;
    background: #ffcf3f; color: #5b3a00; cursor: pointer; margin: 10px 6px;
    box-shadow: 0 4px 0 #c89b00;
  }
  .btn:active { transform: translateY(3px); box-shadow: none; }
  .btn.green { background: #4caf50; color: #fff; border-color: #1b5e20; box-shadow: 0 4px 0 #1b5e20; }
  .btn.small { font-size: 20px; padding: 8px 18px; }
  /* ===== Arena ===== */
  #arena { position: relative; max-width: 1100px; margin: 0 auto; }
  .topbar { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 8px; }
  .pill { background: #fff; border: 3px solid #2e8b57; border-radius: 30px; padding: 6px 18px; font-size: 24px; }
  .sky { font-size: 40px; display: flex; justify-content: space-between; padding: 0 20px; }
  .tree-wrap { position: relative; width: 620px; height: 430px; margin: 0 auto; }
  .trunk {
    position: absolute; bottom: 0; left: 50%; transform: translateX(-50%);
    width: 90px; height: 190px; background: #8b5a2b; border-radius: 20px 20px 0 0;
    border: 3px solid #6d4520;
  }
  .canopy {
    position: absolute; top: 0; left: 50%; transform: translateX(-50%);
    width: 600px; height: 320px; background: #2fae4e; border-radius: 50%;
    border: 4px solid #1e7d32; box-shadow: inset -20px -20px 0 rgba(0,0,0,.08);
  }
  .canopy::after {
    content: ''; position: absolute; width: 420px; height: 200px; background: #37c25c;
    border-radius: 50%; top: 60px; left: 90px;
  }
  .apple {
    position: absolute; font-size: 52px; cursor: pointer; z-index: 5;
    animation: bob 2.4s ease-in-out infinite; filter: drop-shadow(2px 3px 2px rgba(0,0,0,.3));
    transition: transform .15s;
  }
  .apple:hover { transform: scale(1.25); }
  @keyframes bob { 0%,100% { margin-top: 0; } 50% { margin-top: -7px; } }
  .apple.picked { visibility: hidden; }
  .apple .num {
    position: absolute; left: 50%; top: 58%; transform: translateX(-50%);
    font-size: 20px; color: #fff; text-shadow: 1px 1px 2px #000;
  }
  .apple.fly { position: fixed; z-index: 99; transition: all .9s ease-in; pointer-events: none; }
  .ground-animals { font-size: 42px; display: flex; justify-content: space-around; margin-top: -8px; }
  /* ===== Keranjang ===== */
  #baskets { display: flex; justify-content: center; gap: 14px; flex-wrap: wrap; margin-top: 6px; }
  .basket {
    background: rgba(255,255,255,.92); border: 4px solid #888; border-radius: 18px;
    padding: 10px 14px; min-width: 200px;
  }
  .basket .bname { font-size: 26px; }
  .basket .bemoji { font-size: 56px; display: block; }
  .basket .bscore { font-size: 30px; color: #c0392b; }
  .basket .bapples { font-size: 30px; min-height: 38px; }
  .basket.leader { border-color: gold; box-shadow: 0 0 14px gold; }
  /* ===== Popup soal ===== */
  #modal {
    display: none; position: fixed; inset: 0; z-index: 50;
    background: rgba(0,0,0,.55); align-items: center; justify-content: center; padding: 12px;
  }
  #modal.open { display: flex; }
  .modal-box {
    background: #fffdf0; border: 5px solid #2e8b57; border-radius: 24px;
    max-width: 720px; width: 100%; padding: 22px; text-align: center;
    box-shadow: 0 10px 30px rgba(0,0,0,.4);
  }
  .timer-bar { height: 22px; background: #eee; border: 2px solid #999; border-radius: 12px; overflow: hidden; margin: 10px 0; }
  #timerFill { height: 100%; width: 100%; background: #4caf50; }
  #timerText { font-size: 28px; color: #c0392b; }
  .q-text { font-size: 30px; margin: 14px 0; line-height: 1.35; }
  .opt {
    display: block; width: 100%; text-align: left; margin: 10px 0; padding: 14px 18px;
    font-family: 'Times New Roman', Times, serif; font-weight: bold; font-size: 26px;
    background: #e3f2fd; border: 3px solid #1565c0; border-radius: 16px; cursor: pointer;
  }
  .opt:hover { background: #bbdefb; }
  .opt:disabled { opacity: .45; cursor: default; }
  .opt.wrong { background: #ffcdd2; border-color: #c62828; }
  .opt.right { background: #c8e6c9; border-color: #2e7d32; }
  .team-answer { display: flex; gap: 10px; justify-content: center; flex-wrap: wrap; margin: 8px 0 4px; }
  .team-answer button {
    font-family: 'Times New Roman', Times, serif; font-weight: bold; font-size: 22px;
    padding: 8px 16px; border-radius: 30px; border: 3px solid #555; cursor: pointer; background: #fff;
  }
  .team-answer button.sel { border-color: #000; outline: 3px solid gold; }
  #feedback { font-size: 32px; min-height: 44px; margin-top: 8px; }
  /* ===== Akhir ===== */
  #endScreen .card { max-width: 680px; }
  #winnerText { font-size: 34px; color: #c0392b; margin: 14px 0; }
  #finalScores { font-size: 26px; line-height: 1.6; }
  #confettiCanvas { position: fixed; inset: 0; pointer-events: none; z-index: 60; }
  @media (max-width: 700px) {
    h1 { font-size: 36px; }
    .tree-wrap { width: 100%; max-width: 480px; height: 340px; }
    .canopy { width: 100%; height: 250px; }
    .apple { font-size: 42px; }
    .q-text, .opt { font-size: 24px; }
  }
</style>
</head>
<body>

<!-- ===== LAYAR AWAL ===== -->
<div id="startScreen" class="screen active">
  <h1>🍎 PANEN PINTAR 🍎</h1>
  <div class="animals">🐝 🦋 🐞 🐰 🐦 🦔 🐿️ 🌻</div>
  <p>Petik apel di pohon, jawab soal pecahan, dan isi keranjang timmu!</p>
  <div class="card">
    <h2>🌳 Pilih Jumlah Tim 🌳</h2>
    <div class="team-row" id="teamCountRow">
      <button class="btn small" onclick="setTeamCount(2)">2 Tim</button>
      <button class="btn small" onclick="setTeamCount(3)">3 Tim</button>
      <button class="btn small green" onclick="setTeamCount(4)">4 Tim</button>
    </div>
    <p id="teamCountLabel">4 tim dipilih — tulis nama tim di bawah ini:</p>
    <div class="team-row" id="teamSetup"></div>
    <p>⏱️ Setiap soal: <b>1 menit</b> &nbsp;•&nbsp; 🍎 1 apel benar = <b>1 poin</b></p>
    <button class="btn green" onclick="startGame()">▶ MULAI BERMAIN</button>
    <br>
    <button class="btn small" onclick="toggleSound(this)">🔊 Suara: Nyala</button>
  </div>
  <p>Matematika • Bilangan Pecahan Sederhana • Kelas 2 SD</p>
</div>

<!-- ===== ARENA ===== -->
<div id="gameScreen" class="screen">
  <div id="arena">
    <div class="topbar">
      <span class="pill">🍎 PANEN PINTAR</span>
      <span class="pill">🍎 Apel di pohon: <span id="applesLeft">20</span></span>
      <span class="pill">🏆 Pemimpin: <span id="leader">—</span></span>
      <button class="btn small" onclick="toggleSound(this)">🔊 Suara</button>
    </div>
    <div class="sky"><span>☀️</span><span>☁️ 🦋</span><span>🐦</span></div>
    <div class="tree-wrap">
      <div class="canopy"></div>
      <div class="trunk"></div>
      <div id="apples"></div>
    </div>
    <div class="ground-animals"><span>🐰</span><span>🦔</span><span>🐿️</span><span>🐞</span><span>🐝</span></div>
    <div id="baskets"></div>
  </div>
</div>

<!-- ===== POPUP SOAL ===== -->
<div id="modal">
  <div class="modal-box">
    <h2>🍎 Soal Apel <span id="qNum"></span></h2>
    <p>Siapa yang menjawab?</p>
    <div class="team-answer" id="answerTeamRow"></div>
    <div class="timer-bar"><div id="timerFill"></div></div>
    <div id="timerText">⏱️ 60 detik</div>
    <div class="q-text" id="qText"></div>
    <div id="options"></div>
    <div id="feedback"></div>
    <button class="btn small" id="closeBtn" style="display:none" onclick="closeModal()">Tutup</button>
  </div>
</div>

<!-- ===== LAYAR AKHIR ===== -->
<div id="endScreen" class="screen">
  <canvas id="confettiCanvas"></canvas>
  <h1>🎉 PANEN SELESAI! 🎉</h1>
  <div class="animals">🐝 🦋 🐞 🐰 🐦 🦔 🐿️ 🌻</div>
  <div class="card">
    <div id="winnerText"></div>
    <div id="finalScores"></div>
    <button class="btn green" onclick="resetGame()">🔄 Main Lagi</button>
    <button class="btn" onclick="backToStart()">🏠 Ganti Tim</button>
  </div>
</div>

<script>
// ---------- Data tim ----------
const TEAM_DEFS = [
  { name: 'Tim Merah',  color: '#e53935', emoji: '🧺', basket: '🍎' },
  { name: 'Tim Kuning', color: '#fdd835', emoji: '🧺', basket: '🍎' },
  { name: 'Tim Hijau',  color: '#43a047', emoji: '🧺', basket: '🍎' },
  { name: 'Tim Biru',   color: '#1e88e5', emoji: '🧺', basket: '🍎' },
];
let teamCount = 4;
let teams = [];
let scores = [];
let currentApple = null;      // elemen apel yang diklik
let currentQ = null;          // soal yang sedang tampil
let answeringTeam = 0;        // indeks tim yang menjawab
let timerInt = null, timeLeft = 60;
let soundOn = true;
let audioCtx = null;

// ---------- 20 soal: pecahan sederhana, kelas 2 ----------
const QUESTIONS = [
  { q: 'Sebuah apel dipotong menjadi 2 bagian sama besar. Satu bagian disebut…', o: ['Satu per dua (1/2)', 'Satu per tiga (1/3)', 'Dua per dua (2/2)'], a: 0 },
  { q: 'Ibu memotong kue menjadi 4 bagian sama besar. Sinta makan 1 bagian. Berapa bagian kue yang dimakan Sinta?', o: ['1/2 bagian', '1/4 bagian', '1/3 bagian'], a: 1 },
  { q: 'Ada 6 apel. 3 apel dimasukkan ke keranjang. Berapa bagian apel yang ada di keranjang?', o: ['1/3 bagian', '2/3 bagian', '1/2 bagian'], a: 2 },
  { q: 'Pecahan 1/2 dibaca…', o: ['Seperempat', 'Sepertiga', 'Setengah'], a: 2 },
  { q: 'Sebuah pizza dipotong menjadi 3 bagian sama besar. 1 bagian dimakan. Berapa bagian pizza yang tersisa?', o: ['1/3 bagian', '2/3 bagian', '3/3 bagian'], a: 1 },
  { q: 'Manakah yang menunjukkan pecahan 1/4?', o: ['1 dari 2 bagian sama besar', '1 dari 3 bagian sama besar', '1 dari 4 bagian sama besar'], a: 2 },
  { q: 'Ada 8 jeruk. Setengahnya diberikan kepada teman. Berapa jeruk yang diberikan?', o: ['2 jeruk', '4 jeruk', '6 jeruk'], a: 1 },
  { q: 'Pecahan 2/4 sama dengan…', o: ['1/4', '3/4', '1/2'], a: 2 },
  { q: 'Sebuah tali dipotong menjadi 2 bagian sama panjang. Setiap potongan adalah…', o: ['1/2 tali', '1/4 tali', '1/3 tali'], a: 0 },
  { q: 'Ani punya 12 permen. 1/3 permen diberikan kepada adik. Berapa permen yang diterima adik?', o: ['3 permen', '6 permen', '4 permen'], a: 2 },
  { q: 'Pecahan yang nilainya PALING BESAR adalah…', o: ['1/4', '1/3', '1/2'], a: 2 },
  { q: 'Pecahan yang nilainya PALING KECIL adalah…', o: ['1/2', '1/4', '1/3'], a: 1 },
  { q: '1/3 dari 9 apel adalah…', o: ['2 apel', '6 apel', '3 apel'], a: 2 },
  { q: 'Sebuah semangka dipotong menjadi 4 bagian sama besar. 3 bagian dimakan. Pecahan yang dimakan adalah…', o: ['3/4 bagian', '1/4 bagian', '2/4 bagian'], a: 0 },
  { q: '1/4 dari 8 kelereng adalah…', o: ['1 kelereng', '2 kelereng', '4 kelereng'], a: 1 },
  { q: 'Roti dipotong menjadi 2 bagian sama besar. Budi makan 1 bagian. Berapa bagian roti yang tersisa?', o: ['1/2 bagian', '1 bagian', 'Tidak ada sisa'], a: 0 },
  { q: 'Ada 10 pensil. 1/2 pensil diberikan kepada teman. Berapa pensil yang diberikan?', o: ['2 pensil', '10 pensil', '5 pensil'], a: 2 },
  { q: 'Lambang pecahan “sepertiga” adalah…', o: ['1/2', '1/3', '1/4'], a: 1 },
  { q: 'Sebuah cokelat dibagi menjadi 4 bagian sama besar. 2 bagian dimakan. Pecahan yang dimakan adalah…', o: ['1/4 bagian', '3/4 bagian', '2/4 bagian'], a: 2 },
  { q: '1/2 dari 10 apel adalah…', o: ['4 apel', '5 apel', '6 apel'], a: 1 },
];
// Posisi 20 apel di tajuk pohon (persen dari .tree-wrap)
const APPLE_POS = [
  [8,12],[22,6],[38,10],[52,4],[66,10],[80,6],[88,20],[14,26],[30,22],[46,18],
  [62,24],[76,26],[6,40],[22,38],[40,34],[56,38],[72,40],[86,42],[30,50],[58,52]
];

// ---------- Suara (WebAudio, tanpa file) ----------
function beep(freq, dur, delay) {
  if (!soundOn) return;
  try {
    audioCtx = audioCtx || new (window.AudioContext || window.webkitAudioContext)();
    const o = audioCtx.createOscillator(), g = audioCtx.createGain();
    o.type = 'sine'; o.frequency.value = freq;
    g.gain.setValueAtTime(0.15, audioCtx.currentTime + delay);
    g.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + delay + dur);
    o.connect(g); g.connect(audioCtx.destination);
    o.start(audioCtx.currentTime + delay); o.stop(audioCtx.currentTime + delay + dur);
  } catch (e) {}
}
function soundCorrect() { [523, 659, 784].forEach((f, i) => beep(f, 0.25, i * 0.12)); }
function soundWrong() { beep(220, 0.3, 0); }
function soundWin() { [523, 659, 784, 1047, 784, 1047].forEach((f, i) => beep(f, 0.3, i * 0.15)); }
function toggleSound(btn) {
  soundOn = !soundOn;
  btn.textContent = soundOn ? '🔊 Suara: Nyala' : '🔇 Suara: Mati';
}

// ---------- Layar awal ----------
function setTeamCount(n) {
  teamCount = n;
  document.getElementById('teamCountLabel').textContent = n + ' tim dipilih — tulis nama tim di bawah ini:';
  document.querySelectorAll('#teamCountRow .btn').forEach((b, i) => b.classList.toggle('green', [2,3,4][i] === n));
  renderTeamSetup();
}
function renderTeamSetup() {
  const wrap = document.getElementById('teamSetup');
  wrap.innerHTML = '';
  for (let i = 0; i < teamCount; i++) {
    const d = TEAM_DEFS[i];
    const div = document.createElement('div');
    div.className = 'team-pick sel';
    div.style.borderColor = d.color;
    div.innerHTML = '<span class="dot">🧺</span><input value="' + d.name + '" id="teamName' + i + '"><br><span style="color:' + d.color + '">● Keranjang ' + d.name.replace('Tim ', '') + '</span>';
    wrap.appendChild(div);
  }
}
renderTeamSetup();

// ---------- Mulai ----------
function startGame() {
  teams = [];
  for (let i = 0; i < teamCount; i++) {
    const nm = document.getElementById('teamName' + i).value.trim() || TEAM_DEFS[i].name;
    teams.push({ name: nm, color: TEAM_DEFS[i].color });
  }
  scores = teams.map(() => 0);
  showScreen('gameScreen');
  buildTree();
  buildBaskets();
  updateHUD();
}
function showScreen(id) {
  document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}
function buildTree() {
  const wrap = document.getElementById('apples');
  wrap.innerHTML = '';
  APPLE_POS.forEach((pos, i) => {
    const a = document.createElement('span');
    a.className = 'apple';
    a.innerHTML = '🍎<span class="num">' + (i + 1) + '</span>';
    a.style.left = pos[0] + '%';
    a.style.top = pos[1] + '%';
    a.style.animationDelay = (i * 0.15) + 's';
    a.dataset.q = i;
    a.onclick = () => openQuestion(a);
    wrap.appendChild(a);
  });
}
function buildBaskets() {
  const wrap = document.getElementById('baskets');
  wrap.innerHTML = '';
  teams.forEach((t, i) => {
    const div = document.createElement('div');
    div.className = 'basket';
    div.id = 'basket' + i;
    div.style.borderColor = t.color;
    div.innerHTML = '<div class="bname" style="color:' + t.color + '">' + t.name + '</div>' +
      '<span class="bemoji">🧺</span><div class="bapples" id="bapples' + i + '"></div>' +
      '<div class="bscore">🍎 <span id="score' + i + '">0</span> poin</div>';
    wrap.appendChild(div);
  });
}
function updateHUD() {
  const left = document.querySelectorAll('.apple:not(.picked)').length;
  document.getElementById('applesLeft').textContent = left;
  let best = -1, bi = -1, tie = false;
  scores.forEach((s, i) => { if (s > best) { best = s; bi = i; tie = false; } else if (s === best && s > 0) { tie = true; } });
  document.getElementById('leader').textContent = (best > 0 && !tie) ? teams[bi].name : (tie ? 'Seri!' : '—');
  teams.forEach((t, i) => {
    document.getElementById('score' + i).textContent = scores[i];
    document.getElementById('bapples' + i).textContent = '🍎'.repeat(scores[i]);
    document.getElementById('basket' + i).classList.toggle('leader', i === bi && best > 0 && !tie);
  });
  if (left === 0) endGame();
}

// ---------- Soal ----------
function openQuestion(appleEl) {
  currentApple = appleEl;
  currentQ = QUESTIONS[parseInt(appleEl.dataset.q, 10)];
  answeringTeam = 0;
  document.getElementById('qNum').textContent = (parseInt(appleEl.dataset.q, 10) + 1) + ' dari 20';
  document.getElementById('qText').textContent = currentQ.q;
  document.getElementById('feedback').textContent = '';
  document.getElementById('closeBtn').style.display = 'none';
  const row = document.getElementById('answerTeamRow');
  row.innerHTML = '';
  teams.forEach((t, i) => {
    const b = document.createElement('button');
    b.textContent = t.name;
    b.style.background = t.color;
    b.style.color = (t.color === '#fdd835') ? '#333' : '#fff';
    if (i === 0) b.classList.add('sel');
    b.onclick = () => { answeringTeam = i; row.querySelectorAll('button').forEach(x => x.classList.remove('sel')); b.classList.add('sel'); };
    row.appendChild(b);
  });
  const opts = document.getElementById('options');
  opts.innerHTML = '';
  ['A', 'B', 'C'].forEach((letter, i) => {
    const b = document.createElement('button');
    b.className = 'opt';
    b.textContent = letter + '. ' + currentQ.o[i];
    b.onclick = () => answer(i, b);
    opts.appendChild(b);
  });
  document.getElementById('modal').classList.add('open');
  startTimer();
}
function startTimer() {
  clearInterval(timerInt);
  timeLeft = 60;
  tickTimer();
  timerInt = setInterval(() => { timeLeft--; tickTimer(); if (timeLeft <= 0) { clearInterval(timerInt); timeUp(); } }, 1000);
}
function tickTimer() {
  document.getElementById('timerText').textContent = '⏱️ ' + timeLeft + ' detik';
  const fill = document.getElementById('timerFill');
  fill.style.width = (timeLeft / 60 * 100) + '%';
  fill.style.background = timeLeft <= 10 ? '#e53935' : '#4caf50';
}
function timeUp() {
  soundWrong();
  document.getElementById('feedback').textContent = '⏰ Waktu habis! Apel tetap di pohon, coba lagi nanti.';
  document.querySelectorAll('.opt').forEach(b => b.disabled = true);
  document.getElementById('closeBtn').style.display = 'inline-block';
}
function answer(i, btn) {
  if (i === currentQ.a) {
    clearInterval(timerInt);
    btn.classList.add('right');
    document.querySelectorAll('.opt').forEach(b => b.disabled = true);
    document.getElementById('feedback').textContent = '🎉 Jawaban Benar! Apel masuk keranjang ' + teams[answeringTeam].name + '!';
    soundCorrect();
    setTimeout(() => { flyApple(); }, 500);
  } else {
    btn.classList.add('wrong');
    btn.disabled = true;
    document.getElementById('feedback').textContent = '❌ Jawaban Salah, coba lagi!';
    soundWrong();
  }
}
function flyApple() {
  const basket = document.getElementById('basket' + answeringTeam);
  const r1 = currentApple.getBoundingClientRect(), r2 = basket.getBoundingClientRect();
  const fly = currentApple.cloneNode(true);
  fly.className = 'apple fly';
  fly.style.left = r1.left + 'px'; fly.style.top = r1.top + 'px';
  document.body.appendChild(fly);
  currentApple.classList.add('picked');
  document.getElementById('modal').classList.remove('open');
  requestAnimationFrame(() => {
    fly.style.left = (r2.left + r2.width / 2 - 26) + 'px';
    fly.style.top = (r2.top + 10) + 'px';
    fly.style.transform = 'scale(0.6) rotate(360deg)';
  });
  setTimeout(() => {
    fly.remove();
    scores[answeringTeam]++;
    updateHUD();
  }, 950);
}
function closeModal() {
  clearInterval(timerInt);
  document.getElementById('modal').classList.remove('open');
}

// ---------- Akhir permainan ----------
function endGame() {
  const max = Math.max(...scores);
  const winners = teams.filter((t, i) => scores[i] === max);
  const wText = winners.length === 1
    ? 'Selamat! Tim dengan apel terbanyak adalah pemenangnya: 🏆 ' + winners[0].name + ' 🏆'
    : 'Selamat! Seri! Tim dengan apel terbanyak: 🏆 ' + winners.map(w => w.name).join(' & ') + ' 🏆';
  document.getElementById('winnerText').textContent = wText;
  document.getElementById('finalScores').innerHTML =
    teams.map((t, i) => '🍎 ' + t.name + ': <b>' + scores[i] + ' apel</b>').join('<br>');
  showScreen('endScreen');
  soundWin();
  launchConfetti();
}
function launchConfetti() {
  const c = document.getElementById('confettiCanvas');
  c.width = innerWidth; c.height = innerHeight;
  const ctx = c.getContext('2d');
  const colors = ['#e53935', '#fdd835', '#43a047', '#1e88e5', '#ff9800', '#ab47bc'];
  const parts = Array.from({ length: 180 }, () => ({
    x: Math.random() * c.width, y: -20 - Math.random() * c.height * 0.5,
    w: 8 + Math.random() * 8, h: 8 + Math.random() * 8,
    vy: 2 + Math.random() * 3, vx: -1.5 + Math.random() * 3,
    color: colors[Math.floor(Math.random() * colors.length)], rot: Math.random() * Math.PI
  }));
  let frames = 0;
  (function draw() {
    ctx.clearRect(0, 0, c.width, c.height);
    parts.forEach(p => {
      p.y += p.vy; p.x += p.vx; p.rot += 0.05;
      ctx.save(); ctx.translate(p.x, p.y); ctx.rotate(p.rot);
      ctx.fillStyle = p.color; ctx.fillRect(-p.w / 2, -p.h / 2, p.w, p.h); ctx.restore();
    });
    if (++frames < 400) requestAnimationFrame(draw); else ctx.clearRect(0, 0, c.width, c.height);
  })();
}
function resetGame() { startGame(); }
function backToStart() { showScreen('startScreen'); }
</script>
</body>
</html>

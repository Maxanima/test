<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>Магическая печать — Сферы + Mock</title>
<style>
  html, body {
    margin: 0; padding: 0; height: 100%; width: 100vw;
    background: #000;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    overflow: hidden;
    user-select: none;
    -webkit-tap-highlight-color: transparent;
  }
  body {
    min-height: 100vh;
    text-align: center;
    padding: 0;
    box-sizing: border-box;
    width: 100vw;
  }
  #top-bar {
    position: fixed;
    top: 0; left: 0; width: 100vw;
    z-index: 20;
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 56px;
    pointer-events: none;
  }
  #player-info {
    display: flex;
    align-items: center;
    gap: 10px;
    background: rgba(0,0,0,0.5);
    color: #eee;
    font-size: 15px;
    border-radius: 6px;
    padding: 6px 10px;
    margin-left: 12px;
    pointer-events: auto;
    height: 40px;
  }
  #avatar-btn {
    width: 36px; height: 36px; border-radius: 50%; border: 2px solid #4e54c8;
    overflow: hidden; background: #fff; cursor: pointer; display: flex; align-items: center; justify-content: center;
    margin-right: 4px;
    flex-shrink: 0;
  }
  #avatar-btn img {
    width: 100%; height: 100%; object-fit: cover; border-radius: 50%;
  }
  #sphere-counter {
    display: flex;
    align-items: center;
    gap: 8px;
    color: #eee;
    font-weight: 600;
    font-size: 22px;
    margin-right: 12px;
    background: rgba(0,0,0,0.5);
    border-radius: 6px;
    padding: 6px 12px;
    pointer-events: auto;
    height: 40px;
  }
  #sphere-counter svg {
    width: 30px;
    height: 30px;
    filter: drop-shadow(0 0 5px rgba(255,255,255,0.3));
    flex-shrink: 0;
  }
  #mining-btn {
    position: fixed; top: 62px; right: 24px;
    background: linear-gradient(90deg,#34c759,#00b894); color: #fff; font-size: 0.93rem; font-weight: 700;
    border: none; border-radius: 9px; padding: 5px 0; width: 74px; text-align: center;
    box-shadow: 0 2px 12px #34c75966; cursor: pointer; z-index: 11; border-bottom: 2px solid #fff3;
  }
  #mining-btn[disabled] {
    background: #444; color: #aaa; filter: grayscale(0.5) brightness(0.8); cursor: default; border-bottom: 2px solid #222;
  }
  #seal-balance {
    position: fixed;
    top: 56px; left: 50%; transform: translateX(-50%);
    color: #fff;
    font-size: 1.1rem;
    font-weight: 800;
    letter-spacing: 1.1px;
    text-shadow: 0 2px 12px #00ff88, 0 0 2px #fff;
    z-index: 11;
    background: rgba(0,0,0,0.18);
    border-radius: 10px;
    padding: 5px 14px 5px 10px;
    min-width: 90px;
    text-align: center;
    box-sizing: border-box;
    user-select: none;
    width: max-content;
    pointer-events: none;
    border: 2px solid #00ff88;
    filter: drop-shadow(0 0 5px #00ff88);
  }
  #canvas-container {
    position: fixed;
    left: 0; right: 0;
    top: 100px; /* ниже top-bar + seal-balance + mining */
    bottom: 90px; /* выше bottom-nav */
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 1;
    background: none;
    overflow: visible;
    pointer-events: none;
  }
  #sealCanvas {
    display: block;
    background: transparent;
    border-radius: 0;
    box-sizing: border-box;
    touch-action: manipulation;
    image-rendering: auto;
    z-index: 1;
    pointer-events: auto;
    margin: auto;
  }
  #rare-message {
    position: fixed;
    left: 50%;
    transform: translateX(-50%);
    bottom: 90px;
    color: #00ff88;
    font-size: 17px;
    font-weight: bold;
    font-family: monospace;
    text-shadow: 0 0 8px #00ff88;
    text-align: center;
    white-space: pre-line;
    pointer-events: none;
    opacity: 0;
    transition: opacity 0.4s ease;
    z-index: 19;
    background: none;
    border: none;
    box-shadow: none;
    min-width: 0;
    padding: 0;
  }
  #rare-message.active { opacity: 1; }
  #bottom-nav {
    position: fixed;
    bottom: 12px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 12px;
    z-index: 11;
    background: none;
  }
  .nav-btn {
    background: #111;
    color: #fff;
    border: none;
    border-radius: 8px;
    padding: 8px 14px;
    font-size: 14px;
    cursor: pointer;
    user-select: none;
  }
  .tab {
    position: fixed;
    top: 60px;
    left: 50%;
    transform: translateX(-50%);
    width: 90%;
    max-width: 400px;
    background: #111;
    color: white;
    padding: 20px;
    border-radius: 12px;
    box-shadow: 0 0 20px #0ff3;
    z-index: 20;
    display: none;
  }
  .tab h2 { margin-top: 0; }
  .close-btn {
    display: inline-block;
    margin-top: 10px;
    background: #222;
    color: #fff;
    padding: 6px 12px;
    border: none;
    border-radius: 6px;
    cursor: pointer;
  }
  #profile-tab img {
    width: 60px; height: 60px; border-radius: 50%; border: 2px solid #4e54c8;
  }
  #mock-payment-modal {
    position: fixed;
    top: 50%; left: 50%;
    transform: translate(-50%, -50%);
    background: #111;
    border-radius: 14px;
    box-shadow: 0 0 30px #0ff;
    padding: 20px;
    color: #0ff;
    font-family: monospace;
    width: 320px;
    z-index: 30;
    display: none;
  }
  #mock-payment-modal button {
    background: #0ff;
    color: #000;
    border: none;
    border-radius: 8px;
    padding: 8px 16px;
    cursor: pointer;
    font-weight: 700;
    user-select: none;
  }
  #mock-payment-result {
    margin-top: 10px;
    font-weight: 700;
  }
  .rune-collection-cell.filled {
  animation: runeGlow 2s infinite alternate;
  box-shadow: 0 0 16px 4px var(--rune-color, #fff), 0 0 8px 2px #fff3;
  border-width: 3px;
  cursor: pointer;
  position: relative;
  transition: box-shadow 0.2s, border-color 0.2s, color 0.2s;
}
@keyframes runeGlow {
  0% { box-shadow: 0 0 8px 2px var(--rune-color, #fff), 0 0 4px 1px #fff3; }
  100% { box-shadow: 0 0 32px 8px var(--rune-color, #fff), 0 0 16px 4px #fff3; }
}
.rune-collection-cell .rune-tooltip {
  display: none;
  position: absolute;
  left: 50%; top: 110%;
  transform: translateX(-50%);
  background: #222;
  color: #fff;
  font-size: 12px;
  padding: 7px 13px;
  border-radius: 8px;
  white-space: pre-line;
  box-shadow: 0 4px 16px #000a;
  z-index: 99;
}
.rune-collection-cell.filled:hover .rune-tooltip,
.rune-collection-cell.filled:focus .rune-tooltip,
.rune-collection-cell.selected .rune-tooltip {
  display: block;
}
.rune-slot {
  transition: box-shadow 0.3s, border-color 0.3s;
}
.rune-slot {
  width: 60px;
  height: 60px;
  border: 2px dashed #888;
  border-radius: 12px;
  background: #23262e;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2em;
  position: absolute;
  transition: box-shadow 0.3s, border-color 0.3s;
  z-index: 1;
}
.rune-slot.filled {
  border-style: solid;
  box-shadow: 0 0 24px 6px var(--rune-color, #fff), 0 0 8px 2px #fff3;
}
.rune-slot.selected {
  border-color: #fff;
  box-shadow: 0 0 32px 8px #fff, 0 0 16px 4px var(--rune-color, #fff);
}

.chest-svg {
  width: 54px; height: 54px; vertical-align: middle; margin-right: 12px;
  filter: drop-shadow(0 0 12px #ffd700aa);
  transition: transform 0.2s;
}
.chest-svg.opening {
  animation: chestOpenAnim 0.7s;
}
@keyframes chestOpenAnim {
  0% { transform: scale(1) rotate(0deg); }
  20% { transform: scale(1.1) rotate(-7deg);}
  40% { transform: scale(1.12) rotate(7deg);}
  70% { transform: scale(1.05) rotate(0deg);}
  100% { transform: scale(1) rotate(0deg);}
}
#main-rune-slots {
  position: absolute;
  left: 50%;
  top: 50%;
  pointer-events: auto; 
  width: 0; height: 0;
  z-index: 10;
}
.rune-orbit {
  position: absolute;
  left: 50%; top: 50%;
  transform: translate(-50%,-50%);
  pointer-events: auto;
}
.rune-slot {
  width: 70px;
  height: 70px;
  border-radius: 50%;
  border: 3px solid #fff;
  background: #23262e;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2.5em;
  box-shadow: 0 0 24px 6px var(--rune-color, #fff3);
  transition: box-shadow 0.3s, border-color 0.3s;
  cursor: pointer;
  position: absolute;
  left: 0; top: 0;
  z-index: 1;
}
.rune-slot.filled {
  border-color: var(--rune-color, #fff);
  box-shadow: 0 0 32px 8px var(--rune-color, #fff);
  background: #23262e;
}
.rune-slot.empty {
  border-style: dashed;
  color: #888;
  background: #23262e;
}
.neon-rune {
  font-size: 3.5em;
  font-family: inherit;
  font-weight: bold;
  background: none;
  border: none;
  text-shadow:
    0 0 12px var(--rune-color),
    0 0 24px var(--rune-color),
    0 0 48px var(--rune-color);
  color: var(--rune-color);
  transition: text-shadow 0.3s, color 0.3s;
  pointer-events: auto;
  cursor: pointer;
  user-select: none;
  margin: 0 0.25em;
}
#main-rune-slots {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 90px;
  position: relative;
  z-index: 10;
  pointer-events: auto;
}
#main-rune-slots .neon-rune {
  pointer-events: auto;
}

</style>
</head>
<body>

<div id="top-bar">
  <div id="player-info">
    <button id="avatar-btn" title="Профиль">
      <img src="https://i.pravatar.cc/100?u=sealuser" alt="Avatar"/>
    </button>
    <span>Level: <span id="level">1</span> | XP: <span id="xp">0</span>/100</span>
  </div>
  <div id="sphere-counter">
    <svg viewBox="0 0 64 64" fill="none" stroke="white" stroke-width="2" stroke-linejoin="round" stroke-linecap="round">
      <circle cx="32" cy="32" r="30" />
      <circle cx="32" cy="32" r="20" />
      <circle cx="32" cy="32" r="10" />
    </svg>
    <span id="sphere-count">100</span>
  </div>
</div>

<button id="mining-btn">Mining</button>
<div id="seal-balance">$SEAL: <span id="seal-amount">0.000</span></div>
<div id="canvas-container">
  <canvas id="sealCanvas" tabindex="0"></canvas>
</div>
<div id="rare-message"></div>

<div id="bottom-nav">
  <button class="nav-btn" onclick="openTab('shop-tab')">Shop</button>
  <button class="nav-btn" onclick="openTab('quests-tab')">Quests</button>
  <button class="nav-btn" onclick="openTab('friends-tab')">Friends</button>
  <button class="nav-btn" onclick="openTab('rune-collection-tab')">Runes</button>
  <button class="nav-btn" onclick="openTab('chest-tab')">Chest</button>
</div>

<div id="shop-tab" class="tab">
  <h2>Shop</h2>
  <ul>
    <li>10 сфер — 5 TON <button onclick="startMockPayment(10)">Купить</button></li>
    <li>25 сфер — 10 TON <button onclick="startMockPayment(25)">Купить</button></li>
    <li>50 сфер — 18 TON <button onclick="startMockPayment(50)">Купить</button></li>
    <li>100 сфер — 30 TON <button onclick="startMockPayment(100)">Купить</button></li>
    <li>250 сфер — 60 TON <button onclick="startMockPayment(250)">Купить</button></li>
    <li>500 сфер — 100 TON <button onclick="startMockPayment(500)">Купить</button></li>
  </ul>
  <button class="close-btn" onclick="closeTabs()">Close</button>
</div>

<div id="quests-tab" class="tab">
  <h2>Quests</h2>
  <ul>
    <li>Сделай 10 генераций — 10 XP</li>
    <li>Собери 4 одинаковых фигуры — 10 XP</li>
    <li>3 совпадения по цвету — 10 XP</li>
  </ul>
  <button class="close-btn" onclick="closeTabs()">Close</button>
</div>

<div id="friends-tab" class="tab">
  <h2>Friends</h2>
  <p>Приглашённые друзья:</p>
  <ul><li>Пока никого...</li></ul>
  <p>Рейтинг по уровню:</p>
  <ul><li>Игрок1 — Уровень 12</li><li>Игрок2 — Уровень 9</li></ul>
  <button class="close-btn" onclick="closeTabs()">Close</button>
</div>

<div id="profile-tab" class="tab">
  <h2>Profile</h2>
  <div style="display:flex;align-items:center;gap:12px;margin-bottom:12px;">
    <img src="https://i.pravatar.cc/100?u=sealuser" alt="Avatar">
    <div>
      <div style="font-weight:700;font-size:17px;color:#fff;">Seal User</div>
      <div style="font-size:13px;color:#ccc;">Level: <span id="profile-level">1</span></div>
    </div>
  </div>
  <ul id="rare-stats" style="font-size:15px;line-height:1.7;">
    <!-- Заполняется скриптом -->
  </ul>
  <button class="close-btn" onclick="closeTabs()">Close</button>
</div>

<div id="mock-payment-modal">
  <h3>Mock Payment</h3>
  <p>Вы покупаете <span id="mock-payment-amount">0</span> сфер.</p>
  <button id="confirm-payment-btn">Подтвердить</button>
  <button class="close-btn" onclick="closeMockPayment()">Отмена</button>
  <p id="mock-payment-result"></p>
</div>
<div id="rune-collection-tab" class="tab">
  <h2>Коллекция рун</h2>
  <div id="rune-collection-grid" style="display:grid; grid-template-columns:repeat(6,1fr); gap:8px; margin-top:12px;"></div>
  <button class="close-btn" onclick="closeTabs()">Закрыть</button>
</div>
<div id="chest-tab" class="tab">
  <h2>Сундуки</h2>
  <div id="chest-list"></div>
  <div id="chest-open-result"></div>
  <button class="close-btn" onclick="closeTabs()">Закрыть</button>
</div>
<div id="main-rune-slots"></div>

<script>
(() => {
  let runeRotation = 0;
setInterval(() => {
  runeRotation += 0.01; // скорость вращения (можно уменьшить для плавности)
  updateRuneSlots();
}, 30);

  function resizeCanvas() {
    const canvas = document.getElementById('sealCanvas');
    // Квадратный canvas, максимум между панелями
    const topSpace = 100; // top-bar + seal-balance + mining
    const bottomSpace = 90; // bottom-nav + запас
    const avail = Math.min(window.innerWidth, window.innerHeight - topSpace - bottomSpace);
    const dpr = window.devicePixelRatio || 1;
    canvas.width = avail * dpr;
    canvas.height = avail * dpr;
    canvas.style.width = avail + 'px';
    canvas.style.height = avail + 'px';
    return {size: avail, dpr};
  }
  window.addEventListener('resize', resizeCanvas);

  const colors = ['#1E90FF', '#00FFFF', '#7FFF00', '#FF69B4', '#FFD700', '#FF4500'];
  const figureTypes = ['circle', 'equilateralTriangle', 'square'];
  const sizeRange = [160, 190, 220, 250];
  const rotationSpeeds = [0.002, 0.004, 0.006, 0.008];

  const CHANCES = {
    rare: '2%',
    mythic: '0.5%',
    legendary: '0.2%',
    ethereal: '0.02%'
  };

  let spheres = 100, xp = 0, level = 1, seal = 0.000;
  let statTotal = 0, rare = 0, mythic = 0, legendary = 0, ethereal = 0;
  let sealFigures = [];

  const canvas = document.getElementById('sealCanvas');
  const ctx = canvas.getContext('2d');
  const rareMessageEl = document.getElementById('rare-message');
  const sealBalanceEl = document.getElementById('seal-amount');
  const rareStatsEl = document.getElementById('rare-stats');
  const xpEl = document.getElementById('xp');
  const levelEl = document.getElementById('level');
  const profileLevelEl = document.getElementById('profile-level');
  const miningBtn = document.getElementById('mining-btn');
  let miningCooldown = 0;

  function drawShape(ctx, type, x, y, size, color, rotation, glowAlpha, dpr) {
    ctx.save();
    ctx.translate(x, y);
    ctx.rotate(rotation);
    ctx.lineWidth = 7 * dpr;
    ctx.strokeStyle = color;
    ctx.shadowColor = color;
    ctx.shadowBlur = 30 * glowAlpha * dpr;
    ctx.beginPath();
    if (type === 'circle') ctx.arc(0, 0, size / 2, 0, Math.PI * 2);
    else if (type === 'equilateralTriangle') {
      for (let i = 0; i < 3; i++) {
        const angle = i * 2 * Math.PI / 3 - Math.PI / 2;
        const px = (size / Math.sqrt(3)) * Math.cos(angle);
        const py = (size / Math.sqrt(3)) * Math.sin(angle);
        if (i === 0) ctx.moveTo(px, py); else ctx.lineTo(px, py);
      }
      ctx.closePath();
    } else if (type === 'square') {
      const s = size / Math.sqrt(2);
      ctx.rect(-s / 2, -s / 2, s, s);
    }
    ctx.stroke();
    ctx.restore();
  }

  function getMatches(key) {
    const map = {};
    for (const f of sealFigures) map[f[key]] = (map[f[key]] || 0) + 1;
    return Object.values(map);
  }

  function checkRarity() {
    const colorMatch = Math.max(...getMatches('color'));
    const typeMatch = Math.max(...getMatches('type'));

    if (colorMatch === 4 && typeMatch === 4) {
      ethereal++; seal += 1;
      showRareMessage('✨ Ethereal Seal!', CHANCES.ethereal);
      return 'ethereal';
    }
    if (colorMatch === 3 && typeMatch === 3) {
      legendary++; seal += 0.1;
      showRareMessage('🔥 Legendary Seal!', CHANCES.legendary);
      return 'legendary';
    }
    if (colorMatch === 4 || typeMatch === 4) {
      mythic++; seal += 0.01;
      showRareMessage('💎 Mythic Seal!', CHANCES.mythic);
      return 'mythic';
    }
    if (colorMatch === 3 || typeMatch === 3) {
      rare++; seal += 0.001;
      showRareMessage('🌟 Rare Seal!', CHANCES.rare);
      return 'rare';
    }
    showRareMessage('', '');
    return 'common';
  }

  function showRareMessage(title, chance) {
    if (title) {
      rareMessageEl.innerHTML = `${title}<br><span style="font-size:13px;">Chance: ${chance}</span>`;
      rareMessageEl.classList.add('active');
      setTimeout(() => rareMessageEl.classList.remove('active'), 2500);
    } else {
      rareMessageEl.textContent = '';
      rareMessageEl.classList.remove('active');
    }
    updateSeal();
    updateProfileStats && updateProfileStats();
  }

  function generateRandomSeal() {
    sealFigures = [];
    for (let i = 0; i < 4; i++) {
      const type = figureTypes[Math.floor(Math.random() * figureTypes.length)];
      const color = colors[Math.floor(Math.random() * colors.length)];
      const size = sizeRange[Math.floor(Math.random() * sizeRange.length)];
      const speed = rotationSpeeds[Math.floor(Math.random() * rotationSpeeds.length)];
      const direction = Math.random() < 0.5 ? 1 : -1;
      sealFigures.push({ type, color, size, speed, direction, rotation: 0 });
    }
    statTotal++;
    checkRarity();
  }

  let time = 0;
  function render() {
    const {size, dpr} = resizeCanvas();
    ctx.setTransform(1,0,0,1,0,0);
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    const cx = canvas.width / 2, cy = canvas.height / 2;
    // Увеличиваем масштаб фигур на 35% сверх canvas!
    const scale = size / 520 * 1.35 * dpr;
    const glowPulse = 0.75 + 0.25 * Math.sin(time * Math.PI * 2 / 3);
    for (let i = 0; i < sealFigures.length; i++) {
      const f = sealFigures[i];
      f.rotation += f.speed * f.direction;
      drawShape(ctx, f.type, cx, cy, f.size * scale, f.color, f.rotation, glowPulse, dpr);
    }
    time += 1 / 60;
    requestAnimationFrame(render);
  }

  function updateSphereCount() {
    document.getElementById('sphere-count').textContent = spheres;
  }

  function gainXP(amount) {
    xp += amount;
    while (xp >= 100) {
      xp -= 100;
      level++;
    }
    xpEl.textContent = xp;
    levelEl.textContent = level;
    if (profileLevelEl) profileLevelEl.textContent = level;
  }

  function updateSeal() {
    sealBalanceEl.textContent = seal.toFixed(3);
  }

  function updateProfileStats() {
    if (!rareStatsEl) return;
    rareStatsEl.innerHTML = `
      <li>Всего генераций: <span>${statTotal}</span></li>
      <li>🌟 Rare: <span>${rare}</span> <span style="color:#00ff88;font-size:13px;">(${CHANCES.rare})</span></li>
      <li>💎 Mythic: <span>${mythic}</span> <span style="color:#00ff88;font-size:13px;">(${CHANCES.mythic})</span></li>
      <li>🔥 Legendary: <span>${legendary}</span> <span style="color:#00ff88;font-size:13px;">(${CHANCES.legendary})</span></li>
      <li>✨ Ethereal: <span>${ethereal}</span> <span style="color:#00ff88;font-size:13px;">(${CHANCES.ethereal})</span></li>
      <li>Баланс $SEAL: <span>${seal.toFixed(3)}</span></li>
    `;
  }

  function onClick() {
    if (spheres <= 0) return alert('Сферы закончились!');
    spheres--; updateSphereCount(); generateRandomSeal(); gainXP(1);
  }

  canvas.addEventListener('click', function(e) {
    onClick();
  });
  canvas.addEventListener('keydown', e => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      onClick();
    }
  });

  window.openTab = id => {
    document.querySelectorAll('.tab').forEach(t => t.style.display = 'none');
    document.getElementById(id).style.display = 'block';
  };
  window.closeTabs = () => {
    document.querySelectorAll('.tab').forEach(t => t.style.display = 'none');
    closeMockPayment && closeMockPayment();
  };

  document.getElementById('avatar-btn').onclick = () => openTab('profile-tab');

  const modal = document.getElementById('mock-payment-modal');
  const confirmBtn = document.getElementById('confirm-payment-btn');
  const result = document.getElementById('mock-payment-result');
  const amountEl = document.getElementById('mock-payment-amount');
  let amount = 0;
  window.startMockPayment = amt => {
    amount = amt;
    amountEl.textContent = amt;
    modal.style.display = 'block';
    result.textContent = '';
    confirmBtn.disabled = false;
  };
  window.closeMockPayment = () => {
    modal.style.display = 'none';
    result.textContent = '';
    confirmBtn.disabled = false;
  };
  confirmBtn.onclick = () => {
    confirmBtn.disabled = true;
    result.textContent = 'Обработка платежа...';
    setTimeout(() => {
      spheres += amount;
      updateSphereCount();
      result.textContent = `Успешно куплено ${amount} сфер!`;
      confirmBtn.disabled = false;
    }, 1500);
  };

  function updateMiningBtn() {
    const now = Math.floor(Date.now()/1000);
    if (miningCooldown > now) {
      miningBtn.disabled = true;
      let left = miningCooldown - now;
      let h = Math.floor(left/3600);
      let m = Math.floor((left%3600)/60);
      let s = left%60;
      miningBtn.textContent = `Mining: ${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
    } else {
      miningBtn.disabled = false;
      miningBtn.textContent = 'Mining';
    }
  }
  miningBtn.onclick = () => {
    if (miningBtn.disabled) return;
    miningCooldown = Math.floor(Date.now()/1000) + 12*3600;
    seal += 0.10;
    updateSeal();
    updateMiningBtn();
  };
  setInterval(updateMiningBtn, 1000);

  resizeCanvas();
  generateRandomSeal();
  updateSphereCount();
  updateSeal();
  updateProfileStats();
  updateMiningBtn();
  render();
// === РУНЫ, СУНДУКИ, БОНУСЫ ===
const RUNES = [
  {id:1,symbol:'ᚠ',name:'Fehu',rarity:'common',color:'#B0BEC5',bonus:'+5% к $SEAL при выпадении редких печатей',effect:'sealBonusRare',value:0.05},
  {id:2,symbol:'ᚢ',name:'Uruz',rarity:'common',color:'#2196F3',bonus:'+5 XP за генерацию',effect:'xpPerGen',value:5},
  {id:3,symbol:'ᚦ',name:'Thurisaz',rarity:'common',color:'#43A047',bonus:'+5% к награде за приглашённых друзей',effect:'inviteBonus',value:0.05},
  {id:4,symbol:'ᚨ',name:'Ansuz',rarity:'common',color:'#E53935',bonus:'+5% к скорости майнинга сфер',effect:'miningSpeed',value:0.05},
  {id:5,symbol:'ᚱ',name:'Raido',rarity:'common',color:'#FB8C00',bonus:'+1 к сферам при нажатии на кнопку майнинга',effect:'spherePerClick',value:1},
  {id:6,symbol:'ᛃ',name:'Jera',rarity:'common',color:'#F06292',bonus:'-5% к стоимости покупок в магазине',effect:'shopDiscount',value:0.05},
  {id:7,symbol:'ᛉ',name:'Algiz',rarity:'rare',color:'#00FFD0',bonus:'+10% к $SEAL при выпадении редких печатей',effect:'sealBonusRare',value:0.10},
  {id:8,symbol:'ᛟ',name:'Othala',rarity:'rare',color:'#FFD700',bonus:'+10 XP за генерацию',effect:'xpPerGen',value:10},
  {id:9,symbol:'ᛈ',name:'Perthro',rarity:'rare',color:'#FF6F91',bonus:'+10% к награде за приглашённых друзей',effect:'inviteBonus',value:0.10},
  {id:10,symbol:'ᛇ',name:'Eiwaz',rarity:'rare',color:'#7C4DFF',bonus:'+10% к скорости майнинга сфер',effect:'miningSpeed',value:0.10},
  {id:11,symbol:'ᛜ',name:'Ingwaz',rarity:'rare',color:'#00E676',bonus:'+2 к сферам при нажатии на кнопку майнинга',effect:'spherePerClick',value:2},
  {id:12,symbol:'ᛞ',name:'Dagaz',rarity:'rare',color:'#FFB300',bonus:'-10% к стоимости покупок в магазине',effect:'shopDiscount',value:0.10},
  {id:13,symbol:'ᛯ',name:'Sol',rarity:'epic',color:'#00FFF7',bonus:'+25% к $SEAL при выпадении редких печатей',effect:'sealBonusRare',value:0.25},
  {id:14,symbol:'᛬',name:'Star',rarity:'epic',color:'#FFD6FF',bonus:'+25 XP за генерацию',effect:'xpPerGen',value:25},
  {id:15,symbol:'᛭',name:'Ether',rarity:'epic',color:'#A7FFEB',bonus:'+25% к награде за приглашённых друзей',effect:'inviteBonus',value:0.25},
  {id:16,symbol:'ᛮ',name:'Lux',rarity:'epic',color:'#FFF176',bonus:'+25% к скорости майнинга сфер',effect:'miningSpeed',value:0.25},
  {id:17,symbol:'ᚺ',name:'Nova',rarity:'epic',color:'#FF80AB',bonus:'+5 к сферам при нажатии на кнопку майнинга',effect:'spherePerClick',value:5},
  {id:18,symbol:'ᛝ',name:'Aurora',rarity:'epic',color:'#00E6FF',bonus:'-20% к стоимости покупок в магазине',effect:'shopDiscount',value:0.20},
];
const CHESTS = [
  {type:'common',name:'Обычный сундук',price:5,pool:['common','rare'],chances:[0.8,0.2]},
  {type:'rare',name:'Редкий сундук',price:10,pool:['rare','epic'],chances:[0.7,0.3]},
  {type:'epic',name:'Очень редкий сундук',price:15,pool:['epic'],chances:[1.0]},
];
let userRunes = [];
let userRuneSlots = [null,null,null];
let userChests = {common:0, rare:0, epic:0};
let selectedRuneId = null;

// Коллекция рун с анимацией и описанием
function updateRuneCollectionGrid() {
  const grid = document.getElementById('rune-collection-grid');
  grid.innerHTML = '';
  RUNES.forEach((rune, idx) => {
    const owned = userRunes.includes(rune.id);
    const cell = document.createElement('div');
    cell.className = 'rune-collection-cell' + (owned ? ' filled' : '') + (selectedRuneId === rune.id ? ' selected' : '');
    cell.style.setProperty('--rune-color', rune.color);
    cell.tabIndex = owned ? 0 : -1;
    cell.innerHTML = `<span class="rune-symbol">${rune.symbol}</span>
      <div class="rune-tooltip"><b>${rune.name}</b> (${rune.rarity})
      \n${rune.bonus}</div>`;
    if (owned) {
      cell.onclick = () => {
        if (selectedRuneId === rune.id) {
          // Второй клик — назначить в первый пустой слот
          const slotIdx = userRuneSlots.findIndex(s => !s);
          if (slotIdx !== -1 && !userRuneSlots.includes(rune.id)) {
            userRuneSlots[slotIdx] = rune.id;
            selectedRuneId = null;
            saveRuneProgress();
            updateRuneCollectionGrid();
            updateRuneSlots();
            return;
          }
        }
        selectedRuneId = rune.id;
        updateRuneCollectionGrid();
        updateRuneSlots();
      };
    }
    grid.appendChild(cell);
  });
}

// Слоты рун с анимацией и кликами
function updateRuneSlots() {
  const container = document.getElementById('main-rune-slots');
  container.innerHTML = '';
  userRuneSlots.forEach((id, i) => {
    if (!id) return;
    const rune = RUNES.find(r => r.id === id);
    if (!rune) return;
    const el = document.createElement('span');
    el.className = 'neon-rune';
    el.style.setProperty('--rune-color', rune.color);
    el.textContent = rune.symbol;
    el.title = rune.name;
    el.onclick = () => {
      // Удаляем руну из слота
      userRuneSlots[i] = null;
      selectedRuneId = null; // Сброс выбора
      saveRuneProgress();
      updateRuneSlots();
      updateRuneCollectionGrid();
    };
    container.appendChild(el);
  });
}


// Активные бонусы
function getActiveBonuses() {
  const bonuses = {};
  for (const id of userRuneSlots) {
    if (!id) continue;
    const rune = RUNES.find(r => r.id === id);
    if (!bonuses[rune.effect]) bonuses[rune.effect] = 0;
    bonuses[rune.effect] += rune.value;
  }
  return bonuses;
}

// SVG сундук с анимацией
function chestSVG(type) {
  const color = type === "epic" ? "#ffd700" : type === "rare" ? "#00eaff" : "#b0bec5";
  return `<svg class="chest-svg" viewBox="0 0 64 64"><rect x="8" y="20" width="48" height="28" rx="6" fill="${color}" stroke="#333" stroke-width="3"/><rect x="8" y="14" width="48" height="16" rx="6" fill="#fff6" stroke="#333" stroke-width="3"/><rect x="26" y="34" width="12" height="14" rx="3" fill="#333" /></svg>`;
}
function updateChestTab() {
  const chestList = document.getElementById('chest-list');
  chestList.innerHTML = '';
  for (const chest of CHESTS) {
    const count = userChests[chest.type] || 0;
    chestList.innerHTML += `
      <div style="margin-bottom:12px;">
        ${chestSVG(chest.type)}
        <b>${chest.name}</b> (${count} шт.)
        <button onclick="openChest('${chest.type}')">Открыть</button>
        <button onclick="buyChest('${chest.type}')">Купить</button>
      </div>`;
  }
}
window.buyChest = function(type) {
  userChests[type] = (userChests[type]||0)+1;
  updateChestTab();
};
window.openChest = function(type) {
  if (!userChests[type] || userChests[type]<=0) return alert('Нет сундуков этого типа!');
  const chestResult = document.getElementById('chest-open-result');
  const chestSVGEl = document.querySelector('.chest-svg');
  if (chestSVGEl) {
    chestSVGEl.classList.add('opening');
    setTimeout(()=>chestSVGEl.classList.remove('opening'), 700);
  }
  setTimeout(()=>{
    const chest = CHESTS.find(c=>c.type===type);
    let pool = [];
    chest.pool.forEach((rarity, i) => {
      const runes = RUNES.filter(r=>r.rarity===rarity && !userRunes.includes(r.id));
      for (let j=0;j<Math.round(chest.chances[i]*100);j++) pool.push(...runes);
    });
    if (pool.length===0) {
      chestResult.innerHTML = 'Все руны этой редкости уже собраны!';
      return;
    }
    const rune = pool[Math.floor(Math.random()*pool.length)];
    userChests[type]--;
    userRunes.push(rune.id);
    chestResult.innerHTML = `<div style="font-size:22px;margin:14px 0;"><span style="color:${rune.color};font-size:2.2em;">${rune.symbol}</span><br><b>${rune.name}</b><br><span style="font-size:13px;">${rune.bonus}</span></div>`;
    updateChestTab();
    updateRuneCollectionGrid();
    setTimeout(()=>chestResult.innerHTML='', 2500);
  }, 700);
};

// Сохранение/загрузка прогресса
function saveRuneProgress() {
  localStorage.setItem('userRunes', JSON.stringify(userRunes));
  localStorage.setItem('userRuneSlots', JSON.stringify(userRuneSlots));
  localStorage.setItem('userChests', JSON.stringify(userChests));
}
function loadRuneProgress() {
  try {
    userRunes = JSON.parse(localStorage.getItem('userRunes')) || [];
    userRuneSlots = JSON.parse(localStorage.getItem('userRuneSlots')) || [null,null,null];
    userChests = JSON.parse(localStorage.getItem('userChests')) || {common:0, rare:0, epic:0};
  } catch(e) {
    userRunes = [];
    userRuneSlots = [null,null,null];
    userChests = {common:0, rare:0, epic:0};
  }
}
setInterval(saveRuneProgress, 10000);
window.addEventListener('beforeunload', saveRuneProgress);

// При старте
loadRuneProgress();
updateRuneCollectionGrid();
updateRuneSlots();
updateChestTab();

// Интеграция бонусов в майнинг
function onClickWithRunes() {
  const bonuses = getActiveBonuses();
  let spheresPerClick = 1 + (bonuses.spherePerClick || 0);
  if (spheres < spheresPerClick) return alert('Сферы закончились!');
  spheres -= spheresPerClick;
  updateSphereCount();
  let xpGain = 10 + (bonuses.xpPerGen || 0);
  gainXP(xpGain);
  generateRandomSeal();
  updateProfileStats();
}
miningBtn.onclick = onClickWithRunes;


  
})();
</script>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Alex Murphy — Video Editor</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@700;800&family=Outfit:wght@300;400;500&display=swap" rel="stylesheet">

<style>
/* ─── RESET ─── */
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --accent:      #7b61ff;
  --accent2:     #a78bfa;
  --glow:        rgba(123,97,255,0.35);
  --glass-bg:    rgba(255,255,255,0.05);
  --glass-border:rgba(255,255,255,0.12);
  --text:        #f0efff;
  --muted:       #9490b8;
}

html { scroll-behavior: smooth; }

body {
  font-family: 'Outfit', sans-serif;
  background: #0b0c1e;
  color: var(--text);
  overflow-x: hidden;
  min-height: 100vh;
}

/* ─── GRADIENT BG ─── */
.bg-gradient {
  position: fixed;
  inset: 0;
  z-index: 0;
  background:
    radial-gradient(ellipse 80% 60% at 15% 10%,  rgba(80,40,160,0.6) 0%, transparent 60%),
    radial-gradient(ellipse 60% 50% at 85% 85%,  rgba(40,20,110,0.65) 0%, transparent 55%),
    radial-gradient(ellipse 100% 80% at 50% 50%, rgba(18,14,50,1)     0%, #0b0c1e 100%);
  pointer-events: none;
}

/* ─── BUBBLE CANVAS ─── */
#bubble-canvas {
  position: fixed;
  inset: 0;
  z-index: 1;
  pointer-events: none;
}

/* ─── PAGES ─── */
.page {
  display: none;
  min-height: 100vh;
  width: 100%;
  position: relative;
  z-index: 2;
  overflow-x: hidden;
}
.page.active { display: flex; }

/* ════════════ PAGE 1 ════════════ */
#page1 {
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 48px 20px 64px;
}

/* Profile section */
.profile-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  opacity: 0;
  transform: translateY(30px) scale(0.95);
  animation: popIn 0.75s cubic-bezier(0.34,1.56,0.64,1) 0.15s forwards;
}

.profile-pic {
  width: 108px;
  height: 108px;
  border-radius: 50%;
  background: transparent;
  border: 2.5px solid var(--accent2);
  box-shadow: 0 0 0 6px rgba(123,97,255,0.14), 0 0 40px var(--glow);
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

/* Replace this img src with your real photo URL */
.profile-pic img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* Placeholder shown when no real photo */
.profile-placeholder {
  display: flex;
  align-items: center;
  justify-content: center;
}
.profile-placeholder svg { opacity: 0.4; }

.name {
  font-family: 'Syne', sans-serif;
  font-weight: 800;
  font-size: clamp(2rem, 9vw, 2.75rem);
  letter-spacing: -0.5px;
  color: #fff;
  margin-top: 18px;
  text-align: center;
  text-shadow: 0 0 48px var(--glow);
}

.about-text {
  font-size: clamp(0.82rem, 3.8vw, 0.93rem);
  color: var(--muted);
  font-weight: 300;
  text-align: center;
  max-width: 295px;
  line-height: 1.65;
  margin-top: 10px;
}

/* Glass card */
.glass-card {
  margin-top: 28px;
  width: 100%;
  max-width: 338px;
  background: rgba(255,255,255,0.045);
  border: 1px solid var(--glass-border);
  border-radius: 22px;
  backdrop-filter: blur(22px);
  -webkit-backdrop-filter: blur(22px);
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 11px;
  box-shadow: 0 8px 48px rgba(0,0,0,0.45), inset 0 1px 0 rgba(255,255,255,0.07);
  opacity: 0;
  transform: translateY(28px) scale(0.95);
  animation: popIn 0.72s cubic-bezier(0.34,1.56,0.64,1) 0.45s forwards;
}

/* Buttons */
.link-btn {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 13px 16px;
  border-radius: 13px;
  background: rgba(255,255,255,0.055);
  border: 1px solid rgba(255,255,255,0.09);
  color: var(--text);
  font-family: 'Outfit', sans-serif;
  font-size: 0.94rem;
  font-weight: 500;
  text-decoration: none;
  cursor: pointer;
  transition: background 0.22s, border-color 0.22s, transform 0.18s, box-shadow 0.22s;
  letter-spacing: 0.2px;
  -webkit-tap-highlight-color: transparent;
}
.link-btn:hover {
  background: rgba(123,97,255,0.18);
  border-color: rgba(167,139,250,0.45);
  transform: translateY(-2px);
  box-shadow: 0 6px 28px rgba(123,97,255,0.22);
}
.link-btn:active { transform: translateY(0); }

.btn-left  { display: flex; align-items: center; gap: 11px; }
.btn-icon  { width: 24px; height: 24px; flex-shrink: 0; display: flex; align-items: center; justify-content: center; }

.arrow-circle {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  background: var(--accent);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  transition: background 0.2s;
}
.link-btn:hover .arrow-circle { background: var(--accent2); }

.ext-icon { opacity: 0.3; }

/* Social icons */
.social-row {
  display: flex;
  gap: 14px;
  margin-top: 20px;
  opacity: 0;
  transform: translateY(20px);
  animation: popIn 0.65s cubic-bezier(0.34,1.56,0.64,1) 0.7s forwards;
}

.social-btn {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  border: 1.5px solid var(--glass-border);
  background: rgba(255,255,255,0.05);
  backdrop-filter: blur(12px);
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--muted);
  text-decoration: none;
  transition: border-color 0.2s, color 0.2s, transform 0.18s, box-shadow 0.22s;
  -webkit-tap-highlight-color: transparent;
}
.social-btn:hover {
  border-color: var(--accent2);
  color: var(--accent2);
  transform: translateY(-3px);
  box-shadow: 0 6px 22px var(--glow);
}

/* ════════════ PAGE 2 ════════════ */
#page2 {
  flex-direction: column;
  align-items: center;
  padding-bottom: 60px;
}

.portfolio-header {
  width: 100%;
  max-width: 680px;
  padding: 34px 22px 8px;
  display: flex;
  align-items: center;
  gap: 16px;
  opacity: 0;
  animation: fadeUp 0.55s ease 0.05s forwards;
}

.back-btn {
  width: 42px;
  height: 42px;
  border-radius: 50%;
  border: 1.5px solid var(--glass-border);
  background: rgba(255,255,255,0.05);
  backdrop-filter: blur(12px);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: var(--text);
  transition: border-color 0.2s, transform 0.18s;
  flex-shrink: 0;
  -webkit-tap-highlight-color: transparent;
}
.back-btn:hover { border-color: var(--accent2); transform: translateX(-2px); }

.portfolio-title {
  font-family: 'Syne', sans-serif;
  font-size: clamp(1.4rem, 6vw, 1.85rem);
  font-weight: 800;
  color: #fff;
  letter-spacing: -0.3px;
}
.portfolio-sub {
  font-size: 0.8rem;
  color: var(--muted);
  font-weight: 300;
  margin-top: 2px;
}

.video-grid {
  width: 100%;
  max-width: 680px;
  padding: 14px 18px 20px;
  display: flex;
  flex-direction: column;
  gap: 18px;
}

.video-card {
  width: 100%;
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid var(--glass-border);
  background: rgba(255,255,255,0.03);
  box-shadow: 0 4px 32px rgba(0,0,0,0.4);
  opacity: 0;
  transform: translateY(26px);
  transition: transform 0.2s, box-shadow 0.2s;
}
.video-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 14px 44px rgba(123,97,255,0.2);
}
.video-card.animate {
  animation: fadeUp 0.55s cubic-bezier(0.34,1.3,0.64,1) forwards;
}
.video-card iframe {
  width: 100%;
  display: block;
  aspect-ratio: 16/9;
  border: none;
}
.video-label {
  padding: 11px 15px;
  font-size: 0.86rem;
  font-weight: 500;
  color: var(--muted);
  letter-spacing: 0.3px;
}

/* YouTube thumbnail card */
.yt-thumb {
  display: block;
  position: relative;
  width: 100%;
  aspect-ratio: 16/9;
  overflow: hidden;
  text-decoration: none;
  background: #000;
}
.yt-thumb img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
  transition: transform 0.3s ease, opacity 0.3s ease;
}
.yt-thumb:hover img {
  transform: scale(1.03);
  opacity: 0.85;
}
.yt-play {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.2s ease;
}
.yt-thumb:hover .yt-play svg circle { fill: rgba(0,0,0,0.72); }
.yt-thumb:hover .yt-play { transform: scale(1.1); }
.yt-badge {
  position: absolute;
  bottom: 10px;
  right: 10px;
  background: rgba(0,0,0,0.7);
  color: #fff;
  font-size: 0.72rem;
  font-weight: 500;
  padding: 4px 9px;
  border-radius: 6px;
  display: flex;
  align-items: center;
  gap: 5px;
  letter-spacing: 0.3px;
  backdrop-filter: blur(4px);
}

/* Placeholder video card */
.video-placeholder-card {
  opacity: 0.55 !important;
  cursor: not-allowed;
}
.placeholder-screen {
  width: 100%;
  aspect-ratio: 16/9;
  background: rgba(255,255,255,0.03);
  border-bottom: 1px solid var(--glass-border);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 12px;
}
.placeholder-label {
  font-size: 0.78rem;
  color: rgba(255,255,255,0.25);
  font-weight: 500;
  letter-spacing: 1.5px;
  text-transform: uppercase;
}

/* ─── KEYFRAMES ─── */
@keyframes popIn {
  0%   { opacity: 0; transform: translateY(28px) scale(0.95); }
  100% { opacity: 1; transform: translateY(0)    scale(1);    }
}
@keyframes fadeUp {
  0%   { opacity: 0; transform: translateY(26px); }
  100% { opacity: 1; transform: translateY(0);    }
}

/* ─── RESPONSIVE ─── */
@media (max-width: 380px) {
  .glass-card { padding: 12px; }
  .link-btn   { padding: 12px 13px; font-size: 0.88rem; }
  .profile-pic{ width: 92px; height: 92px; }
}
</style>
</head>
<body>

<div class="bg-gradient"></div>
<canvas id="bubble-canvas"></canvas>

<!-- ══════════ PAGE 1 ══════════ -->
<div id="page1" class="page active">

  <div class="profile-wrapper">
    <div class="profile-pic">
      <img src="data:image/png;base64,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" alt="Walker">
    </div>
    <h1 class="name">Walker</h1>
    <p class="about-text">Freelance video editor crafting cinematic stories — reels, short films &amp; brand content.</p>
  </div>

  <div class="glass-card">

    <!-- Portfolio -->
    <button class="link-btn" id="portfolio-btn">
      <span class="btn-left">
        <span class="btn-icon">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
            <rect x="2" y="3" width="20" height="18" rx="2" stroke="#a78bfa" stroke-width="1.7"/>
            <path d="M7 3v18M17 3v18M2 8h5M17 8h5M2 16h5M17 16h5" stroke="#a78bfa" stroke-width="1.4" stroke-linecap="round"/>
          </svg>
        </span>
        Portfolio
      </span>
      <span class="arrow-circle">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
          <path d="M5 12h14M13 6l6 6-6 6" stroke="#fff" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </span>
    </button>

    <!-- WhatsApp -->
    <a class="link-btn" href="https://wa.me/6397797497/?text=Hi" target="_blank" rel="noopener">
      <span class="btn-left">
        <span class="btn-icon">
          <svg width="24" height="24" viewBox="0 0 32 32" fill="none">
            <circle cx="16" cy="16" r="16" fill="#25D366"/>
            <path d="M23.5 8.5A10.44 10.44 0 0 0 16 5.5a10.5 10.5 0 0 0-9.08 15.72L5.5 26.5l5.45-1.43A10.5 10.5 0 1 0 16 5.5Zm0 14.84a8.72 8.72 0 0 1-4.44-1.21l-.32-.19-3.24.85.86-3.15-.2-.33A8.72 8.72 0 1 1 16 24.84Zm4.78-6.53c-.26-.13-1.55-.77-1.79-.85-.24-.09-.41-.13-.58.13s-.67.85-.82 1.02c-.15.17-.3.19-.56.06a7.12 7.12 0 0 1-2.1-1.3 7.9 7.9 0 0 1-1.45-1.81c-.15-.26 0-.4.12-.53.11-.11.26-.3.39-.45a1.77 1.77 0 0 0 .26-.43.48.48 0 0 0-.02-.45c-.06-.13-.58-1.39-.79-1.9-.21-.5-.42-.43-.57-.44h-.49a.94.94 0 0 0-.68.32 2.88 2.88 0 0 0-.9 2.14 5.01 5.01 0 0 0 1.05 2.66 11.47 11.47 0 0 0 4.4 3.88c.61.26 1.09.42 1.46.54a3.52 3.52 0 0 0 1.61.1 2.64 2.64 0 0 0 1.73-1.22 2.14 2.14 0 0 0 .15-1.22c-.06-.11-.22-.17-.48-.3Z" fill="#fff"/>
          </svg>
        </span>
        WhatsApp
      </span>
      <svg class="ext-icon" width="16" height="16" viewBox="0 0 24 24" fill="none">
        <path d="M7 17L17 7M17 7H7M17 7v10" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </a>

    <!-- Telegram -->
    <a class="link-btn" href="https://t.me/Walker576" target="_blank" rel="noopener">
      <span class="btn-left">
        <span class="btn-icon">
          <svg width="24" height="24" viewBox="0 0 32 32" fill="none">
            <circle cx="16" cy="16" r="16" fill="#29A9EB"/>
            <path d="M6.5 15.5c5.2-2.27 8.67-3.77 10.4-4.5 4.95-2.06 5.98-2.42 6.65-2.43.15 0 .47.03.68.2.18.14.23.33.25.47.02.13.05.43.03.66-.22 2.33-1.19 7.99-1.68 10.6-.21 1.1-.61 1.47-1 1.51-.85.07-1.5-.56-2.32-1.1-1.29-.85-2.02-1.38-3.27-2.2-1.45-.96-.51-1.49.32-2.35.21-.22 3.95-3.62 4.02-3.93.01-.04.01-.19-.07-.27-.08-.08-.21-.05-.3-.03-.13.03-2.15 1.37-6.07 4.01-.57.39-1.09.58-1.56.57-.51-.01-1.5-.29-2.23-.53-.9-.29-1.61-.45-1.55-.94.03-.26.39-.52 1.1-.8Z" fill="white"/>
          </svg>
        </span>
        Telegram
      </span>
      <svg class="ext-icon" width="16" height="16" viewBox="0 0 24 24" fill="none">
        <path d="M7 17L17 7M17 7H7M17 7v10" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </a>

    <!-- Fiverr -->
    <a class="link-btn" href="https://www.fiverr.com/s/jjzeLDv" target="_blank" rel="noopener">
      <span class="btn-left">
        <span class="btn-icon">
          <svg width="24" height="24" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
            <rect width="100" height="100" rx="16" fill="#1DBF73"/>
            <text x="48" y="74" text-anchor="middle" font-family="Arial Black,sans-serif" font-size="66" font-weight="900" fill="#fff">f</text>
            <circle cx="76" cy="26" r="7.5" fill="#fff"/>
          </svg>
        </span>
        Fiverr
      </span>
      <svg class="ext-icon" width="16" height="16" viewBox="0 0 24 24" fill="none">
        <path d="M7 17L17 7M17 7H7M17 7v10" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </a>

  </div>

  <!-- Social row -->
  <div class="social-row">
    <!-- SMS -->
    <a class="social-btn" href="sms:6397797497" title="Send SMS">
      <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
        <path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
        <circle cx="8.5"  cy="10" r="1" fill="currentColor"/>
        <circle cx="12"   cy="10" r="1" fill="currentColor"/>
        <circle cx="15.5" cy="10" r="1" fill="currentColor"/>
      </svg>
    </a>
    <!-- Gmail -->
    <a class="social-btn" href="mailto:everreadyto099@gmail.com" title="Email me">
      <svg width="20" height="20" viewBox="0 0 24 24" fill="none">
        <rect x="2" y="4" width="20" height="16" rx="2" stroke="currentColor" stroke-width="1.8"/>
        <path d="M2 7l10 6.5L22 7" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </a>
  </div>

</div>
<!-- end page1 -->

<!-- ══════════ PAGE 2 ══════════ -->
<div id="page2" class="page">

  <div class="portfolio-header">
    <button class="back-btn" id="back-btn" aria-label="Go back">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
        <path d="M19 12H5M11 6l-6 6 6 6" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </button>
    <div>
      <div class="portfolio-title">My Portfolio</div>
      <div class="portfolio-sub">Selected works · Alex Murphy</div>
    </div>
  </div>

  <div class="video-grid" id="video-grid">

    <!--
      ── HOW TO ADD YOUR OWN VIDEOS ──────────────────────────────
      YouTube embed:
        <iframe src="https://www.youtube.com/embed/VIDEO_ID" ...></iframe>
      Local video file:
        <video src="your-video.mp4" controls></video>
    -->

    <!-- Video 1 — Google Drive -->
    <div class="video-card">
      <iframe
        src="https://drive.google.com/file/d/1MUv1MpDZ8TPhsoIc7DmXf_IafQBSTsa5/preview"
        title="Video Edit — Work 1"
        allow="autoplay"
        allowfullscreen
        loading="lazy">
      </iframe>
      <div class="video-label">✦ Video Edit · Work 1</div>
    </div>

    <!-- Video 2 — YouTube thumbnail card -->
    <div class="video-card">
      <a class="yt-thumb" href="https://youtu.be/-8fK4miFxlE" target="_blank" rel="noopener" aria-label="Watch Video Edit Work 2 on YouTube">
        <img src="https://img.youtube.com/vi/-8fK4miFxlE/maxresdefault.jpg"
             onerror="this.src='https://img.youtube.com/vi/-8fK4miFxlE/hqdefault.jpg'"
             alt="Video Edit Work 2" loading="lazy">
        <span class="yt-play">
          <svg width="52" height="52" viewBox="0 0 72 72" fill="none">
            <circle cx="36" cy="36" r="36" fill="rgba(0,0,0,0.55)"/>
            <polygon points="28,20 56,36 28,52" fill="white"/>
          </svg>
        </span>
        <span class="yt-badge">
          <svg width="14" height="10" viewBox="0 0 90 63" fill="none"><rect width="90" height="63" rx="14" fill="#FF0000"/><polygon points="36,15 62,31.5 36,48" fill="white"/></svg>
          YouTube
        </span>
      </a>
      <div class="video-label">✦ Video Edit · Work 2</div>
    </div>

    <!-- Video 3 — Placeholder (link not provided yet) -->
    <div class="video-card video-placeholder-card">
      <div class="placeholder-screen">
        <svg width="48" height="48" viewBox="0 0 24 24" fill="none">
          <circle cx="12" cy="12" r="10" stroke="rgba(255,255,255,0.15)" stroke-width="1.5"/>
          <path d="M10 8l6 4-6 4V8z" fill="rgba(255,255,255,0.2)"/>
        </svg>
        <span class="placeholder-label">Coming Soon</span>
      </div>
      <div class="video-label" style="opacity:0.4;">✦ Video Edit · Work 3</div>
    </div>

  </div>
</div>
<!-- end page2 -->

<script>
document.addEventListener('DOMContentLoaded', function () {

  /* ── PAGE NAV ── */
  document.getElementById('portfolio-btn').addEventListener('click', function () {
    document.getElementById('page1').classList.remove('active');
    var p2 = document.getElementById('page2');
    p2.classList.add('active');
    var items = p2.querySelectorAll('.portfolio-header, .video-card');
    items.forEach(function (el, i) {
      el.classList.remove('animate');
      el.style.animation = 'none';
      el.style.opacity = '0';
      void el.offsetWidth;
      el.style.animation = '';
      setTimeout(function () {
        el.style.animationDelay = (i * 0.13) + 's';
        el.classList.add('animate');
      }, 10);
    });
    window.scrollTo(0, 0);
  });

  document.getElementById('back-btn').addEventListener('click', function () {
    document.getElementById('page2').classList.remove('active');
    document.getElementById('page1').classList.add('active');
    window.scrollTo(0, 0);
  });

  /* ── BUBBLES ── */
  var canvas = document.getElementById('bubble-canvas');
  var ctx = canvas.getContext('2d');
  var W, H, bubbles = [];

  function resize() {
    W = canvas.width = window.innerWidth;
    H = canvas.height = window.innerHeight;
  }
  window.addEventListener('resize', resize);
  resize();

  function rand(a, b) { return a + Math.random() * (b - a); }

  function makeBubble(startBelow) {
    var r = rand(8, 52);
    return {
      x: rand(0, W),
      y: startBelow ? H + r + rand(0, H * 0.5) : rand(-H, H),
      r: r,
      speed: rand(0.22, 0.85),
      wobble: rand(0, Math.PI * 2),
      wobbleSpd: rand(0.005, 0.018),
      wobbleAmt: rand(10, 38),
      alpha: rand(0.04, 0.14)
    };
  }

  for (var i = 0; i < 40; i++) bubbles.push(makeBubble(false));

  function draw() {
    ctx.clearRect(0, 0, W, H);
    bubbles.forEach(function (b) {
      b.y -= b.speed;
      b.wobble += b.wobbleSpd;
      var x = b.x + Math.sin(b.wobble) * b.wobbleAmt;
      var g = ctx.createRadialGradient(x - b.r * 0.3, b.y - b.r * 0.3, b.r * 0.08, x, b.y, b.r);
      g.addColorStop(0, 'rgba(160,140,255,' + (b.alpha * 1.5) + ')');
      g.addColorStop(1, 'rgba(70,50,190,0)');
      ctx.beginPath();
      ctx.arc(x, b.y, b.r, 0, Math.PI * 2);
      ctx.fillStyle = g;
      ctx.fill();
      ctx.strokeStyle = 'rgba(190,170,255,' + (b.alpha * 0.75) + ')';
      ctx.lineWidth = 0.9;
      ctx.stroke();
      if (b.y + b.r < -20) Object.assign(b, makeBubble(true));
    });
    requestAnimationFrame(draw);
  }
  draw();

});
</script>
</body>
</html>

<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>幻の動画 — 再生数10以下の世界</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Noto+Serif+JP:wght@300;400;700&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0a0f;
    --surface: #111118;
    --border: #1e1e2e;
    --accent: #c8f0a0;
    --accent2: #a0d4f0;
    --text: #e8e8f0;
    --muted: #555570;
    --danger: #f0a0a0;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Noto Serif JP', serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Noise texture */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 999;
  }

  header {
    padding: 3rem 2rem 2rem;
    text-align: center;
    border-bottom: 1px solid var(--border);
    position: relative;
  }

  .eyebrow {
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    letter-spacing: 0.3em;
    color: var(--muted);
    text-transform: uppercase;
    margin-bottom: 1rem;
  }

  h1 {
    font-size: clamp(2rem, 6vw, 4rem);
    font-weight: 300;
    line-height: 1.1;
    color: var(--text);
    position: relative;
    display: inline-block;
  }

  h1 span {
    color: var(--accent);
  }

  .subtitle {
    margin-top: 0.75rem;
    font-size: 0.85rem;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
  }

  .counter-badge {
    display: inline-block;
    background: var(--accent);
    color: var(--bg);
    font-family: 'Space Mono', monospace;
    font-size: 0.7rem;
    font-weight: 700;
    padding: 0.2em 0.6em;
    border-radius: 2px;
    margin-left: 0.5rem;
    vertical-align: middle;
  }

  /* API key setup */
  .setup-panel {
    max-width: 600px;
    margin: 3rem auto;
    padding: 2rem;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 4px;
  }

  .setup-panel h2 {
    font-size: 1rem;
    font-weight: 400;
    margin-bottom: 1rem;
    color: var(--accent2);
    font-family: 'Space Mono', monospace;
  }

  .setup-panel p {
    font-size: 0.8rem;
    color: var(--muted);
    line-height: 1.7;
    margin-bottom: 1.5rem;
  }

  .setup-panel a {
    color: var(--accent);
  }

  .input-row {
    display: flex;
    gap: 0.75rem;
  }

  input[type="text"] {
    flex: 1;
    background: var(--bg);
    border: 1px solid var(--border);
    color: var(--text);
    padding: 0.75rem 1rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.8rem;
    border-radius: 2px;
    outline: none;
    transition: border-color 0.2s;
  }

  input[type="text"]:focus {
    border-color: var(--accent);
  }

  button {
    background: var(--accent);
    color: var(--bg);
    border: none;
    padding: 0.75rem 1.5rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.8rem;
    font-weight: 700;
    cursor: pointer;
    border-radius: 2px;
    transition: opacity 0.2s, transform 0.1s;
    white-space: nowrap;
  }

  button:hover { opacity: 0.85; }
  button:active { transform: scale(0.98); }
  button:disabled { opacity: 0.4; cursor: not-allowed; }

  /* Main content */
  .main {
    max-width: 900px;
    margin: 0 auto;
    padding: 2rem;
    display: none;
  }

  .controls {
    display: flex;
    align-items: center;
    gap: 1rem;
    margin-bottom: 2rem;
    flex-wrap: wrap;
  }

  .search-btn {
    background: var(--accent);
    color: var(--bg);
    border: none;
    padding: 0.85rem 2rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.85rem;
    font-weight: 700;
    cursor: pointer;
    border-radius: 2px;
    transition: all 0.2s;
    position: relative;
    overflow: hidden;
  }

  .search-btn::after {
    content: '';
    position: absolute;
    inset: 0;
    background: rgba(0,0,0,0.15);
    opacity: 0;
    transition: opacity 0.2s;
  }

  .search-btn:hover::after { opacity: 1; }

  .status {
    font-family: 'Space Mono', monospace;
    font-size: 0.7rem;
    color: var(--muted);
  }

  .status.loading { color: var(--accent2); }
  .status.error { color: var(--danger); }

  /* Video card */
  .video-display {
    display: none;
    animation: fadeUp 0.5s ease forwards;
  }

  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .video-wrapper {
    position: relative;
    width: 100%;
    padding-bottom: 56.25%;
    background: #000;
    border-radius: 2px;
    overflow: hidden;
    border: 1px solid var(--border);
  }

  .video-wrapper iframe {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    border: none;
  }

  .video-meta {
    margin-top: 1.5rem;
    display: grid;
    grid-template-columns: 1fr auto;
    gap: 1rem;
    align-items: start;
  }

  .video-title {
    font-size: 1.1rem;
    font-weight: 400;
    line-height: 1.4;
    color: var(--text);
  }

  .video-stats {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    gap: 0.4rem;
  }

  .view-count {
    font-family: 'Space Mono', monospace;
    font-size: 2rem;
    font-weight: 700;
    color: var(--accent);
    line-height: 1;
  }

  .view-label {
    font-family: 'Space Mono', monospace;
    font-size: 0.6rem;
    color: var(--muted);
    letter-spacing: 0.15em;
    text-transform: uppercase;
  }

  .video-info-row {
    margin-top: 1rem;
    display: flex;
    gap: 1.5rem;
    flex-wrap: wrap;
  }

  .info-chip {
    font-family: 'Space Mono', monospace;
    font-size: 0.7rem;
    color: var(--muted);
    display: flex;
    align-items: center;
    gap: 0.4rem;
  }

  .info-chip .dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    background: var(--border);
  }

  .video-link {
    display: inline-block;
    margin-top: 1.5rem;
    color: var(--accent2);
    font-family: 'Space Mono', monospace;
    font-size: 0.75rem;
    text-decoration: none;
    border-bottom: 1px solid currentColor;
    padding-bottom: 1px;
    transition: color 0.2s;
  }

  .video-link:hover { color: var(--accent); }

  /* History */
  .history-section {
    margin-top: 3rem;
    border-top: 1px solid var(--border);
    padding-top: 2rem;
  }

  .history-title {
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 1.5rem;
  }

  .history-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 1rem;
  }

  .history-item {
    cursor: pointer;
    border: 1px solid var(--border);
    border-radius: 2px;
    overflow: hidden;
    transition: border-color 0.2s;
    background: var(--surface);
  }

  .history-item:hover { border-color: var(--accent); }

  .history-thumb {
    width: 100%;
    aspect-ratio: 16/9;
    object-fit: cover;
    display: block;
    background: var(--bg);
  }

  .history-info {
    padding: 0.6rem;
  }

  .history-views {
    font-family: 'Space Mono', monospace;
    font-size: 0.7rem;
    color: var(--accent);
    font-weight: 700;
  }

  .history-ttl {
    font-size: 0.72rem;
    color: var(--muted);
    margin-top: 0.2rem;
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    line-height: 1.4;
  }

  /* Scanning animation */
  .scan-indicator {
    display: none;
    align-items: center;
    gap: 0.75rem;
    margin: 2rem 0;
  }

  .scan-indicator.active { display: flex; }

  .scan-bar {
    flex: 1;
    height: 2px;
    background: var(--border);
    border-radius: 1px;
    overflow: hidden;
    position: relative;
  }

  .scan-bar::after {
    content: '';
    position: absolute;
    top: 0;
    left: -40%;
    width: 40%;
    height: 100%;
    background: var(--accent);
    animation: scan 1.2s ease-in-out infinite;
    border-radius: 1px;
  }

  @keyframes scan {
    0% { left: -40%; }
    100% { left: 100%; }
  }

  .scan-text {
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    color: var(--accent2);
    white-space: nowrap;
  }

  .no-api {
    background: rgba(200, 240, 160, 0.05);
    border: 1px solid rgba(200, 240, 160, 0.15);
    border-radius: 4px;
    padding: 2rem;
    margin: 2rem 0;
    text-align: center;
  }

  .no-api h3 {
    color: var(--accent);
    font-size: 0.9rem;
    font-family: 'Space Mono', monospace;
    margin-bottom: 0.75rem;
    font-weight: 400;
  }

  .no-api p {
    color: var(--muted);
    font-size: 0.8rem;
    line-height: 1.7;
  }

  .no-api a { color: var(--accent2); }

  footer {
    text-align: center;
    padding: 3rem 2rem;
    font-family: 'Space Mono', monospace;
    font-size: 0.6rem;
    color: var(--muted);
    letter-spacing: 0.1em;
    border-top: 1px solid var(--border);
    margin-top: 4rem;
  }

  /* Pulse dot */
  .live-dot {
    display: inline-block;
    width: 6px;
    height: 6px;
    background: var(--accent);
    border-radius: 50%;
    margin-right: 0.5rem;
    animation: pulse 2s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.4; transform: scale(0.7); }
  }

  /* ===== 国選択 + 埋め込み動画エリア ===== */
  .region-card {
    max-width: 900px;
    margin: 2rem auto 1.5rem;
    padding: 1.5rem 1.75rem;
    background: rgba(6, 199, 85, 0.04);
    border: 1px solid rgba(6, 199, 85, 0.4);
    border-radius: 4px;
  }

  .region-label {
    display: block;
    font-family: 'Space Mono', monospace;
    font-size: 0.65rem;
    letter-spacing: 0.18em;
    text-transform: uppercase;
    color: var(--accent);
    margin-bottom: 0.75rem;
  }

  .region-row {
    display: flex;
    align-items: center;
    gap: 1.25rem;
    flex-wrap: wrap;
    margin-bottom: 0.75rem;
  }

  .region-select {
    padding: 0.6rem 0.9rem;
    border-radius: 4px;
    border: 1px solid var(--border);
    background: var(--bg);
    color: var(--text);
    font-family: 'Space Mono', monospace;
    font-size: 0.8rem;
    min-width: 180px;
    cursor: pointer;
  }

  .region-select:focus {
    outline: none;
    border-color: var(--accent);
  }

  .region-current {
    font-size: 0.8rem;
    color: var(--muted);
  }

  .region-current strong {
    color: var(--accent);
  }

  .region-source-toggle {
    margin-top: 0.5rem;
    display: flex;
    gap: 1.25rem;
    flex-wrap: wrap;
    font-size: 0.75rem;
    color: var(--muted);
  }

  .region-source-toggle label {
    cursor: pointer;
  }

  .region-video-wrapper {
    max-width: 900px;
    margin: 0 auto;
  }

  .region-video-frame {
    position: relative;
    width: 100%;
    padding-bottom: 56.25%;
    background: #000;
    border-radius: 4px;
    overflow: hidden;
    border: 1px solid var(--border);
  }

  .region-video-frame video {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    border: none;
    background: #000;
  }

  .region-placeholder,
  .region-error {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 0.6rem;
    padding: 1.5rem;
    text-align: center;
    font-size: 0.8rem;
  }

  .region-placeholder {
    color: var(--muted);
  }

  .region-error {
    color: var(--danger);
  }

  .region-hidden {
    display: none;
  }

  .region-note {
    max-width: 900px;
    margin: 0.9rem auto 0;
    font-size: 0.7rem;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
  }
</style>
</head>
<body>

<header>
  <div class="eyebrow">digital archaeology</div>
  <h1>幻の<span>動画</span></h1>
  <div class="subtitle">再生数 <strong style="color:var(--accent)">≤ 10</strong> の動画を探索する</div>
</header>

<!-- 国選択 + 埋め込み動画 -->
<section class="region-card">
  <span class="region-label">// REGION & SOURCE</span>
  <div class="region-row">
    <select id="regionSelect" class="region-select" aria-label="国・リージョンを選択">
      <option value="JP" selected>日本</option>
      <option value="US">アメリカ</option>
      <option value="GB">イギリス</option>
      <option value="DE">ドイツ</option>
      <option value="FR">フランス</option>
      <option value="KR">韓国</option>
    </select>
    <div class="region-current">
      現在: <strong id="regionCurrentLabel">日本</strong> に設定された動画を再生
    </div>
  </div>
  <div class="region-source-toggle">
    <label>
      <input type="radio" name="regionSource" id="regionSourceInternal" value="internal" checked>
      サイト内の動画（例: <code>videos/jp.mp4</code>）
    </label>
    <label>
      <input type="radio" name="regionSource" id="regionSourceExternal" value="external">
      外部のサンプル動画URL（テスト用）
    </label>
  </div>
</section>

<section class="region-video-wrapper">
  <div class="region-video-frame">
    <video id="regionVideo" controls playsinline class="region-hidden">
      <source id="regionVideoSource" src="" type="video/mp4">
      お使いのブラウザは動画の再生に対応していません。
    </video>
    <div id="regionPlaceholder" class="region-placeholder">
      国とソースを選ぶと、ここに動画が埋め込まれて再生されます。
    </div>
    <div id="regionError" class="region-error region-hidden"></div>
  </div>
  <div class="region-note">
    内部動画モードでは <code>videos/jp.mp4</code>, <code>videos/us.mp4</code> ... のように
    このHTMLと同じ階層に <code>videos</code> フォルダを作り、その中にファイルを置いてください。
  </div>
</section>

<!-- API Key Setup -->
<div id="setupPanel" class="setup-panel">
  <h2>// YouTube Data API キーを入力</h2>
  <p>
    このサイトはYouTube Data API v3を使用します。<br>
    <a href="https://console.cloud.google.com/apis/library/youtube.googleapis.com" target="_blank">Google Cloud Console</a> でAPIキーを取得してください（無料枠あり）。<br>
    入力したキーはブラウザ内にのみ保存され、外部には送信されません。
  </p>
  <div class="input-row">
    <input type="text" id="apiKeyInput" placeholder="AIza..." autocomplete="off">
    <button onclick="saveApiKey()">開始する</button>
  </div>
  <div id="keyError" style="margin-top:0.75rem; font-family:'Space Mono',monospace; font-size:0.7rem; color:var(--danger); display:none;"></div>
</div>

<!-- Main App -->
<div id="mainApp" class="main">

  <div class="controls">
    <button class="search-btn" id="searchBtn" onclick="findVideo()">
      ランダム探索
    </button>
    <div class="status" id="statusText"></div>
  </div>
  <p class="region-note" style="margin-top:0.25rem; margin-bottom:1.5rem;">※「国・リージョン」で選んだ地域で視聴可能な動画だけを検索します（日本＝日本で見れる動画）</p>

  <div class="scan-indicator" id="scanIndicator">
    <div class="scan-bar"></div>
    <div class="scan-text" id="scanText">YouTube をスキャン中...</div>
    <div class="scan-bar"></div>
  </div>

  <div class="video-display" id="videoDisplay">
    <div class="video-wrapper">
      <iframe id="videoFrame" allowfullscreen allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"></iframe>
    </div>
    <div class="video-meta">
      <div class="video-title" id="videoTitle"></div>
      <div class="video-stats">
        <div class="view-count" id="viewCount">0</div>
        <div class="view-label">再生回数</div>
      </div>
    </div>
    <div class="video-info-row">
      <div class="info-chip"><span class="dot"></span><span id="channelName"></span></div>
      <div class="info-chip"><span class="dot"></span><span id="publishDate"></span></div>
      <div class="info-chip"><span class="dot"></span><span id="likeCount"></span></div>
    </div>
    <a id="videoLink" href="#" target="_blank" class="video-link">YouTubeで開く →</a>
    <p class="region-note" style="margin-top:0.75rem;">埋め込みで再生できない場合は「YouTubeで開く」から視聴できます。</p>
  </div>

  <!-- History -->
  <div class="history-section" id="historySection" style="display:none;">
    <div class="history-title"><span class="live-dot"></span>発見履歴</div>
    <div class="history-grid" id="historyGrid"></div>
  </div>
</div>

<footer>
  幻の動画 — YouTube Data API v3 を使用 — キーはローカルのみ保存
</footer>

<script>
// ============================
// Config & State
// ============================
let apiKey = '';
const foundHistory = [];

// 国コード → 表示名
const REGION_NAMES = {
  JP: '日本',
  US: 'アメリカ',
  GB: 'イギリス',
  DE: 'ドイツ',
  FR: 'フランス',
  KR: '韓国',
};

// サイト内（相対パス）の動画
const REGION_INTERNAL = {
  JP: 'videos/jp.mp4',
  US: 'videos/us.mp4',
  GB: 'videos/gb.mp4',
  DE: 'videos/de.mp4',
  FR: 'videos/fr.mp4',
  KR: 'videos/kr.mp4',
};

// 外部サンプル動画URL
const REGION_EXTERNAL = {
  JP: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4',
  US: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ElephantsDream.mp4',
  GB: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerBlazes.mp4',
  DE: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerEscapes.mp4',
  FR: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerFun.mp4',
  KR: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerJoyrides.mp4',
};

// Random query generators
const chars = 'abcdefghijklmnopqrstuvwxyz0123456789';
const words = ['cat','dog','rain','coffee','morning','random','hello','test','video','my','vlog','day','life','first','new','home','travel','food','music','funny','cute','game','walk','sky','tree','bird','sea','snow','flower','car'];
const jpWords = ['散歩','料理','猫','犬','日常','朝','夜','空','海','山','旅','家','友達','思い出','テスト','練習','動画','はじめて','記録'];

function randomQuery() {
  const type = Math.random();
  if (type < 0.25) {
    // Random 2-char string
    return chars[Math.floor(Math.random()*chars.length)] + chars[Math.floor(Math.random()*chars.length)];
  } else if (type < 0.5) {
    // English word
    return words[Math.floor(Math.random()*words.length)];
  } else if (type < 0.75) {
    // Japanese word
    return jpWords[Math.floor(Math.random()*jpWords.length)];
  } else {
    // Random date pattern
    const y = 2015 + Math.floor(Math.random()*9);
    const m = String(Math.floor(Math.random()*12)+1).padStart(2,'0');
    return `${y}/${m}`;
  }
}

// ============================
// API Key
// ============================
function saveApiKey() {
  const val = document.getElementById('apiKeyInput').value.trim();
  if (!val || val.length < 20) {
    showKeyError('有効なAPIキーを入力してください');
    return;
  }
  apiKey = val;
  localStorage.setItem('yt_api_key', val);
  document.getElementById('setupPanel').style.display = 'none';
  document.getElementById('mainApp').style.display = 'block';
}

function showKeyError(msg) {
  const el = document.getElementById('keyError');
  el.textContent = msg;
  el.style.display = 'block';
}

// Embedded API key — show app directly
window.addEventListener('DOMContentLoaded', () => {
  apiKey = 'AIzaSyBxhjjCW-T8yPq3SDq1qa20y4pJ2r5FvBg';
  document.getElementById('setupPanel').style.display = 'none';
  document.getElementById('mainApp').style.display = 'block';

  initRegionVideo();
});

// ============================
// Main Search Logic
// ============================
let attempts = 0;

async function findVideo() {
  const btn = document.getElementById('searchBtn');
  btn.disabled = true;
  attempts = 0;
  setScanActive(true);
  setStatus('', '');
  document.getElementById('videoDisplay').style.display = 'none';

  try {
    const result = await searchUntilFound();
    if (result) {
      displayVideo(result);
      addToHistory(result);
    } else {
      setStatus('見つかりませんでした。もう一度試してください。', 'error');
    }
  } catch(e) {
    console.error(e);
    setStatus('⚠ ' + e.message, 'error');
  } finally {
    btn.disabled = false;
    setScanActive(false);
  }
}

async function searchUntilFound(maxAttempts = 15) {
  // 画面上の国選択に合わせてリージョンと言語を指定（日本なら日本の視聴可能動画＋日本語優先）
  const regionSelect = document.getElementById('regionSelect');
  const regionCode = (regionSelect && regionSelect.value) ? regionSelect.value : 'JP';
  const relevanceLanguage = regionCode === 'JP' ? 'ja' : (regionCode === 'KR' ? 'ko' : 'en');

  for (let i = 0; i < maxAttempts; i++) {
    attempts++;
    const q = randomQuery();
    updateScanText(`"${q}" を検索中... (${REGION_NAMES[regionCode] || regionCode}, 試行 ${attempts})`);

    // Search for videos（regionCode でその国で視聴可能な動画に絞る）
    const searchRes = await ytFetch('search', {
      part: 'id',
      q: q,
      type: 'video',
      order: 'date',
      maxResults: 50,
      regionCode: regionCode,
      relevanceLanguage: relevanceLanguage,
      videoDuration: 'any',
    });

    if (!searchRes.items || searchRes.items.length === 0) continue;

    // Shuffle results
    const ids = searchRes.items.map(x => x.id.videoId);
    shuffle(ids);

    // Get stats in batch
    const statsRes = await ytFetch('videos', {
      part: 'statistics,snippet',
      id: ids.join(','),
    });

    if (!statsRes.items) continue;

    // Filter by view count ≤ 10
    const lowViews = statsRes.items.filter(v => {
      const views = parseInt(v.statistics?.viewCount || '999', 10);
      return views <= 10;
    });

    if (lowViews.length > 0) {
      const pick = lowViews[Math.floor(Math.random() * lowViews.length)];
      return pick;
    }
  }
  return null;
}

async function ytFetch(endpoint, params) {
  const url = new URL(`https://www.googleapis.com/youtube/v3/${endpoint}`);
  url.searchParams.set('key', apiKey);
  for (const [k, v] of Object.entries(params)) {
    url.searchParams.set(k, v);
  }
  let res;
  try {
    res = await fetch(url.toString());
  } catch(fetchErr) {
    throw new Error('ネットワーク接続エラー。APIキーとネット環境をご確認ください。(' + fetchErr.message + ')');
  }
  if (!res.ok) {
    let msg = `HTTPエラー ${res.status}`;
    try {
      const err = await res.json();
      msg = err.error?.message || msg;
      if (res.status === 400) msg = 'APIキーが無効または形式エラーです。';
      if (res.status === 403) msg = 'API権限エラー。Google CloudでYouTube Data API v3を有効にしてください。';
    } catch(e) {}
    throw new Error(msg);
  }
  return res.json();
}

// ============================
// Display
// ============================
function displayVideo(video) {
  const id = video.id;
  const snippet = video.snippet || {};
  const stats = video.statistics || {};

  const views = parseInt(stats.viewCount || '0', 10);
  const likes = parseInt(stats.likeCount || '0', 10);
  const title = snippet.title || '（タイトルなし）';
  const channel = snippet.channelTitle || '不明なチャンネル';
  const published = snippet.publishedAt ? new Date(snippet.publishedAt).toLocaleDateString('ja-JP') : '';

  // 埋め込み再生が許可されていない動画でも「YouTubeで開く」で視聴可能に。nocookie はプライバシー用。
  document.getElementById('videoFrame').src = `https://www.youtube-nocookie.com/embed/${id}?autoplay=0`;
  document.getElementById('videoTitle').textContent = title;
  document.getElementById('viewCount').textContent = views;
  document.getElementById('channelName').textContent = channel;
  document.getElementById('publishDate').textContent = published;
  document.getElementById('likeCount').textContent = `👍 ${likes}`;
  document.getElementById('videoLink').href = `https://www.youtube.com/watch?v=${id}`;

  const display = document.getElementById('videoDisplay');
  display.style.display = 'block';
  display.style.animation = 'none';
  void display.offsetWidth;
  display.style.animation = 'fadeUp 0.5s ease forwards';
}

function addToHistory(video) {
  const id = video.id;
  const title = video.snippet?.title || '';
  const thumb = video.snippet?.thumbnails?.medium?.url || `https://img.youtube.com/vi/${id}/mqdefault.jpg`;
  const views = parseInt(video.statistics?.viewCount || '0', 10);

  foundHistory.unshift({ id, title, thumb, views });
  if (foundHistory.length > 12) foundHistory.pop();

  renderHistory();
}

function renderHistory() {
  const section = document.getElementById('historySection');
  const grid = document.getElementById('historyGrid');
  section.style.display = 'block';
  grid.innerHTML = foundHistory.map(v => `
    <div class="history-item" onclick="loadHistoryVideo('${v.id}')">
      <img class="history-thumb" src="${v.thumb}" alt="" loading="lazy">
      <div class="history-info">
        <div class="history-views">${v.views} 回視聴</div>
        <div class="history-ttl">${escapeHtml(v.title)}</div>
      </div>
    </div>
  `).join('');
}

function loadHistoryVideo(id) {
  document.getElementById('videoFrame').src = `https://www.youtube-nocookie.com/embed/${id}?autoplay=1`;
  window.scrollTo({ top: 0, behavior: 'smooth' });
}

// ============================
// Helpers
// ============================
function setScanActive(active) {
  document.getElementById('scanIndicator').classList.toggle('active', active);
}

function updateScanText(text) {
  document.getElementById('scanText').textContent = text;
}

function setStatus(msg, type) {
  const el = document.getElementById('statusText');
  el.textContent = msg;
  el.className = 'status ' + (type || '');
}

function shuffle(arr) {
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
  return arr;
}

function escapeHtml(s) {
  return s.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
}

// ============================
// Region video (埋め込み動画)
// ============================
function getRegionElements() {
  return {
    select: document.getElementById('regionSelect'),
    currentLabel: document.getElementById('regionCurrentLabel'),
    video: document.getElementById('regionVideo'),
    source: document.getElementById('regionVideoSource'),
    placeholder: document.getElementById('regionPlaceholder'),
    error: document.getElementById('regionError'),
    sourceInternal: document.getElementById('regionSourceInternal'),
    sourceExternal: document.getElementById('regionSourceExternal'),
  };
}

function getRegionVideoUrl(code) {
  const els = getRegionElements();
  const useInternal = els.sourceInternal && els.sourceInternal.checked;
  if (useInternal) {
    return REGION_INTERNAL[code] || REGION_INTERNAL.JP;
  }
  return REGION_EXTERNAL[code] || REGION_EXTERNAL.JP;
}

function updateRegionLabel() {
  const els = getRegionElements();
  if (!els.select || !els.currentLabel) return;
  const code = els.select.value || 'JP';
  els.currentLabel.textContent = REGION_NAMES[code] || code;
}

function hideRegionError() {
  const els = getRegionElements();
  if (!els.error) return;
  els.error.classList.add('region-hidden');
  els.error.textContent = '';
}

function showRegionError(msg) {
  const els = getRegionElements();
  if (!els.error || !els.placeholder) return;
  els.error.textContent = msg;
  els.error.classList.remove('region-hidden');
  els.placeholder.classList.add('region-hidden');
}

function updateRegionVideo() {
  const els = getRegionElements();
  if (!els.select || !els.video || !els.source || !els.placeholder) return;

  const code = els.select.value || 'JP';
  updateRegionLabel();
  hideRegionError();

  const url = getRegionVideoUrl(code);
  if (!url) {
    els.video.classList.add('region-hidden');
    els.placeholder.classList.remove('region-hidden');
    return;
  }

  els.source.src = url;
  els.video.classList.remove('region-hidden');
  els.placeholder.classList.add('region-hidden');
  els.video.load();
}

function initRegionVideo() {
  const els = getRegionElements();
  if (!els.select) return; // セクションがない場合は何もしない

  els.select.addEventListener('change', updateRegionVideo);
  if (els.sourceInternal) els.sourceInternal.addEventListener('change', updateRegionVideo);
  if (els.sourceExternal) els.sourceExternal.addEventListener('change', updateRegionVideo);

  if (els.video) {
    els.video.addEventListener('error', () => {
      const code = els.select ? els.select.value : 'JP';
      const path = getRegionVideoUrl(code);
      showRegionError('動画を読み込めません: ' + path + '\\n\\n' +
        'サイト内動画を使う場合はこのパスにファイルを置くか、「外部のサンプル動画URL」を選択してください。');
    });
    els.video.addEventListener('loadeddata', hideRegionError);
    els.video.addEventListener('canplay', hideRegionError);
  }

  updateRegionVideo();
}
</script>
</body>
</html>

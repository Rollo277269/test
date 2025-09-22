```html
<!DOCTYPE html>
<html lang="it" data-theme="light">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>SalesGPT — Sales Strategy Chatbot</title>
  <style>
    /* ========== CSS VARIABLES (PALETTE & TOKENS) ========== */
    :root{
      --brand-purple:#6D28D9; --brand-orange:#FF7A1A;
      --bg:#FFFFFF; --surface:#F8FAFC; --text:#0F172A; --muted:#475569; --border:#E2E8F0;
      --ok:#10B981; --warn:#F59E0B; --error:#EF4444;
      --radius-lg:14px; --radius-sm:8px;
      --shadow-sm:0 1px 2px rgba(0,0,0,.06);
      --shadow-md:0 6px 24px rgba(0,0,0,.08);
      --gap:16px; --pad:16px; --pad-lg:24px;
      --focus: 0 0 0 3px rgba(109,40,217,.35);
      --chip-bg: rgba(109,40,217,.10);
      --chip-text: var(--brand-purple);
    }
    [data-theme="dark"]{
      --bg:#0B0C10; --surface:#111318; --text:#E5E7EB; --muted:#9CA3AF; --border:#1F2937;
      --chip-bg: rgba(255,122,26,.12);
      --chip-text: var(--brand-orange);
      --shadow-sm: 0 1px 2px rgba(0,0,0,.45);
      --shadow-md: 0 10px 30px rgba(0,0,0,.4);
    }
    /* ========== BASE ========== */
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0;background:var(--bg);color:var(--text);font:14px/1.5 system-ui, -apple-system, Segoe UI, Roboto, Inter, Arial, sans-serif;
    }
    a{color:var(--brand-purple);text-decoration:none}
    a:hover{text-decoration:underline}
    .container{
      display:grid;grid-template-rows:auto 1fr;min-height:100vh;
    }
    /* ========== HEADER ========== */
    header{
      position:sticky;top:0;z-index:20;background:linear-gradient(180deg, var(--bg) 70%, transparent);
      backdrop-filter:saturate(120%) blur(6px);border-bottom:1px solid var(--border);
    }
    .head-bar{
      display:flex;align-items:center;gap:12px;padding:10px var(--pad-lg);
      max-width:1400px;margin:0 auto;
    }
    .title{font-size:16px;font-weight:700;letter-spacing:.2px}
    .spacer{flex:1}
    .chip{
      display:inline-flex;align-items:center;gap:8px;padding:6px 10px;border-radius:999px;
      background:var(--chip-bg);color:var(--chip-text);border:1px dashed rgba(0,0,0,.06);border-color:var(--border);
      font-weight:600
    }
    .chip .dot{width:8px;height:8px;border-radius:50%}
    .dot.ready{background:var(--ok)}
    .dot.thinking{background:var(--warn)}
    .dot.error{background:var(--error)}
    .btn{
      appearance:none;border:1px solid var(--border);background:var(--surface);color:var(--text);
      padding:8px 12px;border-radius:10px;cursor:pointer;box-shadow:var(--shadow-sm);
    }
    .btn:focus-visible{outline:none;box-shadow:var(--focus)}
    .btn.primary{
      background: linear-gradient(135deg, var(--brand-purple), var(--brand-orange));
      color:#fff;border:none;
    }
    .btn.icon{display:inline-flex;align-items:center;gap:8px}
    .btn.small{padding:6px 10px;border-radius:8px;font-size:12px}
    .toggle{
      display:inline-flex;align-items:center;gap:8px;padding:6px 10px;border-radius:999px;border:1px solid var(--border);cursor:pointer;
    }
    /* ========== LAYOUT MAIN ========== */
    main{
      max-width:1400px;margin:0 auto;display:grid;gap:var(--gap);
      grid-template-columns: 1fr 2fr 1fr; padding: var(--pad-lg);
    }
    @media (max-width:1100px){
      main{grid-template-columns:1fr}
      aside.left, aside.right{order:2}
      section.center{order:1}
    }
    .card{
      background:var(--surface);border:1px solid var(--border);border-radius:var(--radius-lg);
      box-shadow:var(--shadow-sm);padding:var(--pad-lg);
    }
    .card h2{margin:0 0 10px 0;font-size:15px}
    .muted{color:var(--muted)}
    .row{display:flex;gap:var(--gap);flex-wrap:wrap}
    .field{
      display:flex;flex-direction:column;gap:6px;flex:1;min-width:180px;
    }
    .field input, .field textarea{
      width:100%;padding:10px 12px;border-radius:10px;border:1px solid var(--border);background:var(--bg);color:var(--text)
    }
    .field input:focus, .field textarea:focus{outline:none;box-shadow:var(--focus)}
    .error-text{color:var(--error);font-size:12px;display:none}
    .field.invalid input{border-color:var(--error)}
    .field.invalid .error-text{display:block}
    details summary{cursor:pointer;color:var(--muted);margin:6px 0 10px 0}
    .actions{display:flex;gap:10px;align-items:center;flex-wrap:wrap}
    /* ========== NAV / SIDEBAR ========== */
    nav.sidebar{
      display:flex;flex-direction:column;gap:10px
    }
    .nav-head{
      display:flex;align-items:center;justify-content:space-between;margin-bottom:6px
    }
    .logo{font-weight:800;letter-spacing:.3px}
    .nav-list{display:grid;gap:6px}
    .nav-item{
      padding:10px 12px;border-radius:10px;border:1px solid var(--border);background:var(--bg);cursor:pointer
    }
    .nav-item:hover{box-shadow:var(--shadow-sm)}
    .nav-footer{margin-top:10px;color:var(--muted);font-size:12px}
    /* ========== OUTPUT / TABS ========== */
    .tabs{display:flex;gap:8px;border-bottom:1px solid var(--border);margin:-6px 0 var(--pad) 0}
    .tab{
      padding:8px 12px;border-radius:10px 10px 0 0;border:1px solid var(--border);
      border-bottom:none;background:var(--bg);cursor:pointer;font-weight:600;user-select:none
    }
    .tab.active{background:linear-gradient(180deg,#fff0, #fff0), var(--surface);box-shadow:var(--shadow-sm)}
    [data-theme="dark"] .tab.active{background:var(--surface)}
    .tabpanels{position:relative;min-height:240px}
    .tabpanel{display:none;animation:fadeIn .2s ease}
    .tabpanel.active{display:block}
    @keyframes fadeIn{from{opacity:0;transform:translateY(4px)}to{opacity:1;transform:none}}
    .kpis{display:grid;grid-template-columns:repeat(2,1fr);gap:10px}
    .kpi{border:1px solid var(--border);border-radius:12px;padding:12px;background:var(--bg)}
    .chips{display:flex;gap:8px;flex-wrap:wrap}
    .chip.small{font-size:12px;padding:4px 8px;border-radius:999px;background:var(--chip-bg);color:var(--chip-text)}
    .pp-group{display:grid;gap:10px}
    .pp-item{border:1px dashed var(--border);border-radius:10px;padding:10px;background:var(--bg)}
    .pp-sev.High{color:#fff;background:linear-gradient(135deg, var(--error), #d11)}
    .pp-sev.Med{color:#111;background:linear-gradient(135deg, var(--warn), #FFC34D)}
    .pp-sev.Low{color:#fff;background:linear-gradient(135deg, var(--ok), #2dd4bf)}
    .pp-sev{display:inline-block;font-size:11px;padding:3px 8px;border-radius:999px;margin-left:6px}
    .progress{height:8px;border-radius:999px;background:rgba(0,0,0,.07);overflow:hidden}
    .progress > span{display:block;height:100%;background:linear-gradient(90deg, var(--brand-purple), var(--brand-orange))}
    /* ========== LOADER OUROBOROS ========== */
    .loader-wrap{display:flex;align-items:center;justify-content:center;min-height:220px}
    .ouro{
      width:64px;height:64px;animation:spin 1.2s linear infinite;filter: drop-shadow(0 0 6px rgba(255,122,26,.35));
    }
    @keyframes spin{to{transform:rotate(360deg)}}
    /* optional dash progress */
    .ouro circle{stroke-dasharray:220;stroke-dashoffset:40;animation:dash 2.4s ease-in-out infinite}
    @keyframes dash{0%{stroke-dashoffset:220}50%{stroke-dashoffset:110}100%{stroke-dashoffset:220}}
    /* ========== TOASTS ========== */
    .toasts{position:fixed;z-index:50;right:16px;bottom:16px;display:grid;gap:10px}
    .toast{background:var(--surface);border:1px solid var(--border);box-shadow:var(--shadow-md);padding:12px 14px;border-radius:12px;min-width:220px}
    .toast.error{border-color:var(--error)}
    /* ========== BANNERS / ERROR IN OUTPUT ========== */
    .banner{
      border:1px solid var(--border);background:linear-gradient(180deg, rgba(239,68,68,.10), transparent);
      color:var(--text);padding:12px;border-radius:12px;margin-bottom:10px
    }
    .banner .actions{margin-top:8px}
    pre.code{
      background:var(--bg);border:1px solid var(--border);padding:12px;border-radius:10px;overflow:auto;max-height:240px
    }
    /* ========== DEV CONSOLE (COLLAPSABLE) ========== */
    .dev{
      margin-top:10px;border-top:1px dashed var(--border);padding-top:10px
    }
    .dev summary{cursor:pointer;color:var(--muted)}
    .dev .grid{display:grid;gap:6px}
    .kv{display:flex;gap:6px;font-family:ui-monospace, SFMono-Regular, Menlo, Consolas, monospace}
    .kv b{min-width:120px}
    /* ========== UTILITIES ========== */
    .hidden{display:none !important}
    .right-actions{display:flex;gap:8px;align-items:center}
    .header-actions{display:flex;justify-content:space-between;align-items:center;margin-bottom:8px}
    .divider{height:1px;background:var(--border);margin:10px 0}
    .inline{display:inline-flex;gap:8px;align-items:center}
  </style>
</head>
<body>
<div class="container">
  <!-- ========== HEADER (sticky) ========== -->
  <header>
    <div class="head-bar" role="banner">
      <div class="title">Sales Strategy Chatbot</div>
      <span id="modelChip" class="chip" aria-live="polite"><span class="dot ready"></span><span id="modelStatusText">Ready</span></span>
      <div class="spacer"></div>
      <button id="themeToggle" class="toggle" aria-pressed="false" title="Tema chiaro/scuro" data-testid="theme-toggle">🌙 Tema</button>
      <button id="refreshBtn" class="btn icon small" title="Riesegui ultima richiesta" data-testid="refresh-btn">↻ Refresh</button>
    </div>
  </header>

  <!-- ========== MAIN GRID ========== -->
  <main>
    <!-- ========== LEFT SIDEBAR ========== -->
    <aside class="left">
      <nav class="sidebar card" aria-label="Sidebar">
        <div class="nav-head">
          <div class="logo">🚀 SalesGPT</div>
          <button id="collapseSidebar" class="btn small" title="Collassa">⟨⟨</button>
        </div>
        <div class="nav-list" role="list">
          <div class="nav-item" role="listitem" tabindex="0">Home</div>
          <div class="nav-item" role="listitem" tabindex="0">Nuova Strategia</div>
          <div class="nav-item" role="listitem" tabindex="0">Cronologia</div>
          <div class="nav-item" role="listitem" tabindex="0">Template</div>
          <div class="nav-item" role="listitem" tabindex="0">Impostazioni</div>
        </div>
        <div class="divider"></div>

        <!-- Cronologia -->
        <section aria-labelledby="history-h" class="history">
          <h2 id="history-h">Storico</h2>
          <div id="historyList" class="nav-list" data-testid="history-list" aria-live="polite">
            <!-- items injected -->
          </div>
        </section>

        <div class="divider"></div>
        <!-- Template rapidi -->
        <section aria-labelledby="tpl-h" class="templates">
          <h2 id="tpl-h">Template rapidi</h2>
          <div class="nav-list">
            <button class="nav-item" data-tpl="smb-saas">SMB SaaS</button>
            <button class="nav-item" data-tpl="enterprise-svcs">Enterprise Services</button>
            <button class="nav-item" data-tpl="ecommerce">E-commerce</button>
          </div>
        </section>

        <div class="nav-footer">Privacy • © 2025</div>
      </nav>
    </aside>

    <!-- ========== CENTER (INPUT + TABS OUTPUT) ========== -->
    <section class="center">
      <!-- Input Card -->
      <section class="card" aria-labelledby="create-h">
        <h2 id="create-h">Crea strategia</h2>
        <div class="row">
          <div class="field" id="fnField">
            <label for="fullName">Nome e Cognome <span aria-hidden="true" style="color:var(--error)">*</span></label>
            <input id="fullName" data-testid="full-name" type="text" required
                   placeholder="Es. ‘Elena Rossi’" autocomplete="name" aria-required="true"/>
            <div class="error-text">Campo obbligatorio.</div>
          </div>
        </div>
        <details id="advDetails">
          <summary>Dettagli avanzati (facoltativi)</summary>
          <div class="row" style="margin-top:8px">
            <div class="field"><label>Role</label><input id="role" placeholder="Head of Growth"/></div>
            <div class="field"><label>Company</label><input id="company" placeholder="AcmeCloud"/></div>
            <div class="field"><label>Industry</label><input id="industry" placeholder="SaaS B2B"/></div>
            <div class="field"><label>LinkedIn URL</label><input id="linkedin" placeholder="https://..."/></div>
            <div class="field"><label>Website</label><input id="website" placeholder="https://..."/></div>
            <div class="field"><label>Location</label><input id="location" placeholder="Milano, IT"/></div>
            <div class="field" style="flex-basis:100%"><label>Primary Goal</label><input id="goal" placeholder="Aumentare MRR trimestre"/></div>
            <div class="field" style="flex-basis:100%"><label>Notes</label><textarea id="notes" rows="2" placeholder="Contesto aggiuntivo..."></textarea></div>
          </div>
        </details>
        <div class="actions">
          <button id="submitBtn" class="btn primary icon" data-testid="submit">
            🚀 Genera strategia
          </button>
          <button id="sampleBtn" class="btn icon" data-testid="use-sample">✨ Usa dati d’esempio</button>
          <span class="muted">Invio rapido: <kbd>Enter</kbd></span>
        </div>
      </section>

      <!-- Output Card with Tabs -->
      <section class="card" aria-labelledby="out-h">
        <div class="header-actions">
          <h2 id="out-h">Output</h2>
          <div class="right-actions">
            <button id="copyBtn" class="btn small" title="Copia contenuti">📋 Copy</button>
            <button id="exportBtn" class="btn small" title="Export">⬇️ Export</button>
            <button id="reRunBtn" class="btn small" title="Riesegui chiamata">🔁 Refresh</button>
          </div>
        </div>
        <div id="errorBanner" class="banner hidden" role="alert" aria-live="assertive"></div>

        <div class="tabs" role="tablist" aria-label="Output Tabs">
          <button class="tab active" role="tab" aria-selected="true" aria-controls="tab-bg" id="tabbtn-bg">Background</button>
          <button class="tab" role="tab" aria-selected="false" aria-controls="tab-pp" id="tabbtn-pp">Pain Points</button>
          <button class="tab" role="tab" aria-selected="false" aria-controls="tab-strat" id="tabbtn-strat">Sales Strategy</button>
        </div>

        <div class="tabpanels">
          <!-- Loader -->
          <div id="loader" class="loader-wrap hidden" aria-live="polite">
            <!-- SVG ouroboros (curved arrow biting itself) -->
            <svg class="ouro" viewBox="0 0 100 100" role="img" aria-label="Caricamento">
              <defs>
                <linearGradient id="grad" x1="0%" y1="0%" x2="100%" y2="0%">
                  <stop offset="0%" stop-color="#6D28D9"/>
                  <stop offset="100%" stop-color="#FF7A1A"/>
                </linearGradient>
                <filter id="glow">
                  <feGaussianBlur stdDeviation="1.8" result="blur"/>
                  <feMerge>
                    <feMergeNode in="blur"/><feMergeNode in="SourceGraphic"/>
                  </feMerge>
                </filter>
              </defs>
              <!-- circular arc ~340° -->
              <circle cx="50" cy="50" r="34" fill="none" stroke="url(#grad)" stroke-width="8" stroke-linecap="round" filter="url(#glow)"/>
              <!-- arrow head (small triangle) positioned near 20° to "bite" the tail -->
              <polygon points="83,44 92,50 83,56" fill="url(#grad)">
                <animate attributeName="opacity" values="1;.65;1" dur="1.2s" repeatCount="indefinite"/>
              </polygon>
            </svg>
          </div>

          <!-- Background panel -->
          <section id="tab-bg" class="tabpanel active" role="tabpanel" aria-labelledby="tabbtn-bg">
            <div id="bgSnapshot" class="chips" style="margin-bottom:10px"></div>
            <div id="bgHighlights"></div>
            <div style="margin-top:10px">
              <div class="muted">Confidence</div>
              <div class="progress" aria-label="Confidence">
                <span id="bgConf" style="width:0%"></span>
              </div>
            </div>
          </section>

          <!-- Pain Points panel -->
          <section id="tab-pp" class="tabpanel" role="tabpanel" aria-labelledby="tabbtn-pp">
            <div class="pp-group" id="ppList"></div>
          </section>

          <!-- Strategy panel -->
          <section id="tab-strat" class="tabpanel" role="tabpanel" aria-labelledby="tabbtn-strat">
            <div id="stratContent"></div>
          </section>
        </div>

        <!-- Dev Console (collapsable) -->
        <details class="dev" id="devConsole">
          <summary>Console sviluppatore (debug minimal)</summary>
          <div class="grid" id="devGrid"></div>
          <pre class="code" id="devReq"></pre>
          <pre class="code" id="devRes"></pre>
        </details>
      </section>
    </section>

    <!-- ========== RIGHT (INSIGHTS & KPI) ========== -->
    <aside class="right">
      <section class="card" aria-labelledby="ins-h">
        <h2 id="ins-h">Insights & KPI</h2>
        <div id="insHighlights" class="chips" style="margin-bottom:10px"></div>
        <div class="kpis" id="kpiGrid">
          <!-- cards injected -->
        </div>
        <div class="chips" id="tagList" style="margin-top:10px;max-height:120px;overflow:auto"></div>
      </section>
    </aside>
  </main>
</div>

<!-- ========== EXPORT MODAL (mock) ========== -->
<div id="exportModal" class="hidden" role="dialog" aria-modal="true" aria-labelledby="export-title"
     style="position:fixed;inset:0;display:grid;place-items:center;background:rgba(0,0,0,.35);z-index:60">
  <div class="card" style="width:min(760px, 92vw);max-height:80vh;overflow:auto">
    <div style="display:flex;justify-content:space-between;align-items:center;gap:8px">
      <h2 id="export-title">Export</h2>
      <button id="closeExport" class="btn small" title="Chiudi (Esc)">✕</button>
    </div>
    <p class="muted">Scegli un formato: Markdown (.md), PDF (mock), JSON schema.</p>
    <div class="actions">
      <button id="expMd" class="btn">⬇️ Markdown</button>
      <button id="expPdf" class="btn">⬇️ PDF (mock)</button>
      <button id="expJson" class="btn">⬇️ JSON</button>
    </div>
    <div class="divider"></div>
    <pre class="code" id="exportPreview" style="max-height:260px"></pre>
  </div>
</div>

<!-- ========== TOASTS ========== -->
<div class="toasts" id="toasts" aria-live="polite"></div>

<script>
/* =============================== */
/* ===== INIT UI & UTILITIES ===== */
/* =============================== */
const ENDPOINT = 'https://rolandoai.app.n8n.cloud/webhook-test/a7d20785-33c6-4181-b058-abfbffbc2c97';

const $ = sel => document.querySelector(sel);
const $$ = sel => Array.from(document.querySelectorAll(sel));

const els = {
  fullName: $('#fullName'),
  fnField: $('#fnField'),
  submit: $('#submitBtn'),
  sample: $('#sampleBtn'),
  tabs: $$('.tab'),
  panels: $$('.tabpanel'),
  loader: $('#loader'),
  errorBanner: $('#errorBanner'),
  modelChip: $('#modelChip'),
  modelStatusText: $('#modelStatusText'),
  copyBtn: $('#copyBtn'),
  exportBtn: $('#exportBtn'),
  reRunBtn: $('#reRunBtn'),
  refreshTop: $('#refreshBtn'),
  kpiGrid: $('#kpiGrid'),
  insHighlights: $('#insHighlights'),
  tagList: $('#tagList'),
  bgSnapshot: $('#bgSnapshot'),
  bgHighlights: $('#bgHighlights'),
  bgConf: $('#bgConf'),
  ppList: $('#ppList'),
  stratContent: $('#stratContent'),
  toasts: $('#toasts'),
  historyList: $('#historyList'),
  devGrid: $('#devGrid'),
  devReq: $('#devReq'),
  devRes: $('#devRes'),
  themeToggle: $('#themeToggle'),
  exportModal: $('#exportModal'),
  closeExport: $('#closeExport'),
  expMd: $('#expMd'), expPdf: $('#expPdf'), expJson: $('#expJson'),
  exportPreview: $('#exportPreview'),
};

let lastRequest = null; // store last request payload for refresh/retry
let lastResponse = null; // parsed data or text fallback
let lastRawResponse = '';
let debounceTimer = null;

/* Focus ring & keyboard */
document.addEventListener('keydown', (e)=>{
  if(e.key === 'Enter' && document.activeElement.tagName !== 'TEXTAREA'){
    e.preventDefault(); submitFlow();
  }
  if(e.key === 'Escape' && !els.exportModal.classList.contains('hidden')){
    closeExport();
  }
});

/* Theme toggle */
els.themeToggle.addEventListener('click', ()=>{
  const html = document.documentElement;
  const dark = html.getAttribute('data-theme') === 'dark';
  html.setAttribute('data-theme', dark ? 'light' : 'dark');
  els.themeToggle.setAttribute('aria-pressed', String(!dark));
});

/* Sidebar collapse (visual only) */
$('#collapseSidebar').addEventListener('click', ()=>{
  const left = document.querySelector('aside.left');
  left.classList.toggle('hidden');
});

/* Tabs behavior */
els.tabs.forEach(tab=>{
  tab.addEventListener('click', ()=>{
    els.tabs.forEach(t=>{
      t.classList.toggle('active', t===tab);
      t.setAttribute('aria-selected', t===tab ? 'true':'false');
    });
    els.panels.forEach(p=>p.classList.remove('active'));
    $('#'+tab.getAttribute('aria-controls')).classList.add('active');
  });
});

/* Toast helper */
function toast(msg, type='info', timeout=4000){
  const t = document.createElement('div');
  t.className = 'toast'+(type==='error'?' error':'');
  t.textContent = msg;
  els.toasts.appendChild(t);
  setTimeout(()=>{t.remove()}, timeout);
}

/* Copy helper */
async function copyText(txt){
  try{ await navigator.clipboard.writeText(txt); toast('Copiato negli appunti'); }
  catch{ toast('Copia non riuscita', 'error'); }
}

/* Simple id generator */
const uid = () => Math.random().toString(36).slice(2,9);

/* History save/load */
const HISTORY_KEY = 'salesgpt_history_v1';
function pushHistory(name){
  const arr = JSON.parse(localStorage.getItem(HISTORY_KEY)||'[]');
  const item = { id: uid(), name, date: new Date().toISOString() };
  arr.unshift(item); localStorage.setItem(HISTORY_KEY, JSON.stringify(arr));
  renderHistory();
}
function renderHistory(){
  const arr = JSON.parse(localStorage.getItem(HISTORY_KEY)||'[]');
  els.historyList.innerHTML = '';
  arr.slice(0,10).forEach(it=>{
    const div = document.createElement('div');
    div.className='nav-item'; div.innerHTML = `<strong>${it.name}</strong><br/><span class="muted">${new Date(it.date).toLocaleString()}</span>`;
    div.title = 'Riapri'; div.tabIndex=0;
    div.addEventListener('click', ()=>{ if(it.name) els.fullName.value=it.name; });
    els.historyList.appendChild(div);
  });
}
renderHistory();

/* ========================= */
/* ===== FETCH LOGIC ======= */
/* ========================= */
// Abortable fetch with timeout (20s) and 1 retry (after 1.5s) on network/timeout
async function postWithTimeout(url, body, timeoutMs=20000){
  const attempt = async () => {
    const controller = new AbortController();
    const id = setTimeout(()=>controller.abort(), timeoutMs);
    const started = performance.now();
    let respObj = { ok:false, status:0, headers:{}, text:'', duration:0, corsBlocked:false, requestId:null };
    try{
      const res = await fetch(url, {
        method:'POST',
        headers:{'Content-Type':'application/json'},
        body: JSON.stringify(body),
        signal: controller.signal,
        mode: 'cors',
      });
      clearTimeout(id);
      respObj.ok = res.ok;
      respObj.status = res.status;
      respObj.headers = Object.fromEntries(res.headers.entries());
      respObj.requestId = res.headers.get('x-request-id') || res.headers.get('request-id') || null;
      const ct = res.headers.get('content-type') || '';
      respObj.text = await res.text();
      respObj.duration = Math.round(performance.now()-started);
      return respObj;
    }catch(err){
      clearTimeout(id);
      respObj.duration = Math.round(performance.now()-started);
      // Detect likely CORS/network/timeout
      respObj.corsBlocked = (err instanceof TypeError) || (err.name === 'AbortError');
      respObj.text = (err.name||'Error') + ': ' + (err.message||'');
      return respObj;
    }
  };

  // First attempt
  let r = await attempt();
  // Retry once on network/timeout only (NOT on HTTP error)
  if((!r.ok && r.status===0 && r.corsBlocked) || r.text.startsWith('AbortError')){
    await new Promise(res=>setTimeout(res,1500));
    const r2 = await attempt();
    r2._retry = true;
    return r2;
  }
  return r;
}

/* =============================== */
/* ===== RENDERING FUNCTIONS ===== */
/* =============================== */
function setStatus(mode){ // 'ready' | 'thinking' | 'error'
  const dot = els.modelChip.querySelector('.dot');
  dot.className = 'dot ' + (mode==='thinking'?'thinking':mode==='error'?'error':'ready');
  els.modelStatusText.textContent = mode==='thinking'?'Thinking…':mode==='error'?'Error':'Ready';
}

function showLoader(show){
  els.loader.classList.toggle('hidden', !show);
}

function setErrorBanner(html){
  if(!html){ els.errorBanner.classList.add('hidden'); els.errorBanner.innerHTML=''; return; }
  els.errorBanner.innerHTML = html;
  els.errorBanner.classList.remove('hidden');
}

function safeText(t){ return (t==null||t==='')?'N/D':String(t); }

function renderBackground(bg){
  // chips snapshot (role, company, industry)
  els.bgSnapshot.innerHTML='';
  const mkChip = (label,val)=> {
    const chip = document.createElement('span'); chip.className='chip small';
    chip.textContent = `${label}: ${safeText(val)}`;
    els.bgSnapshot.appendChild(chip);
  };
  mkChip('Role', bg?.role || 'N/D');
  mkChip('Company', bg?.company || 'N/D');
  mkChip('Industry', bg?.industry || 'N/D');

  // highlights bullets
  const list = (bg?.highlights && Array.isArray(bg.highlights)) ? bg.highlights : [];
  const ul = document.createElement('ul'); ul.style.margin='10px 0';
  (list.length?list:['N/D']).forEach(item=>{
    const li = document.createElement('li');
    li.textContent = typeof item==='string'? item : JSON.stringify(item);
    ul.appendChild(li);
  });
  els.bgHighlights.innerHTML=''; els.bgHighlights.appendChild(ul);

  // confidence
  const conf = bg?.confidence!=null ? Math.round((bg.confidence*100)) : 0;
  els.bgConf.style.width = conf+'%';
  els.bgConf.setAttribute('aria-valuenow', String(conf));
}

function renderPainPoints(list){
  els.ppList.innerHTML='';
  const items = Array.isArray(list)?list:[];
  if(!items.length){
    els.ppList.innerHTML = '<div class="muted">N/D</div>'; return;
  }
  items.forEach(pp=>{
    const box = document.createElement('div'); box.className='pp-item';
    const sev = safeText(pp?.severity || 'N/D');
    box.innerHTML = `<div><strong>${safeText(pp?.title)}</strong> <span class="pp-sev ${sev}">${sev}</span></div>
    <div class="muted" style="margin:6px 0">${safeText(pp?.category)}</div>
    <div>${safeText(pp?.context)}</div>`;
    els.ppList.appendChild(box);
  });
}

function renderStrategy(s){
  const wrap = document.createElement('div'); wrap.style.display='grid'; wrap.style.gap='12px';
  const sec = (title, content) => `<div style="border:1px solid var(--border);border-radius:10px;padding:12px;background:var(--bg)">
    <div class="inline"><strong>${title}</strong></div><div style="margin-top:6px">${content}</div></div>`;

  const angle = safeText(s?.targeted_angle);
  const keyMsg = s?.key_message || {};
  const channels = Array.isArray(s?.channels)?s.channels:[];
  const cadence = Array.isArray(s?.cadence)?s.cadence:[];
  const ctas = Array.isArray(s?.cta)?s.cta:[];
  const obj = Array.isArray(s?.objections_rebuttals)?s.objections_rebuttals:[];

  const chList = channels.length? ('<ul>'+channels.map(c=>`<li><b>${safeText(c.name)}:</b> ${safeText(c.why)}</li>`).join('')+'</ul>') : 'N/D';
  const cadList = cadence.length? ('<ul>'+cadence.map(c=>`<li><b>${safeText(c.day)}:</b> ${(Array.isArray(c.actions)?c.actions:['N/D']).join(', ')}</li>`).join('')+'</ul>') : 'N/D';
  const ctaList = ctas.length? ('<ul>'+ctas.map(x=>`<li>${safeText(x)}</li>`).join('')+'</ul>') : 'N/D';
  const objList = obj.length? ('<ul>'+obj.map(o=>`<li><b>${safeText(o.objection)}:</b> ${safeText(o.rebuttal)}</li>`).join('')+'</ul>') : 'N/D';

  wrap.innerHTML =
    sec('Angle', angle) +
    sec('Key Message', `<div><b>${safeText(keyMsg.headline)}</b><div class="muted">${safeText(keyMsg.sub)}</div></div>`) +
    sec('Channels', chList) +
    sec('Cadence', cadList) +
    sec('CTA', ctaList) +
    sec('Objections & Rebuttals', objList) +
    sec('Mini Playbook', '• Personalizza le email con i pain punti prioritari • Sequenza multicanale 6–8 tocchi • Misura risposta entro 48h e adatta la cadenza.');
  els.stratContent.innerHTML=''; els.stratContent.appendChild(wrap);
}

function renderInsights(kpi, background){
  // highlights (chips)
  const hi = background?.highlights || [];
  els.insHighlights.innerHTML='';
  hi.slice(0,8).forEach(h=>{
    const s = document.createElement('span'); s.className='chip small'; s.textContent = typeof h==='string'?h:JSON.stringify(h);
    els.insHighlights.appendChild(s);
  });
  // KPI cards
  els.kpiGrid.innerHTML='';
  const add = (label, value) => {
    const div = document.createElement('div'); div.className='kpi';
    div.innerHTML = `<div class="muted" style="font-size:12px">${label}</div><div style="font-size:20px;font-weight:800">${safeText(value)}</div>`;
    els.kpiGrid.appendChild(div);
  };
  if(kpi){
    add('ICP fit %', (kpi.icp_fit!=null?kpi.icp_fit+'%':'N/D'));
    add('Urgency %', (kpi.urgency!=null?kpi.urgency+'%':'N/D'));
    add('Deal Size', kpi.deal_size_est || 'N/D');
    add('Velocity', kpi.velocity_est || 'N/D');
  } else {
    add('ICP fit %','N/D'); add('Urgency %','N/D'); add('Deal Size','N/D'); add('Velocity','N/D');
  }
  // tags
  els.tagList.innerHTML='';
  (kpi?.tags||[]).forEach(t=>{
    const s = document.createElement('span'); s.className='chip small'; s.textContent = t;
    els.tagList.appendChild(s);
  });
}

/* Compose Markdown/JSON for export */
function composeMarkdown(data){
  const o = data?.output || {};
  const bg = o.background || {};
  const pp = Array.isArray(o.pain_points)?o.pain_points:[];
  const s = o.strategy || {};
  return `# Sales Strategy

## Background
- Role: ${safeText(bg.role)}
- Company: ${safeText(bg.company)}
- Industry: ${safeText(bg.industry)}
- Highlights:
${(bg.highlights||['N/D']).map(h=>`  - ${typeof h==='string'?h:JSON.stringify(h)}`).join('\n')}

Confidence: ${(bg.confidence!=null?Math.round(bg.confidence*100)+'%':'N/D')}

## Pain Points
${pp.length? pp.map(p=>`- **${safeText(p.title)}** [${safeText(p.category)} | ${safeText(p.severity)}]\n  - ${safeText(p.context)}`).join('\n') : '- N/D'}

## Sales Strategy
**Angle:** ${safeText(s.targeted_angle)}

**Key Message**
- Headline: ${safeText(s?.key_message?.headline)}
- Sub: ${safeText(s?.key_message?.sub)}

**Channels**
${Array.isArray(s.channels)&&s.channels.length ? s.channels.map(c=>`- ${safeText(c.name)}: ${safeText(c.why)}`).join('\n'):'- N/D'}

**Cadence**
${Array.isArray(s.cadence)&&s.cadence.length ? s.cadence.map(c=>`- ${safeText(c.day)}: ${(Array.isArray(c.actions)?c.actions:['N/D']).join(', ')}`).join('\n'):'- N/D'}

**CTAs**
${Array.isArray(s.cta)&&s.cta.length ? s.cta.map(x=>`- ${safeText(x)}`).join('\n'):'- N/D'}

**Objections & Rebuttals**
${Array.isArray(s.objections_rebuttals)&&s.objections_rebuttals.length ? s.objections_rebuttals.map(o=>`- ${safeText(o.objection)} → ${safeText(o.rebuttal)}`).join('\n'):'- N/D'}

## KPI
- ICP fit: ${safeText(o?.kpi_insights?.icp_fit)}%
- Urgency: ${safeText(o?.kpi_insights?.urgency)}%
- Deal Size: ${safeText(o?.kpi_insights?.deal_size_est)}
- Velocity: ${safeText(o?.kpi_insights?.velocity_est)}
- Tags: ${(o?.kpi_insights?.tags||[]).join(', ') || 'N/D'}
`;
}

/* =============================== */
/* ====== FLOW: SUBMIT/FETCH ===== */
/* =============================== */
function validate(){
  const name = els.fullName.value.trim().replace(/\s+/g,' ');
  const ok = name.length>0;
  els.fnField.classList.toggle('invalid', !ok);
  return ok ? name : null;
}

function setButtonsLoading(loading){
  [els.submit, els.sample, els.reRunBtn, els.refreshTop].forEach(b=>b.disabled = !!loading);
}

function setFadeInContent(){
  // make the three panels visible (already active ones visible) — CSS animation runs on display toggle
  els.panels.forEach(p=>{
    if(p.classList.contains('active')){ p.style.animation='fadeIn .2s ease'; setTimeout(()=>p.style.animation='',250);}
  });
}

async function submitFlow(forceName=null){
  clearTimeout(debounceTimer);
  debounceTimer = setTimeout(async ()=>{
    const name = forceName ?? validate();
    if(!forceName && !name) { toast('Inserisci Nome e Cognome', 'error'); return; }

    // build request body
    const body = {
      full_name: forceName ? forceName : name,
      source: 'sales-strategy-ui',
      ts: new Date().toISOString()
    };
    lastRequest = body;

    // UI state
    setStatus('thinking'); showLoader(true); setButtonsLoading(true); setErrorBanner('');
    els.devGrid.innerHTML=''; els.devReq.textContent=''; els.devRes.textContent='';
    const started = performance.now();

    // call webhook
    const resp = await postWithTimeout(ENDPOINT, body, 20000);
    const elapsed = Math.round(performance.now() - started);
    lastRawResponse = resp.text;

    // Dev console population
    els.devGrid.innerHTML = `
      <div class="kv"><b>Status</b><span>${resp.status || '—'}</span></div>
      <div class="kv"><b>OK</b><span>${resp.ok}</span></div>
      <div class="kv"><b>Elapsed</b><span>${resp.duration} ms</span></div>
      <div class="kv"><b>Retry</b><span>${resp._retry?'Yes':'No'}</span></div>
      <div class="kv"><b>Request-ID</b><span>${resp.requestId || '—'}</span></div>
    `;
    els.devReq.textContent = `POST ${ENDPOINT}
Content-Type: application/json

${JSON.stringify(body, null, 2)}`;

    // Try parse JSON if Content-Type indicates JSON, else treat as text
    let parsed = null; let isJson = false;
    const ct = (resp.headers['content-type']||'').toLowerCase();
    if(ct.includes('application/json')){
      try{ parsed = JSON.parse(resp.text); isJson = true; }catch(e){
        // invalid JSON though content-type says json: fallback to raw text
        isJson = false;
      }
    } else {
      // try parse anyway; if fail, treat as text/markdown
      try{ parsed = JSON.parse(resp.text); isJson = true; }catch{}
    }

    els.devRes.textContent = resp.text.slice(0, 5000);

    if(resp.corsBlocked && resp.status===0){
      setStatus('error'); showLoader(false); setButtonsLoading(false);
      const curlTs = new Date().toISOString();
      const curl = `curl -X POST '${ENDPOINT}' \\
  -H 'Content-Type: application/json' \\
  -d '{"full_name":"Elena Rossi","source":"sales-strategy-ui","ts":"${curlTs}"}'`;
      setErrorBanner(`<strong>Richiesta bloccata dal browser (CORS/Network)</strong><br/>
        Il tuo browser ha impedito la chiamata all'endpoint (CORS) o la rete non è disponibile. È una limitazione del browser/endpoint.<br/>
        <div class="actions">
          <button class="btn small" id="retryBtn">Riprova</button>
          <button class="btn small" id="copyCurlBtn">Copia richiesta cURL</button>
        </div>
        <div class="muted" style="margin-top:6px">Suggerimento: prova da terminale con cURL o abilita CORS sul webhook.</div>
        <pre class="code">${curl}</pre>`);
      $('#retryBtn')?.addEventListener('click', ()=>submitFlow(forceName?forceName:name));
      $('#copyCurlBtn')?.addEventListener('click', ()=>copyText(curl));
      return;
    }

    if(!resp.ok){
      setStatus('error'); showLoader(false); setButtonsLoading(false);
      setErrorBanner(`<strong>Errore HTTP ${resp.status||'0'}</strong>
        <div class="actions"><button class="btn small" id="retryBtn2">Riprova</button></div>`);
      $('#retryBtn2')?.addEventListener('click', ()=>submitFlow(forceName?forceName:name));
      toast('Richiesta fallita', 'error');
      return;
    }

    // success
    setStatus('ready'); showLoader(false); setButtonsLoading(false);

    if(isJson){
      lastResponse = parsed;
      const out = parsed?.output || {};
      renderBackground(out.background || {});
      renderPainPoints(out.pain_points || []);
      renderStrategy(out.strategy || {});
      renderInsights(out.kpi_insights || null, out.background || {});
      setFadeInContent();
      setErrorBanner('');
    } else {
      // treat as text/markdown fallback
      lastResponse = { text: resp.text };
      // Render text into a simple "Risposta" card in Background tab
      els.bgSnapshot.innerHTML = `<span class="chip small">Risposta</span>`;
      els.bgHighlights.innerHTML = `<pre class="code" style="white-space:pre-wrap">${resp.text}</pre>`;
      els.bgConf.style.width='0%';
      // Clear other panels
      els.ppList.innerHTML = '<div class="muted">N/D</div>';
      els.stratContent.innerHTML = '<div class="muted">N/D</div>';
      els.kpiGrid.innerHTML = '';
      els.insHighlights.innerHTML='';
      els.tagList.innerHTML='';
      setFadeInContent();
    }

    // push to history
    pushHistory(forceName?forceName:name);
    toast(`Completato in ${elapsed} ms`);
  }, 400); // debounce 400ms
}

/* =============================== */
/* ========= BUTTONS ETC ========= */
/* =============================== */
els.submit.addEventListener('click', ()=> submitFlow());
els.sample.addEventListener('click', ()=>{
  // Precompile example values AND still call webhook with Elena Rossi
  els.fullName.value = 'Elena Rossi';
  $('#role').value = 'Head of Growth';
  $('#company').value = 'AcmeCloud';
  $('#industry').value = 'SaaS B2B';
  $('#goal').value = 'Aumentare MRR trimestre';
  submitFlow('Elena Rossi');
});
els.reRunBtn.addEventListener('click', ()=> { if(lastRequest) submitFlow(lastRequest.full_name); else toast('Nessuna richiesta precedente', 'error'); });
els.refreshTop.addEventListener('click', ()=> { if(lastRequest) submitFlow(lastRequest.full_name); else toast('Nessuna richiesta precedente', 'error'); });

/* Input sanitization on blur */
els.fullName.addEventListener('blur', ()=>{
  els.fullName.value = els.fullName.value.trim().replace(/\s+/g,' ');
});

/* Copy button */
els.copyBtn.addEventListener('click', ()=>{
  if(lastResponse?.text){
    copyText(lastResponse.text);
    return;
  }
  const md = composeMarkdown(lastResponse || {});
  copyText(md);
});

/* Export modal controls */
function openExport(){
  const preview = lastResponse?.text ? lastResponse.text : composeMarkdown(lastResponse || {});
  els.exportPreview.textContent = preview;
  els.exportModal.classList.remove('hidden');
}
function closeExport(){
  els.exportModal.classList.add('hidden');
}
els.exportBtn.addEventListener('click', openExport);
els.closeExport.addEventListener('click', closeExport);

/* Export handlers (create Blob downloads) */
function download(name, mime, content){
  const blob = new Blob([content], {type:mime});
  const a = document.createElement('a');
  a.href = URL.createObjectURL(blob);
  a.download = name;
  document.body.appendChild(a); a.click(); a.remove();
  setTimeout(()=>URL.revokeObjectURL(a.href), 1000);
}
els.expMd.addEventListener('click', ()=>{
  const md = lastResponse?.text ? lastResponse.text : composeMarkdown(lastResponse || {});
  download('sales-strategy.md','text/markdown;charset=utf-8', md);
});
els.expPdf.addEventListener('click', ()=>{
  // PDF mock: generate a minimal HTML and save with .pdf extension
  const content = lastResponse?.text ? lastResponse.text : composeMarkdown(lastResponse || {});
  const html = `<!doctype html><html><meta charset="utf-8"><body><pre>${content.replace(/</g,'&lt;')}</pre></body></html>`;
  download('sales-strategy.pdf','application/pdf', html);
});
els.expJson.addEventListener('click', ()=>{
  const data = lastResponse?.text ? { raw_text: lastResponse.text } : (lastResponse || {});
  download('sales-strategy.json','application/json;charset=utf-8', JSON.stringify(data,null,2));
});

/* Tabs header actions: Copy/Export/Refresh mirror */
$('#reRunBtn').addEventListener('click', ()=> { if(lastRequest) submitFlow(lastRequest.full_name); else toast('Nessuna richiesta precedente', 'error'); });

/* Templates quick actions */
$$('.templates .nav-list .nav-item').forEach(btn=>{
  btn.addEventListener('click', ()=>{
    // prefill by template (all call webhook with Elena Rossi per requisiti demo)
    if(btn.dataset.tpl==='smb-saas'){
      els.fullName.value='Elena Rossi'; $('#role').value='Growth Lead'; $('#company').value='Startly'; $('#industry').value='SaaS SMB';
    } else if(btn.dataset.tpl==='enterprise-svcs'){
      els.fullName.value='Elena Rossi'; $('#role').value='Sales Director'; $('#company').value='GigantoCorp'; $('#industry').value='Enterprise Services';
    } else {
      els.fullName.value='Elena Rossi'; $('#role').value='Ecom Manager'; $('#company').value='Shopverse'; $('#industry').value='E-commerce';
    }
    submitFlow('Elena Rossi');
  });
});

/* Accessibility: set ARIA roles/labels dynamically where needed (already added in markup) */

/* =============================== */
/* === INITIAL STATE & HELPERS === */
/* =============================== */
setStatus('ready');
showLoader(false);

/* Provide a one-click "Riprova" in header refresh if needed (wired already). */

/* =============================== */
/* ===== CORS GUIDANCE LINK ===== */
/* =============================== */
/* When CORS/network/timeout occurs, the banner shows "Riprova" and "Copia cURL" with guidance as requested. */
</script>
</body>
</html>

<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>RolandoAI · Chat Dashboard</title>
  <style>
    :root{
      --bg:#0b0d10;             /* app background */
      --panel:#14171b;          /* cards/panels */
      --panel-2:#0f1215;        /* deeper panels */
      --stroke:rgba(255,255,255,.06);
      --text:#e9edf3;
      --muted:#98a2b3;
      --accent:#a3ff6f;         /* lime accent from reference */
      --accent-2:#7dd3fc;       /* subtle blue highlight */
      --radius:18px;
      --shadow:0 10px 30px rgba(0,0,0,.45);
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{
      margin:0; font-family: Inter, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial;
      color:var(--text);
      background:
        radial-gradient(1200px 800px at 80% -10%, rgba(163,255,111,.08), transparent),
        radial-gradient(1000px 700px at -10% 100%, rgba(125,211,252,.05), transparent),
        var(--bg);
    }

    /* ====== Layout ====== */
    .app{
      display:grid; grid-template-rows:auto 1fr auto; height:100%;
      grid-template-columns: 260px 1fr 320px; gap:18px;
      padding: 16px 18px;
    }

    .topbar{
      grid-column: 1 / -1; display:flex; align-items:center; gap:14px;
      padding:12px 16px; background: linear-gradient(180deg, rgba(255,255,255,.04), rgba(255,255,255,.02));
      border:1px solid var(--stroke); border-radius: 16px; box-shadow: var(--shadow);
    }
    .brand{font-weight:700; letter-spacing:.2px}
    .badge{font-size:12px; padding:6px 10px; border-radius:999px; border:1px solid var(--stroke); color:var(--text); background:rgba(255,255,255,.02)}
    .badge.accent{background:rgba(163,255,111,.12); border-color: rgba(163,255,111,.35); color:#d9ffd0}
    .spacer{flex:1}

    /* Sidebars */
    .sidebar-left, .sidebar-right{
      background: linear-gradient(180deg, rgba(255,255,255,.03), rgba(255,255,255,.015));
      border:1px solid var(--stroke); border-radius: var(--radius); box-shadow: var(--shadow);
      padding:14px; display:flex; flex-direction:column; gap:14px;
    }

    .menu-group h4{margin:6px 0 4px; font-size:12px; color:var(--muted); font-weight:600}
    .menu-item{
      display:flex; align-items:center; gap:10px; padding:10px 12px; border-radius:12px;
      background: var(--panel-2); border:1px solid var(--stroke);
      cursor:pointer; user-select:none;
    }
    .menu-item:hover{outline:1px solid rgba(255,255,255,.08)}
    .menu-item .dot{width:8px; height:8px; border-radius:50%; background: var(--accent)}

    .chips{display:grid; grid-template-columns: 1fr; gap:8px}
    .chip{display:flex; justify-content:space-between; align-items:center; padding:10px 12px; border-radius:12px; border:1px solid var(--stroke); background:var(--panel-2); font-size:13px; color:var(--muted)}
    .chip .arrow{opacity:.5}

    /* ====== Center content ====== */
    .center{
      display:grid; grid-template-rows:auto 1fr auto; gap:14px; min-height:0;
    }
    .feature-card{
      padding:16px; border:1px solid var(--stroke); border-radius: var(--radius);
      background: var(--panel); box-shadow: var(--shadow);
    }

    .cards{display:grid; grid-template-columns: repeat(4, 1fr); gap:14px}
    .card{
      border:1px solid var(--stroke); border-radius:16px; background:var(--panel-2);
      padding:16px; min-height:110px; position:relative;
    }
    .skeleton{height:10px; border-radius:6px; background: linear-gradient(90deg, rgba(255,255,255,.06), rgba(255,255,255,.02), rgba(255,255,255,.06)); background-size:200% 100%; animation: shine 2s linear infinite}
    .s1{width:60%; margin-bottom:10px}
    .s2{width:80%; margin-bottom:10px}
    .s3{width:40%}
    @keyframes shine{0%{background-position:200% 0}100%{background-position:-200% 0}}

    /* Chat board */
    .board{
      position:relative; overflow:hidden; border-radius: var(--radius);
      background: var(--panel); border:1px solid var(--stroke); box-shadow: var(--shadow);
      display:flex; flex-direction:column; min-height: 0;
      padding: 14px; gap:12px;
    }
    .board-title{display:flex; align-items:center; gap:10px; font-weight:600}
    .limit{font-size:11px; color:#0b1b0f; background: rgba(163,255,111,.9); padding:4px 8px; border-radius:999px}

    .messages{flex:1; overflow:auto; padding:6px 2px}
    .msg{display:flex; gap:10px; margin: 12px 0; align-items:flex-end; max-width: 82%}
    .msg.ai{flex-direction: row}
    .msg.me{flex-direction: row-reverse; margin-left:auto}
    .avatar{flex:0 0 36px; height:36px; border-radius: 12px; display:grid; place-items:center; background:#0f1419; border:1px solid var(--stroke); color:#bcd3ff; font-weight:700}
    .bubble{padding: 12px 14px; border-radius: 16px; line-height:1.45; font-size:15px; border:1px solid var(--stroke); background:#0f1419; box-shadow: 0 6px 18px rgba(0,0,0,.25)}
    .msg.me .bubble{background: linear-gradient(180deg, rgba(163,255,111,.20), rgba(163,255,111,.10)); border-color: rgba(163,255,111,.35)}
    .msg.ai .bubble{background: linear-gradient(180deg, rgba(125,211,252,.18), rgba(125,211,252,.10)); border-color: rgba(125,211,252,.35)}
    .meta{font-size:11px; color: var(--muted); margin-top:6px}

    .composer{
      display:flex; gap:10px; align-items:center;
      background: var(--panel); border:1px solid var(--stroke); border-radius: 999px; padding:10px 10px 10px 14px;
    }
    .composer input, .composer textarea{
      flex:1; border:none; outline:none; background:transparent; color:var(--text); font-size:14px; resize:none; min-height:42px; max-height:140px;
    }
    .btn{border:none; cursor:pointer; padding:10px 16px; border-radius: 999px; font-weight:600; display:flex; align-items:center; gap:8px}
    .btn-send{background: linear-gradient(180deg, rgba(163,255,111,1), rgba(110,217,74,1)); color:#0a0d10}
    .btn-clear{background: #1b2026; color:var(--muted); border:1px solid var(--stroke)}
    .btn:disabled{opacity:.6; cursor:not-allowed}

    /* Right column */
    .right-head{display:flex; align-items:center; justify-content:space-between}
    .list{display:flex; flex-direction:column; gap:10px; overflow:auto}
    .item{padding:12px 12px; border-radius:12px; border:1px solid var(--stroke); background:var(--panel-2); display:flex; justify-content:space-between; gap:10px; align-items:center}
    .item .label{font-size:13px; color:#cbd5e1; overflow:hidden; text-overflow:ellipsis; white-space:nowrap; max-width: 200px}
    .small{font-size:11px; color:var(--muted)}

    /* Toast */
    .toast{position:fixed; right:20px; bottom:20px; padding:12px 14px; background:#1b2230; color:#fff; border:1px solid var(--stroke); border-radius:12px; opacity:0; pointer-events:none; transition:.25s; transform: translateY(8px)}
    .toast.show{opacity:1; transform: translateY(0)}

    /* Responsive */
    @media (max-width: 1100px){
      .app{grid-template-columns: 1fr; grid-template-rows: auto auto 1fr auto;}
      .sidebar-left, .sidebar-right{display:none}
    }
  </style>
</head>
<body>
  <div class="app">
    <!-- ===== Topbar ===== -->
    <div class="topbar">
      <div class="brand">Aidy</div>
      <span class="badge">Plugins</span>
      <span class="badge accent">GPT-4</span>
      <div class="spacer"></div>
      <span class="badge">Enabled Plugins: <strong>Design‑GPT</strong></span>
      <span class="badge" id="session-pill">Sessione <span id="session-id">—</span></span>
      <span class="badge" id="status">Pronto</span>
    </div>

    <!-- ===== Left Sidebar ===== -->
    <aside class="sidebar-left">
      <div class="menu-group">
        <div class="menu-item"><span class="dot"></span> <span>Chat Bot</span></div>
        <div class="menu-item"><span class="dot" style="opacity:.3"></span> <span>Help Center</span></div>
        <div class="menu-item"><span class="dot" style="opacity:.3"></span> <span>Report</span></div>
        <div class="menu-item"><span class="dot" style="opacity:.3"></span> <span>Settings</span></div>
        <div class="menu-item"><span class="dot" style="opacity:.3"></span> <span>Logout</span></div>
      </div>
      <div class="menu-group">
        <h4>Trending Topic</h4>
        <div class="chips">
          <div class="chip">Web3 <span class="arrow">↗</span></div>
          <div class="chip">Figma <span class="arrow">↗</span></div>
          <div class="chip">Website Design <span class="arrow">↗</span></div>
          <div class="chip">Design with AI <span class="arrow">↗</span></div>
        </div>
      </div>
    </aside>

    <!-- ===== Center ===== -->
    <section class="center">
      <div class="feature-card">
        <div class="cards">
          <div class="card">
            <div class="skeleton s1"></div>
            <div class="skeleton s2"></div>
            <div class="skeleton s3"></div>
          </div>
          <div class="card">
            <div class="skeleton s1"></div>
            <div class="skeleton s2"></div>
            <div class="skeleton s3"></div>
          </div>
          <div class="card">
            <div class="skeleton s1"></div>
            <div class="skeleton s2"></div>
            <div class="skeleton s3"></div>
          </div>
          <div class="card">
            <div class="skeleton s1"></div>
            <div class="skeleton s2"></div>
            <div class="skeleton s3"></div>
          </div>
        </div>
        <p style="margin:14px 0 0; color:var(--muted); font-size:14px">Great! Se i piani qui sopra ti piacciono possiamo iniziare a preparare un preventivo dettagliato. Pront* a partire?</p>
      </div>

      <div class="board" role="main" aria-live="polite">
        <div class="board-title">Chat History <span class="limit" id="count-pill">0/600</span></div>
        <div id="messages" class="messages"></div>
      </div>

      <form id="composer" class="composer" autocomplete="off">
        <span style="opacity:.7">💬</span>
        <textarea id="input" placeholder="Hi! how may I help you, please enter…" required></textarea>
        <button class="btn btn-clear" type="button" id="clear">Clear</button>
        <button class="btn btn-send" id="send" type="submit" aria-label="Invia">Send</button>
      </form>
    </section>

    <!-- ===== Right Sidebar ===== -->
    <aside class="sidebar-right">
      <div class="right-head">
        <div>
          <div style="font-weight:600;">Chats</div>
          <div class="small">Chat History <span id="history-count">0</span></div>
        </div>
        <div class="badge" id="unread">12</div>
      </div>
      <div id="chatlist" class="list" aria-label="Elenco chat"></div>
      <div style="display:flex; gap:10px; margin-top:auto">
        <button class="btn btn-clear" type="button" id="newchat">New Chat</button>
        <button class="btn btn-clear" type="button" id="deleteall">Delete All</button>
      </div>
    </aside>

    <!-- footer filler -->
    <div style="grid-column:1/-1; text-align:center; color:#6b7280; font-size:12px; padding:6px 0">© 2024 — All Right Reserved, Built by Groeic</div>
  </div>

  <div id="toast" class="toast" role="status" aria-live="polite"></div>

  <script>
    // ——— Config ———
    const WEBHOOK_URL = "https://rolandoai.app.n8n.cloud/webhook-test/3f194f88-1ee5-43ce-bd9f-02ad51387733";

    // Utility: persistent session id
    function getSessionId(){
      const key = 'aichat:sessionId';
      let id = localStorage.getItem(key);
      if(!id){ id = Math.random().toString(36).slice(2, 10); localStorage.setItem(key, id); }
      return id;
    }

    // Elements
    const $messages = document.getElementById('messages');
    const $input = document.getElementById('input');
    const $form = document.getElementById('composer');
    const $send = document.getElementById('send');
    const $clear = document.getElementById('clear');
    const $toast = document.getElementById('toast');
    const $status = document.getElementById('status');
    const $session = document.getElementById('session-id');
    const $count = document.getElementById('count-pill');
    const $chatlist = document.getElementById('chatlist');
    const $historyCount = document.getElementById('history-count');

    // Load session id
    const SESSION_ID = getSessionId();
    $session.textContent = SESSION_ID;

    // Restore history
    const HISTORY_KEY = 'aichat:history:v2';
    let history = [];
    try {
      history = JSON.parse(localStorage.getItem(HISTORY_KEY) || '[]');
      history.forEach(renderMessage);
      if(history.length === 0){
        renderMessage({role:'ai', content:"Ciao! Sono l'agente AI collegato al tuo webhook n8n. Mandami un messaggio e risponderò qui."});
      }
      updateCounts();
      rebuildChatList();
    } catch(e){ console.warn(e); }

    // Autosize
    $input.addEventListener('input', () => {
      $input.style.height = 'auto';
      $input.style.height = Math.min($input.scrollHeight, 140) + 'px';
    });

    // Submit
    $form.addEventListener('submit', async (e)=>{
      e.preventDefault();
      const text = $input.value.trim();
      if(!text) return;

      const userMsg = {role:'me', content: text, ts: Date.now()};
      addToHistory(userMsg);
      $input.value = ''; $input.style.height = '42px';

      const typing = renderTyping();
      setStatus('Elaborazione…');
      toggleSending(true);

      try{
        const payload = { message: text, sessionId: SESSION_ID, timestamp: new Date().toISOString(), source: 'chat-dashboard' };
        const res = await fetch(WEBHOOK_URL, {
          method: 'POST', headers: { 'Content-Type': 'application/json' }, mode: 'cors', body: JSON.stringify(payload)
        });

        typing.remove();
        if(!res.ok){ throw new Error('HTTP ' + res.status + ' — ' + (await res.text())); }

        let aiText = '';
        const ct = res.headers.get('Content-Type') || '';
        if(ct.includes('application/json')){
          const data = await res.json();
          aiText = extractAiText(data) || JSON.stringify(data, null, 2);
        } else { aiText = await res.text(); }

        addToHistory({role:'ai', content: aiText, ts: Date.now()});
        setStatus('Pronto');
      } catch(err){
        typing.remove();
        addToHistory({role:'ai', content: '❌ Errore: ' + (err.message || String(err))});
        toast('Invio fallito. Controlla il webhook o i CORS.');
        setStatus('Errore');
      } finally {
        toggleSending(false);
        scrollToBottom();
      }
    });

    // Clear chat
    $clear.addEventListener('click', ()=>{
      history = [];
      saveHistory();
      $messages.innerHTML = '';
      renderMessage({role:'ai', content:'Nuova chat avviata. Come posso aiutarti?'});
      updateCounts();
    });

    // Right panel actions
    document.getElementById('newchat').addEventListener('click', ()=> $clear.click());
    document.getElementById('deleteall').addEventListener('click', ()=>{
      localStorage.removeItem(HISTORY_KEY);
      history = []; $messages.innerHTML=''; rebuildChatList(); updateCounts();
      renderMessage({role:'ai', content:'Cronologia cancellata.'});
    });

    function extractAiText(obj){
      const candidates = ['reply','message','text','output','result','data'];
      for(const k of candidates){ if(typeof obj[k] === 'string') return obj[k]; }
      if(Array.isArray(obj) && obj.length){
        if(typeof obj[0] === 'string') return obj.join('
');
        if(typeof obj[0] === 'object' && obj[0].content) return obj.map(x=>x.content).join('
');
      }
      return '';
    }

    function renderMessage({role, content, ts}){
      const wrap = document.createElement('div');
      wrap.className = `msg ${role === 'me' ? 'me' : 'ai'}`;
      const avatar = document.createElement('div'); avatar.className='avatar'; avatar.textContent = role === 'me' ? 'ME' : 'AI';
      const bubble = document.createElement('div'); bubble.className='bubble'; bubble.innerText = content;
      const meta = document.createElement('div'); meta.className='meta'; if(ts){ meta.textContent = new Date(ts).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'}); }
      const side = document.createElement('div'); side.appendChild(bubble); side.appendChild(meta);
      wrap.appendChild(avatar); wrap.appendChild(side); $messages.appendChild(wrap);
    }

    function renderTyping(){
      const wrap = document.createElement('div'); wrap.className='msg ai';
      const avatar = document.createElement('div'); avatar.className='avatar'; avatar.textContent='AI';
      const bubble = document.createElement('div'); bubble.className='bubble'; bubble.innerHTML='<span class="typing"><span></span><span></span><span></span></span>';
      const side = document.createElement('div'); side.appendChild(bubble); wrap.appendChild(avatar); wrap.appendChild(side);
      $messages.appendChild(wrap); scrollToBottom(); return wrap;
    }

    function addToHistory(msg){
      history.push(msg); renderMessage(msg); saveHistory(); updateCounts(); rebuildChatList();
    }

    function saveHistory(){
      try{ localStorage.setItem(HISTORY_KEY, JSON.stringify(history)); }catch(e){}
    }

    function updateCounts(){
      $count.textContent = history.length + '/600';
      $historyCount.textContent = history.filter(m=>m.role==='me').length;
    }

    function rebuildChatList(){
      $chatlist.innerHTML = '';
      const lastUser = history.filter(m=>m.role==='me').slice(-8).reverse();
      lastUser.forEach((m,i)=>{
        const el = document.createElement('div'); el.className='item';
        const label = document.createElement('div'); label.className='label'; label.textContent = m.content.slice(0,40) + (m.content.length>40?'…':'');
        const arrow = document.createElement('div'); arrow.className='small'; arrow.textContent='›';
        el.appendChild(label); el.appendChild(arrow);
        el.addEventListener('click', ()=>{
          // scroll to last matching message
          const nodes = Array.from(document.querySelectorAll('.msg.me .bubble'));
          const node = nodes.find(n => n.innerText.startsWith(m.content.slice(0,10)));
          if(node){ node.scrollIntoView({behavior:'smooth', block:'center'}); node.parentElement.parentElement.classList.add('pulse'); setTimeout(()=> node.parentElement.parentElement.classList.remove('pulse'), 800); }
        });
        $chatlist.appendChild(el);
      });
    }

    function scrollToBottom(){ $messages.scrollTop = $messages.scrollHeight + 200; }
    function toggleSending(sending){ $send.disabled = sending; $input.disabled = sending; }
    function toast(text){ const t=$toast; t.textContent=text; t.classList.add('show'); clearTimeout(t._t); t._t=setTimeout(()=>t.classList.remove('show'), 2400); }
    function setStatus(text){ $status.textContent = text; }

    // Keyboard: Enter submit
    $input.addEventListener('keydown', (e)=>{ if(e.key==='Enter' && !e.shiftKey){ e.preventDefault(); $form.requestSubmit(); } });
  </script>
</body>
</html>

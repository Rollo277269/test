<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Agente Spia · Interfaccia Chat</title>
  <style>
    :root{
      --bg:#0b0d10;
      --panel:#14171b;
      --panel-2:#0f1215;
      --stroke:rgba(255,255,255,.06);
      --text:#e9edf3;
      --muted:#9aa3b2;
      --accent:#a3ff6f;
      --radius:18px;
      --shadow:0 10px 30px rgba(0,0,0,.45);
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{margin:0;font-family:Inter,system-ui,-apple-system,Segoe UI,Roboto,Helvetica,Arial;background:var(--bg);color:var(--text)}

    /* ===== Layout: SOLO 3 RIQUADRI ===== */
    .app{display:grid;grid-template-columns:260px 1fr 260px;gap:18px;height:100%;padding:18px}
    .panel{background:var(--panel);border:1px solid var(--stroke);border-radius:var(--radius);box-shadow:var(--shadow);overflow:hidden}

    /* Storico (sx) */
    .history{display:flex;flex-direction:column;padding:14px}
    .history-head{display:flex;gap:8px;align-items:center;margin-bottom:10px}
    .btn{border:1px solid var(--stroke);background:var(--panel-2);color:var(--text);border-radius:12px;padding:8px 10px;cursor:pointer;font-weight:600}
    .btn.primary{background:var(--accent);color:#0a0d10;border-color:rgba(163,255,111,.5)}
    .list{display:flex;flex-direction:column;gap:8px;overflow:auto}
    .session{display:flex;justify-content:space-between;align-items:center;gap:8px;padding:10px 12px;border-radius:12px;background:var(--panel-2);border:1px solid var(--stroke);cursor:pointer}
    .session.active{outline:1px solid rgba(163,255,111,.45)}
    .session .label{font-size:13px;color:#cbd5e1;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;max-width:170px}
    .session .count{font-size:11px;color:var(--muted)}

    /* Chat (centro) */
    .chat{display:flex;flex-direction:column;min-height:0}
    .messages{flex:1;overflow:auto;padding:16px}
    .msg{display:flex;gap:10px;margin:12px 0;align-items:flex-end;max-width:80%}
    .msg.me{flex-direction:row-reverse;margin-left:auto}
    .avatar{flex:0 0 36px;height:36px;border-radius:12px;display:grid;place-items:center;background:#0f1419;border:1px solid var(--stroke);color:#bcd3ff;font-weight:700}
    .bubble{padding:12px 14px;border-radius:16px;line-height:1.45;font-size:15px;border:1px solid var(--stroke);background:#0f1419}
    .msg.me .bubble{background:rgba(163,255,111,.15)}
    .msg.ai .bubble{background:rgba(125,211,252,.12)}

    .composer{margin:14px;position:relative;display:flex;align-items:center;gap:10px}
    .composer textarea{flex:1;border:none;border-radius:999px;padding:16px 140px 16px 52px;font-size:15px;background:var(--panel);color:var(--text);resize:none;min-height:58px;outline:none}
    .composer .send{position:absolute;right:14px;top:50%;transform:translateY(-50%);border:none;border-radius:999px;padding:12px 18px;font-weight:700;background:var(--accent);color:#0a0d10;cursor:pointer}
    .composer .hint{position:absolute;left:26px;top:50%;transform:translateY(-50%);font-size:14px;color:var(--muted);pointer-events:none;opacity:.6}

    /* Dettagli (dx) – minimal, niente fronzoli */
    .details{padding:14px;color:var(--muted);font-size:13px}

    @media(max-width:1000px){
      .app{grid-template-columns:1fr;padding:14px}
      .panel.details{display:none}
    }
  </style>
</head>
<body>
  <div class="app">
    <!-- === Riquadro 1: STORICO CHAT (passa a precedente/successiva) === -->
    <aside class="panel history" id="history">
      <div class="history-head">
        <button class="btn" id="prev">‹</button>
        <button class="btn" id="next">›</button>
        <button class="btn primary" id="new">Nuova</button>
        <div style="margin-left:auto;font-size:12px;color:var(--muted)">Sessioni: <span id="sessionsCount">0</span></div>
      </div>
      <div id="sessions" class="list" aria-label="Storico chat"></div>
    </aside>

    <!-- === Riquadro 2: CHAT CENTRALE (elemento principale) === -->
    <section class="panel chat" id="chat">
      <div id="messages" class="messages"></div>
      <form id="composer" class="composer" autocomplete="off">
        <div class="hint" id="hint">Voglio vendere a…</div>
        <textarea id="input" placeholder="Scrivi: ‘Voglio vendere <prodotto/servizio> a <Nome Cognome>’ e aggiungi dettagli utili." required></textarea>
        <button class="send" id="send" type="submit">Send</button>
      </form>
    </section>

    <!-- === Riquadro 3: DETTAGLI (minimi) === -->
    <aside class="panel details" id="details">
      <div><strong>Agente:</strong> Agente Spia</div>
      <div style="margin-top:8px">Questa UI è un'interfaccia: il lavoro vero è nel backend via Webhook.</div>
      <div style="margin-top:12px">Webhook: <code style="font-size:12px">/webhook-test/3f194f88-…</code></div>
    </aside>
  </div>

  <script>
    // ===== Config =====
    const WEBHOOK_URL = "https://rolandoai.app.n8n.cloud/webhook-test/3f194f88-1ee5-43ce-bd9f-02ad51387733";

    // ===== Stato (sessioni) =====
    const SESS_KEY = "agentespia:sessions:v1";
    let sessions = load(SESS_KEY) || [];
    let currentId = sessions[0]?.id || createSession().id;

    // ===== Elementi =====
    const $sessions = document.getElementById("sessions");
    const $sessionsCount = document.getElementById("sessionsCount");
    const $messages = document.getElementById("messages");
    const $form = document.getElementById("composer");
    const $input = document.getElementById("input");
    const $hint  = document.getElementById("hint");

    // Init
    rebuildSessions();
    renderConversation();

    // Overlay hint
    $input.addEventListener("input", ()=>{ $hint.style.opacity = $input.value ? .2 : .6; });

    // Navigazione storico
    document.getElementById("new").addEventListener("click", ()=>{ currentId = createSession().id; rebuildSessions(); renderConversation(); });
    document.getElementById("prev").addEventListener("click", ()=> step(-1));
    document.getElementById("next").addEventListener("click", ()=> step(1));

    // Invio messaggio -> POST al webhook
    $form.addEventListener("submit", async (e)=>{
      e.preventDefault();
      const text = $input.value.trim(); if(!text) return;
      pushMsg({role:"me", content:text});
      $input.value=""; $hint.style.opacity=.6;

      const intent = parseIntent(text); // estrae prodotto/servizio e Nome/Cognome se presenti

      try{
        const res = await fetch(WEBHOOK_URL, {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ agent:"Agente Spia", message:text, intent })
        });

        let reply = await res.text();
        try {
          const data = JSON.parse(reply);
          reply = pickText(data) || formatStrategy(data) || JSON.stringify(data, null, 2);
        } catch(_) {}
        pushMsg({role:"ai", content: reply});
      }catch(err){
        pushMsg({role:"ai", content: "❌ Errore invio webhook: " + (err.message || String(err))});
      }
    });

    // ===== Funzioni =====
    function createSession(){
      const s = { id: Math.random().toString(36).slice(2,10), title: "Chat " + new Date().toLocaleString(), history: [] };
      sessions.unshift(s); save(SESS_KEY, sessions); return s;
    }
    function getCurrent(){ return sessions.find(s=>s.id===currentId); }
    function step(dir){
      if(!sessions.length) return;
      const i = sessions.findIndex(s=>s.id===currentId);
      const j = (i + dir + sessions.length) % sessions.length;
      currentId = sessions[j].id; rebuildSessions(); renderConversation();
    }
    function pushMsg(msg){ const s=getCurrent(); s.history.push({ ...msg, ts:Date.now() }); save(SESS_KEY, sessions); renderMsg(msg); }

    function renderConversation(){
      $messages.innerHTML="";
      const s = getCurrent();
      if(!s.history.length){
        renderMsg({role:"ai", content:"Scrivi: “Voglio vendere … a …”. L’Agente Spia genererà background + strategia di vendita."});
      }
      s.history.forEach(m=> renderMsg(m));
      $messages.scrollTop = $messages.scrollHeight;
    }
    function renderMsg({role, content}){
      const wrap = document.createElement("div"); wrap.className = `msg ${role}`;
      const avatar = document.createElement("div"); avatar.className = "avatar"; avatar.textContent = role==="me" ? "ME" : "AI";
      const bubble = document.createElement("div"); bubble.className = "bubble"; bubble.innerText = content;
      wrap.appendChild(avatar); wrap.appendChild(bubble); $messages.appendChild(wrap);
      $messages.scrollTop = $messages.scrollHeight + 200;
    }

    function rebuildSessions(){
      $sessions.innerHTML="";
      sessions.forEach(s=>{
        const el = document.createElement("div"); el.className = "session" + (s.id===currentId ? " active" : "");
        const label = document.createElement("div"); label.className="label"; label.textContent = s.title;
        const count = document.createElement("div"); count.className="count"; count.textContent = s.history.length;
        el.appendChild(label); el.appendChild(count);
        el.addEventListener("click", ()=>{ currentId=s.id; rebuildSessions(); renderConversation(); });
        $sessions.appendChild(el);
      });
      $sessionsCount.textContent = sessions.length;
    }

    // Parser: "Voglio vendere (prodotto/servizio) a (Nome Cognome)"
    function parseIntent(text){
      const re = /voglio\\s+vendere\\s+(.+?)\\s+a\\s+([a-zà-ú]+)\\s+([a-zà-ú]+)\\b/i;
      const m  = text.match(re);
      if(!m) return { raw:text };
      const productOrService = m[1].trim();
      const first = cap(m[2]); const last = cap(m[3]);
      return { raw:text, productOrService, target:{ firstName:first, lastName:last, fullName:first+' '+last } };
    }
    function cap(s){ return (s||'').charAt(0).toUpperCase() + (s||'').slice(1); }

    // Interpreta la risposta JSON del webhook (quando presente)
    function pickText(obj){
      const keys = ["reply","message","text","output","result","data"];
      for(const k of keys){ if(typeof obj?.[k] === "string") return obj[k]; }
      return "";
    }
    function formatStrategy(obj){
      const parts = [];
      if(typeof obj.background === "string") parts.push("Background:\\n"+obj.background);
      if(typeof obj.strategy  === "string") parts.push("Strategia:\\n"+obj.strategy);
      return parts.length ? parts.join("\\n\\n") : "";
    }

    // storage helpers
    function save(k,v){ try{ localStorage.setItem(k, JSON.stringify(v)); }catch(_){} }
    function load(k){ try{ return JSON.parse(localStorage.getItem(k)||"null"); }catch(_){ return null; } }
  </script>
</body>
</html>

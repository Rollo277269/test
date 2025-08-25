<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Dashboard Automazione AI – Reminder Pagamento Fatture</title>
  <style>
    :root{
      --bg:#0f1115; --panel:#161a22; --panel-2:#1c2230; --muted:#8b94a7; --text:#e8ecf1; --accent:#7c9cff; --ok:#4ade80; --warn:#f59e0b; --bad:#ef4444; --card:#11151d; --chip:#22293a;
      --shadow: 0 10px 30px rgba(0,0,0,.35), inset 0 1px 0 rgba(255,255,255,.02);
      --radius: 18px;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{margin:0;background:linear-gradient(180deg, #0d1016 0%, #0f1115 50%, #0b0d12 100%);color:var(--text);font:500 15px/1.5 system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, Noto Sans, Helvetica, Arial}
    a{color:var(--accent);text-decoration:none}
    .app{max-width:1200px;margin:24px auto;padding:0 16px}
    header{display:flex;gap:16px;align-items:center;justify-content:space-between;margin-bottom:18px}
    .brand{display:flex;gap:12px;align-items:center}
    .logo{width:40px;height:40px;border-radius:12px;background:linear-gradient(135deg,#6ea1ff,#9b7cff);display:grid;place-items:center;box-shadow:var(--shadow)}
    .logo span{font-weight:900;color:white;letter-spacing:.5px}
    h1{font-size:20px;margin:0}
    .subtitle{color:var(--muted);font-size:12px;margin-top:2px}
    .toolbar{display:flex;gap:10px;flex-wrap:wrap;align-items:center}
    .btn{background:var(--panel);border:1px solid #222a3a;color:var(--text);padding:10px 12px;border-radius:12px;cursor:pointer;display:inline-flex;gap:8px;align-items:center;box-shadow:var(--shadow)}
    .btn:active{transform:translateY(1px)}
    .btn.primary{background:linear-gradient(135deg,#3e65ff,#8a7cff);border-color:#3851c8}
    .btn.ghost{background:transparent;border-color:#2a3347}
    .btn.small{padding:6px 8px;border-radius:10px;font-size:13px}
    .chip{background:var(--chip);border:1px solid #2a3347;padding:6px 10px;border-radius:999px;color:#cfd6e6;font-size:12px}

    .grid{display:grid;grid-template-columns:repeat(12,1fr);gap:14px}
    .card{background:var(--panel);border:1px solid #21283a;border-radius:var(--radius);box-shadow:var(--shadow)}
    .card .hd{padding:14px 16px;border-bottom:1px solid #202638;color:#c9d3ea;display:flex;justify-content:space-between;align-items:center}
    .card .bd{padding:18px 16px}

    .kpis{grid-column:1/-1;display:grid;grid-template-columns:repeat(12,1fr);gap:14px}
    .kpi{grid-column:span 3;background:var(--panel-2);border:1px solid #24304a;border-radius:16px;padding:14px 16px;position:relative;overflow:hidden}
    .kpi h3{margin:0 0 8px 0;font-size:12px;color:#a7b0c2;text-transform:uppercase;letter-spacing:.08em}
    .kpi .val{font-size:28px;font-weight:800}
    .kpi .delta{font-size:12px;color:#9db0ff;margin-top:6px}
    .kpi .tag{position:absolute;right:12px;top:12px;font-size:12px;padding:4px 8px;border-radius:999px;border:1px solid #2b3550;background:#1a2130;color:#b7c5ff}

    @media (max-width: 1000px){.kpi{grid-column:span 6}}
    @media (max-width: 620px){.kpi{grid-column:span 12}}

    .panel-flex{display:flex;gap:14px;flex-wrap:wrap}
    .panel{flex:1;min-width:320px}

    /* Chart */
    .chart-wrap{height:320px; position:relative}
    svg{display:block}
    .axis path,.axis line{stroke:#35405b}
    .axis text{fill:#a7b0c2;font-size:12px}
    .bar{fill:url(#gradBar)}
    .bar:hover{opacity:.9}
    .tooltip{position:absolute;pointer-events:none;background:#101521;border:1px solid #2b3550;color:#dfe7ff;padding:8px 10px;border-radius:10px;font-size:12px;box-shadow:var(--shadow);transform:translate(-50%,-120%);white-space:nowrap}

    /* Table */
    table{width:100%;border-collapse:collapse}
    th, td{padding:10px 8px;border-bottom:1px solid #20283b;text-align:left}
    th{font-size:12px;color:#9aa6bf;text-transform:uppercase;letter-spacing:.06em}
    tr:hover td{background:#121826}

    .controls{display:flex;gap:10px;flex-wrap:wrap;align-items:center}
    .field{display:flex;flex-direction:column;gap:6px}
    .field label{font-size:12px;color:#a7b0c2}
    .input, select{background:#0e1422;border:1px solid #24304a;color:#dfe7ff;border-radius:10px;padding:10px 10px;min-height:40px}
    .input::placeholder{color:#6e7890}

    footer{margin:20px 0;color:#7f8aa6;font-size:12px}
    .mono{font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace}
  </style>
</head>
<body>
  <div class="app">
    <header>
      <div class="brand">
        <div class="logo"><span>AI</span></div>
        <div>
          <h1>Dashboard – Reminder Pagamenti Fatture</h1>
          <div class="subtitle">Automazione AI: solleciti a +7, +14, +21, +35 giorni dalla scadenza</div>
        </div>
      </div>
      <div class="toolbar">
        <button class="btn ghost small" id="btnSample">Carica dati d'esempio</button>
        <label class="btn small" for="fileInput">Importa JSON</label>
        <input type="file" id="fileInput" accept="application/json" style="display:none">
        <button class="btn primary small" id="btnExport">Esporta JSON</button>
      </div>
    </header>

    <section class="card">
      <div class="hd">
        <div>Controlli</div>
        <div class="chip" id="runStatus">Frequenza di esecuzione: <span class="mono" id="freqLabel">giornaliera</span></div>
      </div>
      <div class="bd controls">
        <div class="field">
          <label>Intervallo date (pagamenti post-reminder)</label>
          <div style="display:flex;gap:8px;flex-wrap:wrap">
            <input type="date" class="input" id="dateFrom">
            <input type="date" class="input" id="dateTo">
            <button class="btn small" id="btnToday">Ultimi 30 giorni</button>
          </div>
        </div>
        <div class="field">
          <label>Frequenza di esecuzione automazione</label>
          <select id="freqSelect">
            <option value="giornaliera">Giornaliera</option>
            <option value="ogni_6h">Ogni 6 ore</option>
            <option value="settimanale">Settimanale</option>
          </select>
        </div>
        <div class="field">
          <label>Filtra per promemoria che ha generato il pagamento</label>
          <select id="reminderFilter">
            <option value="all">Tutti</option>
            <option value="7">+7 giorni</option>
            <option value="14">+14 giorni</option>
            <option value="21">+21 giorni</option>
            <option value="35">+35 giorni</option>
          </select>
        </div>
        <div style="flex:1"></div>
        <button class="btn" id="btnReset">Reset filtri</button>
      </div>
    </section>

    <section class="kpis">
      <div class="kpi" id="kpiRecovered">
        <span class="tag">€</span>
        <h3>Guadagni Recuperati (Totale)</h3>
        <div class="val" id="recoveredTotal">€ 0</div>
        <div class="delta" id="recoveredAvg">Media per cliente: € 0</div>
      </div>
      <div class="kpi" id="kpiEmails">
        <span class="tag">@</span>
        <h3>Email Inviate con Successo</h3>
        <div class="val" id="emailsSent">0</div>
        <div class="delta" id="emailsPerContact">Medie: 0 per contatto</div>
      </div>
      <div class="kpi" id="kpiTime">
        <span class="tag">⏱</span>
        <h3>Tempo Medio di Pagamento (post-reminder)</h3>
        <div class="val" id="avgPayTime">—</div>
        <div class="delta" id="medianPayTime">Mediana: —</div>
      </div>
      <div class="kpi" id="kpiContacts">
        <span class="tag">👤</span>
        <h3>Contatti Processati</h3>
        <div class="val" id="contactsProcessed">0</div>
        <div class="delta" id="invoicesCount">Fatture: 0</div>
      </div>
    </section>

    <section class="grid" style="margin-top:14px">
      <div class="card" style="grid-column:span 7">
        <div class="hd">Pagamenti generati dal promemoria</div>
        <div class="bd">
          <div class="chart-wrap" id="chartWrap">
            <div class="tooltip" id="tooltip" style="display:none"></div>
            <svg id="chart" width="100%" height="100%" preserveAspectRatio="none"></svg>
          </div>
        </div>
      </div>
      <div class="card" style="grid-column:span 5">
        <div class="hd">Ultimi contatti paganti</div>
        <div class="bd">
          <table id="tblRecent">
            <thead>
              <tr>
                <th>Data pagamento</th>
                <th>Contatto</th>
                <th>Fattura</th>
                <th>€ Importo</th>
                <th>Promemoria</th>
                <th>Email OK</th>
              </tr>
            </thead>
            <tbody></tbody>
          </table>
        </div>
      </div>
    </section>

    <footer>
      <div>Schema dati JSON atteso (array): <span class="mono">{ id, contact, amount, due_date, payment_date, reminders_sent:["7","14",...], trigger:"7|14|21|35", email_success:int }</span></div>
    </footer>
  </div>

  <svg width="0" height="0" style="position:absolute">
    <defs>
      <linearGradient id="gradBar" x1="0" x2="0" y1="0" y2="1">
        <stop offset="0%" stop-color="#8ba2ff"/>
        <stop offset="100%" stop-color="#6d7cff"/>
      </linearGradient>
    </defs>
  </svg>

  <script>
    // ---------- Utilities ----------
    const fmtEUR = (n) => new Intl.NumberFormat('it-IT',{style:'currency',currency:'EUR', maximumFractionDigits:0}).format(n||0);
    const fmtInt = (n) => new Intl.NumberFormat('it-IT').format(Math.round(n||0));
    const daysBetween = (a,b) => Math.round((new Date(b) - new Date(a)) / (1000*60*60*24));
    const clamp = (x,a,b)=>Math.max(a,Math.min(b,x));

    const state = { data: [], filters: { from:null, to:null, trigger:'all' }, freq:'giornaliera' };

    function saveLocal(){ localStorage.setItem('ai-reminder-dashboard', JSON.stringify(state)); }
    function loadLocal(){ try{ const s = JSON.parse(localStorage.getItem('ai-reminder-dashboard')); if(s){ Object.assign(state, s); } }catch(e){} }

    // ---------- Sample Data ----------
    function sampleData(){
      const today = new Date();
      const items = [];
      const contacts = ['Alfa Srl','Beta SpA','Gamma SNC','Delta SRLS','Epsilon SA','Zeta GmbH','Eta Ltd','Theta BV','Iota Inc','Kappa LLC'];
      const rnd = (a,b)=>Math.floor(Math.random()*(b-a+1))+a;
      let id=1000;
      for(let i=0;i<64;i++){
        const c = contacts[rnd(0,contacts.length-1)];
        const due = new Date(today); due.setDate(due.getDate() - rnd(15,80));
        const amount = rnd(200, 4800);
        const reminderOptions=['7','14','21','35'];
        const trigger = reminderOptions[rnd(0,reminderOptions.length-1)];
        const payLag = rnd(1,10); // days after reminder
        const payment_date = new Date(due); payment_date.setDate(payment_date.getDate() + parseInt(trigger) + payLag);
        const email_success = rnd(1,4); // emails sent successfully
        const sent = reminderOptions.filter(x=>parseInt(x)<=parseInt(trigger));
        items.push({
          id:id++, contact:c, amount, due_date:iso(due), payment_date:iso(payment_date),
          reminders_sent:sent, trigger, email_success
        });
      }
      return items;
    }
    function iso(d){ const z = new Date(d); z.setHours(12,0,0,0); return z.toISOString().slice(0,10); }

    // ---------- Data Handling ----------
    function applyFilters(){
      let rows = [...state.data];
      if(state.filters.from){ rows = rows.filter(r => new Date(r.payment_date) >= new Date(state.filters.from)); }
      if(state.filters.to){ const t = new Date(state.filters.to); t.setHours(23,59,59,999); rows = rows.filter(r => new Date(r.payment_date) <= t); }
      if(state.filters.trigger!== 'all'){ rows = rows.filter(r => String(r.trigger)===String(state.filters.trigger)); }
      return rows;
    }

    function computeMetrics(){
      const rows = applyFilters();
      const contactsSet = new Set(rows.map(r=>r.contact));
      const recovered = rows.reduce((s,r)=>s + (r.amount||0), 0);
      const avgPerContact = contactsSet.size ? recovered / contactsSet.size : 0;
      const emails = rows.reduce((s,r)=>s + (r.email_success||0), 0);
      const emailsPerContact = contactsSet.size ? emails/contactsSet.size : 0;
      const lags = rows.map(r=>{
        const dDue = new Date(r.due_date);
        const dPay = new Date(r.payment_date);
        const trig = parseInt(r.trigger||0);
        return (isFinite(trig) ? daysBetween(new Date(dDue.getTime()+trig*86400000), dPay) : daysBetween(dDue, dPay));
      }).filter(v=>isFinite(v) && v>=0);
      const avgLag = lags.length ? (lags.reduce((a,b)=>a+b,0)/lags.length) : null;
      const medLag = lags.length ? (lags.sort((a,b)=>a-b)[Math.floor(lags.length/2)]) : null;
      const byTrigger = {7:0,14:0,21:0,35:0};
      rows.forEach(r=>{ const k = String(r.trigger); if(byTrigger[k]!==undefined) byTrigger[k]++; });
      return { rows, contacts:contactsSet.size, invoices:rows.length, recovered, avgPerContact, emails, emailsPerContact, avgLag, medLag, byTrigger };
    }

    // ---------- Rendering ----------
    function render(){
      document.getElementById('freqLabel').textContent = state.freq.replace('_',' ');
      const m = computeMetrics();
      document.getElementById('recoveredTotal').textContent = fmtEUR(m.recovered);
      document.getElementById('recoveredAvg').textContent = `Media per cliente: ${fmtEUR(m.avgPerContact)}`;
      document.getElementById('emailsSent').textContent = fmtInt(m.emails);
      document.getElementById('emailsPerContact').textContent = `Medie: ${m.emailsPerContact.toFixed(2)} per contatto`;
      document.getElementById('avgPayTime').textContent = m.avgLag==null? '—' : `${m.avgLag.toFixed(1)} gg`;
      document.getElementById('medianPayTime').textContent = m.medLag==null? 'Mediana: —' : `Mediana: ${m.medLag} gg`;
      document.getElementById('contactsProcessed').textContent = fmtInt(m.contacts);
      document.getElementById('invoicesCount').textContent = `Fatture: ${fmtInt(m.invoices)}`;
      renderChart(m.byTrigger);
      renderTable(m.rows);
      saveLocal();
    }

    function renderTable(rows){
      const tb = document.querySelector('#tblRecent tbody');
      tb.innerHTML = '';
      // Sort by payment_date desc
      const view = [...rows].sort((a,b)=> new Date(b.payment_date) - new Date(a.payment_date)).slice(0,12);
      for(const r of view){
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>${r.payment_date}</td>
          <td>${escapeHtml(r.contact)}</td>
          <td>#${r.id}</td>
          <td>${fmtEUR(r.amount)}</td>
          <td>+${r.trigger}g</td>
          <td>${r.email_success}</td>
        `;
        tb.appendChild(tr);
      }
    }

    function escapeHtml(s){ return String(s).replace(/[&<>"']/g, c=> ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;','\'':'&#39;'}[c])); }

    // Simple responsive SVG Bar Chart
    function renderChart(byTrigger){
      const el = document.getElementById('chart');
      const wrap = document.getElementById('chartWrap');
      const tooltip = document.getElementById('tooltip');
      const W = wrap.clientWidth; const H = wrap.clientHeight; el.setAttribute('viewBox', `0 0 ${W} ${H}`);
      const pad = {l:50, r:20, t:20, b:40};
      const triggers = [7,14,21,35];
      const data = triggers.map(k=>({label:`+${k}g`, value: byTrigger[k]||0, k}))
      const maxV = Math.max(5, ...data.map(d=>d.value));
      const bw = (W - pad.l - pad.r) / data.length * 0.6;
      const gap = (W - pad.l - pad.r) / data.length * 0.4;

      // clear
      el.innerHTML = '';

      // axes
      const axis = document.createElementNS('http://www.w3.org/2000/svg','g');
      axis.setAttribute('class','axis');
      const x0 = pad.l, y0 = H - pad.b;
      axis.appendChild(line(x0, y0, W-pad.r, y0));
      axis.appendChild(line(x0, pad.t, x0, y0));
      // y ticks
      const ticks = 5;
      for(let i=0;i<=ticks;i++){
        const y = pad.t + (H-pad.t-pad.b) * (i/ticks);
        const val = Math.round(maxV * (1 - i/ticks));
        const l = line(x0, y, W-pad.r, y); l.setAttribute('stroke-dasharray','2 4'); l.setAttribute('opacity','0.5'); axis.appendChild(l);
        const txt = text(x0-8, y+4, fmtInt(val)); txt.setAttribute('text-anchor','end'); axis.appendChild(txt);
      }
      // x labels
      data.forEach((d, i)=>{
        const x = pad.l + i*((bw+gap)) + bw/2;
        const txt = text(x, H-pad.b+18, d.label); txt.setAttribute('text-anchor','middle'); axis.appendChild(txt);
      });
      el.appendChild(axis);

      // bars
      const gBars = document.createElementNS('http://www.w3.org/2000/svg','g');
      data.forEach((d,i)=>{
        const x = pad.l + i*(bw+gap);
        const h = (H-pad.t-pad.b) * (d.value/maxV);
        const y = (H-pad.b) - h;
        const r = rect(x, y, bw, h); r.setAttribute('class','bar'); r.style.cursor='pointer';
        r.addEventListener('mousemove', (e)=>{
          tooltip.style.display = 'block';
          tooltip.textContent = `${d.label}: ${fmtInt(d.value)} pagamenti`;
          const bb = wrap.getBoundingClientRect();
          tooltip.style.left = (e.clientX - bb.left) + 'px';
          tooltip.style.top = (y + 6) + 'px';
        });
        r.addEventListener('mouseleave', ()=>{ tooltip.style.display='none'; });
        gBars.appendChild(r);
      });
      el.appendChild(gBars);

      function line(x1,y1,x2,y2){ const l = document.createElementNS('http://www.w3.org/2000/svg','line'); l.setAttribute('x1',x1); l.setAttribute('y1',y1); l.setAttribute('x2',x2); l.setAttribute('y2',y2); return l; }
      function rect(x,y,w,h){ const r = document.createElementNS('http://www.w3.org/2000/svg','rect'); r.setAttribute('x',x); r.setAttribute('y',y); r.setAttribute('width',w); r.setAttribute('height',h); r.setAttribute('rx',8); return r; }
      function text(x,y,txt){ const t = document.createElementNS('http://www.w3.org/2000/svg','text'); t.setAttribute('x',x); t.setAttribute('y',y); t.textContent = txt; return t; }
    }

    // ---------- IO ----------
    document.getElementById('fileInput').addEventListener('change', (ev)=>{
      const file = ev.target.files[0]; if(!file) return;
      const reader = new FileReader();
      reader.onload = ()=>{
        try{
          const parsed = JSON.parse(reader.result);
          if(!Array.isArray(parsed)) throw new Error('Il JSON deve essere un array di oggetti');
          state.data = parsed;
          render();
        }catch(e){ alert('Errore parsing JSON: ' + e.message); }
      };
      reader.readAsText(file);
    });

    document.getElementById('btnExport').addEventListener('click', ()=>{
      const blob = new Blob([JSON.stringify(state.data,null,2)], {type:'application/json'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a'); a.href = url; a.download = 'reminder_data.json'; a.click(); URL.revokeObjectURL(url);
    });

    document.getElementById('btnSample').addEventListener('click', ()=>{ state.data = sampleData(); render(); });

    document.getElementById('btnToday').addEventListener('click', ()=>{
      const to = new Date(); const from = new Date(); from.setDate(to.getDate()-30);
      document.getElementById('dateFrom').value = iso(from); document.getElementById('dateTo').value = iso(to);
      state.filters.from = iso(from); state.filters.to = iso(to); render();
    });

    document.getElementById('btnReset').addEventListener('click', ()=>{
      state.filters = {from:null,to:null,trigger:'all'};
      document.getElementById('dateFrom').value=''; document.getElementById('dateTo').value=''; document.getElementById('reminderFilter').value='all';
      render();
    });

    document.getElementById('dateFrom').addEventListener('change', (e)=>{ state.filters.from = e.target.value || null; render(); });
    document.getElementById('dateTo').addEventListener('change', (e)=>{ state.filters.to = e.target.value || null; render(); });
    document.getElementById('reminderFilter').addEventListener('change', (e)=>{ state.filters.trigger = e.target.value; render(); });

    document.getElementById('freqSelect').addEventListener('change', (e)=>{ state.freq = e.target.value; document.getElementById('freqLabel').textContent = state.freq.replace('_',' '); saveLocal(); });

    // ---------- Init ----------
    loadLocal();
    // If no data, preload sample for a great first impression
    if(!state.data || !state.data.length){ state.data = sampleData(); }
    // Restore filters/controls
    document.getElementById('freqSelect').value = state.freq || 'giornaliera';
    document.getElementById('reminderFilter').value = state.filters.trigger || 'all';
    if(state.filters.from) document.getElementById('dateFrom').value = state.filters.from;
    if(state.filters.to) document.getElementById('dateTo').value = state.filters.to;
    render();
  </script>
</body>
</html>

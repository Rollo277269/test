<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Follow-up Calls Dashboard</title>
  <!-- Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <!-- Chart.js -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
  <!-- FullCalendar -->
  <link href="https://cdn.jsdelivr.net/npm/fullcalendar@6.1.15/index.global.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/fullcalendar@6.1.15/index.global.min.js"></script>
  <style>
    :root{
      --bg:#0b0f14; --panel:#0f141b; --panel-2:#131a22; --border:#1f2937;
      --text:#e5e7eb; --muted:#9aa3af; --accent:#38bdf8; --accent-2:#8b5cf6;
      --good:#22c55e; --warn:#f59e0b; --ring:0 0 0 1px rgba(56,189,248,.25),0 0 0 6px rgba(56,189,248,.07);
    }
    *{box-sizing:border-box} html,body{height:100%}
    body{
      margin:0;
      background:
        radial-gradient(1100px 520px at 80% -10%, rgba(56,189,248,.08), transparent 60%),
        radial-gradient(900px 500px at -10% 20%, rgba(139,92,246,.06), transparent 60%),
        var(--bg);
      color:var(--text); font-family:Inter,system-ui,-apple-system,Segoe UI,Roboto,Arial;
    }
    .shell{max-width:1200px; margin:0 auto; padding:24px}
    .topbar{display:flex; align-items:center; justify-content:space-between; gap:16px; margin-bottom:18px}
    .brand{display:flex; align-items:center; gap:10px}
    .brand .dot{width:10px; height:10px; border-radius:50%; background:linear-gradient(135deg,var(--accent),var(--accent-2)); box-shadow:0 0 18px rgba(56,189,248,.75)}
    .brand h1{font-size:22px; font-weight:700; letter-spacing:.2px; margin:0}
    .filters{display:flex; gap:10px; align-items:center}
    .btn, select{background:var(--panel); color:var(--text); border:1px solid var(--border); border-radius:10px; padding:10px 12px; font-weight:600; cursor:pointer}
    .btn:hover, select:hover{border-color:#334155}

    .grid{display:grid; grid-template-columns:repeat(12,1fr); gap:16px}
    .card{background:linear-gradient(180deg, rgba(255,255,255,.02), rgba(255,255,255,0) 40%), var(--panel); border:1px solid var(--border); border-radius:16px; padding:16px; box-shadow:0 10px 18px rgba(0,0,0,.25)}
    .card h3{margin:0 0 10px 0; font-size:14px; color:var(--muted); font-weight:600; text-transform:uppercase; letter-spacing:.1em}

    .ring{position:relative; border-radius:14px; padding:14px; background:var(--panel-2); border:1px solid var(--border); box-shadow:var(--ring)}
    .kpi{display:flex; align-items:end; justify-content:space-between}
    .kpi .value{font-size:28px; font-weight:800}
    .kpi .delta{font-size:12px; color:var(--muted)}
    .muted{color:var(--muted)}

    .legend{display:flex; gap:8px; flex-wrap:wrap; margin-top:6px}
    .legend .item{display:flex; align-items:center; gap:8px; font-size:12px; color:var(--muted); background:var(--panel-2); border:1px solid var(--border); border-radius:10px; padding:6px 10px; cursor:pointer; transition:transform .15s ease, border-color .15s ease}
    .legend .item:hover{transform:translateY(-1px); border-color:#334155}
    .legend .item.off{opacity:.55}
    .legend .sw{width:10px; height:10px; border-radius:50%}

    .chart-wrap{height:360px; position:relative}
    #statusChart{width:100%; height:100%}

    #calendar{padding:8px; background:var(--panel); border:1px solid var(--border); border-radius:16px}
    .fc{--fc-border-color:var(--border); --fc-page-bg-color:transparent; --fc-neutral-bg-color:#0d131a; --fc-today-bg-color:rgba(56,189,248,.08); --fc-list-event-hover-bg-color:rgba(255,255,255,.02); --fc-theme-standard-bg-color:transparent; --fc-neutral-text-color:var(--muted); color:var(--text)}
    .fc .fc-toolbar-title{font-size:16px; font-weight:700}
    .fc .fc-button{background:var(--panel-2); border:1px solid var(--border); color:var(--text); border-radius:10px}
    .fc .fc-button:hover{border-color:#334155}

    .pipeline{display:flex; gap:12px; margin-top:8px}
    .pill{border:1px solid var(--border); background:var(--panel-2); border-radius:999px; padding:8px 12px; font-size:12px}
    .pill .dot{width:8px; height:8px; display:inline-block; border-radius:50%; margin-right:6px}

    .footer{margin-top:18px; color:var(--muted); font-size:12px; text-align:center}
    @media (max-width:1024px){.grid{grid-template-columns:1fr}}
  </style>
</head>
<body>
  <div class="shell">
    <div class="topbar">
      <div class="brand"><div class="dot"></div><h1>Overview – Follow-up Calls</h1></div>
      <div class="filters">
        <select id="range">
          <option value="7">Ultimi 7 giorni</option>
          <option value="14">Ultimi 14 giorni</option>
          <option value="30" selected>Ultimi 30 giorni</option>
          <option value="90">Ultimi 90 giorni</option>
        </select>
        <button class="btn" id="refresh">Aggiorna</button>
      </div>
    </div>

    <!-- KPIs -->
    <section class="grid" style="margin-bottom:12px">
      <div class="card ring" style="grid-column: span 3">
        <h3>Tempo medio risposta</h3>
        <div class="kpi"><div class="value" id="avgResponse">–</div><div class="delta muted" id="avgResponseDelta"></div></div>
      </div>
      <div class="card ring" style="grid-column: span 3">
        <h3>Tasso di no-show</h3>
        <div class="kpi"><div class="value" id="noShowRate">–</div><div class="delta muted" id="noShowDelta"></div></div>
      </div>
      <div class="card ring" style="grid-column: span 3">
        <h3>Chiamate riprogrammate</h3>
        <div class="kpi"><div class="value" id="rescheduledCount">–</div><div class="delta muted" id="rescheduledDelta"></div></div>
      </div>
      <div class="card ring" style="grid-column: span 3">
        <h3>Media riprogrammazioni</h3>
        <div class="kpi"><div class="value" id="avgReschedules">–</div><div class="delta muted" id="avgReschedulesDelta"></div></div>
      </div>
    </section>

    <!-- Chart + Pipeline -->
    <section class="grid" style="margin-bottom:12px">
      <div class="card" style="grid-column: span 8">
        <div style="display:flex; align-items:center; justify-content:space-between; gap:12px">
          <h3>Chiamate: Confermate vs Riprogrammare vs Nessuna risposta</h3>
          <div class="legend" id="legend"></div>
        </div>
        <div class="chart-wrap"><canvas id="statusChart"></canvas></div>
      </div>
      <div class="card" style="grid-column: span 4">
        <h3>Pipeline</h3>
        <div class="pipeline" id="pipeline"></div>
        <p class="muted" style="margin-top:8px">Stato corrente dei lead nel periodo selezionato.</p>
      </div>
    </section>

    <!-- Calendar -->
    <section class="grid">
      <div class="card" style="grid-column: span 12">
        <h3>Calendario – Slot occupati</h3>
        <div id="calendar"></div>
      </div>
    </section>

    <p class="footer">UI dark ispirata alla reference. Single-file, client-side only.</p>
  </div>

  <script>
    // ========= Demo dataset (sostituisci con i tuoi dati) =========
    function daysAgo(n){ const d=new Date(); d.setDate(d.getDate()-n); return d; }
    const DATA = [
      { id: 1, title: 'Call – Rossi SRL',  start: daysAgo(1).toISOString().slice(0,10)+'T10:00:00', end: daysAgo(1).toISOString().slice(0,10)+'T10:30:00', status:'confermata',      responseHours: 3,  attended:true,  reschedules:0 },
      { id: 2, title: 'Call – Bianchi Spa',start: daysAgo(2).toISOString().slice(0,10)+'T15:00:00', end: daysAgo(2).toISOString().slice(0,10)+'T15:45:00', status:'riprogrammare', responseHours: 12, attended:true,  reschedules:1 },
      { id: 3, title: 'Call – Verdi SNC',  start: daysAgo(3).toISOString().slice(0,10)+'T09:30:00', end: daysAgo(3).toISOString().slice(0,10)+'T10:00:00', status:'confermata',      responseHours: 5,  attended:false, reschedules:0 },
      { id: 4, title: 'Call – Gamma Ltd',  start: daysAgo(4).toISOString().slice(0,10)+'T11:30:00', end: daysAgo(4).toISOString().slice(0,10)+'T12:00:00', status:'nessuna_risposta', responseHours: null, attended:false, reschedules:0 },
      { id: 5, title: 'Call – Delta Spa',  start: daysAgo(5).toISOString().slice(0,10)+'T16:00:00', end: daysAgo(5).toISOString().slice(0,10)+'T16:30:00', status:'riprogrammare', responseHours: 20, attended:true,  reschedules:2 },
      { id: 6, title: 'Call – Omega LLC',  start: daysAgo(6).toISOString().slice(0,10)+'T13:00:00', end: daysAgo(6).toISOString().slice(0,10)+'T13:30:00', status:'confermata',      responseHours: 2,  attended:true,  reschedules:0 },
      { id: 7, title: 'Call – Test A',     start: daysAgo(8).toISOString().slice(0,10)+'T10:30:00', end: daysAgo(8).toISOString().slice(0,10)+'T11:00:00', status:'nessuna_risposta', responseHours: null, attended:false, reschedules:0 },
      { id: 8, title: 'Call – Test B',     start: daysAgo(9).toISOString().slice(0,10)+'T17:00:00', end: daysAgo(9).toISOString().slice(0,10)+'T17:30:00', status:'confermata',      responseHours: 7,  attended:true,  reschedules:0 },
      { id: 9, title: 'Call – Test C',     start: daysAgo(12).toISOString().slice(0,10)+'T12:00:00', end: daysAgo(12).toISOString().slice(0,10)+'T12:30:00', status:'riprogrammare', responseHours: 9,  attended:true,  reschedules:3 },
      { id:10, title: 'Call – Test D',     start: daysAgo(14).toISOString().slice(0,10)+'T09:00:00', end: daysAgo(14).toISOString().slice(0,10)+'T09:30:00', status:'confermata',      responseHours: 4,  attended:false, reschedules:1 },
    ];

    // ========= Utilities =========
    const byDayKey = d => new Date(d).toISOString().slice(0,10);
    function dateKeysRange(days){
      const out=[]; const start=new Date(); start.setHours(0,0,0,0); start.setDate(start.getDate()-(days-1));
      for(let i=0;i<days;i++){ const d=new Date(start); d.setDate(start.getDate()+i); out.push(d.toISOString().slice(0,10)); }
      return out;
    }
    function filterByRange(days){
      const start=new Date(); start.setDate(start.getDate()-(days-1)); start.setHours(0,0,0,0);
      return DATA.filter(e => new Date(e.start) >= start);
    }

    function computeMetrics(events, days){
      const responses = events.map(e=>e.responseHours).filter(v => typeof v === 'number');
      const avgResponse = responses.length ? (responses.reduce((a,b)=>a+b,0)/responses.length) : 0;

      const confirmed = events.filter(e=>e.status==='confermata');
      const noShows   = confirmed.filter(e=>e.attended===false);
      const noShowRate = confirmed.length ? (noShows.length/confirmed.length)*100 : 0;

      const rescheduled = events.filter(e=>e.status==='riprogrammare');
      const rescheduledCount = rescheduled.length;
      const avgReschedules = rescheduled.length ? (rescheduled.reduce((s,e)=>s+e.reschedules,0)/rescheduled.length) : 0;

      const labels = dateKeysRange(days);
      const byKey = {};
      events.forEach(e=>{
        const k = byDayKey(e.start);
        if(!byKey[k]) byKey[k] = {confirmed:0,reschedule:0,noresp:0};
        if(e.status==='confermata') byKey[k].confirmed++;
        else if(e.status==='riprogrammare') byKey[k].reschedule++;
        else byKey[k].noresp++;
      });
      const series = {
        labels,
        confirmed: labels.map(k=>byKey[k]?.confirmed||0),
        reschedule: labels.map(k=>byKey[k]?.reschedule||0),
        noresp: labels.map(k=>byKey[k]?.noresp||0)
      };

      const pipeline = {
        Confermate: confirmed.length,
        Riprogrammare: rescheduledCount,
        "Nessuna risposta": events.filter(e=>e.status==='nessuna_risposta').length
      };

      return { avgResponse, noShowRate, rescheduledCount, avgReschedules, series, pipeline };
    }

    // ========= UI Bindings / State =========
    const elAvgResponse = document.getElementById('avgResponse');
    const elNoShowRate = document.getElementById('noShowRate');
    const elRescheduledCount = document.getElementById('rescheduledCount');
    const elAvgReschedules = document.getElementById('avgReschedules');
    const elPipeline = document.getElementById('pipeline');
    const rangeSelect = document.getElementById('range');

    let chart; // Chart.js instance
    const chartColors = { confirmed:'#38bdf8', reschedule:'#f59e0b', noresp:'#8b5cf6' };
    const datasetIndexMap = { confirmed:0, reschedule:1, noresp:2 };
    let visibility = { confirmed:true, reschedule:true, noresp:true };

    function renderLegend(){
      const legend = document.getElementById('legend');
      legend.innerHTML = '';
      [
        {key:'confirmed', label:'Confermate', c: chartColors.confirmed},
        {key:'reschedule', label:'Da riprogrammare', c: chartColors.reschedule},
        {key:'noresp',     label:'Nessuna risposta', c: chartColors.noresp},
      ].forEach(item=>{
        const el = document.createElement('button');
        el.className = 'item' + (visibility[item.key] ? '' : ' off');
        el.type='button';
        el.innerHTML = `<span class="sw" style="background:${item.c}"></span>${item.label}`;
        el.addEventListener('click', ()=>{
          visibility[item.key] = !visibility[item.key];
          el.classList.toggle('off', !visibility[item.key]);
          if(chart){ chart.setDatasetVisibility(datasetIndexMap[item.key], visibility[item.key]); chart.update(); }
        });
        legend.appendChild(el);
      });
    }

    function formatHours(h){ if(h>=24){const d=Math.floor(h/24), r=Math.round(h%24); return `${d}g ${r}h`; } return `${h.toFixed(1)}h`; }

    function updateUI(days){
      const ev = filterByRange(days);
      const m = computeMetrics(ev, days);

      elAvgResponse.textContent   = formatHours(m.avgResponse);
      elNoShowRate.textContent    = m.noShowRate.toFixed(1)+'%';
      elRescheduledCount.textContent = m.rescheduledCount.toString();
      elAvgReschedules.textContent= m.avgReschedules.toFixed(2);

      // Pipeline
      elPipeline.innerHTML = '';
      Object.entries(m.pipeline).forEach(([k,v])=>{
        const color = k.includes('Confermate')? 'var(--good)': k.includes('riprog')? 'var(--warn)' : 'var(--accent-2)';
        const pill = document.createElement('div'); pill.className='pill';
        pill.innerHTML = `<span class="dot" style="background:${color}"></span><strong>${k}</strong> · ${v}`;
        elPipeline.appendChild(pill);
      });

      // Chart
      const ctx = document.getElementById('statusChart');
      const data = {
        labels: m.series.labels,
        datasets: [
          { label:'Confermate',       data:m.series.confirmed,  tension:.36, borderColor:chartColors.confirmed,  backgroundColor:'rgba(56,189,248,.10)', fill:true, pointRadius:0, pointHoverRadius:4, borderWidth:2, hidden:!visibility.confirmed },
          { label:'Da riprogrammare', data:m.series.reschedule, tension:.36, borderColor:chartColors.reschedule, backgroundColor:'rgba(245,158,11,.10)', fill:true, pointRadius:0, pointHoverRadius:4, borderWidth:2, hidden:!visibility.reschedule },
          { label:'Nessuna risposta', data:m.series.noresp,     tension:.36, borderColor:chartColors.noresp,     backgroundColor:'rgba(139,92,246,.12)', fill:true, pointRadius:0, pointHoverRadius:4, borderWidth:2, hidden:!visibility.noresp }
        ]
      };
      const options = {
        responsive:true, maintainAspectRatio:false,
        interaction:{ mode:'index', intersect:false },
        animation:{ duration:700, easing:'easeOutQuart' },
        scales:{
          x:{ grid:{ color:'rgba(148,163,184,.10)' }, ticks:{ color:'#9aa3af' } },
          y:{ grid:{ color:'rgba(148,163,184,.10)' }, ticks:{ color:'#9aa3af' }, beginAtZero:true, grace:'10%' }
        },
        plugins:{
          legend:{ display:false },
          tooltip:{ enabled:true, backgroundColor:'#0b1220', titleColor:'#e5e7eb', bodyColor:'#cbd5e1', borderColor:'#334155', borderWidth:1, padding:10,
            callbacks:{ label:(ctx)=>` ${ctx.dataset.label}: ${ctx.parsed.y}` } }
        }
      };
      if(chart) chart.destroy();
      chart = new Chart(ctx, { type:'line', data, options });

      // Calendar
      const fcEvents = ev.map(e=>({
        id:e.id, title:e.title, start:e.start, end:e.end,
        backgroundColor: e.status==='confermata'   ? 'rgba(34,197,94,.25)' :
                        e.status==='riprogrammare' ? 'rgba(245,158,11,.25)' : 'rgba(139,92,246,.25)',
        borderColor:     e.status==='confermata'   ? 'rgba(34,197,94,.55)' :
                        e.status==='riprogrammare' ? 'rgba(245,158,11,.55)' : 'rgba(139,92,246,.55)',
        textColor:'#e5e7eb'
      }));
      calendar.removeAllEvents();
      calendar.addEventSource(fcEvents);
    }

    // Init calendar
    const calendarEl = document.getElementById('calendar');
    const calendar = new FullCalendar.Calendar(calendarEl, {
      initialView:'timeGridWeek', nowIndicator:true, height:600,
      headerToolbar:{ left:'prev,next today', center:'title', right:'dayGridMonth,timeGridWeek,timeGridDay' },
      slotMinTime:'08:00:00', slotMaxTime:'19:00:00', allDaySlot:false, eventDisplay:'block'
    });
    calendar.render();

    renderLegend();
    // First render
    updateUI(parseInt(rangeSelect.value,10));
    // Bindings
    document.getElementById('refresh').addEventListener('click', ()=> updateUI(parseInt(rangeSelect.value,10)));
    rangeSelect.addEventListener('change', ()=> updateUI(parseInt(rangeSelect.value,10)));

    /* ============ Integrazione live =============
       Sostituisci l'array DATA con i tuoi record:
       {id,title,start,end,status,responseHours,attended,reschedules}
       e richiama updateUI(nGiorni) dopo il fetch().
    ============================================= */
  </script>
</body>
</html>

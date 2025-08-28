<!doctype html>
<html lang="it">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Spia Competizione — Dashboard (Mock)</title>
  <style>
    :root{
      --bg:#0a0a0b;--panel:#1a1a1d;--panel-light:#242429;
      --accent:#4f46e5;--accent-light:#6366f1;
      --success:#10b981;--warning:#f59e0b;
      --text:#f8fafc;--text-muted:#94a3b8;--text-2:#64748b;
      --border:rgba(148,163,184,.1);--shadow:rgba(0,0,0,.5);
      --glow:0 0 0 2px rgba(79,70,229,.15), 0 10px 30px rgba(79,70,229,.25);
    }
    *{box-sizing:border-box;margin:0;padding:0}
    body{font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif;background:var(--bg);color:var(--text)}
    /* Layout full viewport */
    .dashboard{
      max-width:1400px;margin:0 auto;padding:24px;
      display:grid;grid-template-columns:360px 1fr;gap:24px;height:100dvh;
    }
    .sidebar,.main{min-height:0}
    .sidebar{display:flex;flex-direction:column;gap:20px}
    .main{display:flex;flex-direction:column;gap:24px;overflow:hidden}
    .card{background:var(--panel);border:1px solid var(--border);border-radius:16px;padding:24px;box-shadow:0 4px 20px var(--shadow)}

    /* Header */
    .header{display:flex;justify-content:space-between;align-items:center}
    .title{display:flex;align-items:center;gap:10px;font-size:24px;font-weight:800}
    .led{width:8px;height:8px;border-radius:50%;background:var(--success);box-shadow:0 0 8px var(--success)}
    .tabs{display:flex;gap:8px;background:var(--panel-light);padding:4px;border-radius:12px}
    .tab{padding:8px 16px;border:none;border-radius:8px;background:transparent;color:var(--text-muted);font-weight:700}
    .tab.active{background:var(--accent);color:#fff;box-shadow:var(--glow)}

    /* Form */
    .group{margin-top:18px}
    .label{font-size:13px;font-weight:700;color:var(--text-muted);margin-bottom:8px;display:block}
    .input-row{display:flex;gap:12px}
    .input{flex:1;padding:12px 14px;border-radius:10px;border:1px solid var(--border);background:var(--panel-light);color:var(--text)}
    .pills{display:flex;gap:8px;margin-top:8px;flex-wrap:wrap}
    .pill{padding:6px 10px;border-radius:999px;background:var(--panel-light);border:1px solid var(--border);font-size:12px;color:var(--text-muted)}
    .pill.strong{background:linear-gradient(135deg,var(--accent),var(--accent-light));color:#fff;border-color:transparent}
    .shortcuts{display:flex;gap:8px;margin-top:8px}
    .btn-s{padding:8px 12px;border-radius:8px;border:1px solid var(--border);background:var(--panel-light);color:var(--text-muted);font-size:12px}
    .btn-s.active{background:var(--accent);color:#fff;border-color:var(--accent)}
    .btn{width:100%;margin-top:14px;padding:14px;border-radius:12px;border:none;
         background:linear-gradient(135deg,var(--accent),var(--accent-light));color:#fff;font-weight:900}
    .help{margin-top:8px;font-size:11px;color:var(--text-2)}

    /* Progress (mock) */
    .status{display:flex;align-items:center;gap:8px;margin-top:16px}
    .status .dot{width:8px;height:8px;border-radius:50%;background:var(--success);box-shadow:0 0 8px var(--success)}
    .status .text{font-size:12px;color:var(--text-muted)}
    .bar{width:100%;height:8px;border-radius:6px;background:var(--panel-light);overflow:hidden;margin-top:8px}
    .fill{height:100%;width:100%;background:linear-gradient(90deg,var(--accent),var(--accent-light))}

    /* Metrics */
    .metrics{display:grid;grid-template-columns:repeat(4,1fr);gap:20px}
    .metric{text-align:center;border:1px solid var(--border);border-radius:12px;padding:18px;background:var(--panel)}
    .metric .val{font-size:24px;font-weight:800}
    .metric .lbl{font-size:12px;color:var(--text-muted);text-transform:uppercase;letter-spacing:.5px}

    /* Lower grid */
    .lower{display:grid;grid-template-columns:2fr 1fr;gap:24px;min-height:0;flex:1}
    .chart-card{display:flex;flex-direction:column;min-height:0}
    .chart-head{display:flex;justify-content:space-between;align-items:center;margin-bottom:12px}
    .chart-title{font-size:18px;font-weight:800}
    .chart-avg{font-size:28px;font-weight:900;
      background:linear-gradient(135deg,var(--accent),var(--accent-light));-webkit-background-clip:text;-webkit-text-fill-color:transparent}
    .chart-box{flex:1;min-height:0;border:1px solid var(--border);border-radius:12px;padding:12px;background:var(--panel)}
    .chart-svg{width:100%;height:100%}

    .details{display:flex;flex-direction:column;min-height:0}
    .details-head{display:flex;justify-content:space-between;align-items:center}
    .toggle{display:flex;gap:6px;background:var(--panel-light);padding:4px;border-radius:10px}
    .toggle .t{border:none;border-radius:8px;padding:8px 10px;background:transparent;color:var(--text-muted);font-weight:800}
    .toggle .t.active{background:var(--accent);color:#fff}
    .list{margin-top:12px;overflow:auto;flex:1}
    .item{display:flex;justify-content:space-between;align-items:center;background:var(--panel-light);border:1px solid var(--border);border-radius:10px;padding:14px;margin-bottom:8px}
    .i-info{display:flex;flex-direction:column;gap:2px}
    .i-name{font-weight:800}
    .i-sub{font-size:12px;color:var(--text-muted)}
    .i-price{font-weight:900;color:var(--accent)}
    .list::-webkit-scrollbar{width:6px}
    .list::-webkit-scrollbar-thumb{background:var(--accent);border-radius:3px}

    @media (max-width:1024px){
      .dashboard{grid-template-columns:1fr;padding:16px;height:auto;min-height:100dvh}
      .metrics{grid-template-columns:repeat(2,1fr)}
      .lower{grid-template-columns:1fr}
      .chart-box{height:300px}
    }
  </style>
</head>
<body>
  <div class="dashboard">
    <!-- Sidebar -->
    <aside class="sidebar">
      <div class="header">
        <div class="title"><span class="led"></span> Spia Competizione</div>
        <div class="tabs">
          <button class="tab active">App. 1</button>
          <button class="tab">App. 2</button>
        </div>
      </div>

      <div class="card">
        <div class="group">
          <label class="label">📅 Check-in / Check-out</label>
          <div class="input-row">
            <input class="input" type="date" value="2025-09-03" />
            <input class="input" type="date" value="2025-09-07" />
          </div>
          <div class="pills">
            <span class="pill">4 notti</span>
            <span class="pill strong">Agg. 11:48</span>
          </div>
          <div class="shortcuts">
            <button class="btn-s">+3 notti</button>
            <button class="btn-s">+5 notti</button>
            <button class="btn-s active">+7 notti</button>
          </div>
        </div>

        <div class="group">
          <label class="label">🏠 Numero appartamenti da analizzare</label>
          <input class="input" type="number" value="60" />
          <div class="help">Suggerimento: 150–300 è un buon campione.</div>
        </div>

        <button class="btn">🔍 Avvia Analisi Competizione</button>

        <div class="status"><span class="dot"></span><span class="text">Analisi completata con successo</span></div>
        <div class="bar"><div class="fill"></div></div>
      </div>
    </aside>

    <!-- Main -->
    <main class="main">
      <!-- Metrics -->
      <section class="metrics">
        <div class="metric">
          <div class="val">240 €</div>
          <div class="lbl">Prezzo Medio</div>
        </div>
        <div class="metric">
          <div class="val">60</div>
          <div class="lbl">Appartamenti</div>
        </div>
        <div class="metric">
          <div class="val">180–320 €</div>
          <div class="lbl">Range Prezzi</div>
        </div>
        <div class="metric">
          <div class="val">11:48</div>
          <div class="lbl">Ultimo Aggiornamento</div>
        </div>
      </section>

      <!-- Chart + Details -->
      <section class="lower">
        <!-- Chart -->
        <div class="card chart-card">
          <div class="chart-head">
            <h3 class="chart-title">Andamento Prezzi Competizione</h3>
            <div class="chart-avg">240 €</div>
          </div>
          <div class="chart-box">
            <!-- SVG statico (mock) -->
            <svg class="chart-svg" viewBox="0 0 700 260" preserveAspectRatio="none" aria-hidden="true">
              <!-- griglia -->
              <g stroke="rgba(148,163,184,0.12)" stroke-width="1">
                <line x1="0" y1="220" x2="700" y2="220"/><!-- 180€ -->
                <line x1="0" y1="180" x2="700" y2="180"/><!-- 220€ -->
                <line x1="0" y1="140" x2="700" y2="140"/><!-- 260€ -->
                <line x1="0" y1="100" x2="700" y2="100"/><!-- 300€ -->
              </g>
              <!-- area -->
              <defs>
                <linearGradient id="g1" x1="0" y1="0" x2="0" y2="1">
                  <stop offset="0%" stop-color="rgba(99,102,241,0.45)"/>
                  <stop offset="100%" stop-color="rgba(99,102,241,0.05)"/>
                </linearGradient>
              </defs>
              <!-- punti fittizi (media ≈ 240): 228,236,244,251,239,247,236 -->
              <!-- linea -->
              <path d="M20,175
                       C80,165 120,160 120,158
                       S220,150 220,148
                       S320,142 320,138
                       S420,146 420,150
                       S520,144 520,140
                       S620,150 620,155"
                    fill="none" stroke="#4f46e5" stroke-width="3" />
              <!-- area sotto la linea -->
              <path d="M20,175
                       C80,165 120,160 120,158
                       S220,150 220,148
                       S320,142 320,138
                       S420,146 420,150
                       S520,144 520,140
                       S620,150 620,155
                       L620,240 L20,240 Z"
                    fill="url(#g1)" />
              <!-- markers -->
              <g fill="#4f46e5">
                <circle cx="20"  cy="175" r="4"/>
                <circle cx="120" cy="158" r="4"/>
                <circle cx="220" cy="148" r="4"/>
                <circle cx="320" cy="138" r="4"/>
                <circle cx="420" cy="150" r="4"/>
                <circle cx="520" cy="140" r="4"/>
                <circle cx="620" cy="155" r="4"/>
              </g>
            </svg>
          </div>
        </div>

        <!-- Details -->
        <div class="card details">
          <div class="details-head">
            <h3 style="font-size:18px;font-weight:800">Dettagli Appartamenti</h3>
            <div class="toggle">
              <button class="t active">Dettagli</button>
              <button class="t">Storico</button>
            </div>
          </div>

          <div class="list" style="margin-bottom:10px">
            <!-- 12 elementi d'esempio -->
            <div class="item">
              <div class="i-info">
                <div class="i-name">Ca’ Rialto Loft</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">960 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Laguna Suite</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">1.020 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">San Marco View</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">1.040 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Canal Grande Stay</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">920 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Gondola House</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">980 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Dorsoduro Chic</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">1.120 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Arsenale Attic</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">1.000 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Sestiere Elegance</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">890 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Fondamenta Classy</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">1.060 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Biennale Studio</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">940 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Murano Pearl</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">1.080 €</div>
            </div>
            <div class="item">
              <div class="i-info">
                <div class="i-name">Giudecca Horizon</div>
                <div class="i-sub"><a href="#" style="color:var(--accent);text-decoration:none">🔗 Visualizza annuncio</a></div>
              </div>
              <div class="i-price">1.010 €</div>
            </div>
          </div>

          <!-- mini storico (statico) -->
          <h4 style="margin:6px 0 8px;font-size:13px;color:var(--text-muted);font-weight:800">Storico analisi</h4>
          <div class="list" style="max-height:140px">
            <div class="item" style="background:transparent;border-style:dashed">
              <div class="i-info">
                <div class="i-name">27/08 → 03/09 • 58 apt</div>
                <div class="i-sub">Prezzo medio 238 € • Range 175–315 €</div>
              </div>
              <div class="i-price">OK</div>
            </div>
            <div class="item" style="background:transparent;border-style:dashed">
              <div class="i-info">
                <div class="i-name">20/08 → 24/08 • 62 apt</div>
                <div class="i-sub">Prezzo medio 241 € • Range 180–328 €</div>
              </div>
              <div class="i-price">OK</div>
            </div>
            <div class="item" style="background:transparent;border-style:dashed">
              <div class="i-info">
                <div class="i-name">10/08 → 15/08 • 64 apt</div>
                <div class="i-sub">Prezzo medio 243 € • Range 185–334 €</div>
              </div>
              <div class="i-price">OK</div>
            </div>
          </div>
        </div>
      </section>
    </main>
  </div>
</body>
</html>

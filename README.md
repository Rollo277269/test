
<html lang="it">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Spia Competizione — Dashboard Appartamenti</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/flatpickr/4.6.13/flatpickr.min.css">
  <style>
    :root{ --bg:#0a0a0b; --panel:#1a1a1d; --panel-light:#242429; --accent:#4f46e5; --accent-light:#6366f1; --success:#10b981; --warning:#f59e0b; --danger:#ef4444; --text:#f8fafc; --text-muted:#94a3b8; --text-secondary:#64748b; --border:rgba(148, 163, 184, 0.1); --shadow:rgba(0, 0, 0, 0.5); }
    * { box-sizing: border-box; margin: 0; padding: 0; }
    body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; background: var(--bg); color: var(--text); line-height: 1.5; }
    .dashboard { max-width: 1400px; margin: 0 auto; padding: 24px; display: grid; grid-template-columns: 360px 1fr; gap: 24px; min-height: 100vh; }
    .sidebar { display: flex; flex-direction: column; gap: 20px; }
    .main-content { display: flex; flex-direction: column; gap: 24px; }
    .card { background: var(--panel); border-radius: 16px; padding: 24px; border: 1px solid var(--border); box-shadow: 0 4px 20px var(--shadow); }
    .header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 24px; }
    .header h1 { font-size: 24px; font-weight: 700; color: var(--text); }
    .apartment-tabs { display: flex; gap: 8px; background: var(--panel-light); padding: 4px; border-radius: 12px; }
    .tab { padding: 8px 16px; border-radius: 8px; cursor: pointer; font-size: 14px; font-weight: 500; color: var(--text-muted); transition: all 0.2s ease; border: none; background: transparent; }
    .tab.active { background: var(--accent); color: white; box-shadow: 0 2px 8px rgba(79, 70, 229, 0.3); }
    .form-group { margin-bottom: 20px; }
    .form-group label { display: block; font-size: 14px; font-weight: 500; color: var(--text-muted); margin-bottom: 8px; }
    .form-group input, .form-group select { width: 100%; padding: 12px 16px; border-radius: 10px; border: 1px solid var(--border); background: var(--panel-light); color: var(--text); font-size: 14px; transition: all 0.2s ease; }
    .form-group input:focus, .form-group select:focus { outline: none; border-color: var(--accent); box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.1); }
    /* Enhanced Calendar Styling - Fixed */
    .flatpickr-calendar { background: var(--panel) !important; border: 1px solid var(--border) !important; border-radius: 16px !important; box-shadow: 0 12px 48px rgba(0, 0, 0, 0.8) !important; font-family: inherit !important; }
    .flatpickr-calendar.open { z-index: 9999 !important; }
    .flatpickr-months { background: var(--panel-light) !important; border-radius: 16px 16px 0 0 !important; padding: 16px !important; border-bottom: 1px solid var(--border) !important; }
    .flatpickr-month { background: transparent !important; color: var(--text) !important; height: auto !important; }
    .flatpickr-current-month { font-size: 16px !important; font-weight: 600 !important; color: var(--text) !important; line-height: 1 !important; display: flex !important; align-items: center !important; }
    .flatpickr-current-month .flatpickr-monthDropdown-months { background: var(--panel) !important; color: var(--text) !important; border: 1px solid var(--border) !important; border-radius: 8px !important; padding: 4px 8px !important; margin: 0 4px !important; font-weight: 600 !important; }
    .flatpickr-current-month input.cur-year { background: var(--panel) !important; color: var(--text) !important; border: 1px solid var(--border) !important; border-radius: 8px !important; padding: 4px 8px !important; margin: 0 4px !important; font-weight: 600 !important; width: 70px !important; }
    .flatpickr-weekdays { background: var(--panel-light) !important; padding: 12px 0 !important; }
    .flatpickr-weekday { background: var(--panel-light) !important; color: var(--text-muted) !important; font-weight: 600 !important; font-size: 12px !important; line-height: 1 !important; padding: 8px 0 !important; }
    .flatpickr-days { background: var(--panel) !important; padding: 8px !important; }
    .flatpickr-day { background: transparent !important; color: var(--text) !important; border-radius: 12px !important; margin: 2px !important; font-weight: 500 !important; width: 36px !important; height: 36px !important; line-height: 36px !important; font-size: 14px !important; border: none !important; display: flex !important; align-items: center !important; justify-content: center !important; }
    .flatpickr-day:hover { background: var(--panel-light) !important; color: var(--text) !important; border: none !important; }
    .flatpickr-day.selected, .flatpickr-day.startRange, .flatpickr-day.endRange { background: var(--accent) !important; color: white !important; font-weight: 600 !important; border: none !important; box-shadow: 0 2px 8px rgba(79, 70, 229, 0.4) !important; }
    .flatpickr-day.inRange { background: rgba(79, 70, 229, 0.15) !important; color: var(--text) !important; border: none !important; }
    .flatpickr-day.today { background: var(--panel-light) !important; color: var(--accent) !important; font-weight: 600 !important; border: 1px solid var(--accent) !important; }
    .flatpickr-day.today.selected { background: var(--accent) !important; color: white !important; border: none !important; }
    .flatpickr-day.flatpickr-disabled, .flatpickr-day.prevMonthDay, .flatpickr-day.nextMonthDay { color: var(--text-secondary) !important; background: transparent !important; }
    .flatpickr-prev-month, .flatpickr-next-month { color: var(--text-muted) !important; fill: var(--text-muted) !important; padding: 12px !important; border-radius: 8px !important; width: 40px !important; height: 40px !important; }
    .flatpickr-prev-month:hover, .flatpickr-next-month:hover { color: var(--text) !important; fill: var(--text) !important; background: var(--panel-light) !important; }
    .flatpickr-prev-month svg, .flatpickr-next-month svg { width: 16px !important; height: 16px !important; }
    #daterange { font-weight: 500 !important; cursor: pointer !important; transition: all 0.2s ease !important; }
    #daterange:hover { border-color: rgba(79, 70, 229, 0.5) !important; }
    #daterange:focus { border-color: var(--accent) !important; }
    .quick-intervals { display: flex; gap: 8px; margin-top: 8px; }
    .quick-btn { padding: 8px 12px; border-radius: 8px; border: 1px solid var(--border); background: var(--panel-light); color: var(--text-muted); font-size: 12px; cursor: pointer; transition: all 0.2s ease; }
    .quick-btn:hover, .quick-btn.active { background: var(--accent); color: white; border-color: var(--accent); }
    .spy-btn { width: 100%; padding: 14px; border-radius: 10px; border: none; background: linear-gradient(135deg, var(--accent), var(--accent-light)); color: white; font-size: 16px; font-weight: 600; cursor: pointer; transition: all 0.2s ease; margin-bottom: 16px; }
    .spy-btn:hover { transform: translateY(-1px); box-shadow: 0 8px 25px rgba(79, 70, 229, 0.3); }
    .spy-btn:disabled { opacity: 0.6; cursor: not-allowed; transform: none; }
    .demo-btn { width: 100%; padding: 12px; border-radius: 10px; border: 1px solid var(--border); background: var(--panel-light); color: var(--text-muted); font-size: 14px; cursor: pointer; transition: all 0.2s ease; }
    .demo-btn:hover { background: var(--panel); color: var(--text); }
    .progress-section { padding: 20px 0; border-top: 1px solid var(--border); }
    .progress-bar { width: 100%; height: 8px; background: var(--panel-light); border-radius: 4px; overflow: hidden; margin-bottom: 12px; }
    .progress-fill { height: 100%; background: linear-gradient(90deg, var(--accent), var(--accent-light)); width: 0%; transition: width 0.3s ease; }
    .status-text { font-size: 13px; color: var(--text-muted); }
    .metrics-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 20px; margin-bottom: 24px; }
    .metric-card { background: var(--panel); border-radius: 12px; padding: 20px; text-align: center; border: 1px solid var(--border); }
    .metric-value { font-size: 24px; font-weight: 700; color: var(--text); margin-bottom: 4px; }
    .metric-label { font-size: 12px; color: var(--text-muted); text-transform: uppercase; letter-spacing: 0.5px; }
    .chart-section { display: grid; grid-template-columns: 2fr 1fr; gap: 24px; }
    .chart-card { position: relative; min-height: 400px; }
    .chart-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
    .chart-title { font-size: 18px; font-weight: 600; color: var(--text); }
    .avg-price { font-size: 32px; font-weight: 700; background: linear-gradient(135deg, var(--accent), var(--accent-light)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; background-clip: text; }
    .details-card { max-height: 400px; overflow-y: auto; }
    .details-card::-webkit-scrollbar { width: 6px; }
    .details-card::-webkit-scrollbar-track { background: var(--panel-light); border-radius: 3px; }
    .details-card::-webkit-scrollbar-thumb { background: var(--accent); border-radius: 3px; }
    .detail-item { display: flex; justify-content: space-between; align-items: center; padding: 16px; margin-bottom: 8px; background: var(--panel-light); border-radius: 10px; transition: all 0.2s ease; }
    .detail-item:hover { background: var(--panel); transform: translateX(2px); }
    .detail-info { display: flex; flex-direction: column; }
    .detail-id { font-size: 14px; font-weight: 600; color: var(--text); }
    .detail-address { font-size: 12px; color: var(--text-muted); margin-top: 2px; }
    .detail-price { font-size: 16px; font-weight: 600; color: var(--accent); }
    .webhook-info { background: var(--panel-light); border-radius: 8px; padding: 16px; margin-top: 20px; border-left: 4px solid var(--accent); }
    .webhook-info h4 { color: var(--text); margin-bottom: 8px; font-size: 14px; }
    .webhook-info code { background: var(--bg); padding: 8px 12px; border-radius: 6px; font-size: 12px; color: var(--accent-light); word-break: break-all; display: block; }
    @media (max-width: 1024px) { .dashboard { grid-template-columns: 1fr; padding: 16px; } .metrics-grid { grid-template-columns: repeat(2, 1fr); } .chart-section { grid-template-columns: 1fr; } }
    .empty-state { text-align: center; color: var(--text-muted); padding: 40px 20px; }
    .chart-canvas { max-height: 300px; }
  </style>
</head>
<body>
  <div class="dashboard">
    <!-- Sidebar -->
    <div class="sidebar">
      <div class="header">
        <h1>Spia Competizione</h1>
        <div class="apartment-tabs">
          <button class="tab active" data-apartment="1">App. 1</button>
          <button class="tab" data-apartment="2">App. 2</button>
        </div>
      </div>

      <div class="card">
        <div class="form-group">
          <label>📅 Check-in / Check-out</label>
          <input type="text" id="daterange" placeholder="Seleziona date soggiorno" readonly>
        </div>

        <div class="form-group">
          <label>🏠 Numero appartamenti da analizzare</label>
          <input type="number" id="num_apartments" min="1" max="10000" value="200" placeholder="Es. 200">
        </div>

        <button id="spyBtn" class="spy-btn">🔍 Spia Competizione</button>

        <div class="progress-section">
          <div class="progress-bar">
            <div id="progressBar" class="progress-fill"></div>
          </div>
          <div class="status-text">Stato: <span id="statusText">In attesa</span></div>
        </div>
      </div>
    </div>

    <!-- Main Content -->
    <div class="main-content">
      <!-- Metrics Overview -->
      <div class="metrics-grid">
        <div class="metric-card">
          <div class="metric-value" id="avgPrice">...</div>
          <div class="metric-label">Prezzo Medio</div>
        </div>
        <div class="metric-card">
          <div class="metric-value" id="totalApartments">...</div>
          <div class="metric-label">Appartamenti</div>
        </div>
        <div class="metric-card">
          <div class="metric-value" id="priceRange">...</div>
          <div class="metric-label">Range Prezzi</div>
        </div>
        <div class="metric-card">
          <div class="metric-value" id="lastUpdate">...</div>
          <div class="metric-label">Ultimo Aggiornamento</div>
        </div>
      </div>

      <!-- Charts Section -->
      <div class="chart-section">
        <div class="card chart-card">
          <div class="chart-header">
            <h3 class="chart-title">Andamento Prezzi Competizione</h3>
            <div class="avg-price" id="mainAvgPrice">... €</div>
          </div>
          <canvas id="priceChart" class="chart-canvas"></canvas>
        </div>

        <div class="card details-card">
          <div class="chart-header">
            <h3 class="chart-title">Dettagli Appartamenti</h3>
          </div>
          <div id="apartmentsList">
            <div class="empty-state">
              <p>Nessun dato disponibile</p>
              <p style="font-size: 12px; margin-top: 8px;">Avvia un'analisi per vedere i dettagli</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- libs -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/flatpickr/4.6.13/flatpickr.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/flatpickr/4.6.13/l10n/it.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.min.js"></script>
  <!-- Google Identity Services (OAuth) -->
  <script src="https://accounts.google.com/gsi/client" async defer></script>

  <script>
    // -----------------------------
    // Global state / constants
    // -----------------------------
    let selectedInterval = 7;
    let currentApartment = 1;
    let priceChart = null;

    // Google OAuth + Sheets
    const GOOGLE_SHEETS_CONFIG = {
      clientId: '15796170061-495gqatd2f372n0l1en4b6rvibhj2j8a.apps.googleusercontent.com',
      sheetId: 'YOUR_SHEET_ID_HERE' // <-- Sostituisci con l'ID del tuo Google Sheet
    };
    const SHEETS_SCOPE = 'https://www.googleapis.com/auth/spreadsheets.readonly';

    let tokenClient = null;
    let accessToken = null;
    let oauthReady = false;
    let oauthResolve = null;

    // -----------------------------
    // UI: Flatpickr
    // -----------------------------
    const fp = flatpickr('#daterange', {
      mode: 'range',
      dateFormat: 'd/m/Y',
      locale: 'it',
      minDate: 'today',
      defaultDate: [
        new Date(),
        new Date(Date.now() + 7 * 86400000)
      ],
      enable: [
        function(date) {
          return date.getTime() >= new Date().setHours(0,0,0,0);
        }
      ]
    });

    // -----------------------------
    // Tabs switching
    // -----------------------------
    document.querySelectorAll('.tab').forEach(tab => {
      tab.addEventListener('click', () => {
        document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
        tab.classList.add('active');
        currentApartment = parseInt(tab.dataset.apartment, 10);
        resetDashboard();
      });
    });

    // -----------------------------
    // Chart
    // -----------------------------
    function initChart() {
      const ctx = document.getElementById('priceChart').getContext('2d');
      priceChart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: [],
          datasets: [{
            label: 'Prezzo Medio Giornaliero',
            data: [],
            borderColor: '#4f46e5',
            backgroundColor: 'rgba(79, 70, 229, 0.1)',
            fill: true,
            tension: 0.4,
            pointRadius: 4,
            pointHoverRadius: 8,
            borderWidth: 3
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: { legend: { display: false } },
          scales: {
            x: {
              grid: { color: 'rgba(148, 163, 184, 0.1)', drawBorder: false },
              ticks: { color: '#94a3b8', font: { size: 11 } }
            },
            y: {
              grid: { color: 'rgba(148, 163, 184, 0.1)', drawBorder: false },
              ticks: {
                color: '#94a3b8',
                font: { size: 11 },
                callback: function(value) { return value + ' €'; }
              }
            }
          }
        }
      });
    }

    // -----------------------------
    // Reset dashboard
    // -----------------------------
    function resetDashboard() {
      document.getElementById('avgPrice').textContent = '...';
      document.getElementById('totalApartments').textContent = '...';
      document.getElementById('priceRange').textContent = '...';
      document.getElementById('lastUpdate').textContent = '...';
      document.getElementById('mainAvgPrice').textContent = '... €';
      document.getElementById('apartmentsList').innerHTML = `
        <div class="empty-state">
          <p>Nessun dato disponibile</p>
          <p style="font-size: 12px; margin-top: 8px;">Avvia un'analisi per vedere i dettagli</p>
        </div>
      `;
      document.getElementById('statusText').textContent = 'In attesa';
      document.getElementById('progressBar').style.width = '0%';
      if (priceChart) {
        priceChart.data.labels = [];
        priceChart.data.datasets[0].data = [];
        priceChart.update();
      }
    }

    // -----------------------------
    // OAuth helpers
    // -----------------------------
    window.onload = () => {
      initChart();
      resetDashboard();
      initOAuth();
    };

    function initOAuth() {
      tokenClient = google.accounts.oauth2.initTokenClient({
        client_id: GOOGLE_SHEETS_CONFIG.clientId,
        scope: SHEETS_SCOPE,
        callback: (resp) => {
          if (resp && resp.access_token) {
            accessToken = resp.access_token;
            oauthReady = true;
            if (oauthResolve) oauthResolve(accessToken);
          }
        }
      });
    }

    async function ensureOAuth() {
      if (accessToken) return accessToken;
      return new Promise((resolve) => {
        oauthResolve = resolve;
        tokenClient.requestAccessToken({ prompt: 'consent' });
      });
    }

    async function sheetsGet(range) {
      await ensureOAuth();
      const url = `https://sheets.googleapis.com/v4/spreadsheets/${GOOGLE_SHEETS_CONFIG.sheetId}/values/${encodeURIComponent(range)}?valueRenderOption=UNFORMATTED_VALUE`;
      const res = await fetch(url, {
        headers: { Authorization: `Bearer ${accessToken}` }
      });
      if (!res.ok) {
        throw new Error(`HTTP error! status: ${res.status}`);
      }
      return res.json();
    }

    // -----------------------------
    // Timer
    // -----------------------------
    function runProgressTimer(seconds) {
      return new Promise(resolve => {
        if (seconds <= 0) {
          document.getElementById('progressBar').style.width = '100%';
          setTimeout(() => {
            document.getElementById('progressBar').style.width = '0%';
            resolve();
          }, 500);
          return;
        }
        let elapsed = 0;
        const interval = setInterval(() => {
          elapsed++;
          const progress = Math.min(100, (elapsed / seconds) * 100);
          document.getElementById('progressBar').style.width = progress + '%';

          const remaining = Math.max(0, seconds - elapsed);
          const mm = Math.floor(remaining / 60);
          const ss = remaining % 60;
          document.getElementById('statusText').textContent =
            mm > 0 ? `Attesa: ${mm}m ${ss}s rimanenti...` : `Attesa: ${ss}s rimanenti...`;

          if (elapsed >= seconds) {
            clearInterval(interval);
            setTimeout(() => {
              document.getElementById('progressBar').style.width = '0%';
              resolve();
            }, 300);
          }
        }, 1000);
      });
    }

    // -----------------------------
    // Sheets data (mappatura A-G)
    // -----------------------------
    // C (Delay) viene letto subito dopo il POST, per le date scelte; se non trovato, usa il primo valore valido in colonna C.
    async function getDelayFromGoogleSheets(startDate, endDate) {
      const resp = await sheetsGet('A2:G10000');
      const rows = resp.values || [];
      let delay = null;

      // prova a prendere il delay per le date selezionate (E, F)
      for (const r of rows) {
        const e = r[4]; // E Start-date (YYYY-MM-DD)
        const f = r[5]; // F End-date
        const d = r[2]; // C Delay (minuti)
        if (e === startDate && f === endDate && d !== undefined && d !== '') {
          const n = parseInt(d, 10);
          if (!Number.isNaN(n)) { delay = n; break; }
        }
      }
      // fallback: primo valore valido di colonna C
      if (delay === null) {
        for (const r of rows) {
          const d = r[2];
          const n = parseInt(d, 10);
          if (!Number.isNaN(n)) { delay = n; break; }
        }
      }
      return (delay === null ? 2 : delay); // minuti (default 2)
    }

    // Ritorna le righe che combaciano con le date (E/F). Mappatura: A URL, B Price, C Delay, D Avrg.Price, E Start, F End, G ID.
    async function getApartmentDataFromGoogleSheets(startDate, endDate) {
      const resp = await sheetsGet('A2:G10000');
      const rows = resp.values || [];
      const filtered = rows.filter(r => r[4] === startDate && r[5] === endDate);

      return filtered.map(r => ({
        url: r[0] || '',
        price: Number(r[1] || 0),
        delay: Number.parseInt(r[2] || 0, 10),
        avgPriceRow: Number(r[3] || 0),
        start_date: r[4] || '',
        end_date: r[5] || '',
        id: r[6] || '',
        // Non avendo una colonna "address", mostriamo l'URL come info secondaria
        address: r[0] || ''
      }));
    }

    // -----------------------------
    // Real-time/progressive helpers (riusati una volta per popolare la lista)
    // -----------------------------
    let currentAnalysis = { apartments: [], totalReceived: 0, averagePrice: 0, isComplete: false };

    function updateDashboardProgressively() {
      document.getElementById('totalApartments').textContent = currentAnalysis.apartments.length;

      const listContainer = document.getElementById('apartmentsList');
      listContainer.innerHTML = '';
      currentAnalysis.apartments.forEach((apartment, index) => {
        const item = document.createElement('div');
        item.className = 'detail-item';
        item.style.opacity = '0';
        item.style.transform = 'translateY(10px)';
        item.innerHTML = `
          <div class="detail-info">
            <div class="detail-id">#${apartment.id || (index + 1)}</div>
            <div class="detail-address">${apartment.address}</div>
            <div class="detail-address">
              <a href="${apartment.url}" target="_blank" style="color: var(--accent); font-size: 11px; text-decoration: none;">
                🔗 Visualizza annuncio
              </a>
            </div>
          </div>
          <div class="detail-price">${Number(apartment.price || 0).toLocaleString('it-IT')} €</div>
        `;
        listContainer.appendChild(item);
        setTimeout(() => {
          item.style.transition = 'all 0.3s ease';
          item.style.opacity = '1';
          item.style.transform = 'translateY(0)';
        }, index * 100);
      });

      if (priceChart && currentAnalysis.apartments.length > 0) {
        const prices = currentAnalysis.apartments.map(apt => Number(apt.price || 0));
        const avgSoFar = prices.reduce((a, b) => a + b, 0) / prices.length;
        priceChart.data.labels = currentAnalysis.apartments.map((_, i) => `Apt ${i + 1}`);
        priceChart.data.datasets[0].data = prices;
        priceChart.data.datasets[0].label = `Prezzi Ricevuti (${currentAnalysis.apartments.length})`;
        priceChart.update();

        document.getElementById('avgPrice').textContent = `${Math.round(avgSoFar).toLocaleString('it-IT')} €`;
        document.getElementById('mainAvgPrice').textContent = `${Math.round(avgSoFar).toLocaleString('it-IT')} €`;
      }
    }

    function completeDashboardUpdate() {
      const prices = currentAnalysis.apartments.map(apt => Number(apt.price || 0)).filter(n => !Number.isNaN(n));
      const avg = prices.length ? Math.round(prices.reduce((a, b) => a + b, 0) / prices.length) : 0;

      document.getElementById('avgPrice').textContent = `${avg.toLocaleString('it-IT')} €`;
      document.getElementById('mainAvgPrice').textContent = `${avg.toLocaleString('it-IT')} €`;

      if (prices.length > 0) {
        const minPrice = Math.min(...prices);
        const maxPrice = Math.max(...prices);
        document.getElementById('priceRange').textContent = `${minPrice.toLocaleString('it-IT')}-${maxPrice.toLocaleString('it-IT')}€`;
      } else {
        document.getElementById('priceRange').textContent = '0-0€';
      }

      document.getElementById('lastUpdate').textContent =
        new Date().toLocaleTimeString('it-IT', { hour: '2-digit', minute: '2-digit' });

      // Manteniamo la tua resa grafica: serie temporale sintetica basata sulla media finale
      if (priceChart) {
        const labels = [];
        const dataset = [];
        for (let i = selectedInterval - 1; i >= 0; i--) {
          const d = new Date(Date.now() - i * 86400000);
          labels.push(d.toLocaleDateString('it-IT', { day: '2-digit', month: '2-digit' }));
          const variation = (Math.random() - 0.5) * 0.15; // ±7.5%
          dataset.push(Math.max(0, Math.round(avg * (1 + variation))));
        }
        priceChart.data.labels = labels;
        priceChart.data.datasets[0].data = dataset;
        priceChart.data.datasets[0].label = 'Andamento Prezzi Periodo';
        priceChart.update();
      }

      document.getElementById('statusText').textContent = 'Analisi completata con successo';
      document.getElementById('spyBtn').disabled = false;
    }

    // -----------------------------
    // PROCESSO "SPIA COMPETIZIONE"
    // -----------------------------
   document.addEventListener('DOMContentLoaded', () => {
  const spyBtn = document.getElementById('spyBtn');
  if (!spyBtn) return;

  spyBtn.addEventListener('click', async function (event) {
    event.preventDefault();
    try {
      this.disabled = true;
      document.getElementById('statusText').textContent = 'Preparazione analisi...';

      // Reset stato runtime
      currentAnalysis = { apartments: [], totalReceived: 0, averagePrice: 0, isComplete: false };
      resetDashboard();

      // Dati form
      const numApartments = parseInt(document.getElementById('num_apartments').value, 10) || 200;
      const selectedDates = fp.selectedDates;
      let startDate, endDate;
      if (selectedDates && selectedDates.length === 2) {
        startDate = selectedDates[0].toISOString().slice(0, 10);
        endDate   = selectedDates[1].toISOString().slice(0, 10);
      } else {
        const today = new Date();
        const nextWeek = new Date(today.getTime() + 7 * 86400000);
        startDate = today.toISOString().slice(0, 10);
        endDate   = nextWeek.toISOString().slice(0, 10);
        fp.setDate([today, nextWeek]);
      }

      // 1) POST al webhook
      const payload = {
        numero_appartamenti: numApartments,
        start_date: startDate,
        end_date: endDate,
        appartamento_id: currentApartment
      };
      document.getElementById('statusText').textContent = 'Invio richiesta al sistema...';
      try {
        const res = await fetch('https://hook.eu2.make.com/rel45yd0qwuq5p3l1kkf9bcb40ioxpgz', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
          body: JSON.stringify(payload)
        });
        console.log('📡 Webhook status:', res.status);
      } catch (e) {
        console.warn('⚠️ Errore invio webhook (continuo lo stesso):', e);
      }

      // 2) Timer fisso di 240 secondi
      const fixedDelaySeconds = 240;
      document.getElementById('statusText').textContent = `Attesa elaborazione (${fixedDelaySeconds / 60} minuti)...`;
      await runProgressTimer(fixedDelaySeconds);

      // 3) Recupera i risultati finali da Sheets (A-G) solo dopo il timer
      document.getElementById('statusText').textContent = 'Recupero risultati finali...';
      const apartmentsData = await getApartmentDataFromGoogleSheets(startDate, endDate);

      if (!apartmentsData || apartmentsData.length === 0) {
        document.getElementById('statusText').textContent = 'Nessun appartamento trovato per le date selezionate';
        this.disabled = false;
        return;
      }

      // 4) Aggiorna dashboard
      currentAnalysis.apartments = apartmentsData;
      completeDashboardUpdate(); // metriche e grafico finali
    } catch (error) {
      console.error('❌ Errore processo spia:', error);
      document.getElementById('statusText').textContent = 'Errore durante l’analisi';
      this.disabled = false;
    }
  });
});

    // -----------------------------
    // RIMOSSI: response webhook, demo/fallback, listener simulazioni
    // -----------------------------

  </script>
</body>
</html>

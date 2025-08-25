<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dashboard Reminder Pagamenti</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      margin: 0;
      font-family: 'Inter', sans-serif;
      background-color: #0d0d0d;
      color: #f5f5f5;
    }

    header {
      padding: 1rem 2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background-color: #111;
      border-bottom: 1px solid #222;
    }

    header h1 {
      font-size: 1.5rem;
      font-weight: 600;
    }

    .dashboard {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
      gap: 1.5rem;
      padding: 2rem;
    }

    .card {
      background: linear-gradient(145deg, #1a1a1a, #141414);
      border: 1px solid #222;
      border-radius: 16px;
      padding: 1.5rem;
      box-shadow: 0 0 12px rgba(0,0,0,0.6);
      transition: transform 0.2s;
    }

    .card:hover {
      transform: translateY(-4px);
    }

    .card h2 {
      font-size: 0.9rem;
      font-weight: 500;
      color: #aaa;
      margin-bottom: 0.5rem;
    }

    .card p {
      font-size: 1.8rem;
      font-weight: 600;
    }

    .chart-container {
      background: linear-gradient(145deg, #1a1a1a, #141414);
      border: 1px solid #222;
      border-radius: 16px;
      padding: 1.5rem;
      box-shadow: 0 0 12px rgba(0,0,0,0.6);
      margin: 2rem;
    }

    .chart-container h2 {
      margin-bottom: 1rem;
      font-size: 1rem;
      font-weight: 500;
      color: #aaa;
    }
  </style>
</head>
<body>
  <header>
    <h1>Dashboard Reminder Pagamenti</h1>
    <button style="background:#222; color:#fff; border:none; padding:0.5rem 1rem; border-radius:8px; cursor:pointer;">Aggiorna</button>
  </header>

  <main>
    <section class="dashboard">
      <div class="card">
        <h2>Totale Recuperato</h2>
        <p id="totale">€0</p>
      </div>
      <div class="card">
        <h2>Media per Cliente</h2>
        <p id="media">€0</p>
      </div>
      <div class="card">
        <h2>Email Inviate</h2>
        <p id="email">0</p>
      </div>
      <div class="card">
        <h2>Contatti Processati</h2>
        <p id="contatti">0</p>
      </div>
      <div class="card">
        <h2>Tempo Medio Pagamento</h2>
        <p id="tempo">0 giorni</p>
      </div>
      <div class="card">
        <h2>Frequenza di Esecuzione</h2>
        <p>7 / 14 / 21 / 35 giorni</p>
      </div>
    </section>

    <section class="chart-container">
      <h2>Pagamenti Dopo Reminder</h2>
      <canvas id="graficoPagamenti"></canvas>
    </section>
  </main>

  <script>
    // Esempio dati mock, da sostituire con chiamata API al DB
    const datiFatture = [
      { stato: 'Pagato', giorni_passati: 10, giorni_medi_pagamento: 12, ammontare: 500 },
      { stato: 'Pagato', giorni_passati: 20, giorni_medi_pagamento: 18, ammontare: 1200 },
      { stato: 'Scaduto', giorni_passati: 30, giorni_medi_pagamento: null, ammontare: 800 },
      { stato: 'Pagato', giorni_passati: 35, giorni_medi_pagamento: 25, ammontare: 600 },
    ];

    const pagati = datiFatture.filter(f => f.stato === 'Pagato');
    const scaduti = datiFatture.filter(f => f.stato === 'Scaduto');

    const totaleRecuperato = pagati.reduce((sum, f) => sum + f.ammontare, 0);
    const mediaCliente = (pagati.length > 0) ? (totaleRecuperato / pagati.length) : 0;
    const tempoMedio = (pagati.length > 0) ? (pagati.reduce((sum, f) => sum + f.giorni_medi_pagamento, 0) / pagati.length) : 0;

    document.getElementById('totale').textContent = `€${totaleRecuperato.toLocaleString()}`;
    document.getElementById('media').textContent = `€${mediaCliente.toFixed(2)}`;
    document.getElementById('email').textContent = datiFatture.length * 4; // supponiamo 4 reminder
    document.getElementById('contatti').textContent = datiFatture.length;
    document.getElementById('tempo').textContent = `${tempoMedio.toFixed(1)} giorni`;

    const ctx = document.getElementById('graficoPagamenti').getContext('2d');
    new Chart(ctx, {
      type: 'line',
      data: {
        labels: ['7 giorni','14 giorni','21 giorni','35 giorni'],
        datasets: [{
          label: 'Pagamenti ricevuti',
          data: [3, 5, 8, 10],
          borderColor: '#4ade80',
          backgroundColor: 'rgba(74,222,128,0.2)',
          tension: 0.3,
          fill: true
        }]
      },
      options: {
        plugins: { legend: { labels: { color: '#fff' } } },
        scales: {
          x: { ticks: { color: '#aaa' }, grid: { color: '#222' } },
          y: { ticks: { color: '#aaa' }, grid: { color: '#222' } }
        }
      }
    });
  </script>
</body>
</html>

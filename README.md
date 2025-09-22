<!DOCTYPE html>
<html lang="it" data-theme="light">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sales Strategy Chatbot</title>
    <style>
        /* CSS Variables */
        :root {
            --brand-purple: #6D28D9;
            --brand-orange: #FF7A1A;
            --bg: #FFFFFF;
            --surface: #F8FAFC;
            --text: #0F172A;
            --muted: #475569;
            --border: #E2E8F0;
            --ok: #10B981;
            --warn: #F59E0B;
            --error: #EF4444;
            --radius-lg: 14px;
            --radius-sm: 8px;
            --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
            --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
            --gap: 16px;
            --pad: 16px;
            --pad-lg: 24px;
        }

        [data-theme="dark"] {
            --bg: #0B0C10;
            --surface: #111318;
            --text: #E5E7EB;
            --muted: #9CA3AF;
            --border: #1F2937;
        }

        /* Reset & Base */
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; background: var(--bg); color: var(--text); line-height: 1.5; }
        button, input { font: inherit; color: inherit; }
        button { cursor: pointer; }
        a { color: var(--brand-purple); text-decoration: none; }
        .focus-visible { outline: 2px solid var(--brand-purple); outline-offset: 2px; }

        /* Layout */
        .app { display: grid; grid-template-rows: auto 1fr; height: 100vh; }
        header { position: sticky; top: 0; background: var(--surface); border-bottom: 1px solid var(--border); padding: var(--pad); display: flex; align-items: center; justify-content: space-between; z-index: 10; }
        header h1 { font-size: 1.25rem; }
        .theme-toggle { background: none; border: none; font-size: 1.5rem; }
        .status-chip { display: inline-flex; align-items: center; gap: 4px; padding: 4px 8px; border-radius: var(--radius-sm); background: var(--surface); border: 1px solid var(--border); }
        .status-dot { width: 8px; height: 8px; border-radius: 50%; background: var(--ok); }
        .status-thinking .status-dot { background: var(--warn); }
        .status-error .status-dot { background: var(--error); }

        .main-grid { display: grid; grid-template-columns: 1fr 2fr 1fr; gap: var(--gap); padding: var(--pad-lg); }
        @media (max-width: 1024px) { .main-grid { grid-template-columns: 1fr; } }

        aside.left { background: var(--surface); border-right: 1px solid var(--border); padding: var(--pad); display: flex; flex-direction: column; justify-content: space-between; }
        aside.left.collapsed { width: 60px; overflow: hidden; }
        .logo { font-weight: bold; margin-bottom: var(--pad); }
        nav ul { list-style: none; }
        nav li { margin-bottom: 8px; }
        .footer { font-size: 0.875rem; color: var(--muted); }

        .center { display: flex; flex-direction: column; gap: var(--gap); }
        .card { background: var(--surface); border: 1px solid var(--border); border-radius: var(--radius-lg); padding: var(--pad); box-shadow: var(--shadow-sm); }
        .input-card form { display: flex; flex-direction: column; gap: var(--gap); }
        .field { display: flex; flex-direction: column; }
        .field label { font-size: 0.875rem; margin-bottom: 4px; }
        .field input { padding: 8px; border: 1px solid var(--border); border-radius: var(--radius-sm); }
        .field input.invalid { border-color: var(--error); }
        .error-text { font-size: 0.75rem; color: var(--error); margin-top: 4px; }
        .disclosure { margin-top: var(--gap); }
        .disclosure summary { cursor: pointer; font-weight: bold; }
        .cta { background: var(--brand-purple); color: white; border: none; padding: 12px; border-radius: var(--radius-sm); display: flex; align-items: center; gap: 8px; }
        .example-link { color: var(--brand-purple); cursor: pointer; }

        .output-card { position: relative; min-height: 300px; }
        .tabs { display: flex; border-bottom: 1px solid var(--border); }
        .tab { padding: 8px 16px; cursor: pointer; border-bottom: 2px solid transparent; }
        .tab.active { border-bottom-color: var(--brand-purple); font-weight: bold; }
        .tab-content { display: none; padding: var(--pad); }
        .tab-content.active { display: block; }
        .actions { display: flex; gap: 8px; margin-bottom: var(--pad); }
        .actions button { background: none; border: 1px solid var(--border); padding: 4px 8px; border-radius: var(--radius-sm); }

        .right { background: var(--surface); border-left: 1px solid var(--border); padding: var(--pad); }

        /* Loader */
        .loader { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); width: 64px; height: 64px; }
        .loader svg { width: 100%; height: 100%; animation: rotate 1.2s linear infinite; }
        @keyframes rotate { to { transform: rotate(360deg); } }
        .loader path { stroke: url(#gradient); stroke-width: 4; stroke-linecap: round; stroke-dasharray: 150 200; stroke-dashoffset: 0; filter: drop-shadow(0 0 4px var(--brand-orange)); }
        /* Optional dash animation: animation: dash 1.5s ease-in-out infinite; */
        /* @keyframes dash { 0% { stroke-dashoffset: 150; } 50% { stroke-dashoffset: 75; } 100% { stroke-dashoffset: 0; } } */

        /* Debug Console */
        .debug { margin-top: var(--gap); border-top: 1px solid var(--border); padding-top: var(--pad); font-size: 0.875rem; color: var(--muted); }
        .debug summary { cursor: pointer; }

        /* Toast */
        .toast { position: fixed; bottom: 20px; right: 20px; background: var(--surface); border: 1px solid var(--border); padding: 12px; border-radius: var(--radius-sm); box-shadow: var(--shadow-md); max-width: 300px; }

        /* Modal */
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); align-items: center; justify-content: center; }
        .modal-content { background: var(--bg); padding: var(--pad-lg); border-radius: var(--radius-lg); max-width: 600px; width: 90%; }
        .modal button { margin-top: var(--gap); }

        /* Chips, Progress, etc. */
        .chip { display: inline-block; padding: 4px 8px; background: var(--brand-orange); color: white; border-radius: var(--radius-sm); font-size: 0.875rem; }
        .progress { height: 8px; background: var(--border); border-radius: var(--radius-sm); overflow: hidden; }
        .progress-bar { height: 100%; background: var(--brand-purple); }
        .bullet-list { list-style: disc; padding-left: 20px; }
        .severity-high { color: var(--error); }
        .severity-med { color: var(--warn); }
        .severity-low { color: var(--ok); }
    </style>
</head>
<body>
    <div class="app">
        <header>
            <h1>Sales Strategy Chatbot</h1>
            <div>
                <button class="theme-toggle" aria-label="Toggle theme">🌙</button>
                <span class="status-chip" id="status"><span class="status-dot"></span> Ready</span>
            </div>
        </header>
        <div class="main-grid">
            <aside class="left" data-testid="sidebar">
                <div class="logo">SalesGPT</div>
                <nav>
                    <ul>
                        <li>Home</li>
                        <li>Nuova Strategia</li>
                        <li>Cronologia</li>
                        <li>Template</li>
                        <li>Impostazioni</li>
                    </ul>
                </nav>
                <div class="footer">Privacy</div>
            </aside>
            <section class="center">
                <div class="card input-card">
                    <form id="input-form">
                        <div class="field">
                            <label for="full_name">Nome e Cognome *</label>
                            <input type="text" id="full_name" placeholder="Es. ‘Elena Rossi’" required aria-required="true">
                            <div class="error-text" id="full_name_error"></div>
                        </div>
                        <details class="disclosure">
                            <summary>Dettagli avanzati</summary>
                            <div class="field"><label for="role">Role</label><input type="text" id="role"></div>
                            <div class="field"><label for="company">Company</label><input type="text" id="company"></div>
                            <div class="field"><label for="industry">Industry</label><input type="text" id="industry"></div>
                            <div class="field"><label for="linkedin">LinkedIn URL</label><input type="text" id="linkedin"></div>
                            <div class="field"><label for="website">Website</label><input type="text" id="website"></div>
                            <div class="field"><label for="location">Location</label><input type="text" id="location"></div>
                            <div class="field"><label for="primary_goal">Primary Goal</label><input type="text" id="primary_goal"></div>
                            <div class="field"><label for="notes">Notes</label><input type="text" id="notes"></div>
                        </details>
                        <button type="submit" class="cta" disabled>Genera strategia 🚀</button>
                        <span class="example-link" id="use-example">Usa dati d’esempio</span>
                    </form>
                </div>
                <div class="card output-card" id="output">
                    <div class="loader" id="loader" style="display: none;">
                        <svg viewBox="0 0 100 100">
                            <defs>
                                <linearGradient id="gradient" x1="0%" y1="0%" x2="100%" y2="0%">
                                    <stop offset="0%" stop-color="var(--brand-purple)" />
                                    <stop offset="100%" stop-color="var(--brand-orange)" />
                                </linearGradient>
                            </defs>
                            <path d="M50 10 A40 40 0 1 1 50 90 L60 80 L50 90 L40 80 Z" fill="none" />
                        </svg>
                    </div>
                    <div class="tabs">
                        <div class="tab active" data-tab="background">Background</div>
                        <div class="tab" data-tab="pain_points">Pain Points</div>
                        <div class="tab" data-tab="strategy">Sales Strategy</div>
                    </div>
                    <div class="actions">
                        <button id="copy">Copy</button>
                        <button id="export">Export</button>
                        <button id="refresh">Refresh</button>
                    </div>
                    <div class="tab-content active" id="background-content"></div>
                    <div class="tab-content" id="pain_points-content"></div>
                    <div class="tab-content" id="strategy-content"></div>
                    <details class="debug">
                        <summary>Console Sviluppatore</summary>
                        <pre id="debug-log"></pre>
                    </details>
                </div>
            </section>
            <aside class="right" id="insights"></aside>
        </div>
    </div>
    <div class="toast" id="toast" style="display: none;"></div>
    <div class="modal" id="export-modal">
        <div class="modal-content">
            <h2>Export</h2>
            <button id="export-md">Markdown (.md)</button>
            <button id="export-pdf">PDF (mock)</button>
            <button id="export-json">JSON</button>
            <button class="close-modal">Chiudi</button>
        </div>
    </div>

    <script>
        // Init UI
        const form = document.getElementById('input-form');
        const fullNameInput = document.getElementById('full_name');
        const cta = form.querySelector('.cta');
        const loader = document.getElementById('loader');
        const output = document.getElementById('output');
        const status = document.getElementById('status');
        const debugLog = document.getElementById('debug-log');
        const toast = document.getElementById('toast');
        const tabs = document.querySelectorAll('.tab');
        const tabContents = document.querySelectorAll('.tab-content');
        const insights = document.getElementById('insights');
        const exportModal = document.getElementById('export-modal');
        let responseData = null;
        let startTime = null;
        let isLoading = false;

        // Theme Toggle
        document.querySelector('.theme-toggle').addEventListener('click', () => {
            document.documentElement.dataset.theme = document.documentElement.dataset.theme === 'light' ? 'dark' : 'light';
        });

        // Tabs Switching
        tabs.forEach(tab => {
            tab.addEventListener('click', () => {
                tabs.forEach(t => t.classList.remove('active'));
                tab.classList.add('active');
                tabContents.forEach(c => c.classList.remove('active'));
                document.getElementById(`${tab.dataset.tab}-content`).classList.add('active');
            });
        });

        // Sanitize Input
        function sanitize(str) {
            return str.trim().replace(/\s+/g, ' ');
        }

        // Show Toast
        function showToast(message) {
            toast.textContent = message;
            toast.style.display = 'block';
            setTimeout(() => toast.style.display = 'none', 5000);
        }

        // Update Status
        function updateStatus(text, className) {
            status.className = `status-chip ${className || ''}`;
            status.querySelector('span:last-child').textContent = text;
        }

        // Log Debug
        function logDebug(message) {
            debugLog.textContent += `${new Date().toISOString()} - ${message}\n`;
        }

        // Fetch Logic
        async function fetchStrategy(fullName, retry = 0) {
            if (isLoading) return;
            isLoading = true;
            cta.disabled = true;
            loader.style.display = 'block';
            updateStatus('Thinking…', 'status-thinking');
            startTime = Date.now();

            const controller = new AbortController();
            const timeoutId = setTimeout(() => controller.abort(), 20000);

            const body = {
                full_name: sanitize(fullName),
                source: 'sales-strategy-ui',
                ts: new Date().toISOString()
            };

            logDebug(`Request: ${JSON.stringify(body)}`);

            try {
                const response = await fetch('https://rolandoai.app.n8n.cloud/webhook-test/a7d20785-33c6-4181-b058-abfbffbc2c97', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(body),
                    signal: controller.signal
                });
                clearTimeout(timeoutId);

                const elapsed = Date.now() - startTime;
                logDebug(`Status: ${response.status}, Elapsed: ${elapsed}ms`);

                if (!response.ok) throw new Error(`HTTP ${response.status}`);

                const contentType = response.headers.get('Content-Type');
                let data;
                if (contentType && contentType.includes('application/json')) {
                    data = await response.json();
                } else {
                    data = await response.text();
                }

                handleResponse(data);
            } catch (error) {
                logDebug(`Error: ${error.message}`);
                if ((error.name === 'AbortError' || error.message.includes('network')) && retry < 1) {
                    setTimeout(() => fetchStrategy(fullName, retry + 1), 1500);
                    return;
                }
                handleError(error);
            } finally {
                isLoading = false;
                cta.disabled = false;
                loader.style.display = 'none';
            }
        }

        // Handle Response
        function handleResponse(data) {
            responseData = data;
            updateStatus('Ready', '');
            output.style.opacity = 0;
            setTimeout(() => {
                if (typeof data === 'object' && data.output) {
                    renderJSON(data.output);
                } else {
                    renderText(data);
                }
                output.style.opacity = 1;
            }, 200);
        }

        // Render JSON
        function renderJSON(output) {
            const { background, pain_points, strategy, kpi_insights } = output;

            // Background
            const bgContent = document.getElementById('background-content');
            bgContent.innerHTML = '';
            if (background) {
                const snapshot = document.createElement('div');
                snapshot.innerHTML = `<span class="chip">${background.role || 'N/D'}</span> <span class="chip">${background.company || 'N/D'}</span> <span class="chip">${background.industry || 'N/D'}</span>`;
                bgContent.appendChild(snapshot);
                const ul = document.createElement('ul');
                ul.className = 'bullet-list';
                (background.highlights || []).forEach(h => {
                    const li = document.createElement('li');
                    li.textContent = h;
                    ul.appendChild(li);
                });
                bgContent.appendChild(ul);
                const conf = document.createElement('div');
                conf.innerHTML = `Confidence: <div class="progress"><div class="progress-bar" style="width: ${ (background.confidence || 0) * 100 }%"></div></div>`;
                bgContent.appendChild(conf);
            } else {
                bgContent.textContent = 'N/D';
            }

            // Pain Points
            const ppContent = document.getElementById('pain_points-content');
            ppContent.innerHTML = '';
            if (pain_points) {
                pain_points.forEach(p => {
                    const div = document.createElement('div');
                    div.innerHTML = `<strong>${p.category || 'N/D'}: ${p.title || 'N/D'}</strong> <span class="severity-${(p.severity || '').toLowerCase()}">${p.severity || 'N/D'}</span><p>${p.context || 'N/D'}</p>`;
                    ppContent.appendChild(div);
                });
            } else {
                ppContent.textContent = 'N/D';
            }

            // Strategy
            const stratContent = document.getElementById('strategy-content');
            stratContent.innerHTML = '';
            if (strategy) {
                const blocks = ['targeted_angle', 'key_message', 'channels', 'cadence', 'cta', 'objections_rebuttals'];
                blocks.forEach(block => {
                    const div = document.createElement('div');
                    div.innerHTML = `<h3>${block.replace('_', ' ').toUpperCase()}</h3>`;
                    const val = strategy[block];
                    if (Array.isArray(val)) {
                        const ul = document.createElement('ul');
                        val.forEach(item => {
                            const li = document.createElement('li');
                            li.textContent = typeof item === 'object' ? JSON.stringify(item) : item;
                            ul.appendChild(li);
                        });
                        div.appendChild(ul);
                    } else if (typeof val === 'object') {
                        div.innerHTML += JSON.stringify(val);
                    } else {
                        div.innerHTML += val || 'N/D';
                    }
                    stratContent.appendChild(div);
                });
            } else {
                stratContent.textContent = 'N/D';
            }

            // Insights
            insights.innerHTML = '';
            if (kpi_insights) {
                const h2 = document.createElement('h2');
                h2.textContent = 'Insights & KPI';
                insights.appendChild(h2);
                const scores = document.createElement('div');
                scores.innerHTML = `ICP Fit: ${kpi_insights.icp_fit || 'N/D'}%<br>Urgency: ${kpi_insights.urgency || 'N/D'}%<br>Deal Size: ${kpi_insights.deal_size_est || 'N/D'}<br>Velocity: ${kpi_insights.velocity_est || 'N/D'}`;
                insights.appendChild(scores);
                const tags = document.createElement('div');
                (kpi_insights.tags || []).forEach(t => {
                    const chip = document.createElement('span');
                    chip.className = 'chip';
                    chip.textContent = t;
                    tags.appendChild(chip);
                });
                insights.appendChild(tags);
            }
        }

        // Render Text Fallback
        function renderText(text) {
            const bgContent = document.getElementById('background-content');
            bgContent.innerHTML = `<pre>${text}</pre>`;
            document.querySelector('.tab[data-tab="pain_points"]').style.display = 'none';
            document.querySelector('.tab[data-tab="strategy"]').style.display = 'none';
        }

        // Handle Error
        function handleError(error) {
            updateStatus('Error', 'status-error');
            showToast(error.message);
            const banner = document.createElement('div');
            banner.style.background = var(--error);
            banner.style.color = 'white';
            banner.style.padding = '12px';
            banner.style.marginBottom = 'var(--gap)';
            banner.innerHTML = `${error.message}<br><button onclick="retry()">Riprova</button> <button onclick="copyCurl()">Copia cURL</button>`;
            if (error.message.includes('CORS')) {
                banner.innerHTML += '<p>Il browser ha bloccato la richiesta per motivi di sicurezza CORS. Prova da terminale con cURL.</p>';
            }
            output.insertBefore(banner, output.firstChild);
        }

        // Retry
        function retry() {
            fetchStrategy(fullNameInput.value);
        }

        // Copy cURL
        function copyCurl() {
            const curl = `curl -X POST 'https://rolandoai.app.n8n.cloud/webhook-test/a7d20785-33c6-4181-b058-abfbffbc2c97' -H 'Content-Type: application/json' -d '${JSON.stringify({ full_name: sanitize(fullNameInput.value), source: "sales-strategy-ui", ts: new Date().toISOString() })}'`;
            navigator.clipboard.writeText(curl);
            showToast('cURL copiato!');
        }

        // Form Submit
        let debounceTimer;
        form.addEventListener('submit', e => {
            e.preventDefault();
            clearTimeout(debounceTimer);
            debounceTimer = setTimeout(() => {
                if (!fullNameInput.value.trim()) {
                    fullNameInput.classList.add('invalid');
                    document.getElementById('full_name_error').textContent = 'Campo obbligatorio';
                    return;
                }
                fullNameInput.classList.remove('invalid');
                document.getElementById('full_name_error').textContent = '';
                fetchStrategy(fullNameInput.value);
            }, 400);
        });

        // Enter Submit
        fullNameInput.addEventListener('keydown', e => {
            if (e.key === 'Enter') form.dispatchEvent(new Event('submit'));
        });

        // Example Data
        document.getElementById('use-example').addEventListener('click', () => {
            fullNameInput.value = 'Elena Rossi';
            document.getElementById('role').value = 'Head of Growth';
            document.getElementById('company').value = 'AcmeCloud';
            document.getElementById('industry').value = 'SaaS B2B';
            document.getElementById('primary_goal').value = 'Aumentare MRR trimestre';
            fetchStrategy('Elena Rossi');
        });

        // Refresh
        document.getElementById('refresh').addEventListener('click', () => {
            if (fullNameInput.value) fetchStrategy(fullNameInput.value);
        });

        // Copy
        document.getElementById('copy').addEventListener('click', () => {
            navigator.clipboard.writeText(JSON.stringify(responseData));
            showToast('Copiato!');
        });

        // Export Modal
        document.getElementById('export').addEventListener('click', () => {
            exportModal.style.display = 'flex';
        });
        document.querySelector('.close-modal').addEventListener('click', () => {
            exportModal.style.display = 'none';
        });

        // Export MD
        document.getElementById('export-md').addEventListener('click', () => {
            const md = `# Sales Strategy\n\n## Background\n...\n## Pain Points\n...\n## Strategy\n...`; // Compose from data
            download('strategy.md', md);
        });

        // Export PDF Mock
        document.getElementById('export-pdf').addEventListener('click', () => {
            download('strategy.pdf', 'Mock PDF content');
        });

        // Export JSON
        document.getElementById('export-json').addEventListener('click', () => {
            download('strategy.json', JSON.stringify(responseData));
        });

        function download(filename, text) {
            const el = document.createElement('a');
            el.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(text));
            el.setAttribute('download', filename);
            el.click();
        }

        // Esc Close Modal
        document.addEventListener('keydown', e => {
            if (e.key === 'Escape') exportModal.style.display = 'none';
        });

        // Enable CTA on input
        fullNameInput.addEventListener('input', () => {
            cta.disabled = !fullNameInput.value.trim();
        });
    </script>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Customer Service KPI Dashboard</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />

  <!-- Libraries from CDN (GitHub Pages compatible) -->
  <script src="https://cdn.jsdelivr.net/npm/papaparse@5.4.1/papaparse.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>

  <style>
    :root {
      --bg: #f5f7fb;
      --card-bg: #ffffff;
      --accent: #2563eb;
      --accent-soft: #dbeafe;
      --text-main: #111827;
      --text-muted: #6b7280;
      --border-subtle: #e5e7eb;
    }

    * {
      box-sizing: border-box;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    }

    body {
      margin: 0;
      background: var(--bg);
      color: var(--text-main);
    }

    header {
      padding: 1.5rem 2rem 0.75rem;
      border-bottom: 1px solid var(--border-subtle);
      background: #ffffffaa;
      backdrop-filter: blur(10px);
      position: sticky;
      top: 0;
      z-index: 20;
    }

    header h1 {
      margin: 0;
      font-size: 1.4rem;
      font-weight: 700;
    }

    header p {
      margin: 0.25rem 0 0;
      font-size: 0.9rem;
      color: var(--text-muted);
    }

    .layout {
      padding: 1.5rem 2rem 2.5rem;
      max-width: 1200px;
      margin: 0 auto;
    }

    .panel {
      background: var(--card-bg);
      border-radius: 0.9rem;
      padding: 1rem 1.25rem;
      box-shadow: 0 10px 25px rgba(15, 23, 42, 0.05);
      border: 1px solid var(--border-subtle);
      margin-bottom: 1.5rem;
    }

    .panel h2 {
      margin: 0 0 0.4rem;
      font-size: 1rem;
    }

    .panel p.desc {
      margin: 0;
      font-size: 0.8rem;
      color: var(--text-muted);
    }

    .file-grid {
      display: grid;
      gap: 0.75rem;
      margin-top: 0.75rem;
    }

    @media (min-width: 640px) {
      .file-grid {
        grid-template-columns: repeat(3, minmax(0, 1fr));
      }
    }

    .file-input-group {
      display: flex;
      flex-direction: column;
      gap: 0.25rem;
      font-size: 0.8rem;
    }

    .file-input-group label {
      font-weight: 600;
    }

    .file-input-group span {
      color: var(--text-muted);
    }

    input[type="file"] {
      font-size: 0.8rem;
    }

    .badge {
      display: inline-flex;
      align-items: center;
      gap: 0.25rem;
      padding: 0.15rem 0.5rem;
      border-radius: 999px;
      background: var(--accent-soft);
      color: var(--accent);
      font-size: 0.7rem;
      font-weight: 600;
      text-transform: uppercase;
      letter-spacing: 0.03em;
    }

    .options-row {
      margin-top: 0.75rem;
      display: flex;
      flex-wrap: wrap;
      gap: 0.75rem;
      align-items: center;
      font-size: 0.8rem;
    }

    select, button {
      padding: 0.35rem 0.7rem;
      border-radius: 999px;
      border: 1px solid var(--border-subtle);
      background: #fff;
      font-size: 0.8rem;
    }

    button {
      background: var(--accent);
      color: #fff;
      border-color: var(--accent);
      cursor: pointer;
    }

    button:disabled {
      opacity: 0.5;
      cursor: default;
    }

    .kpi-grid {
      display: grid;
      gap: 0.75rem;
      margin-top: 1rem;
    }

    @media (min-width: 768px) {
      .kpi-grid {
        grid-template-columns: repeat(4, minmax(0, 1fr));
      }
    }

    .kpi-card {
      background: #f9fafb;
      border-radius: 0.8rem;
      padding: 0.7rem 0.8rem;
      border: 1px solid #e5e7eb;
      display: flex;
      flex-direction: column;
      gap: 0.3rem;
    }

    .kpi-label {
      font-size: 0.75rem;
      color: var(--text-muted);
      text-transform: uppercase;
      letter-spacing: 0.04em;
      font-weight: 600;
    }

    .kpi-value {
      font-size: 1.1rem;
      font-weight: 700;
    }

    .kpi-sub {
      font-size: 0.7rem;
      color: var(--text-muted);
    }

    .grid-2 {
      display: grid;
      gap: 1rem;
      margin-top: 1rem;
    }

    @media (min-width: 900px) {
      .grid-2 {
        grid-template-columns: 3fr 2fr;
      }
    }

    .chart-card {
      background: #f9fafb;
      border-radius: 0.8rem;
      padding: 0.75rem 0.9rem;
      border: 1px solid #e5e7eb;
    }

    .chart-card h3 {
      margin: 0 0 0.25rem;
      font-size: 0.9rem;
    }

    .chart-card p {
      margin: 0 0 0.5rem;
      font-size: 0.75rem;
      color: var(--text-muted);
    }

    canvas {
      max-width: 100%;
    }

    .table-wrapper {
      margin-top: 0.75rem;
      max-height: 260px;
      overflow: auto;
      border-radius: 0.6rem;
      border: 1px solid #e5e7eb;
      background: #fff;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      font-size: 0.75rem;
    }

    th, td {
      padding: 0.35rem 0.45rem;
      border-bottom: 1px solid #f3f4f6;
      text-align: left;
    }

    th {
      position: sticky;
      top: 0;
      background: #f9fafb;
      z-index: 5;
      font-weight: 600;
      color: #4b5563;
    }

    tbody tr:nth-child(even) {
      background: #f9fafb;
    }

    .pill {
      display: inline-flex;
      padding: 0.1rem 0.45rem;
      border-radius: 999px;
      border: 1px solid #e5e7eb;
      background: #f9fafb;
      font-size: 0.7rem;
      color: #4b5563;
    }

    .status-closed {
      background: #dcfce7;
      border-color: #bbf7d0;
      color: #166534;
    }

    .footer-note {
      margin-top: 0.75rem;
      font-size: 0.7rem;
      color: var(--text-muted);
    }
  </style>
</head>
<body>
  <header>
    <div style="display:flex;justify-content:space-between;align-items:flex-start;gap:0.75rem;">
      <div>
        <h1>Customer Service KPI Dashboard</h1>
        <p>Upload your Zoho Desk CSV exports to see resolution time, incoming volume, and status distribution.</p>
      </div>
      <span class="badge">Zoho Desk · GitHub Pages Ready</span>
    </div>
  </header>

  <main class="layout">
    <!-- FILE INPUTS -->
    <section class="panel">
      <h2>1. Upload data</h2>
      <p class="desc">
        Use Zoho Desk exports with the following headers:<br>
        <strong>Incoming:</strong> TICKET ID, TICKET OWNER, RECEIVED TIME, STATUS, CHANNEL<br>
        <strong>Resolution:</strong> TICKET ID, AGENT RESOLVED TIME, AGENT RESOLUTION TIME, STATUS, CHANNEL
      </p>

      <div class="file-grid">
        <div class="file-input-group">
          <label for="incomingFile">Incoming tickets CSV</label>
          <span>Zoho: report with TICKET ID, TICKET OWNER, RECEIVED TIME, STATUS, CHANNEL</span>
          <input type="file" id="incomingFile" accept=".csv" />
        </div>
        <div class="file-input-group">
          <label for="resolutionFile">Resolution CSV</label>
          <span>Zoho: Resolution Time report with AGENT RESOLUTION TIME & STATUS</span>
          <input type="file" id="resolutionFile" accept=".csv" />
        </div>
        <div class="file-input-group">
          <label for="responseFile">Response CSV (optional)</label>
          <span>Zoho: Response Time report (loaded for future use)</span>
          <input type="file" id="responseFile" accept=".csv" />
        </div>
      </div>

      <div class="options-row">
        <div>
          <label for="yearSelect"><strong>Report Year:</strong></label>
          <select id="yearSelect"></select>
        </div>
        <button id="recalcBtn" disabled>Recalculate KPIs</button>
        <span id="statusText" style="color:var(--text-muted);"></span>
      </div>
    </section>

    <!-- KPIs -->
    <section class="panel">
      <h2>2. KPI overview</h2>
      <p class="desc">High-level metrics calculated dynamically from your Zoho CSVs.</p>

      <div class="kpi-grid">
        <div class="kpi-card">
          <div class="kpi-label">Total tickets (year)</div>
          <div class="kpi-value" id="kpiTotalTickets">–</div>
          <div class="kpi-sub" id="kpiTotalTicketsSub">Upload data to calculate</div>
        </div>

        <div class="kpi-card">
          <div class="kpi-label">Avg resolution time</div>
          <div class="kpi-value" id="kpiAvgResolution">–</div>
          <div class="kpi-sub">Closed / Resolved tickets · AGENT RESOLUTION TIME</div>
        </div>

        <div class="kpi-card">
          <div class="kpi-label">Avg tickets / month</div>
          <div class="kpi-value" id="kpiAvgPerMonth">–</div>
          <div class="kpi-sub" id="kpiAvgPerMonthSub">Based on months with activity</div>
        </div>

        <div class="kpi-card">
          <div class="kpi-label">Distinct agents</div>
          <div class="kpi-value" id="kpiAgents">–</div>
          <div class="kpi-sub" id="kpiAgentsSub">From TICKET OWNER</div>
        </div>
      </div>
    </section>

    <!-- CHARTS & TABLE -->
    <section class="panel">
      <h2>3. Charts & breakdown</h2>
      <p class="desc">Visuals for monthly volume and status distribution.</p>

      <div class="grid-2">
        <div class="chart-card">
          <h3>Monthly incoming tickets</h3>
          <p>Counts the number of tickets created per month in the selected year (RECEIVED TIME).</p>
          <canvas id="monthlyChart" height="170"></canvas>
        </div>

        <div class="chart-card">
          <h3>Status distribution</h3>
          <p>Percentage of tickets per status (all statuses from Incoming CSV).</p>
          <canvas id="statusChart" height="170"></canvas>
        </div>
      </div>

      <div class="chart-card" style="margin-top:1rem;">
        <h3>Ticket summary (pivot-style)</h3>
        <p>Breakdown of tickets per status and per agent (TICKET OWNER) for the selected year.</p>

        <div class="table-wrapper">
          <table id="summaryTable">
            <thead>
              <tr>
                <th>Status</th>
                <th>Agent (Ticket Owner)</th>
                <th>Tickets</th>
              </tr>
            </thead>
            <tbody>
              <!-- Filled by JS -->
            </tbody>
          </table>
        </div>

        <div class="footer-note">
          This acts like a pivot table: grouped by <strong>STATUS</strong> and <strong>TICKET OWNER</strong> from Incoming.
        </div>
      </div>
    </section>

    <!-- QUALITY FRAMEWORK PLACEHOLDER -->
    <section class="panel">
      <h2>4. Quality framework notes</h2>
      <p class="desc">
        Use this space as a digital “sheet” for your QA framework & SOP notes (mirroring your Excel quality framework sheet).
      </p>
      <div style="margin-top:0.5rem;">
        <textarea id="qualityNotes" placeholder="Example:&#10;- Define what 'Resolved' means (technical vs. general inquiries)&#10;- QA sampling plan per agent&#10;- Escalation vs. refund guidelines"
          style="width:100%;min-height:140px;border-radius:0.7rem;border:1px solid var(--border-subtle);padding:0.6rem;font-size:0.8rem;resize:vertical;"></textarea>
      </div>
      <p class="footer-note">
        Tip: copy/paste these notes into your internal docs or Excel QA sheet so everything stays in sync.
      </p>
    </section>
  </main>

  <script>
    // --------- State ---------
    let incomingData = [];
    let resolutionData = [];
    let responseData = [];

    let monthlyChart = null;
    let statusChart = null;

    const MONTH_LABELS = ["Jan","Feb","Mar","Apr","May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"];

    const yearSelect = document.getElementById('yearSelect');
    const recalcBtn = document.getElementById('recalcBtn');
    const statusText = document.getElementById('statusText');

    const kpiTotalTickets = document.getElementById('kpiTotalTickets');
    const kpiTotalTicketsSub = document.getElementById('kpiTotalTicketsSub');
    const kpiAvgResolution = document.getElementById('kpiAvgResolution');
    const kpiAvgPerMonth = document.getElementById('kpiAvgPerMonth');
    const kpiAvgPerMonthSub = document.getElementById('kpiAvgPerMonthSub');
    const kpiAgents = document.getElementById('kpiAgents');
    const kpiAgentsSub = document.getElementById('kpiAgentsSub');
    const summaryTableBody = document.querySelector('#summaryTable tbody');

    // --------- Helpers ---------
    function normalizeHeader(name) {
      if (!name) return '';
      return String(name).toLowerCase().replace(/[^a-z0-9]+/g, '');
    }

    function parseZohoDate(value) {
      if (!value) return null;
      const d = new Date(value);
      return isNaN(d.getTime()) ? null : d;
    }

    function getHeaderMap(row) {
      const map = {};
      Object.keys(row).forEach(key => {
        map[normalizeHeader(key)] = key;
      });
      return map;
    }

    function extractField(row, map, aliases) {
      // Exact / normalized match
      for (const alias of aliases) {
        const norm = normalizeHeader(alias);
        if (map[norm] !== undefined) {
          const rawKey = map[norm];
          return row[rawKey];
        }
      }
      // Fuzzy: alias contained in header
      for (const key in map) {
        for (const alias of aliases) {
          const normAlias = normalizeHeader(alias);
          if (key.includes(normAlias)) {
            return row[map[key]];
          }
        }
      }
      return null;
    }

    // Find real Zoho header row (starts at TICKET ID) and build objects
    function parseZohoTable(rawRows) {
      if (!rawRows || !rawRows.length) return [];
      let headerIndex = rawRows.findIndex(r =>
        String((r && r[0]) || '').trim().toUpperCase() === 'TICKET ID'
      );
      if (headerIndex === -1) return [];

      const headerRow = rawRows[headerIndex].map(v => String(v || '').trim());
      const dataRows = rawRows.slice(headerIndex + 1);

      const objects = [];
      for (const row of dataRows) {
        if (!row || row.length === 0) continue;
        // Skip fully empty rows
        const nonEmpty = row.some(c => c !== null && c !== undefined && String(c).trim() !== '');
        if (!nonEmpty) continue;

        const obj = {};
        headerRow.forEach((h, idx) => {
          obj[h] = row[idx];
        });
        objects.push(obj);
      }
      return objects;
    }

    function ensureYears() {
      const years = new Set();
      const all = [...incomingData, ...resolutionData];
      all.forEach(r => {
        // For incoming, use created date; for resolution, use resolved date
        if (r.__createdDate) {
          years.add(r.__createdDate.getFullYear());
        }
        if (r.__resolvedDate) {
          years.add(r.__resolvedDate.getFullYear());
        }
      });
      const arr = Array.from(years).sort();
      yearSelect.innerHTML = '';
      if (arr.length === 0) {
        const thisYear = new Date().getFullYear();
        const opt = document.createElement('option');
        opt.value = thisYear;
        opt.textContent = thisYear;
        yearSelect.appendChild(opt);
      } else {
        arr.forEach(y => {
          const opt = document.createElement('option');
          opt.value = y;
          opt.textContent = y;
          yearSelect.appendChild(opt);
        });
      }
    }

    function updateStatus(message, isError = false) {
      statusText.textContent = message || '';
      statusText.style.color = isError ? '#b91c1c' : '#6b7280';
    }

    // Parse Zoho-style duration strings into hours
    // Supports:
    // - "HH:MM:SS"
    // - "3d 5h", "1w 2d", "2mos 2w", "4d 21h", etc.
    function parseDurationToHours(text) {
      if (!text) return null;
      text = String(text).trim();
      if (!text) return null;

      // HH:MM:SS
      if (/^\d{1,2}:\d{2}:\d{2}$/.test(text)) {
        const [h, m, s] = text.split(':').map(v => parseInt(v, 10) || 0);
        return h + (m / 60) + (s / 3600);
      }

      let totalHours = 0;
      let matched = false;
      const parts = text.split(/\s+/);

      parts.forEach(p => {
        const m = p.match(/^(\d+)([a-zA-Z]+)$/);
        if (!m) return;
        matched = true;
        const value = parseInt(m[1], 10);
        const unit = m[2].toLowerCase();

        if (unit.startsWith('h')) {
          totalHours += value; // hours
        } else if (unit.startsWith('d')) {
          totalHours += value * 24;
        } else if (unit.startsWith('w')) {
          totalHours += value * 24 * 7;
        } else if (unit.startsWith('mo')) {
          totalHours += value * 24 * 30; // approx month
        } else if (unit === 'm') {
          totalHours += value / 60; // minutes
        }
      });

      if (!matched) {
        // If it's something like "17 Jan 2026 03:54 AM", treat as invalid duration
        return null;
      }
      return totalHours;
    }

    // --------- CSV Loaders ---------
    function handleZohoFile(input, callback) {
      const file = input.files[0];
      if (!file) return;
      Papa.parse(file, {
        header: false,
        skipEmptyLines: false,
        complete: function(results) {
          const rows = results.data || [];
          const table = parseZohoTable(rows);
          callback(table);
        }
      });
    }

    document.getElementById('incomingFile').addEventListener('change', function() {
      handleZohoFile(this, rows => {
        if (!rows.length) {
          incomingData = [];
          updateStatus('Incoming CSV is empty.', true);
          return;
        }
        const headerMap = getHeaderMap(rows[0]);
        incomingData = rows.map(r => {
          const map = headerMap; // all share same header set
          const createdRaw = extractField(r, map, ['Received Time', 'RECEIVED TIME']);
          const status = extractField(r, map, ['Status']);
          const ticketId = extractField(r, map, ['Ticket Id', 'TICKET ID']);
          // Prefer TICKET OWNER as agent
          const agent = extractField(r, map, ['Ticket Owner', 'Agent', 'Owner', 'Assignee']);
          const createdDate = parseZohoDate(createdRaw);
          return {
            raw: r,
            __createdDate: createdDate,
            __status: status || '',
            __ticketId: ticketId || '',
            __agent: agent || ''
          };
        });
        ensureYears();
        recalcBtn.disabled = false;
        updateStatus('Incoming tickets loaded. Select year and click "Recalculate KPIs".');
      });
    });

    document.getElementById('resolutionFile').addEventListener('change', function() {
      handleZohoFile(this, rows => {
        if (!rows.length) {
          resolutionData = [];
          updateStatus('Resolution CSV is empty.', true);
          return;
        }
        const headerMap = getHeaderMap(rows[0]);
        resolutionData = rows.map(r => {
          const map = headerMap;
          const resolvedRaw = extractField(r, map, ['Agent Resolved Time', 'Resolved Time', 'Closed Time']);
          const status = extractField(r, map, ['Status']);
          const ticketId = extractField(r, map, ['Ticket Id', 'TICKET ID']);
          const agent = extractField(r, map, ['Ticket Owner', 'Closed By', 'Agent', 'Owner', 'Assignee']);
          const resolvedDate = parseZohoDate(resolvedRaw);
          const durationRaw = extractField(r, map, ['Agent Resolution Time', 'Resolution Time']);
          const durationHours = parseDurationToHours(durationRaw);

          return {
            raw: r,
            __resolvedDate: resolvedDate,
            __status: status || '',
            __ticketId: ticketId || '',
            __agent: agent || '',
            __resolutionHours: durationHours
          };
        });
        ensureYears();
        recalcBtn.disabled = false;
        updateStatus('Resolution CSV loaded. Select year and click "Recalculate KPIs".');
      });
    });

    document.getElementById('responseFile').addEventListener('change', function() {
      handleZohoFile(this, rows => {
        responseData = rows;
        updateStatus('Response CSV loaded (not yet used in calculations).');
      });
    });

    // --------- KPI & chart calculation ---------
    function calculateAndRender() {
      if (!incomingData.length) {
        updateStatus('Please upload at least the Incoming tickets CSV.', true);
        return;
      }

      const year = parseInt(yearSelect.value, 10);
      if (isNaN(year)) {
        updateStatus('Invalid year selected.', true);
        return;
      }

      updateStatus(`Calculating KPIs for ${year}...`);

      const incomingYear = incomingData.filter(r => r.__createdDate && r.__createdDate.getFullYear() === year);
      const resolutionYear = resolutionData.filter(r => r.__resolvedDate && r.__resolvedDate.getFullYear() === year);

      // ---- KPI: Total tickets ----
      const totalTickets = incomingYear.length;
      kpiTotalTickets.textContent = totalTickets ? totalTickets.toLocaleString() : '0';
      kpiTotalTicketsSub.textContent = totalTickets
        ? `Tickets received in ${year}`
        : `No tickets found for ${year}`;

      // ---- KPI: Distinct agents (TICKET OWNER) ----
      const agentsSet = new Set();
      incomingYear.forEach(r => {
        if (r.__agent) agentsSet.add(String(r.__agent).trim());
      });
      kpiAgents.textContent = agentsSet.size ? agentsSet.size : '0';
      kpiAgentsSub.textContent = agentsSet.size
        ? `Agents (Ticket Owners) handled tickets in ${year}`
        : 'No agents detected from incoming CSV';

      // ---- KPI: Average tickets per month ----
      const perMonthCounts = new Array(12).fill(0);
      const monthsSeen = new Set();

      incomingYear.forEach(r => {
        if (!r.__createdDate) return;
        const m = r.__createdDate.getMonth(); // 0-based
        perMonthCounts[m] += 1;
        monthsSeen.add(m);
      });

      const activeMonths = monthsSeen.size || 1;
      const avgPerMonth = totalTickets / activeMonths;
      kpiAvgPerMonth.textContent = isFinite(avgPerMonth)
        ? avgPerMonth.toFixed(1)
        : '0';
      kpiAvgPerMonthSub.textContent = `Across ${activeMonths} month(s) with activity`;

      // ---- KPI: Avg resolution time (hours, closed/resolved only) ----
      const durations = [];
      resolutionYear.forEach(r => {
        const statusStr = String(r.__status || '').toLowerCase();
        if (statusStr.includes('closed') || statusStr.includes('resolved')) {
          if (typeof r.__resolutionHours === 'number' && isFinite(r.__resolutionHours)) {
            durations.push(r.__resolutionHours);
          }
        }
      });

      if (durations.length) {
        const sumHours = durations.reduce((a, b) => a + b, 0);
        const avgHours = sumHours / durations.length;
        const days = avgHours / 24;
        kpiAvgResolution.textContent = `${avgHours.toFixed(1)}h (${days.toFixed(2)}d)`;
      } else {
        kpiAvgResolution.textContent = '–';
      }

      // ---- Chart: Monthly incoming tickets ----
      renderMonthlyChart(perMonthCounts);

      // ---- Chart: Status distribution ----
      const statusCounts = {};
      incomingYear.forEach(r => {
        const s = (r.__status || 'Unknown').trim();
        if (!statusCounts[s]) statusCounts[s] = 0;
        statusCounts[s] += 1;
      });
      renderStatusChart(statusCounts);

      // ---- Pivot-style summary table: Status x Ticket Owner ----
      renderSummaryTable(incomingYear);

      updateStatus(`KPIs updated for ${year}.`);
    }

    function renderMonthlyChart(perMonthCounts) {
      const ctx = document.getElementById('monthlyChart').getContext('2d');
      if (monthlyChart) {
        monthlyChart.destroy();
      }
      monthlyChart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: MONTH_LABELS,
          datasets: [{
            label: 'Tickets received',
            data: perMonthCounts
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          scales: {
            x: { grid: { display: false }},
            y: {
              beginAtZero: true,
              ticks: { precision: 0 }
            }
          },
          plugins: {
            legend: { display: false },
            tooltip: {
              callbacks: {
                label: ctx => `${ctx.parsed.y} ticket(s)`
              }
            }
          }
        }
      });
    }

    function renderStatusChart(statusCounts) {
      const labels = Object.keys(statusCounts);
      const values = labels.map(l => statusCounts[l]);
      const total = values.reduce((a, b) => a + b, 0) || 1;
      const percents = values.map(v => (v / total * 100));

      const ctx = document.getElementById('statusChart').getContext('2d');
      if (statusChart) {
        statusChart.destroy();
      }
      statusChart = new Chart(ctx, {
        type: 'pie',
        data: {
          labels,
          datasets: [{
            data: percents
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            tooltip: {
              callbacks: {
                label: ctx => {
                  const label = ctx.label || 'Status';
                  const value = statusCounts[label] || 0;
                  const pct = ctx.parsed.toFixed(1);
                  return `${label}: ${value} (${pct}%)`;
                }
              }
            }
          }
        }
      });
    }

    function renderSummaryTable(incomingYear) {
      summaryTableBody.innerHTML = '';
      if (!incomingYear.length) return;

      const map = {};
      incomingYear.forEach(r => {
        const status = (r.__status || 'Unknown').trim();
        const agent = (r.__agent || 'Unassigned').trim();
        const key = status + '||' + agent;
        map[key] = (map[key] || 0) + 1;
      });

      const rows = Object.entries(map)
        .map(([key, count]) => {
          const [status, agent] = key.split('||');
          return { status, agent, count };
        })
        .sort((a, b) => {
          if (a.status === b.status) {
            return a.agent.localeCompare(b.agent);
          }
          return a.status.localeCompare(b.status);
        });

      rows.forEach(row => {
        const tr = document.createElement('tr');

        const tdStatus = document.createElement('td');
        const statusLower = row.status.toLowerCase();
        const span = document.createElement('span');
        span.textContent = row.status || 'Unknown';
        span.className = 'pill';
        if (statusLower.includes('closed') || statusLower.includes('resolved')) {
          span.classList.add('status-closed');
        }
        tdStatus.appendChild(span);

        const tdAgent = document.createElement('td');
        tdAgent.textContent = row.agent || 'Unassigned';

        const tdCount = document.createElement('td');
        tdCount.textContent = row.count;

        tr.appendChild(tdStatus);
        tr.appendChild(tdAgent);
        tr.appendChild(tdCount);

        summaryTableBody.appendChild(tr);
      });
    }

    // --------- Event wiring ---------
    recalcBtn.addEventListener('click', calculateAndRender);

    // Initialize year dropdown with current year so it's never empty
    (function initYearDropdown() {
      const thisYear = new Date().getFullYear();
      const opt = document.createElement('option');
      opt.value = thisYear;
      opt.textContent = thisYear;
      yearSelect.appendChild(opt);
    })();
  </script>
</body>
</html>

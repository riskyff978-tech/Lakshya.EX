<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Trading P&L Dashboard</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0a0f;
    --surface: #12121a;
    --surface2: #1a1a26;
    --border: #2a2a3d;
    --green: #00ff87;
    --red: #ff3d6e;
    --blue: #4d9fff;
    --yellow: #ffd166;
    --purple: #b388ff;
    --text: #e8e8f0;
    --muted: #6b6b8a;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Syne', sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Grid background */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: 
      linear-gradient(rgba(77,159,255,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(77,159,255,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  .container { max-width: 1400px; margin: 0 auto; padding: 20px; position: relative; z-index: 1; }

  /* Header */
  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 20px 0 30px;
    border-bottom: 1px solid var(--border);
    margin-bottom: 30px;
  }

  .logo {
    font-size: 22px;
    font-weight: 800;
    letter-spacing: -1px;
  }

  .logo span { color: var(--green); }

  .header-right { display: flex; gap: 12px; align-items: center; }

  .badge {
    padding: 6px 14px;
    border-radius: 20px;
    font-size: 12px;
    font-weight: 700;
    font-family: 'Space Mono', monospace;
    letter-spacing: 1px;
  }

  .badge-live { background: rgba(0,255,135,0.15); color: var(--green); border: 1px solid rgba(0,255,135,0.3); }
  .badge-date { background: var(--surface2); color: var(--muted); border: 1px solid var(--border); }

  /* Tabs */
  .tabs {
    display: flex;
    gap: 4px;
    background: var(--surface);
    padding: 6px;
    border-radius: 12px;
    margin-bottom: 28px;
    border: 1px solid var(--border);
    width: fit-content;
  }

  .tab {
    padding: 10px 24px;
    border-radius: 8px;
    border: none;
    background: transparent;
    color: var(--muted);
    font-family: 'Syne', sans-serif;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
    letter-spacing: 0.5px;
  }

  .tab.active {
    background: var(--surface2);
    color: var(--text);
    border: 1px solid var(--border);
  }

  .tab:hover:not(.active) { color: var(--text); }

  /* Section */
  .section { display: none; }
  .section.active { display: block; }

  /* Stats Grid */
  .stats-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
    gap: 16px;
    margin-bottom: 28px;
  }

  .stat-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 20px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.2s;
  }

  .stat-card:hover { border-color: var(--blue); }

  .stat-card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
  }

  .stat-card.green::before { background: var(--green); }
  .stat-card.red::before { background: var(--red); }
  .stat-card.blue::before { background: var(--blue); }
  .stat-card.yellow::before { background: var(--yellow); }
  .stat-card.purple::before { background: var(--purple); }

  .stat-label {
    font-size: 11px;
    font-weight: 600;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 10px;
  }

  .stat-value {
    font-family: 'Space Mono', monospace;
    font-size: 26px;
    font-weight: 700;
    line-height: 1;
    margin-bottom: 6px;
  }

  .stat-sub {
    font-size: 12px;
    color: var(--muted);
    font-family: 'Space Mono', monospace;
  }

  .green-text { color: var(--green); }
  .red-text { color: var(--red); }
  .blue-text { color: var(--blue); }
  .yellow-text { color: var(--yellow); }
  .purple-text { color: var(--purple); }

  /* Charts Row */
  .charts-row {
    display: grid;
    grid-template-columns: 2fr 1fr;
    gap: 16px;
    margin-bottom: 28px;
  }

  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 14px;
    padding: 24px;
  }

  .card-title {
    font-size: 13px;
    font-weight: 700;
    letter-spacing: 1px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  /* Chart Canvas */
  .chart-wrap { position: relative; }

  canvas { width: 100% !important; }

  /* Trade Table */
  .table-wrap { overflow-x: auto; }

  table {
    width: 100%;
    border-collapse: collapse;
    font-family: 'Space Mono', monospace;
    font-size: 13px;
  }

  thead th {
    text-align: left;
    padding: 12px 16px;
    font-size: 11px;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: var(--muted);
    border-bottom: 1px solid var(--border);
    font-family: 'Syne', sans-serif;
    font-weight: 600;
  }

  tbody tr {
    border-bottom: 1px solid rgba(42,42,61,0.5);
    transition: background 0.15s;
    cursor: pointer;
  }

  tbody tr:hover { background: var(--surface2); }

  tbody td { padding: 14px 16px; }

  .segment-tag {
    display: inline-block;
    padding: 3px 10px;
    border-radius: 20px;
    font-size: 10px;
    font-weight: 700;
    letter-spacing: 1px;
    font-family: 'Syne', sans-serif;
  }

  .tag-options { background: rgba(179,136,255,0.15); color: var(--purple); }
  .tag-equity { background: rgba(77,159,255,0.15); color: var(--blue); }
  .tag-commodity { background: rgba(255,209,102,0.15); color: var(--yellow); }

  .pnl-pos { color: var(--green); }
  .pnl-neg { color: var(--red); }

  /* Add Trade Form */
  .form-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 14px;
    margin-bottom: 16px;
  }

  .form-group label {
    display: block;
    font-size: 11px;
    font-weight: 600;
    letter-spacing: 1px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 8px;
  }

  .form-group input,
  .form-group select {
    width: 100%;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 10px 14px;
    color: var(--text);
    font-family: 'Space Mono', monospace;
    font-size: 13px;
    outline: none;
    transition: border-color 0.2s;
  }

  .form-group input:focus,
  .form-group select:focus { border-color: var(--blue); }

  .form-group select option { background: var(--surface2); }

  .btn {
    padding: 12px 28px;
    border-radius: 8px;
    border: none;
    font-family: 'Syne', sans-serif;
    font-size: 14px;
    font-weight: 700;
    cursor: pointer;
    letter-spacing: 0.5px;
    transition: all 0.2s;
  }

  .btn-primary {
    background: var(--green);
    color: #000;
  }

  .btn-primary:hover { opacity: 0.85; transform: translateY(-1px); }

  .btn-danger {
    background: rgba(255,61,110,0.15);
    color: var(--red);
    border: 1px solid rgba(255,61,110,0.3);
    padding: 8px 14px;
    font-size: 12px;
  }

  /* Period selector */
  .period-btns { display: flex; gap: 6px; }
  .period-btn {
    padding: 4px 12px;
    border-radius: 6px;
    border: 1px solid var(--border);
    background: transparent;
    color: var(--muted);
    font-family: 'Syne', sans-serif;
    font-size: 11px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
    letter-spacing: 0.5px;
  }
  .period-btn.active, .period-btn:hover {
    background: var(--surface2);
    color: var(--text);
    border-color: var(--blue);
  }

  /* Donut */
  .donut-wrap {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
  }

  .legend { width: 100%; }

  .legend-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px 0;
    border-bottom: 1px solid var(--border);
    font-size: 13px;
  }

  .legend-label { display: flex; align-items: center; gap: 10px; color: var(--muted); }
  .legend-dot { width: 8px; height: 8px; border-radius: 50%; }

  /* Responsive */
  @media (max-width: 768px) {
    .charts-row { grid-template-columns: 1fr; }
    .stats-grid { grid-template-columns: repeat(2, 1fr); }
  }

  /* Scrollbar */
  ::-webkit-scrollbar { width: 6px; height: 6px; }
  ::-webkit-scrollbar-track { background: var(--surface); }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 3px; }

  .empty-state {
    text-align: center;
    padding: 40px;
    color: var(--muted);
    font-size: 14px;
  }

  .filter-row {
    display: flex;
    gap: 10px;
    margin-bottom: 16px;
    flex-wrap: wrap;
    align-items: center;
  }

  .filter-select {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 8px 14px;
    color: var(--text);
    font-family: 'Syne', sans-serif;
    font-size: 13px;
    outline: none;
    cursor: pointer;
  }
</style>
</head>
<body>
<div class="container">

  <header>
    <div class="logo">TRADE<span>DESK</span></div>
    <div class="header-right">
      <span class="badge badge-live">● LIVE</span>
      <span class="badge badge-date" id="currentDate"></span>
    </div>
  </header>

  <!-- Tabs -->
  <div class="tabs">
    <button class="tab active" onclick="switchTab('overview')">Overview</button>
    <button class="tab" onclick="switchTab('options')">Options</button>
    <button class="tab" onclick="switchTab('equity')">Equity</button>
    <button class="tab" onclick="switchTab('commodity')">Commodity</button>
    <button class="tab" onclick="switchTab('trades')">All Trades</button>
    <button class="tab" onclick="switchTab('add')">+ Add Trade</button>
  </div>

  <!-- OVERVIEW -->
  <div id="tab-overview" class="section active">
    <div class="stats-grid" id="overviewStats"></div>
    <div class="charts-row">
      <div class="card">
        <div class="card-title">
          P&L Curve
          <div class="period-btns">
            <button class="period-btn active" onclick="setPeriod('1W',this)">1W</button>
            <button class="period-btn" onclick="setPeriod('1M',this)">1M</button>
            <button class="period-btn" onclick="setPeriod('3M',this)">3M</button>
            <button class="period-btn" onclick="setPeriod('ALL',this)">ALL</button>
          </div>
        </div>
        <div class="chart-wrap"><canvas id="pnlChart" height="200"></canvas></div>
      </div>
      <div class="card">
        <div class="card-title">Segment Split</div>
        <div class="donut-wrap">
          <canvas id="donutChart" height="180" width="180"></canvas>
          <div class="legend" id="donutLegend"></div>
        </div>
      </div>
    </div>
    <div class="card">
      <div class="card-title">Recent Trades</div>
      <div class="table-wrap" id="recentTradesTable"></div>
    </div>
  </div>

  <!-- OPTIONS -->
  <div id="tab-options" class="section">
    <div class="stats-grid" id="optionsStats"></div>
    <div class="card" style="margin-bottom:16px">
      <div class="card-title">Options P&L</div>
      <div class="chart-wrap"><canvas id="optionsChart" height="200"></canvas></div>
    </div>
    <div class="card">
      <div class="card-title">Options Trades</div>
      <div class="table-wrap" id="optionsTable"></div>
    </div>
  </div>

  <!-- EQUITY -->
  <div id="tab-equity" class="section">
    <div class="stats-grid" id="equityStats"></div>
    <div class="card" style="margin-bottom:16px">
      <div class="card-title">Equity P&L</div>
      <div class="chart-wrap"><canvas id="equityChart" height="200"></canvas></div>
    </div>
    <div class="card">
      <div class="card-title">Equity Trades</div>
      <div class="table-wrap" id="equityTable"></div>
    </div>
  </div>

  <!-- COMMODITY -->
  <div id="tab-commodity" class="section">
    <div class="stats-grid" id="commodityStats"></div>
    <div class="card" style="margin-bottom:16px">
      <div class="card-title">Commodity P&L</div>
      <div class="chart-wrap"><canvas id="commodityChart" height="200"></canvas></div>
    </div>
    <div class="card">
      <div class="card-title">Commodity Trades</div>
      <div class="table-wrap" id="commodityTable"></div>
    </div>
  </div>

  <!-- ALL TRADES -->
  <div id="tab-trades" class="section">
    <div class="filter-row">
      <select class="filter-select" id="filterSegment" onchange="renderAllTrades()">
        <option value="ALL">All Segments</option>
        <option value="Options">Options</option>
        <option value="Equity">Equity</option>
        <option value="Commodity">Commodity</option>
      </select>
      <select class="filter-select" id="filterType" onchange="renderAllTrades()">
        <option value="ALL">Buy & Sell</option>
        <option value="BUY">Buy</option>
        <option value="SELL">Sell</option>
      </select>
      <select class="filter-select" id="filterResult" onchange="renderAllTrades()">
        <option value="ALL">All Results</option>
        <option value="WIN">Winners</option>
        <option value="LOSS">Losers</option>
      </select>
    </div>
    <div class="card">
      <div class="card-title" id="allTradesTitle">All Trades</div>
      <div class="table-wrap" id="allTradesTable"></div>
    </div>
  </div>

  <!-- ADD TRADE -->
  <div id="tab-add" class="section">
    <div class="card">
      <div class="card-title">Add New Trade</div>
      <div class="form-grid">
        <div class="form-group">
          <label>Date</label>
          <input type="date" id="tradeDate">
        </div>
        <div class="form-group">
          <label>Segment</label>
          <select id="tradeSegment">
            <option value="Options">Options</option>
            <option value="Equity">Equity</option>
            <option value="Commodity">Commodity</option>
          </select>
        </div>
        <div class="form-group">
          <label>Symbol</label>
          <input type="text" id="tradeSymbol" placeholder="e.g. NIFTY, GOLD">
        </div>
        <div class="form-group">
          <label>Type</label>
          <select id="tradeType">
            <option value="BUY">BUY</option>
            <option value="SELL">SELL</option>
          </select>
        </div>
        <div class="form-group">
          <label>Qty / Lots</label>
          <input type="number" id="tradeQty" placeholder="0">
        </div>
        <div class="form-group">
          <label>Entry Price</label>
          <input type="number" id="tradeEntry" placeholder="0.00">
        </div>
        <div class="form-group">
          <label>Exit Price</label>
          <input type="number" id="tradeExit" placeholder="0.00">
        </div>
        <div class="form-group">
          <label>P&L (₹)</label>
          <input type="number" id="tradePnl" placeholder="Auto or manual">
        </div>
        <div class="form-group">
          <label>Notes</label>
          <input type="text" id="tradeNotes" placeholder="Optional">
        </div>
      </div>
      <button class="btn btn-primary" onclick="addTrade()">Add Trade</button>
    </div>
  </div>

</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<script>
// ============ DATA ============
let trades = JSON.parse(localStorage.getItem('trades') || '[]');

// Sample data if empty
if (trades.length === 0) {
  trades = [
    { id: 1, date: '2026-03-01', segment: 'Options', symbol: 'NIFTY CE', type: 'BUY', qty: 50, entry: 120, exit: 185, pnl: 3250, notes: 'Breakout trade' },
    { id: 2, date: '2026-03-03', segment: 'Options', symbol: 'BANKNIFTY PE', type: 'BUY', qty: 25, entry: 80, exit: 45, pnl: -875, notes: 'Stop hit' },
    { id: 3, date: '2026-03-05', segment: 'Equity', symbol: 'RELIANCE', type: 'BUY', qty: 10, entry: 2850, exit: 2920, pnl: 700, notes: '' },
    { id: 4, date: '2026-03-07', segment: 'Commodity', symbol: 'GOLD', type: 'SELL', qty: 1, entry: 87500, exit: 87100, pnl: 400, notes: 'Resistance sell' },
    { id: 5, date: '2026-03-10', segment: 'Options', symbol: 'NIFTY PE', type: 'BUY', qty: 50, entry: 95, exit: 160, pnl: 3250, notes: 'Put buy on breakdown' },
    { id: 6, date: '2026-03-12', segment: 'Equity', symbol: 'TCS', type: 'BUY', qty: 5, entry: 3900, exit: 3820, pnl: -400, notes: 'Loss' },
    { id: 7, date: '2026-03-14', segment: 'Commodity', symbol: 'SILVER', type: 'BUY', qty: 1, entry: 95000, exit: 96200, pnl: 1200, notes: '' },
    { id: 8, date: '2026-03-17', segment: 'Options', symbol: 'FINNIFTY CE', type: 'BUY', qty: 40, entry: 55, exit: 110, pnl: 2200, notes: '' },
    { id: 9, date: '2026-03-19', segment: 'Equity', symbol: 'INFY', type: 'BUY', qty: 20, entry: 1650, exit: 1700, pnl: 1000, notes: '' },
    { id: 10, date: '2026-03-21', segment: 'Commodity', symbol: 'CRUDEOIL', type: 'SELL', qty: 1, entry: 6800, exit: 7050, pnl: -250, notes: 'Bad trade' },
    { id: 11, date: '2026-03-24', segment: 'Options', symbol: 'NIFTY CE', type: 'BUY', qty: 50, entry: 140, exit: 95, pnl: -2250, notes: '' },
    { id: 12, date: '2026-03-26', segment: 'Equity', symbol: 'HDFC', type: 'BUY', qty: 8, entry: 1750, exit: 1810, pnl: 480, notes: '' },
  ];
  saveTrades();
}

function saveTrades() {
  localStorage.setItem('trades', JSON.stringify(trades));
}

// ============ UTILS ============
function fmt(n) {
  return (n >= 0 ? '+' : '') + '₹' + Math.abs(n).toLocaleString('en-IN');
}

function fmtPlain(n) {
  return '₹' + Math.abs(n).toLocaleString('en-IN');
}

function getSegmentTrades(seg) {
  if (seg === 'ALL') return trades;
  return trades.filter(t => t.segment === seg);
}

function calcStats(arr) {
  const total = arr.reduce((s, t) => s + t.pnl, 0);
  const wins = arr.filter(t => t.pnl > 0);
  const losses = arr.filter(t => t.pnl < 0);
  const winRate = arr.length ? ((wins.length / arr.length) * 100).toFixed(1) : 0;
  const avgWin = wins.length ? wins.reduce((s, t) => s + t.pnl, 0) / wins.length : 0;
  const avgLoss = losses.length ? losses.reduce((s, t) => s + t.pnl, 0) / losses.length : 0;
  const rr = avgLoss !== 0 ? Math.abs(avgWin / avgLoss).toFixed(2) : '—';
  return { total, wins: wins.length, losses: losses.length, winRate, avgWin, avgLoss, rr, count: arr.length };
}

function tagClass(seg) {
  if (seg === 'Options') return 'tag-options';
  if (seg === 'Equity') return 'tag-equity';
  return 'tag-commodity';
}

// ============ STATS CARDS ============
function renderStats(containerId, arr) {
  const s = calcStats(arr);
  const el = document.getElementById(containerId);
  el.innerHTML = `
    <div class="stat-card ${s.total >= 0 ? 'green' : 'red'}">
      <div class="stat-label">Total P&L</div>
      <div class="stat-value ${s.total >= 0 ? 'green-text' : 'red-text'}">${fmt(s.total)}</div>
      <div class="stat-sub">${s.count} trades</div>
    </div>
    <div class="stat-card blue">
      <div class="stat-label">Win Rate</div>
      <div class="stat-value blue-text">${s.winRate}%</div>
      <div class="stat-sub">${s.wins}W / ${s.losses}L</div>
    </div>
    <div class="stat-card green">
      <div class="stat-label">Avg Win</div>
      <div class="stat-value green-text">${s.wins ? fmtPlain(s.avgWin) : '—'}</div>
      <div class="stat-sub">per winning trade</div>
    </div>
    <div class="stat-card red">
      <div class="stat-label">Avg Loss</div>
      <div class="stat-value red-text">${s.losses ? fmtPlain(s.avgLoss) : '—'}</div>
      <div class="stat-sub">per losing trade</div>
    </div>
    <div class="stat-card yellow">
      <div class="stat-label">Risk:Reward</div>
      <div class="stat-value yellow-text">${s.rr}</div>
      <div class="stat-sub">avg R:R ratio</div>
    </div>
  `;
}

// ============ TA

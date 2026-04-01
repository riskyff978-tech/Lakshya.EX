<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Trading Dashboard</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      background: #0b0f1a;
      color: white;
      font-family: Arial;
      padding: 20px;
    }
    .card {
      background: #12182a;
      padding: 20px;
      border-radius: 12px;
      margin-bottom: 20px;
    }
    button {
      padding: 10px;
      margin: 5px;
      background: #1f2a44;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    }
    button:hover {
      background: #00ff99;
      color: black;
    }
  </style>
</head>
<body>

<h2>📊 Portfolio Dashboard</h2>

<div class="card">
  <h3>Cumulative P&L</h3>
  <button onclick="loadData('1w')">1W</button>
  <button onclick="loadData('1m')">1M</button>
  <button onclick="loadData('3m')">3M</button>

  <canvas id="chart"></canvas>
</div>

<div class="card">
  <h3>Segment Split</h3>
  <p id="segment">Click button to load data</p>
</div>

<div class="card">
  <h3>Recent Activity</h3>
  <ul id="activity"></ul>
</div>

<script>
let chart;

function loadData(type) {
  let data;

  if(type === '1w'){
    data = [1000, 1500, 1200, 2000, 2500];
  } else if(type === '1m'){
    data = [1000, 2000, 3000, 2500, 4000];
  } else {
    data = [1000, 3000, 2000, 5000, 7000];
  }

  const labels = ["Day1","Day2","Day3","Day4","Day5"];

  if(chart) chart.destroy();

  chart = new Chart(document.getElementById("chart"), {
    type: 'line',
    data: {
      labels: labels,
      datasets: [{
        label: "P&L",
        data: data
      }]
    }
  });

  // Segment
  document.getElementById("segment").innerText =
    "Equity: 60% | Options: 40%";

  // Activity
  document.getElementById("activity").innerHTML = `
    <li>Buy NIFTY 22000 CE</li>
    <li>Sell BANKNIFTY PE</li>
    <li>Profit booked ₹1200</li>
  `;
}
</script>

</body>
</html>

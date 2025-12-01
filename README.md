# Introduction
* * *
Can **doing well** in finance also mean **doing good** for the planet? If you’ve asked yourself this question, you’re not alone—we have too! Sustainable investing has been gaining a lot of attention lately, and we wanted to see whether ethical investing can also stand up financially. To find out, we looked at data from ESG funds and traditional ETFs, comparing them across several financial metrics to explore how they behave differently.

## What are ESG funds?
* * *

ESG stands for **Environmental**, **Social**, and **Governance**, basically, the three pillars of responsible investing. An ESG ETF is a fund that includes companies that do well in these areas. For example, it might invest in companies that are eco-friendly, treat their employees and communities well, and have strong, transparent leadership. Think of ESG funds as a way to align your investments with your values. But a quick heads-up: the rules for what counts as “ESG” aren’t set in stone yet. Each fund issuer can have slightly different standards putting more importance into certain categories, so ESG funds can vary quite a bit.

# The Data
* * *

The data used in this investigation contains historical daily data on all the tickers that are currently trading on NASDAQ starting from different timepoints up to April 2020. The daily data contains: **Opening**,**Closing**,**Highest** and **Lowest** prices as well as the total **volume** traded on that specific day. We also have access to metadata for each company where we find our ESG labels.

## Interactive Stock Volume Viewer

Explore trading volume data for any stock in our dataset. Search for a stock symbol and view its historical trading volume patterns.

<style>
  .stock-viewer-container {
    max-width: 900px;
    margin: 20px 0;
  }
  
  .search-controls {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    flex-wrap: wrap;
  }
  
  #stockSearch {
    flex: 1;
    min-width: 200px;
    padding: 8px 12px;
    font-size: 14px;
    border: 1px solid #ddd;
    border-radius: 4px;
  }
  
  #loadBtn {
    padding: 8px 20px;
    background-color: #0366d6;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 14px;
  }
  
  #loadBtn:hover {
    background-color: #0256c7;
  }
  
  #loadBtn:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  
  #chartContainer {
    position: relative;
    height: 400px;
    margin-top: 20px;
    padding: 10px;
    border: 1px solid #eee;
    border-radius: 4px;
  }
  
  #errorMessage {
    color: #d73a49;
    padding: 10px;
    margin-top: 10px;
    background-color: #ffeef0;
    border-radius: 4px;
    display: none;
  }
  
  .stock-info {
    margin-top: 15px;
    padding: 10px;
    background-color: #f6f8fa;
    border-radius: 4px;
  }
</style>

<div class="stock-viewer-container">
  <div class="search-controls">
    <input type="text" id="stockSearch" placeholder="Enter stock symbol (e.g. AAPL)" autocomplete="off">
    <button id="loadBtn">Load Chart</button>
  </div>
  
  <div id="errorMessage"></div>
  
  <div id="chartContainer" style="display:none;">
    <canvas id="chart"></canvas>
  </div>
  
  <div class="stock-info" id="stockInfo" style="display:none;"></div>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js@3.9.1/dist/chart.min.js"></script>

<script>
// Load stock data from JSON file
let window_STOCK_DATA = null;

fetch('/assets/data/stocks_data.json')
  .then(response => {
    if (!response.ok) throw new Error('Failed to load stock data');
    return response.json();
  })
  .then(data => {
    window.STOCK_DATA = data;
    console.log(`Loaded data for ${Object.keys(data).length} stocks`);
  })
  .catch(error => {
    console.error('Error loading stock data:', error);
    showError('Failed to load stock data. Please refresh the page.');
  });

function showError(message) {
    const errorDiv = document.getElementById('errorMessage');
    if (errorDiv) {
        errorDiv.textContent = message;
        errorDiv.style.display = 'block';
        setTimeout(() => {
            errorDiv.style.display = 'none';
        }, 5000);
    }
}

function loadStock(symbol) {
    // Clear previous error
    const errorDiv = document.getElementById('errorMessage');
    if (errorDiv) errorDiv.style.display = 'none';
    
    // Validate input
    if (!symbol || symbol.trim() === '') {
        showError('Please enter a stock symbol');
        return;
    }
    
    // Check if data is loaded
    if (!window.STOCK_DATA) {
        showError('Stock data is still loading... Please try again in a moment');
        return;
    }
    
    // Get data
    const upperSymbol = symbol.toUpperCase().trim();
    const data = window.STOCK_DATA[upperSymbol];
    
    if (!data || data.length === 0) {
        showError(`Stock symbol "${upperSymbol}" not found. Please check the symbol and try again.`);
        return;
    }
    
    // Prepare chart data
    const labels = data.map(d => d.date);
    const volumes = data.map(d => d.volume);
    
    // Calculate statistics
    const maxVolume = Math.max(...volumes);
    const minVolume = Math.min(...volumes);
    const avgVolume = (volumes.reduce((a, b) => a + b, 0) / volumes.length).toFixed(0);
    
    // Destroy old chart if exists
    let chart = document.getElementById('chart').chart;
    if (chart) {
        chart.destroy();
    }
    
    // Get canvas context
    const ctx = document.getElementById("chart").getContext("2d");
    
    // Create new chart
    const newChart = new Chart(ctx, {
        type: "bar",
        data: {
            labels: labels,
            datasets: [{
                label: `Trading Volume - ${upperSymbol}`,
                data: volumes,
                backgroundColor: '#0366d6',
                borderColor: '#0256c7',
                borderWidth: 1,
                borderRadius: 2
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: true,
            plugins: {
                legend: {
                    display: true,
                    position: 'top'
                },
                title: {
                    display: true,
                    text: `Trading Volume for ${upperSymbol}`
                }
            },
            scales: {
                y: {
                    beginAtZero: true,
                    title: {
                        display: true,
                        text: 'Volume'
                    },
                    ticks: {
                        callback: function(value) {
                            return value.toLocaleString();
                        }
                    }
                },
                x: {
                    title: {
                        display: true,
                        text: 'Date'
                    }
                }
            }
        }
    });
    
    // Store chart reference
    document.getElementById('chart').chart = newChart;
    
    // Show chart and info
    document.getElementById('chartContainer').style.display = 'block';
    
    // Update stock info
    const infoDiv = document.getElementById('stockInfo');
    infoDiv.innerHTML = `
        <strong>${upperSymbol} Statistics</strong><br>
        Records: ${data.length} trading days<br>
        Max Volume: ${maxVolume.toLocaleString()}<br>
        Min Volume: ${minVolume.toLocaleString()}<br>
        Avg Volume: ${avgVolume.toLocaleString()}
    `;
    infoDiv.style.display = 'block';
}

// Event listener for load button
const loadBtn = document.getElementById("loadBtn");
if (loadBtn) {
    loadBtn.addEventListener("click", () => {
        const symbol = document.getElementById("stockSearch").value;
        loadStock(symbol);
    });
}

// Allow Enter key to load stock
const stockSearch = document.getElementById("stockSearch");
if (stockSearch) {
    stockSearch.addEventListener("keypress", (e) => {
        if (e.key === 'Enter') {
            const symbol = document.getElementById("stockSearch").value;
            loadStock(symbol);
        }
    });
}
</script>


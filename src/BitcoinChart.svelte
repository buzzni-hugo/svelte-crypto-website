<!-- BitcoinChart.svelte -->
<script>
    import { onMount, createEventDispatcher } from 'svelte';

    import Chart from 'chart.js/auto';
    import moment from 'moment';

    let selectedTimeframe = '5y';  // Default selected timeframe
    let hoveredPrice = null;
    let hoveredTime = null;

    function updateDisplay(price, time) {
      hoveredPrice = price;
      hoveredTime = time;
    }
    const dispatch = createEventDispatcher();
    function selectTimeframe(timeframe) {
      selectedTimeframe= timeframe;
      dispatch('timeframe', timeframe);
    }
    export let bitcoinHistoricalData = [];
    
    let myChart;
    
    onMount(() => {
      const ctx = document.getElementById('bitcoinChart').getContext('2d');
      myChart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: bitcoinHistoricalData.map(item => moment(item[0]).format('lll')),
          datasets: [{
            label: 'Bitcoin Price in USD',
            data: bitcoinHistoricalData.map(item => item[1]),
            borderColor: '#2979ff',
            backgroundColor: 'rgba(41, 121, 255, 0.5)',  // Blue background
            fill: 'origin',  // Fill to the bottom
            tension: 0.2,
          }],
        },
        options: {
          scales: {
            x: {
              grid: {
                display: false,  // Remove background grid
              },
              ticks: {
                autoSkip: true,
                maxTicksLimit: 7  // Stick to 7 levels
              }
            },
            y: {
              grid: {
                display: false,  // Remove background grid
              },
              ticks: {
                beginAtZero: true,
                maxTicksLimit: 7  // Stick to 7 levels
              }
            }
          },
          plugins: {
            tooltip: {
              enabled: true,
              callbacks: {
                label: function (context) {
                  const price = context.parsed.y;

                  const timeFormat = ['1d', '1h'].includes(selectedTimeframe) ? 'lll' : 'LL';
                  const time = moment(context.parsed.x).format(timeFormat);
                  updateDisplay(price, time);
                  
                },
                title: function(context) {
                  const timeFormat = ['1d', '1h'].includes(selectedTimeframe) ? 'lll' : 'LL';
                  
                  return moment(context[0].parsed.x).format(timeFormat);
                }
              }
            }
          }
        }
      });
    });
  
    $: if (myChart) {
      const timeFormat = ['1d', '1h'].includes(selectedTimeframe) ? 'LT' : 'll';
      myChart.data.labels = bitcoinHistoricalData.map(item => moment(item[0]).format(timeFormat));
      myChart.data.datasets[0].data = bitcoinHistoricalData.map(item => item[1]);
      myChart.update();
    }
</script>
<div class="info-bar">
  {hoveredPrice !== null ? `Price: $${hoveredPrice}` : 'Hover over the chart to see the price'}
</div>
<canvas id="bitcoinChart"></canvas>

<div class="button-container">
  <button class:selected={selectedTimeframe === '5y'} on:click={() => selectTimeframe('5y')}>5 Years</button>
  <button class:selected={selectedTimeframe === '1y'} on:click={() => selectTimeframe('1y')}>1 Year</button>
  <button class:selected={selectedTimeframe === '6m'} on:click={() => selectTimeframe('6m')}>6 Months</button>
  <button class:selected={selectedTimeframe === '3m'} on:click={() => selectTimeframe('3m')}>3 Months</button>
  <button class:selected={selectedTimeframe === '1m'}  on:click={() => selectTimeframe('1m')}>1 Months</button>
  <button class:selected={selectedTimeframe === '1w'} on:click={() => selectTimeframe('1w')}>1 Week</button>
  <button class:selected={selectedTimeframe === '1d'} on:click={() => selectTimeframe('1d')}>1 Day</button>
</div>
<!-- Add more buttons for other timeframes -->
<style>
  /* Aligns buttons to the right */
  div.button-container {
    text-align: right;
  }

  /* Basic styling for buttons */
  button {
    background: none;
    border: none;
    cursor: pointer;
    color: gray;
    font-size: calc(1em + 0.5vw);  /* Responsive font size */
    transition: color 0.3s ease;
  }

  /* Changes color to blue when hovered */
  button:hover {
    color: blue;
  }

  /* Changes color to blue when active */
  button:active, button.selected {
    color: blue;
  }
</style>
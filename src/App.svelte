<script>
	import { onMount } from 'svelte';
	import axios from 'axios';
	import BitcoinChart from './BitcoinChart.svelte';
  
	let bitcoinHistoricalData = [];
  
	async function fetchBitcoinData(timeframe) {
	  try {
		const response = await axios.get('https://api.coingecko.com/api/v3/coins/bitcoin/market_chart', {
		  params: {
			vs_currency: 'usd',
			days: timeframe,
		  },
		});
		bitcoinHistoricalData = response.data.prices;
	  } catch (error) {
		console.error('There was an error fetching data:', error);
	  }
	}
  
	function handleTimeframe(event) {
	  const timeframe = event.detail;
	  let days;
	  switch (timeframe) {
		case '5y':
		  days = 1825;
		  break;
		case '1y':
		  days = 365;
		  break;
		case '6m':
		  days = 180;
		  break;
		case '3m':
		  days = 90;
		  break;
		case '1m':
		  days = 30;
		  break;
		case '1w':
		  days = 7;
		  break;
		case '1d':
		  days = 1;
		  break;
	  }
	  fetchBitcoinData(days);
	}
  
	onMount(() => {
	  fetchBitcoinData(7);  // Default to 7 days
	});
  </script>
  
  <main>
	<h1>Bitcoin Historical Data</h1>
	<BitcoinChart {bitcoinHistoricalData} on:timeframe={handleTimeframe} />
  </main>
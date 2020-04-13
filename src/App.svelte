<script>
	import { onMount } from 'svelte';

	import Chart from './components/Chart.svelte';
	// https://data.worldbank.org/indicator/sp.pop.grow (2018 data)
	import populationGrowth from './populationGrowth.json';

	// https://data.worldbank.org/indicator/SP.POP.TOTL (2018 data)
	import population from './population.json';

	let data;
	let infectionIncrement = 0;
	let recoveredIncrement = 0;
	let datePosition = 0;
	let scope = 'US';
	let date;

	let touchDistance, 
		startX, 
		startY;

	let s = 0;
	let i = 0;
	let r = 0;
	let m = 0;

	let loaded = false;

	$: pandemicEnded = s == 0 && i == 0;

	async function getAverageInfected() {
		let country = data[scope];

		let cumulativeInfected = country.map((day) => {
			return day.confirmed;
		}).reduce((acc, cur) => {
			return acc + cur;
		}, 0);

		let averageInfected = cumulativeInfected / country.length; 

		return averageInfected;
	}

	async function getLatestRecovered() {
		let country = data[scope];

		let latestDay = country[country.length - 1];

		let latest = latestDay.recovered + latestDay.deaths; 

		return latest;
	}

	async function setInitialSIR() {
		m = population[scope];

		if (s < 0) {
			i -= recoveredIncrement;
		} else {
			i = data[scope][data[scope].length - 1].confirmed + infectionIncrement - recoveredIncrement;
		}
		r = data[scope][data[scope].length - 1].recovered + data[scope][data[scope].length - 1].deaths + recoveredIncrement;
		s = m - i - r;
		
		date = formatDate(new Date().toISOString());
	}

	async function getData() {
		const res = await fetch("https://pomber.github.io/covid19/timeseries.json");
		data = await res.json();
		console.log(data);
		return data;
	}

	function formatDate(rawDate) {
		let dateArr = rawDate.split('T')[0].split('-');

		let formattedDate = dateArr[0] + '-' + dateArr[1].split('0').join('') + '-' + dateArr[2].split('0').join('');

		return formattedDate;
	}

	function adjustValues(direction) {
		m = population[scope];

		datePosition += direction;

		// check for current position (past, present, or future)
		if (datePosition < 0 && data[scope][Math.abs(datePosition)]) {
			// search for real data from before today
			i = data[scope][Math.abs(datePosition)].confirmed
			r = data[scope][Math.abs(datePosition)].recovered + data[scope][Math.abs(datePosition)].deaths;
		} else {
			// if there is no susceptible population, reduce those recovered
			if (s <= 0 && i > 0) {
				i -= recoveredIncrement * direction;
			} else {
				i += (infectionIncrement - recoveredIncrement) * direction;
			}

			r += recoveredIncrement * direction;
		}

		if (datePosition == -1 || datePosition == 1) {
			date = `${datePosition} day`;
		} else {
			date = `${datePosition} days`;
		}

		if (i < 0) {
			i = 0;
		}

		if (r > m) {
			r = m;
			s = 0;
		} else {
			s = m - i - r;
		}
	}

	function handleWheel(e) {
		e.preventDefault();
		adjustValues(e.deltaY);
	}

	function handleTouchStart(e) {
		let touchobj = e.changedTouches[0]
        touchDistance = 0
        startX = touchobj.pageX
        startY = touchobj.pageY
        e.preventDefault()
	}

	function handleTouchMove(e) {
		e.preventDefault();
	}

	function handleTouchEnd(e) {
		let touchobj = e.changedTouches[0]
        touchDistance = touchobj.pageY - startY

        let delta;

        if (touchDistance < 0) {
        	delta = touchDistance * 2;
        } else {
        	delta = touchDistance * 8;
        }

        adjustValues(delta);
        e.preventDefault()
	} 

	onMount(async() => {
		await getData();
		infectionIncrement = await getAverageInfected();
		recoveredIncrement = await getLatestRecovered();
		await setInitialSIR();

		loaded = true;
	});
</script>

<style>
	header {
		display: grid;
		grid-template-columns: 1fr 30%;
		align-items: center;
		justify-content: center;
		padding: 0 5%;
		height: 10%;
	}

	h1 {
		color: var(--green);
	}

	header > h3 {
		text-align: right;
	}

	main {
		height: 80%;
		display: grid;
		align-items: center;
		justify-content: center;
	}

	footer {
		height: 10%;
		overflow: auto;
	}

	footer > h3 {
		text-align: center;
	}

	h3 {
		color: var(--black);
	}
</style>

<header>
	<h1>COVID-19 SIR</h1>
	<h3>?</h3>
</header>
<main>
	{#if loaded}
		<Chart {s} {i} {r} {m} />
	{/if}
</main>
{#if pandemicEnded}
	<footer>
		<h3>
			pandemic ended at around {date}
		</h3>
	</footer>
{:else}
	<footer on:wheel={handleWheel} on:touchstart={handleTouchStart} on:touchmove={handleTouchMove} on:touchend={handleTouchEnd}>
		<h3>{date}</h3>
	</footer>
{/if}
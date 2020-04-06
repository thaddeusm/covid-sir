<script>
	import { onMount } from 'svelte';

	import S from './components/S.svelte';
	// https://data.worldbank.org/indicator/sp.pop.grow (2018 data)
	import populationGrowth from './populationGrowth.json';

	// https://data.worldbank.org/indicator/SP.POP.TOTL (2018 data)
	import population from './population.json';

	let covidData = getData();

	async function getData() {
		const res = await fetch("https://pomber.github.io/covid19/timeseries.json");
		const data = await res.json();
		console.log(data);
		return data;
	}
</script>

<style>
	
</style>

<main>
	{#await covidData}
		<h1>loading</h1>
	{:then value}
		<S height={population["World"]} />
	{:catch error}
		<h1>Something went wrong: {error}</h1>
	{/await}
</main>
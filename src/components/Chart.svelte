<script>
	export let s = 0;
	export let i = 0;
	export let r = 0;
	export let m = 0;

	$: sHeight = `height: ${s < 0 ? 0 : (s / m) * 100}%`;
	$: iHeight = `height: ${i < 0 ? 0 : (i / m) * 100}%`;
	$: rHeight = `height: ${(r / m) * 100}%`;

	let maxHeight = `height: 100%`;
</script>

<style>
	.circle {
		width: 20rem;
		height: 20rem;
		margin: 0 auto;
		border-radius: 100%;
		background: var(--light-gray);
		overflow: hidden;
	}

	.s {
		background: var(--orange);
	}

	.i {
		background: var(--red);
	}

	.r {
		background: var(--green);
	}

	.light {
		color: var(--white);
	}

	section {
		transition: all .3s ease;
		display: grid;
		align-content: center;
		justify-content: center;
	}
</style>

<div class="circle">
	<section class="s" style={sHeight.split(/[ %]/g)[1] > 100 ? maxHeight : sHeight}>
		{#if s / m > .3}
			<h3>susceptible</h3>
		{/if}
	</section>
	<section class="i" style={iHeight.split(/[ %]/g)[1] > 100 ? maxHeight : iHeight}>
		{#if i / m > .1}
			<h3 class="light">infected</h3>
		{/if}
	</section>
	<section class="r" style={rHeight.split(/[ %]/g)[1] > 100 ? maxHeight : rHeight}>
		{#if r / m > .3}
			<h3 class="light">recovered</h3>
		{/if}
	</section>
</div>
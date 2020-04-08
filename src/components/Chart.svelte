<script>
	export let s = 0;
	export let i = 0;
	export let r = 0;
	export let m = 0;

	$: sHeight = `height: ${s < 0 ? 0 : (s / m) * 100}%`;
	$: iHeight = `height: ${i < 0 ? 0 : (i / m) * 100}%`;
	$: rHeight = `height: ${(r / m) * 100}%`;
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
</style>

<div class="circle">
	<section class="s" style={sHeight}></section>
	<section class="i" style={iHeight}></section>
	<section class="r" style={rHeight}></section>
</div>
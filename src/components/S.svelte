<script>
	export let height = null;

	$: size = `height: ${height / 5000000}rem`;
</script>

<style>
	div {
		background: blue;
		width: 3rem;
	}
</style>

<div style={size}>
	
</div>
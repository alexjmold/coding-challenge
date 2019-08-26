<template>
	<div class="grid-container">
		<div
			class="row"
			v-for="index in rows"
			:key="index"
			:style="{
				width: `${gridItemSize * columns}px`,
				height: `${gridItemSize}px`
			}"
		>
			<div
				class="column"
				v-for="index in columns"
				:key="index"
				:style="{
					width: `${gridItemSize}px`,
					height: `${gridItemSize}px`
				}"
			>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		props: [
			'columns',
			'rows'
		],
		data() {
			return {
				gridItemSize: 100,
			};
		},
		mounted() {

		}
	}
</script>

<style lang="scss">
.grid-container {
	margin: 50px auto;
	position: relative;
	width: auto;
}

.row {
	margin: 0;
	padding: 0;
}

.column {
	border: 1px solid black;
	display: inline-block;
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

</style>
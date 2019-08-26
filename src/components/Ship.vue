<template>
	<div ref="ship" class="ship-container">
		<div class="ship"></div>
	</div>
</template>

<script>
	import anime from 'animejs/lib/anime.es.js';

	export default {
		data() {
			return {
				timeline: null,
			};
		},
		mounted() {
			this.timeline = anime.timeline();
		},
		methods: {
			addAnimation(shipInformation) {
				console.log(shipInformation);
				this.timeline.add({
					targets: this.$refs.ship,
					bottom: () => {
						return shipInformation.y * 100;
					},
					left: () => {
						return shipInformation.x * 100;
					},
					rotate: () => {
						if (shipInformation.direction === 'E') {
							return 90;
						} else if (shipInformation.direction === 'S') {
							return 180;
						} else if (shipInformation.direction === 'W') {
							return -90;
						}

						return 0;
					},
					duration: 1000,
					easing: 'easeOutCirc',
				});
			},
			playAnimation() {
				this.timeline.play();
			}
		}
	}
</script>

<style lang="scss">
	.ship {
		width: 15px;
		height: 40px;
		// transform-origin: 50% 50%;
		border-radius: 30px 30px 5px 5px;
		background-color: #97DFFC;
	}

	.ship-container {
		display: inline-block;
		position: absolute;
		transform-origin: 50% 50%;
		bottom: 0;
		left: 0;
		// transform: translate(-50%, -50%);
	}
</style>
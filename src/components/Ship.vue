<template>
	<div ref="ship" class="ship-container">
		<div class="ship"></div>
	</div>
</template>

<script>
	import anime from 'animejs/lib/anime.es.js';

	export default {
		props: [
			'gridItemSize'
		],
		data() {
			return {
				timeline: null,
				currentRotation: 0,
			};
		},
		methods: {
			createAnimation(data) {
				const steps = data.steps;
				const startOrientations = {
					'N': 0,
					'E': 90,
					'S': 180,
					'W': -90
				};

				const startRotation = startOrientations[data.initialPosition.direction];
				this.$refs.ship.style.transform = `rotate(${startRotation}deg)`;
				this.currentRotation = startRotation;
				this.$refs.ship.style.bottom = `${(data.initialPosition.y * this.gridItemSize) - 20}px`;
				this.$refs.ship.style.left = `${(data.initialPosition.x * this.gridItemSize) - 7.5}px`;

				this.timeline = anime.timeline();

				this.timeline.add({
					targets: this.$refs.ship,
					opacity: [0, 1],
				});


				for (let i = 0; i < steps.length; i += 1) {
					const step = steps[i];
					this.timeline.add({
						targets: this.$refs.ship,
						bottom: () => {
							return`${(step.y * this.gridItemSize) - 20}px`;
						},
						left: () => {
							return`${(step.x * this.gridItemSize) - 7.5}px`;
						},
						rotate: () => {
							if (step.instruction === 'L') {
								this.currentRotation -= 90;
								return this.currentRotation;
							} else if (step.instruction === 'R') {
								this.currentRotation += 90;
								return this.currentRotation;
							}
						},
						duration: 700,
						easing: 'easeInOutCirc',
					});
				}

				this.timeline.play();
			}
		}
	}
</script>

<style lang="scss">
	.ship {
		width: 15px;
		height: 40px;
		border-radius: 30px 30px 5px 5px;
		background-color: #97DFFC;
	}

	.ship-container {
		display: inline-block;
		position: absolute;
		transform-origin: 50% 50%;
		bottom: 0;
		left: 0;
		width: 15px;
		height: 40px;
		opacity: 0;
	}
</style>
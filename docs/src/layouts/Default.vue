<template>
	<div class="page__wrap">
		<div class="alerts__wrap copy-content">
			<div class="alert reduced-motion-alert">
				<p>
					AnimXYZ animations are disabled if your browser or OS has reduced motion setting turned on.
					<a href="https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion" target="_blank">
						Learn more here.
					</a>
				</p>
			</div>
		</div>

		<div class="xray-toggle__wrap">
			<button class="xray-toggle" :class="{ active: xRayToggled }" @click="toggleXRay(!xRayToggled)">
				<Cube class="xray-cube" :style="{ transform: xRayCubeTransform }"></Cube>
				<span class="screen-reader-only">Turn X-Ray {{ xRayToggled ? 'Off' : 'On' }}</span>
			</button>

			<div class="xray-tooltip__wrap">
				<XyzTransition xyz mode="out-in">
					<div class="xray-tooltip" key="xray-tooltip-on" v-if="xRayToggled">XYZ-ray On</div>
					<div class="xray-tooltip" key="xray-tooltip-off" v-if="!xRayToggled">XYZ-ray Off</div>
				</XyzTransition>
			</div>

			<XyzTransition xyz duration="auto">
				<div class="xray-invert__wrap xyz-none" v-if="xRayToggled">
					<div class="xray-invert xyz-nested"></div>
					<div class="xray-invert xyz-nested" xyz="inherit delay-4"></div>
				</div>
			</XyzTransition>
		</div>

		<div class="page-content__wrap" :class="{ 'xyz-xray': xRayToggled }">
			<slot></slot>
		</div>
	</div>
</template>

<static-query>
query {
  metadata {
    siteName
  }
}
</static-query>

<script>
import Cube from '~/components/reusable/Cube'

export default {
	components: {
		Cube,
	},
	data() {
		return {
			xRayToggled: false,
			xRayCubeTransform: null,
		}
	},
	methods: {
		toggleXRay(toggled) {
			this.xRayToggled = toggled
			this.randomizeXRayCubeTransform()
			this.$gtag.event('XYZ-Ray Toggled')
		},
		randomizeXRayCubeTransform() {
			this.xRayCubeTransform = `rotateX(${-0.5 + Math.random()}turn) rotateY(${-0.5 + Math.random()}turn) rotateZ(${
				-0.5 + Math.random()
			}turn)`
		},
	},
	mounted() {
		this.randomizeXRayCubeTransform()
	},
}
</script>

<style lang="scss" scoped>
.alerts__wrap {
	position: fixed;
	top: 0;
	left: 0;
	max-width: 100%;
	padding: $sp-m;
	z-index: 9;
	display: flex;
	flex-direction: column;
	align-items: flex-start;

	@include media('<tablet') {
		padding: $sp-s;
	}
}

.alert {
	font-size: $fs-s;
	line-height: 1.125rem;
	font-weight: 500;
	padding: $sp-xxs $sp-xs;
	border-radius: $br-m;

	& + & {
		margin-top: $sp-s;
	}

	p {
		color: white;
	}

	a {
		color: white;
		font-weight: 700;

		&::before {
			--anchor-underline-color: white;
		}

		&:hover,
		&:focus {
			color: white;
		}
	}
}

.reduced-motion-alert {
	display: none;
	background-color: $red;

	@media (prefers-reduced-motion: reduce) {
		display: block;
	}
}

.xray-toggle__wrap {
	position: fixed;
	bottom: $sp-xl;
	right: $sp-xl;
	z-index: 4;

	@include media('<tablet') {
		right: initial;
		left: 2.25rem;
		bottom: 2.25rem;
		z-index: 3;
	}
}

.xray-toggle {
	perspective: 10rem;
	transition: transform 0.3s $ease-out-back;
	padding: 3rem;
	margin: -3rem;

	&:hover,
	&:focus {
		transform: scale(1.125);
	}
}

.xray-tooltip__wrap {
	position: absolute;
	right: $sp-xl;
	top: 0rem;
	opacity: 0;
	transition: opacity 0.3s ease-in;

	.xray-toggle:hover + & {
		opacity: 1;
	}

	@include media('<tablet') {
		right: initial;
		left: $sp-xl;
	}
}

.xray-tooltip {
	--xyz-origin: 9.5rem center;
	--xyz-in-rotate-z: 0.5turn;
	--xyz-out-rotate-z: -0.5turn;
	line-height: 2rem;
	width: 6.5rem;
	color: $cyan;
	font-size: $fs-s;
	font-weight: 600;
	text-align: center;
	white-space: nowrap;
	background-color: primary-color(900, 0.85);
	backdrop-filter: invert(1);
	border-radius: 1rem;
	pointer-events: none;

	@include media('<tablet') {
		--xyz-origin: -3rem center;
		--xyz-in-rotate-z: -0.5turn;
		--xyz-out-rotate-z: 0.5turn;
	}
}

.xray-cube {
	--cube-size: 2rem;
	transition: transform 1s $ease-in-out-back;

	::v-deep {
		.cube__face {
			box-shadow: inset 0 0 0 1.5px primary-color(50), inset 0 0 0 1rem primary-color(400);
			transition: 1s $ease-in-out;
			transition-property: background-color, box-shadow;

			.xray-toggle.active & {
				background-color: transparentize($cyan, 0.9);
				box-shadow: inset 0 0 0 2px $cyan;
			}
		}
	}
}

.xray-invert__wrap {
	pointer-events: none;
	--xyz-translate-x: -50%;
	--xyz-translate-y: -50%;
	--xyz-scale-x: 0.001;
	--xyz-scale-y: 0.001;
	--xyz-duration: 1.25s;
}

.xray-invert {
	@include size(1vmax);
	position: absolute;
	border-radius: 50%;
	transform: translate(-50%, -50%) scale(283);

	.xyz-in &,
	.xyz-out & {
		backdrop-filter: invert(1);
	}
}
</style>

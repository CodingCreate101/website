<template>
	<Link
		class="button"
		:class="{
			'button__outline-light': theme === 'light',
			'button__outline-dark': theme === 'dark',
			'button__size-1': size === 1,
			'button__size-2': size === 2
		}"
		:link="link"
		:external="external"
	>
		{{ text }}
	</Link>
</template>

<script>
import Link from "../Link";

export default {
	name: "OutlineButton",
	components: {
		Link
	},
	props: {
		text: {
			type: String
		},
		link: {
			type: String,
			default: null
		},
		size: {
			type: Number,
			default: 2
		},
		theme: {
			type: String,
			default: "dark"
		},
		external: {
			type: Boolean,
			default: false
		}
	}
};
</script>

<style lang='sass'>
.button
	&__outline
		&-light
			border: 1px solid rgba(255, 255, 255, .8)
			color: rgba(255, 255, 255, .8)

			&:hover
				border: 1px solid rgba(255, 255, 255, 1)
				color: rgba(255, 255, 255, 1)

		&-dark
			border: 1px solid rgba(#222831, .2)
			color: rgba(#222831, .8)

			&:hover
				color: #00ADB5
</style>

<template>
	<div class="setup">
		<ul class="setup_menu">
			<li
				class="setup_menu_item"
				:class="{'active': active == 'howToUse'}"
				@click="changeTab('howToUse')"
			>How to Use</li>
			<li
				class="setup_menu_item"
				:class="{'active': active == 'shortcuts'}"
				@click="changeTab('shortcuts')"
			>Shortcuts</li>
			<li
				class="setup_menu_item"
				:class="{'active': active == 'about'}"
				@click="changeTab('about')"
			>About</li>
		</ul>
		<HowToUse v-if="active=='howToUse'" />
		<Shortcuts v-if="active=='shortcuts'" />
		<About v-if="active=='about'" />
	</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import About from './setup/About.vue';
import Shortcuts from './setup/shortcuts.vue';
import HowToUse from './setup/howToUse.vue';

export default defineComponent({
	components: {
		About,
		Shortcuts,
		HowToUse
	},
	data() {
		return {
			active: 'shortcuts'
		}
	},
	methods: {
		changeTab(tab: string) {
			this.active = tab;
		}
	},
});

</script>

<style lang="scss">
.setup{
	display: flex;
	flex-direction: column;
	justify-content: stretch;
	align-items: center;
	max-height: 100%;
	min-height: 1px;
	height: 100%;
	background: #2b2d31;

	&_menu{
		display: flex;
		flex-direction: row;
		justify-content: space-around;
		align-items: center;
		width: 100%;
		height: 50px;
		color: #FFFFFF;
		font-size: 14px;

		&_item{
			cursor: pointer;
			transition: 0.3s ease;

			&:hover, &.active{
				color: #3FB27F;
			}
		}
	}
}

</style>

<template>
	<div>
		<p class="line">{{ line }}.</p>
		<input type="text" class="input" v-model="lineCalc.text" @focus="focus(i)" @blur="blur()">
		<p class="result">{{ lineCalc.result }}</p>
	</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { LineCalc } from '@/interfaces/LineCalc';

export default defineComponent({
	props: {
		line: {
			type: number,
			required: true
		},
	},
	data() {
		return {
			isActive: false as boolean,
			lineCalc: {} as LineCalc
		}
	},
	computed: {
		isFocused() {
			return (index: number) => this.isActive === index;
		}
	},
	methods: {
		focus(index: number) {
			this.isActive = index;
		},
		blur() {
			this.isActive = -1;
		}
	}
});

</script>

<style lang="scss" scoped>
</style>

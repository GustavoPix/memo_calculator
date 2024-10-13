<template>
	<div class="calculator">
		<ul>
			<li
				class="calculator-line"
				:class="{'active': isActive == i}"
				v-for="(line, i) in lines"
				:key="'calc_line_' + i"
				@click="selectInput(i, $event)"
			>
				<p class="line">{{ i }}.</p>
				<input
					type="text"
					class="input"
					v-model="line.text"
					@focus="focus(i)"
					@blur="blur()"
				>
				<p class="result">{{ line.result }}</p>
			</li>
		</ul>
	</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

interface Line {
	text: string;
	result: number;
}

export default defineComponent({
	data(){
		return {
			isActive: 0,
			lines: [] as Line[]
		}
	},
	computed: {
		isFocused() {
			return (index: number) => this.isActive === index;
		}
	},
	methods: {
		pushLine() {
			this.lines.push({
				text: '',
				result: 0
			});
		},
		focus(index: number) {
			this.isActive = index;
		},
		blur() {
			this.isActive = -1;
		},
		selectInput(index: number, event: Event) {
			this.isActive = index;
			(event.target as HTMLElement).querySelector('input')?.focus();
		}
	},
	mounted() {
		this.pushLine();
	}
});
</script>

<style lang="scss" scoped>
.calculator {
	background: #2b2d31;
	padding-top: 10px;
	overflow-y: auto;
	min-height: 1px;
	height: 100%;
	max-height: 100%;
	flex: 1;
}
.calculator-line{
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 10px;
	gap: 10px;
	cursor: text;

	&.active{
		background: #313338;
	}
}
.line{
	color: #C3C3C3;
	font-size: 14px;
	text-align: left;
}
.input{
	background: none;
	border: none;
	color: #3FB27F;
	font-size: 14px;
	width: 100%;

	&:focus{
		outline: none;
	}
}

.result{
	color: #C3C3C3;
	font-size: 14px;
	text-align: right;
}
</style>

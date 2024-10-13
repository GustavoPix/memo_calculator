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
					:ref="'calc_line_' + i"
					@focus="focus(i)"
					@blur="blur()"
					@keyup="inputEvent(i, $event)"
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
		},
		inputEvent(index: number, event: KeyboardEvent) {
			this.checkChangeLineKey(event);
			if (index === this.lines.length - 1) {
				this.checkAddLine();
			}
			if (this.lines.length > 1) {
				this.checkTwoLinesEmpty(index);
			}
		},
		checkChangeLineKey(event: KeyboardEvent) {
			let acceptedKeys = ['ArrowUp', 'ArrowDown', 'Enter', 'Tab',];
			if (!acceptedKeys.includes(event.key)) {
				return;
			}
			event.preventDefault();
			switch (event.key) {
				case 'ArrowUp':
					this.changeLine(-1);
					break;
				case 'ArrowDown':
				case 'Enter':
				case 'Tab':
					this.changeLine(1);
					break;
			}
		},
		changeLine(direction: number) {
			if (direction !== 1 && direction !== -1) {
				return;
			}
			if (this.lines[this.isActive + direction]) {
				this.isActive += direction;
				this.$nextTick(() => {
    				this.setFocusInput(this.isActive);
				});

			}
		},
		checkAddLine() {
			if (this.lines[this.lines.length - 1].text.trim() !== '') {
				this.pushLine();
			}
		},
		checkTwoLinesEmpty(index: number) {
			if (this.lines[index].text.trim() !== '')
				return;
			if (index < this.lines.length - 1) {
				if (this.lines[index + 1].text.trim() === '') {
					this.lines.splice(index + 1, 1);
				}
			}
			if (index > 0) {
				if (this.lines[index - 1].text.trim() === '') {
					this.lines.splice(index - 1, 1);
				}
			}
			if (this.isActive > this.lines.length - 1) {
				this.setFocusInput(this.lines.length - 1);
			}
		},
		setFocusInput(index: number) {
			this.isActive = index;
			this.$nextTick(() => {
				const key = 'calc_line_' + index;
				const input = this.$refs[key] as HTMLInputElement[];
				if (input.length) {
					input[0].focus();
				}
			});
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

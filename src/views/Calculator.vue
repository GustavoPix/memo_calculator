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
				<div class="top">
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
				</div>
				<p class="result">{{ line.result }}</p>
				<div class="debug">
					<span>result: {{ line.result }}</span>
					<span>variable: {{ line.variable }}</span>
					<span>usedVariable: {{ line.usedVariable }}</span>
				</div>
			</li>
		</ul>
	</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { evaluate } from 'mathjs';

interface Line {
	text: string;
	result: number;
	variable?: string;
	usedVariable?: string[];
}

interface Variables {
  [key: string]: any;
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
			this.prepareLineContext(index);
		},
		prepareLineContext(index: number) {
			let line = this.lines[index];
			if (line.text.trim() === '') {
				line.result = 0;
				return;
			}
			this.calcSetVariable(line);
			this.calcSetUsedVariable(line);
			let variables = this.calcGetUsedVariableValues(line, index);
			let expression = line.text;
			if (variables !== undefined) {
				expression = this.calcSetExpression(line, variables);
			}
			try {
				line.result = evaluate(expression);
			} catch (error) {
				line.result = 0;
			}
			this.calcUpdateNextLines(index);
		},
		calcSetVariable(line: Line) {
			let text = line.text.trim();
			if (!text.includes('=')) {
				line.variable = undefined;
				return;
			}
			text = text.replace(/\s*=\s*/g, '=');
			let variable = text.split('=')[0];
			if (variable === '') {
				line.variable = undefined;
				return;
			}
			if (variable === line.variable) {
				return;
			}
			line.variable = variable;
			line.text.replace(/\s*=\s*/g, '=');
		},
		calcSetUsedVariable(line: Line) {
			let text = line.text.trim();
			let maybeVariables:string[] = [];
			text = text.split('=')[1];
			if (text === undefined || text === '') {
				text = line.text.trim();
			}
			maybeVariables = text.match(/[a-zA-Z_]+/g) || [];
			line.usedVariable = maybeVariables;
		},
		calcGetUsedVariableValues(line: Line, index: number) {
			let variables: Variables = {};
			if (line.usedVariable === undefined) {
				return;
			}
			line.usedVariable.forEach((variable) => {
				let lineIndex = this.lines.findIndex((line, i) => {
					return i < index && line.variable === variable;
				});
				if (lineIndex === -1) {
					return;
				}
				let value = this.lines[lineIndex].result;
				variables[variable] = value;
			});
			return variables;
		},
		calcSetExpression(line: Line, variables: Variables) {
			let text = line.text.trim();
			if (line.variable !== undefined) {
				text = text.split('=')[1];
			}
			if (line.usedVariable !== undefined) {
				line.usedVariable.forEach((variable) => {
					let value = variables[variable];
					if (value === undefined) {
						return;
					}
					text = text.replace(new RegExp(variable, 'g'), value.toString());
				});
			}
			return text;
		},
		calcUpdateNextLines(index: number) {
			let line = this.lines[index];
			if (line.variable === undefined) {
				return;
			}
			let variables: string[] = [];
			variables.push(line.variable);
			for (let i = index + 1; i < this.lines.length; i++) {
				let nextLine = this.lines[i];
				if (nextLine.usedVariable === undefined) {
					continue;
				}
				if (nextLine.usedVariable.includes(line.variable)) {
					this.prepareLineContext(i);
				}
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
	flex-direction: column;

	.top{
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 10px;
		gap: 10px;
		cursor: text;
	}

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

.debug{
	width: 100%;
	display: flex;
	justify-content: space-between;
	font-size: 12px;
	color: #C3C3C3;
	gap: 10px;
}
</style>

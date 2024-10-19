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
					@keyup="inputEventUp(i, $event)"
					@keydown="inputEventDown(i, $event)"
					>
					<p class="result">{{ line.result }}</p>
				</div>
			</li>
		</ul>
	</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { evaluate, re } from 'mathjs';

interface Line {
	text: string;
	result: number;
	variable?: string;
	usedVariable?: string[];
	expression?: string;
}

interface Variables {
  [key: string]: any;
}

export default defineComponent({
	data(){
		return {
			isActive: 0,
			lines: [] as Line[],
			pressedKeys: [] as string[]
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
		inputEventUp(index: number, event: KeyboardEvent) {
			this.checkChangeLineKey(event);
			if (index === this.lines.length - 1) {
				this.checkAddLine();
			}
			if (this.lines.length > 1) {
				this.checkTwoLinesEmpty(index);
			}
			this.prepareLineContext(index);

			let indexKeyToRemove = this.pressedKeys.indexOf(event.key);
			if (indexKeyToRemove !== -1) {
				this.pressedKeys.splice(indexKeyToRemove, 1);
			}
		},
		inputEventDown(index: number, event: KeyboardEvent) {
			this.pressedKeys.push(event.key);
			if (this.shortkeyEvent(event)) {
				return;
			}
		},
		shortkeyEvent(event: KeyboardEvent) {

			let isShortkey:boolean = false;
			if (event.key === 'c' && this.pressedKeys.includes('Control')) {
				this.shortkeyCopy();
				isShortkey = true;
			}
			if (event.key === 'Backspace' && this.pressedKeys.includes('Control')) {
				this.shortkeyClearLine();
				isShortkey = true;
			}
			if (event.key === 'Enter' && this.pressedKeys.includes('Control')) {
				this.shortcodeCloneResult();
				isShortkey = true;
			}
			if (event.key === 'Escape') {
				this.shortcodeClearAll();
				isShortkey = true;
			}

			return isShortkey;
		},
		prepareLineContext(index: number) {
			let line = this.lines[index];
			if (line.text.trim() === '') {
				line.result = 0;
				return;
			}
			this.calcSetVariable(line, index);
			this.calcSetUsedVariable(line);
			let variables = this.calcGetUsedVariableValues(line, index);
			let expression = line.text;
			if (variables !== undefined) {
				expression = this.calcSetExpression(line, variables);
			}
			try {
				line.expression = expression;
				line.result = evaluate(expression);
			} catch (error) {
				line.result = 0;
			}
			this.calcUpdateNextLines(index);
		},
		calcSetVariable(line: Line, index: number) {
			let text = line.text.trim();
			let olderVariable = line.variable;
			if (!text.includes('=')) {
				line.variable = undefined;
				if (olderVariable) {
					this.calcUpdateRemoveNextLines(index, olderVariable);
				}
				return;
			}
			let variable = text.split('=')[0];
			variable = variable.replace(/\s/g, '');
			if (variable === '') {
				line.variable = undefined;
				if (olderVariable) {
					this.calcUpdateRemoveNextLines(index, olderVariable);
				}
				return;
			}
			if (variable === line.variable) {
				return;
			}
			line.variable = variable;
			if (olderVariable) {
				this.calcUpdateRemoveNextLines(index, olderVariable);
			}
			line.text = variable + '=' + text.split('=')[1];
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
				let lines = this.lines.filter((line, i) => {
					return i < index && line.variable === variable;
				});
				if (lines.length === 0) {
					return;
				}
				let lineIndex = lines.length - 1;
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
					text = text.replace(new RegExp(`\\b${variable}\\b`, 'g'), value.toString());
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
		calcUpdateRemoveNextLines(index: number, onderVariable: string) {
			for (let i = index + 1; i < this.lines.length; i++) {
				let nextLine = this.lines[i];
				if (nextLine.usedVariable === undefined) {
					continue;
				}
				if (nextLine.usedVariable.includes(onderVariable)) {
					this.prepareLineContext(i);
				}
			}
			this.calcUpdateNextLines(index);
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
				case 'Tab':
					this.changeLine(1);
					break;
			}
			if (event.key === 'Enter' && !this.pressedKeys.includes('Control')) {
				this.changeLine(1);
			}
			if (event.key === 'Tab' && this.pressedKeys.includes('Shift')) {
				this.changeLine(-1);
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
		},
		shortkeyCopy() {
			let line = this.lines[this.isActive];
			navigator.clipboard.writeText(line.result.toString());
			console.log('Copied to clipboard: ' + line.result);
		},
		shortkeyClearLine() {
			let line = this.lines[this.isActive];
			line.text = '';
			this.prepareLineContext(this.isActive);
		},
		shortcodeCloneResult() {
			let line = this.lines[this.isActive];
			let result = line.result;
			this.lines[this.lines.length - 1].text = result.toString();
			this.prepareLineContext(this.lines.length - 1);
			this.pushLine();
		},
		shortcodeClearAll() {
			this.lines = [];
			this.pushLine();
			this.setFocusInput(0);
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

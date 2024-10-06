<template>
	<div class="calculator">
		<ul>
			<li class="calculator-line" v-for="(line, i) in 10" :key="'calc_line_' + i">
				<p class="line">{{ i }}.</p>
				<input type="text" class="input" v-model="textRef">
				<p class="result">{{ i * 5 }}</p>
			</li>
		</ul>
	</div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
	data(){
		return {
			textRef: '$a = 5'
		}
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

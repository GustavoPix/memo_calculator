<template>
	<ul class="shortcuts">
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">Arrow Up, Shift + Tab</p>
			<p class="shortcuts_description">Go to back line</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">Arrow Down, Enter, Tab</p>
			<p class="shortcuts_description">Go to next line</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">Ctrl + C</p>
			<p class="shortcuts_description">Copy result to clipboard</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">Ctrl + Backspace</p>
			<p class="shortcuts_description">Clear line</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">Ctrl + Enter</p>
			<p class="shortcuts_description">Insert new line with result this line</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">Esc</p>
			<p class="shortcuts_description">Clear all lines</p>
		</li>
	</ul>
</template>
<script>
</script>
<style lang="scss">
.shortcuts{
	display: flex;
	flex-direction: column;
	justify-content: stretch;
	align-items: center;
	max-height: 100%;
	width: 100%;
	min-height: 1px;
	height: 100%;
	background: #2b2d31;

	&_line{
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
		width: 100%;
		gap: 20px;
		padding: 10px;

		&:nth-child(even){
			background: #313338;
		}
	}

	&_shortcut{
		font-size: 14px;
		color: #FFFFFF;
		text-align: left;
	}

	&_description{
		font-size: 14px;
		color: #c3c3c3;
		text-align: right;
	}
}
</style>

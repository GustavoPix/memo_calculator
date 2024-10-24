<template>
	<ul class="shortcuts">
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">You can set a simple expression like this:</p>
			<p class="shortcuts_description">2+3</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">Or you can set a complex regular expression like this:</p>
			<p class="shortcuts_description">2+3*4</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">Also, Your create variables:</p>
			<p class="shortcuts_description">a=2</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">And use it in another line:</p>
			<p class="shortcuts_description">a+3</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">You can create a variours variables and use like this</p>
			<p class="shortcuts_description">(5+c)*myvar-g</p>
		</li>
		<li class="shortcuts_line">
			<p class="shortcuts_shortcut">And for advanced users, enjoy for realy complex expression</p>
			<p class="shortcuts_description">complex({ r: sqrt(2), phi: pi / 4 })</p>
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

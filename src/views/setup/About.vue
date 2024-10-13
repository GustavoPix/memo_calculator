<template>
	<div class="about">
		<svg width="96" height="96" viewBox="0 0 96 96" fill="none" xmlns="http://www.w3.org/2000/svg">
			<rect x="51" y="22" width="54" height="6" rx="1" transform="rotate(90 51 22)" fill="#3FB27F"/>
			<rect x="74" y="51" width="52" height="7" rx="1" transform="rotate(-180 74 51)" fill="#3FB27F"/>
			<rect x="8" y="84" width="80" height="8" rx="1" fill="#3FB27F"/>
			<rect x="8" y="4" width="80" height="8" rx="1" fill="#3FB27F"/>
		</svg>
		<h1 class="title">Memo-calc</h1>
		<p class="text">Thank you for using Memo-cal, please consider supporting me financially <a href="https://payto.me/gustavopix" target="_blank">here</a></p>
		<p class="text">For any questions or concerns, please contact me at: <a href="mailto:gustavo1pix@gmail.com">gustavo1pix@gmail.com</a></p>
		<p class="text">For more information, please visit my website: <a href="https://gustavo1pix.github.io/">gustavo1pix.github.io</a></p>
		<p class="copy">v1.0.0 - Gustavo Carvalho</p>
		<p class="copy">© 2024 Gustavo Carvalho. All rights reserved.</p>
	</div>
</template>

<script setup lang="ts">
</script>

<style lang="scss">
.about{
  display: flex;
  flex-direction: column;
  justify-content: stretch;
  align-items: center;
  max-height: 100%;
  min-height: 1px;
  height: 100%;
  gap: 20px;
  padding: 20px;
  background: #2b2d31;
}

.title{
	font-size: 28px;
	color: #FFFFFF;
}
.text{
	font-size: 14px;
	color: #FFFFFF;
	text-align: center;

	a{
		color: #FFFFFF;
		font-style: italic;
		transition: 0.3s ease;

		&:hover{
			color: #3FB27F;
		}
	}
}

.copy{
	font-size: 12px;
	color: #8F8F8F;
	text-align: center;
}

</style>

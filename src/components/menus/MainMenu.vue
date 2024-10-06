<template>
	<div class="main-menu">
		<h1>Main Menu</h1>
		<ul>
			<li><a href="#home">Home</a></li>
			<li><a href="#about">About</a></li>
			<li><a href="#contact">Contact</a></li>
		</ul>
	</div>
</template>

<script>
export default {
	name: 'MainMenu'
}
</script>

<style scoped>
.main-menu {
	text-align: center;
}

.main-menu ul {
	list-style-type: none;
	padding: 0;
}

.main-menu li {
	display: inline;
	margin: 0 10px;
}

.main-menu a {
	text-decoration: none;
	color: #333;
}

.main-menu a:hover {
	text-decoration: underline;
}
</style>

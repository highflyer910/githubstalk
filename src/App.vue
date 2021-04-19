<template>
	<div id="app">
		<Search />
	</div>
</template>

<script>
import Search from './components/Search.vue'

export default {
	name: 'App',
	components: {
		Search
	}

}
</script>

<style>

body {
  background-color: #FDFDFE;
}
  

#app {
  padding: 20px 0;
}

</style>

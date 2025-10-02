<template>
	<div class="box">
	<div class="inside-box">
		<img :src="project.image" alt="...">
			<h3>{{project.title}}</h3>
			<p>{{project.description}}
			</p>
		</div>
	</div>
</template>

<script setup>
	import { defineProps } from 'vue';

	defineProps({
		project: Object
	})
	
</script>
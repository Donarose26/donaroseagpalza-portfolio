<template>
	<!-- My Projects -->
	<section class="container-fluid py-md-5" id="projects">

        <h2 cclass="text-center">My Projects</h2>

        <!-- Loop through each group of 3 projects -->

        <div
            v-for="(group, index) in chunkedProjects"
            :key="index"
            class="row justify-content-center g-4 py-md-0 my-md-3"
        >
            <!-- For each project in the current group, render a project card componet -->
            <ProjectCard
            v-for="project in group"
            :key="project.id"
            :project="project"
            class="col-sm-6 col-md-3 my-3 d-flex"
            />

        </div>
    </section>


</template>

<script setup>
    import { computed } from 'vue';

    import ProjectCard from './ProjectCard.vue';

    import projects from '../data/projects.json';

    const chunkSize = 3

    const chunkedProjects = computed(() => {
        const chunks = []; //Start with an empty list of rows

        for (let i = 0; i < projects.length; i += chunkSize) {
            chunks.push(projects.slice(i, i + chunkSize))
        }

        return chunks;
    })
</script>
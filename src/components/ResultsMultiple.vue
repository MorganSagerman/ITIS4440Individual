<script setup>
const props = defineProps({
    answer: Array,
    empty: Boolean
});
</script>

<template>
    <div class="resultsCard">
        <h3 v-if="empty">No exercise(s) found matching those parameters.</h3>
        <div v-else>
            <h3>Possible workouts include:</h3>
            <span v-for="x in answer" :key="x.id">
                {{ x.name }}
                <br>
            </span>
        </div>
        <footer />
    </div>
</template>
<script setup>
const props = defineProps({
    answer: Array,
    empty: Boolean
});
</script>

<template>
    <div class="resultsCard">
        <h3 v-if="empty">No exercise found matching those parameters.</h3>
        <div v-else>
            <h3>You could try:</h3>
            <span>{{ answer[0].name }}</span>
        </div>
        <footer />
    </div>
</template>
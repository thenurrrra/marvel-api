<script setup>
    import { defineProps } from 'vue';

    const props = defineProps(["iscomics", "comicstitle", "selected", "chrctrs"])
</script>

<template>
    <div class="comics">
        <button @click="$emit('show-comics')" class="show-btn">{{ comicstitle }}</button>
        <div v-if="iscomics" class="items">
            <div v-if="chrctrs[selected].comics.items.length > 0">
                <div v-for="comics in chrctrs[selected].comics.items">
                    <p>{{ comics.name }}</p>
                </div>
            </div>
            <div v-else>
                <p>No comics</p>
            </div>
        </div>
    </div>
</template>
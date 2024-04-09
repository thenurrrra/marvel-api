<script setup>
import { defineProps } from 'vue';
    const props = defineProps(["suggests", "images", "selected"]);    
</script>

<template>
    <div class="suggests__list" v-for="(character, index) in suggests" :key="index" v-if="selected === null">
        <p>{{ index + 1 }}. {{ character }}</p>
        <img class="suggest-img" :src="images[index]" alt="thumnail" @click="$emit('show-hero', index)">
    </div>
</template>
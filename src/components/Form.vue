<script setup>
    const user = defineModel();
</script>

<template>
    <form>
        <input type="text" v-model="user" placeholder="Enter the character name" @input="$emit('input-emit')">
    </form>
</template>
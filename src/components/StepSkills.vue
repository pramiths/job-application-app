<template>
    <div class="step-personal-info">
        <h2>Personal Information</h2>
        <div class="form-group">
            <label for="skill">Skill:</label>
            <input type="text" id="skill" v-model="localData.skill" required />
        </div>
        <div class="form-group">
            <label for="skillLevel">Skill Level:</label>
            <input type="text" id="skillLevel" v-model="localData.skillLevel" required />
        </div>
    </div>
</template>

<script setup>
</script>
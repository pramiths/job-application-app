<template>
    <div class="step-container">
        <div class="form-group">
            <label for="name">Name:<span class="required"></span></label>
            <input 
                type="text" 
                id="name" 
                placeholder="Enter your full name"
                v-model="localData.fullNem" 
                required
            />
        </div>
        <div class="form-group">
            <label for="email">Email:<span class="required"></span></label>
            <input 
                type="email" 
                id="email" 
                placeholder="Enter your email address"
                v-model="localData.email"
                required
            />
        </div>
        <div class="form-group">
            <label for="phone">Phone Number:<span class="required"></span></label>
            <input 
                type="tel" 
                id="phone" 
                placeholder="Enter your phone number"
                v-model="localData.phone"
                required
            />
        </div>
        <div class="form-group">
            <label for="languages">Languages:<span class="required"></span></label>
            <input 
                type="text" 
                id="languages" 
                placeholder="Enter languages you speak"
                v-model="localData.languages"
            />
        </div>

        <button 
            type="button"
            class="primary"
            @click="handleNext"
        >
            Save & Next
        </button>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
    modelValue: {
        type: Object,
        required: true,
    },
});

const localData = ref({ ...props.modelValue.personal });

const handleNext = () => {
    const updatedData = {
        ...props.modelValue,
        personal: { ...localData.value },
    };
    console.log('Updated form data:', updatedData);
};

</script>
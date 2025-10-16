<template>
    <div class="step-container">
        <h2>Personal Information</h2>
        <p class="step-description">Please provide your personal details below.</p>
        <div class="form-group">
            <label for="fullName">Full Name<span class="required">*</span></label>
            <input 
                type="text" 
                id="fullName" 
                placeholder="Enter your full name"
                v-model="localData.fullName" 
                required
                @input="validateField('fullName')"
            />
            <span v-if="errors.fullName" class="error-message">{{ errors.fullName }}</span>
        </div>
        <div class="form-group">
            <label for="email">Email:<span class="required">*</span></label>
            <input 
                type="email" 
                id="email" 
                placeholder="Enter your email address"
                v-model="localData.email"
                required
                @input="validateField('email')"
            />
            <span v-if="errors.email" class="error-message">{{ errors.email }}</span>
        </div>
        <div class="form-group">
            <label for="phone">Phone Number:<span class="required">*</span></label>
            <input 
                type="tel" 
                id="phone" 
                placeholder="Enter your phone number"
                v-model="localData.phone"
                required
                @input="validateField('phone')"
            />
            <span v-if="errors.phone" class="error-message">{{ errors.phone }}</span>
        </div>
        <div class="form-group">
            <label for="languages">Languages:<span class="required">*</span></label>
            <input 
                type="text" 
                id="languages" 
                placeholder="Enter languages you speak"
                v-model="localData.languages"
                required
                @input="validateField('languages')"
            />
            <span v-if="errors.languages" class="error-message">{{ errors.languages }}</span>
        </div>

        <button 
            type="button"
            class="btn primary"
            @click="handleNext"
        >
            Save & Next
        </button>
    </div>
</template>

<script setup>
import { ref, watch } from 'vue';

const props = defineProps({
    modelValue: {
        type: Object,
        required: true,
    },
});

//emits
const emit = defineEmits(['update:modelValue', 'next', 'prev']);

const localData = ref({ ...props.modelValue.personal });

//error handling
const errors = ref({
    fullName: '',
    email: '',
    phone: '',
    languages: '',
})

// for field validation
const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
const phoneRegex = /^\d{10,}$/

const validateField = (field) => {
    console.log('Validating field:', field);
    errors.value[field] = '';
    switch(field) { 
        case 'fullName':
            if (!localData.value.fullName.trim()) {
                errors.value.fullName = 'Name is required.';
            }
            break;
        case 'email':
            if (!localData.value.email.trim()) {
                errors.value.email = 'Email is required.';
            } else if (!emailRegex.test(localData.value.email)) {
                errors.value.email = 'Invalid email format.';
            }
            break;
        case 'phone':
            if (!localData.value.phone.trim()) {
                errors.value.phone = 'Phone number is required.';
            } else if (!phoneRegex.test(localData.value.phone)) {
                errors.value.phone = 'Invalid phone number format.';
            }
            break;
        case 'languages':
            if (!localData.value.languages.trim()) {
                errors.value.languages = 'Please list at least one language.';
            }
            break;
    }
}

const validateAll = () => {
    validateField('fullName');
    validateField('email');
    validateField('phone');
    validateField('languages');
    return !Object.values(errors.value).some(error => error !== '');
}

// save & proceed to next step
const handleNext = () => {
    if (!validateAll()) {
        console.log('Validation errors:', errors.value);
        return;
    }
    // checking updated data
    const updatedData = {
        ...props.modelValue,
        personal: { ...localData.value },
    };
    console.log('Updated form data:', updatedData);

    //emit updated data to parent and got to next step
    emit('update:modelValue', {
        ...props.modelValue,
        personal: localData.value
    });
    emit('next', true);
};

// Watch for prop changes to update local data
watch(() => props.modelValue.personal, (newVal, oldVal) => {
  // comparing with old value to avoid unnecessary updates
  if (!oldVal || JSON.stringify(newVal) !== JSON.stringify(localData.value)) {
    localData.value = { ...newVal }
  }
})

</script>

<style scoped>
.step-container {
  padding: 2rem 0;
}

h2 {
  color: #1e293b;
  margin-bottom: 0.5rem;
  font-size: 1.75rem;
}

.step-description {
  color: #64748b;
  margin-bottom: 2rem;
}

.required {
  color: #ef4444;
}

.helper-text {
  display: block;
  margin-top: 0.25rem;
  color: #64748b;
  font-size: 0.875rem;
}

.button-group {
  display: flex;
  gap: 1rem;
  margin-top: 2rem;
  justify-content: flex-end;
}
</style>
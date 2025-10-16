<template>
  <div class="step-container">
    <h2>Education</h2>
    <p class="step-description">Add your educational details below.</p>

    <div v-for="(edu, index) in localData" :key="index" class="education-card">
      <div class="card-header">
        <h3>Education {{ index + 1 }}</h3>
        <!-- Remove button -->
        <button 
          type="button" 
          class="remove-btn" 
          @click="removeEducation(index)"
          title="Remove this education"
        >
          X
        </button>
      </div>

        <!-- Institution Name -->
      <div class="form-group">
        <label :for="`institution-${index}`">Institution Name <span class="required">*</span></label>
        <input
          :id="`institution-${index}`"
          v-model="edu.institution"
          type="text"
          placeholder="School, College, or University name"
          :class="{ error: errors[index]?.institution }"
          @input="validateEducation(index, 'institution')"
        />
        <span v-if="errors[index]?.institution" class="error-message">{{ errors[index].institution }}</span>
      </div>

      <!-- Education Type -->
      <div class="form-group">
        <label :for="`type-${index}`">Type <span class="required">*</span></label>
        <select
          :id="`type-${index}`"
          v-model="edu.type"
          :class="{ error: errors[index]?.type }"
          @change="validateEducation(index, 'type')"
        >
          <option value="">Select type</option>
          <option value="School">School</option>
          <option value="UG">UG</option>
          <option value="PG">PG</option>
        </select>
        <span v-if="errors[index]?.type" class="error-message">{{ errors[index].type }}</span>
      </div>

      <!-- Degree/Cerification -->
      <div class="form-group">
        <label :for="`degree-${index}`">Degree/Certification <span class="required">*</span></label>
        <input
          :id="`degree-${index}`"
          v-model="edu.degree"
          type="text"
          placeholder="e.g., Bachelor of Science, High School Diploma"
          :class="{ error: errors[index]?.degree }"
          @input="validateEducation(index, 'degree')"
        />
        <span v-if="errors[index]?.degree" class="error-message">{{ errors[index].degree }}</span>
      </div>

      <!-- Year of Completion -->
      <div class="form-group">
        <label :for="`year-${index}`">Year of Completion <span class="required">*</span></label>
        <input
          :id="`year-${index}`"
          v-model.number="edu.year"
          type="number"
          min="1950"
          :max="new Date().getFullYear() + 10"
          placeholder="e.g., 2020"
          :class="{ error: errors[index]?.year }"
          @input="validateEducation(index, 'year')"
        />
        <span v-if="errors[index]?.year" class="error-message">{{ errors[index].year }}</span>
      </div>
    </div>

    <!-- Add Education DEtail button -->
    <button type="button" class="add-btn" @click="addEducation">
      Add Education
    </button>
    <span v-if="localData.length === 0 && validationFailed" class="error-message error-no-details">Enter the education details.</span>

    <!-- Navigation Buttons -->
    <div class="button-group">
      <button type="button" class="secondary" @click="handlePrev">
        Previous
      </button>
      <button type="button" class="primary" @click="handleNext">
        Next Step
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  modelValue: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update:modelValue', 'next', 'prev']);

const localData = ref([...props.modelValue.education]);
const errors = ref([]);
const validationFailed = ref(false);

// Watch for prop changes to update local data
watch(() => props.modelValue.education, (newVal, oldVal) => {
  // Only update when restore/reset or initial load
  if (!oldVal || newVal.length !== oldVal.length || JSON.stringify(newVal) !== JSON.stringify(localData.value)) {
    localData.value = [...newVal];
    if (!oldVal || newVal.length !== oldVal.length) {
      errors.value = newVal.map(() => ({}));
    }
  }
});

const addEducation = () => {
  localData.value.push({
    institution: '',
    type: '',
    degree: '',
    year: ''
  });
  errors.value.push({});
}

const removeEducation = (index) => {
  localData.value.splice(index, 1);
  errors.value.splice(index, 1);
}

const validateEducation = (index, field) => {
  if (!errors.value[index]) {
    errors.value[index] = {};
  }
  
  errors.value[index][field] = '';
  const edu = localData.value[index];
  
  switch (field) {
    case 'institution':
      if (!edu.institution?.trim()) {
        errors.value[index].institution = 'Institution name is required';
      }
      break;
    case 'type':
      if (!edu.type) {
        errors.value[index].type = 'Type is required';
      }
      break;
    case 'degree':
      if (!edu.degree?.trim()) {
        errors.value[index].degree = 'Degree/Certification is required';
      }
      break;
    case 'year':
      const currentYear = new Date().getFullYear();
      if (!edu.year) {
        errors.value[index].year = 'Year is required';
      } else if (edu.year < 1950 || edu.year > currentYear + 10) {
        errors.value[index].year = `Year must be between 1950 and ${currentYear + 10}`;
      }
      break
  }
}

const validateAll = () => {
  if (localData.value.length === 0) {
    validationFailed.value = true;
    return false;
  }
  localData.value.forEach((_, index) => {
    validateEducation(index, 'institution')
    validateEducation(index, 'type')
    validateEducation(index, 'degree')
    validateEducation(index, 'year')
  });
  
  return !errors.value.some(err => Object.values(err).some(msg => msg !== ''));
}

const handleNext = () => {
  if (validateAll()) {
    emit('update:modelValue', {
      ...props.modelValue,
      education: localData.value
    });
    emit('next', true);
  }
}

const handlePrev = () => {
  emit('update:modelValue', {
    ...props.modelValue,
    education: localData.value
  });
  emit('prev');
}

watch(localData, (newVal) => {
  emit('update:modelValue', {
    ...props.modelValue,
    education: newVal
  });
}, { deep: true });

</script>

<style scoped>
.education-card {
  background: #f8fafc;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  padding: 1.5rem;
  margin-bottom: 1.5rem;
  position: relative;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

.card-header h3 {
  color: #475569;
  font-size: 1.1rem;
  font-weight: 600;
}

.remove-btn {
  background: #ef4444;
  color: white;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.2rem;
  padding: 0;
}

.remove-btn:hover {
  background: #dc2626;
  transform: scale(1.2);
  transition: ease-in 0.2s;
}

.add-btn {
  width: 100%;
  background: #f1f5f9;
  color: #667eea;
  border: 2px dashed #667eea;
  padding: 1rem;
  border-radius: 8px;
  font-weight: 600;
  margin-bottom: 2rem;
}

.add-btn:hover {
  background: #e0e7ff;
  border-color: #5568d3;
}

.error-no-details {
  display: block;
  margin-top: -0.5rem;
  color: #ef4444;
  font-size: 0.875rem;
  text-align: center;
}
</style>

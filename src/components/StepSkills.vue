<template>
  <div class="step-container">
    <h2>Skills</h2>
    <p class="step-description">Add your skills and proficiency levels (you can add multiple skills)</p>

    <div v-for="(skill, index) in localData" :key="index" class="skill-card">
      <div class="card-header">
        <h3>Skill {{ index + 1 }}</h3>
        <button 
          type="button" 
          class="remove-btn" 
          @click="removeSkill(index)"
          title="Remove this skill"
        >
          X
        </button>
      </div>

      <!-- Skill -->
      <div class="skill-row">
        <div class="form-group">
          <label :for="`skill-name-${index}`">Skill Name <span class="required">*</span></label>
          <input
            :id="`skill-name-${index}`"
            v-model="skill.name"
            type="text"
            placeholder="e.g., JavaScript, Project Management"
            :class="{ error: errors[index]?.name }"
            @input="validateSkill(index, 'name')"
          />
          <span v-if="errors[index]?.name" class="error-message">{{ errors[index].name }}</span>
        </div>

        <!-- Skill Level -->
        <div class="form-group">
          <label :for="`skill-level-${index}`">Skill Level <span class="required">*</span></label>
          <select
            :id="`skill-level-${index}`"
            v-model="skill.level"
            :class="{ error: errors[index]?.level }"
            @change="validateSkill(index, 'level')"
          >
            <option value="">Select level</option>
            <option value="Beginner">Beginner</option>
            <option value="Intermediate">Intermediate</option>
            <option value="Expert">Expert</option>
          </select>
          <span v-if="errors[index]?.level" class="error-message">{{ errors[index].level }}</span>
        </div>
      </div>
    </div>

    <!-- Add More Skills -->
    <button type="button" class="add-btn" @click="addSkill">
      Add Skills
    </button>
    <span v-if="localData.length === 0 && validationFailed" class="error-message error-no-details">Add atleast one skill.</span>

    <!-- Navigation -->
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
import { ref, watch } from 'vue';

const props = defineProps({
  modelValue: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(['update:modelValue', 'next', 'prev']);

const localData = ref([...props.modelValue.skills]);
const errors = ref([]);
const validationFailed = ref(false);

// Watch for prop changes to update local data
watch(() => props.modelValue.skills, (newVal, oldVal) => {
  // Only sync restore/reset scenario or initial load
  if (!oldVal || newVal.length !== oldVal.length || JSON.stringify(newVal) !== JSON.stringify(localData.value)) {
    localData.value = [...newVal];
    if (!oldVal || newVal.length !== oldVal.length) {
      errors.value = newVal.map(() => ({}));
    }
  }
})

const addSkill = () => {
  localData.value.push({
    name: '',
    level: ''
  });
  errors.value.push({});
}

const removeSkill = (index) => {
  localData.value.splice(index, 1);
  errors.value.splice(index, 1);
}

const validateSkill = (index, field) => {
  if (!errors.value[index]) {
    errors.value[index] = {};
  }
  
  errors.value[index][field] = '';
  const skill = localData.value[index];
  
  switch (field) {
    case 'name':
      if (!skill.name?.trim()) {
        errors.value[index].name = 'Skill name is required';
      }
      break;
    case 'level':
      if (!skill.level) {
        errors.value[index].level = 'Skill level is required';
      }
      break;
  }
}

const validateAll = () => {
  if (localData.value.length === 0) {
    validationFailed.value = true;
    return false;
  }
  
  localData.value.forEach((_, index) => {
    validateSkill(index, 'name')
    validateSkill(index, 'level')
  });
  
  return !errors.value.some(err => Object.values(err).some(msg => msg !== ''));
}

const handleNext = () => {
  if (validateAll()) {
    emit('update:modelValue', {
      ...props.modelValue,
      skills: localData.value
    });
    emit('next', true);
  }
}

const handlePrev = () => {
  emit('update:modelValue', {
    ...props.modelValue,
    skills: localData.value
  });
  emit('prev');
}

watch(localData, (newVal) => {
  emit('update:modelValue', {
    ...props.modelValue,
    skills: newVal
  })
}, { deep: true });

</script>

<style scoped>
.skill-card {
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

.skill-row {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 1rem;
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
  transition: all 0.3s ease;
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

@media (max-width: 768px) {
  .skill-row {
    grid-template-columns: 1fr;
  }
}
</style>

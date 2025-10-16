<template>
  <div class="multi-step-form">
    <ProgressBar 
      :steps="steps"
      :currentStep="currentStep"
      :completedSteps="completedSteps"
      @jump="jumpToStep" 
    />
    <component 
      :is="currentComponent" 
      :key="currentStep"
      v-model="formData"
      @next="nextStep"
      @prev="prevStep"
    />

    <div v-if="showSuccess" class="success-message">
      Application submitted successfully! Thank you for applying.
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch} from 'vue';
import StepPersonalInfo from './StepPersonalInfo.vue';
import StepWorkExperience from './StepWorkExperience.vue';
import StepEducation from './StepEducation.vue';
import StepSkills from './StepSkills.vue';
import ProgressBar from './ProgressBar.vue';

const steps = [
  {label: 'Personal Info', component: 'StepPersonalInfo'},
  {label: 'Work Experience', component: 'StepWorkExperience'},
  {label: 'Education', component: 'StepEducation'},
  {label: 'Skills', component: 'StepSkills'},
];

const currentStep = ref(0);
const completedSteps = ref([]); // track completed steps in form
const showSuccess = ref(false)

// initialize form data structure
const formData = ref({
  personal: {
    fullName: '',
    email: '',
    phone: '',
    languages: '',
  },
  work: [],
  education: [],
  skills: [],
});

const currentComponent = computed(() => {
  const stepName = steps[currentStep.value].component;
  const components = {
    StepPersonalInfo,
    StepWorkExperience,
    StepEducation,
    StepSkills,
  };
  console.log('Step Name:', stepName);
  return components[stepName];
});

//Navigation between steps
const jumpToStep = (index) => {
  if (completedSteps.value.includes(index) || index === Math.max(...completedSteps.value, -1) + 1) {
    currentStep.value = index;
  }
};

const nextStep = (isValid = true) => {
  console.log('Next Step Called, isValid:', isValid);
  if (!isValid) return;
  
  // Mark current step as completed
  if (!completedSteps.value.includes(currentStep.value)) {
    completedSteps.value.push(currentStep.value);
  }
  
  // Move to next step or submit if last step
  if (currentStep.value < steps.length - 1) {
    currentStep.value++;
  } else {
    submitForm();
  }
}

const prevStep = () => {
  if (currentStep.value > 0) {
    currentStep.value--;
  }
}

//Submit form
const submitForm = () => {
  console.log('Form Submitted:', formData.value)
  showSuccess.value = true;
  
  // Clear localStorage after successful submission
  localStorage.removeItem('jobApplicationForm');
  
  // Reset after a submitting delay
  setTimeout(() => {
    showSuccess.value = false
    currentStep.value = 0
    completedSteps.value = []
    formData.value = {
      personal: { fullName: '', email: '', phone: '', languages: '' },
      work: [],
      education: [],
      skills: []
    }
  }, 1000)
}

// Load data from local storage on mount
onMounted(() => {
  const savedData = localStorage.getItem('jobApplicationForm');
  if (savedData) {
    try {
      const parsedData = JSON.parse(savedData);
      formData.value = parsedData.formData || formData.value;
      completedSteps.value = parsedData.completedSteps || [];
      currentStep.value = parsedData.currentStep || 0;
    } catch (err) {
      console.error('Failed to load saved form data:', err);
    }
  }
});

// Save to local storange when step or form data changes
watch([formData, currentStep, completedSteps], () => {
  localStorage.setItem('jobApplicationForm', JSON.stringify({
    formData: formData.value,
    currentStep: currentStep.value,
    completedSteps: completedSteps.value
  }));
}, { deep: true });


</script>

<style scoped>
.multi-step-form {
  position: relative;
}
</style>

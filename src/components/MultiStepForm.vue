<script setup>
import { ref, computed} from 'vue';
import StepPersonalInfo from './StepPersonalInfo.vue';
import StepWorkExperience from './StepWorkExperience.vue';
import StepEducation from './StepEducation.vue';
import StepSkills from './StepSkills.vue';

const steps = [
  {label: 'Personal Info', component: 'StepPersonalInfo'},
  {label: 'Work Experience', component: 'StepWorkExperience'},
  {label: 'Education', component: 'StepEducation'},
  {label: 'Skills', component: 'StepSkills'},
];

const currentStep = ref(0);

// initialize form data structure
const formData = ref({
  personal: {
    name: '',
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

</script>

<template>
  <div class="multi-step-form">
    <component :is="currentComponent" :key="currentStep" v-model="formData"/>
  </div>
</template>

<style scoped>

</style>

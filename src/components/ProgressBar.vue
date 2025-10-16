<template>
  <div class="progress-container">
    <div class="progress-bar">
      <div 
        class="progress-fill" 
        :style="{ width: progressPercentage + '%' }"
      ></div>
    </div>
    
    <div class="steps-container">
      <div 
        v-for="(step, index) in steps" 
        :key="index"
        class="step"
        :class="{ 
          active: index === currentStep, 
          completed: completedSteps.includes(index),
          clickable: canJumpTo(index)
        }"
        @click="handleStepClick(index)"
      >
        <div class="step-circle">
          <span v-if="completedSteps.includes(index)">✓</span>
          <span v-else>{{ index + 1 }}</span>
        </div>
        <div class="step-label">{{ step.label }}</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  steps: {
    type: Array,
    required: true,
  },
  currentStep: {
    type: Number,
    required: true,
  },
  completedSteps: {
    type: Array,
    default: () => [],
  }
})

const emit = defineEmits(['jump']);

const progressPercentage = computed(() => {
  return (props.currentStep / (props.steps.length - 1)) * 100;
})

const canJumpTo = (index) => {
  // Can jump to completed steps or the next incomplete step
  return props.completedSteps.includes(index) || index === Math.max(...props.completedSteps, -1) + 1
}

const handleStepClick = (index) => {
  if (canJumpTo(index)) {
    emit('jump', index);
  }
}
</script>

<style scoped>
.progress-container {
  margin-bottom: 3rem;
}

.progress-bar {
  height: 8px;
  background: #e2e8f0;
  border-radius: 10px;
  overflow: hidden;
  margin-bottom: 1.5rem;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #367e2d, #0b7d20);
  transition: width 0.4s ease;
  border-radius: 10px;
}

.steps-container {
  display: flex;
  justify-content: space-between;
  position: relative;
}

.step {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  opacity: 0.6;
  transition: opacity 0.3s ease;
}

.step.active {
  opacity: 1;
}

.step.completed {
  opacity: 1;
}

.step.clickable {
  cursor: pointer;
}

.step.clickable:hover .step-circle {
  transform: scale(1.1);
}

.step-circle {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: #e2e8f0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  color: #6300B3;;
  margin-bottom: 0.5rem;
  transition: all 0.3s ease;
  z-index: 1;
}

.step.active .step-circle {
  background: #6300B3;
  color: white;
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
}

.step.completed .step-circle {
  background: #10b981;
  color: white;
}

.step-label {
  font-size: 0.75rem;
  text-align: center;
  color: #64748b;
  font-weight: 500;
  max-width: 90px;
}

.step.active .step-label {
  color: #667eea;
  font-weight: 600;
}

.step.completed .step-label {
  color: #10b981;
}

@media (max-width: 768px) {
  .step-label {
    font-size: 0.65rem;
    max-width: 70px;
  }
  
  .step-circle {
    width: 32px;
    height: 32px;
    font-size: 0.9rem;
  }
}
</style>

<script setup>
import { ref } from 'vue';
import MultiStepForm from './components/MultiStepForm.vue';
</script>

<template>
  <div class="app-container">
    <h1>Job Application Form</h1>
    <MultiStepForm />
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
  padding: 2rem 1rem;
}

.app-container {
  /* max-width: 800px; */
  margin: 0 auto;
  padding: 2.5rem;
  border-radius: 16px;
  background: #ffffff;
}

.app-container h1 {
  text-align: center;
  color: #333;
  margin-bottom: 2rem;
  font-size: 2rem;
  font-weight: 600;
}

button {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  font-family: inherit;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

button.primary {
  background: #6300B3;
  color: white;
}

button.primary:hover:not(:disabled) {
  background: #5b01a5;;
  transform: translateY(-2px);
}

button.secondary {
  background: #e2e8f0;
  color: #334155;
}

button.secondary:hover:not(:disabled) {
  background: #cbd5e1;
}

input, select, textarea {
  width: 100%;
  padding: 0.75rem;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  font-size: 1rem;
  font-family: inherit;
  background: #fff;
  color: #475569;
}

input:focus, select:focus, textarea:focus {
  outline: none;
  border-color: #6c01c3;;
}

input.error, select.error, textarea.error {
  border-color: #ef4444;
}

label {
  display: block;
  margin-bottom: 0.5rem;
  color: #475569;
  font-weight: 500;
  font-size: 0.95rem;
}

.error-message {
  color: #ef4444;
  font-size: 0.875rem;
  margin-top: 0.25rem;
  display: block;
}

.form-group {
  margin-bottom: 1.5rem;
}

.success-message {
  background: #10b981;
  color: white;
  padding: 1rem;
  border-radius: 8px;
  text-align: center;
  margin: 1rem 0;
  font-weight: 500;
}

/* Responsive styles for diffrent screens */
@media (max-width: 768px) {
  .app-container {
    padding: 1.5rem;
  }
  
  .app-container h1 {
    font-size: 1.5rem;
  }
}

@media (min-width: 769px) {
  .app-container {
    max-width: 80vw;
    padding: 1.5rem;
  }
  
  .app-container h1 {
    font-size: 1.5rem;
  }
}
</style>
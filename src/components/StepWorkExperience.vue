<template>
  <div class="step-container">
    <h3 class="form-title">Work Experience</h3>
    
    <p class="step-description">
      Please provide your work experience details below.
    </p>

    <div
      v-for="(work, index) in localData"
      :key="index"
      class="experience-card"
    >
      <div class="card-header">
        <h3>Experience {{ index + 1 }}</h3>
        <button 
          type="button" 
          class="remove-btn" 
          @click="removeExperience(index)"
          title="Remove this experience"
        >
          X
        </button>
      </div>

      <!-- Company -->
      <div class="form-group">
        <label :for="'company' + index">Company:</label>
        <input
          type="text"
          placeholder="Company Name"
          :id="'company' + index"
          v-model="work.company"
          :class="{ error: errors[index]?.company }"
          @input="validateExperience(index, 'company')"
          required
        />
        <span v-if="errors[index]?.company" class="error-message">{{
          errors[index].company
        }}</span>
      </div>
      <!-- Role/Designation -->
      <div class="form-group">
        <label :for="'role' + index">Designation/Role:</label>
        <input
          type="text"
          placeholder="Your Role/Designation"
          :id="'role' + index"
          v-model="work.role"
          :class="{ error: errors[index]?.role }"
          @input="validateExperience(index, 'role')"
          required
        />
        <span v-if="errors[index]?.role" class="error-message">{{
          errors[index].role
        }}</span>
      </div>
      <!-- DUration -->
      <div class="date-row">
        <div class="form-group">
          <label :for="`start-${index}`"
            >Start Date <span class="required">*</span></label
          >
          <input
            :id="`start-${index}`"
            v-model="work.startDate"
            type="date"
            :class="{ error: errors[index]?.startDate }"
            @input="validateExperience(index, 'startDate')"
          />
          <span v-if="errors[index]?.startDate" class="error-message">{{
            errors[index].startDate
          }}</span>
        </div>

        <div class="form-group">
          <label :for="`end-${index}`"
            >End Date <span class="required">*</span></label
          >
          <input
            :id="`end-${index}`"
            v-model="work.endDate"
            type="date"
            :class="{ error: errors[index]?.endDate }"
            @input="validateExperience(index, 'endDate')"
          />
          <span v-if="errors[index]?.endDate" class="error-message">{{
            errors[index].endDate
          }}</span>
        </div>
      </div>

      <div class="form-group">
        <label :for="`description-${index}`">Description:</label>
        <textarea
          :id="`description-${index}`"
          v-model="work.description"
          rows="3"
          placeholder="Brief description of your responsibilities and achievements"
        ></textarea>
      </div>

    </div>

    <!-- Add Experience Button -->
    <button type="button" class="add-btn" @click="addExperience">
      Add Work Experience
    </button>
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
  import { ref, watch } from "vue";

  const props = defineProps({
    modelValue: {
      type: Object,
      required: true,
    },
  });

  const emit = defineEmits(["update:modelValue", "next", "prev"]);

  const localData = ref([...props.modelValue.work]);
  const errors = ref([]);

  // Watch for prop changes to update local data (for localStorage restoration)
  watch(
    () => props.modelValue.work,
    (newVal, oldVal) => {
      // Only update if length changed or initial load
      if (
        !oldVal ||
        newVal.length !== oldVal.length ||
        JSON.stringify(newVal) !== JSON.stringify(localData.value)
      ) {
        localData.value = [...newVal];
        // Only reset errors if array length changed
        if (!oldVal || newVal.length !== oldVal.length) {
          errors.value = newVal.map(() => ({}));
        }
      }
    }
  );

  const addExperience = () => {
    localData.value.push({
      company: "",
      role: "",
      startDate: "",
      endDate: "",
      description: "",
    });
    errors.value.push({});
  };

  const removeExperience = (index) => {
    localData.value.splice(index, 1);
    errors.value.splice(index, 1);
  };

  const validateExperience = (index, field) => {
    if (!errors.value[index]) {
      errors.value[index] = {};
    }

    errors.value[index][field] = "";
    const work = localData.value[index];

    switch (field) {
      case "company":
        if (!work.company?.trim()) {
          errors.value[index].company = "Company name is required";
        }
        break;
      case "role":
        if (!work.role?.trim()) {
          errors.value[index].role = "Role is required";
        }
        break;
      case "startDate":
        if (!work.startDate) {
          errors.value[index].startDate = "Start date is required";
        }
        break;
      case "endDate":
        if (!work.endDate) {
          errors.value[index].endDate = "End date is required";
        } else if (work.startDate && work.endDate < work.startDate) {
          errors.value[index].endDate = "End date must be after start date";
        }
        break;
    }
  };

  const validateAll = () => {
    // Allow skipping this section if no entries for freshers
    if (localData.value.length === 0) return true;

    localData.value.forEach((_, index) => {
      validateExperience(index, "company");
      validateExperience(index, "role");
      validateExperience(index, "startDate");
      validateExperience(index, "endDate");
    });

    console.log("Errors after validation:", errors.value);

    return !errors.value.some((err) =>
      Object.values(err).some((msg) => msg !== "")
    );
  };

  const handleNext = () => {
    console.log("Handle Next Clicked");
    if (validateAll()) {
      emit("update:modelValue", {
        ...props.modelValue,
        work: localData.value,
      });
      emit("next", true);
    }
  };

  const handlePrev = () => {
    emit("update:modelValue", {
      ...props.modelValue,
      work: localData.value,
    });
    emit("prev");
  };

  watch(
    localData,
    (newVal) => {
      emit("update:modelValue", {
        ...props.modelValue,
        work: newVal,
      });
    },
    { deep: true }
  );
</script>

<style scoped>
  .experience-card {
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
    width: 30%;
    height: 2.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.2rem;
    padding: 0;
  }

  .remove-btn:hover {
    background: #dc2626;
  }

  .date-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
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

  @media (max-width: 768px) {
    .date-row {
      grid-template-columns: 1fr;
    }
  }
</style>

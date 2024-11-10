<template>
  <div
    v-if="show"
    class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50"
  >
    <div class="bg-white p-6 rounded-md shadow-md w-full max-w-lg">
      <h2 class="text-xl font-semibold mb-4">{{ title }}</h2>

      <form @submit.prevent="onSubmit">
        <div v-for="(field, key) in fields" :key="key" class="mb-4">
          <label :for="key" class="block font-medium mb-1">{{
            field.form.title
          }}</label>

          <!-- Input field -->
          <input
            v-if="field.form.type === 'input'"
            :id="key"
            v-model="formData[key]"
            :placeholder="field.form.placeholder"
            class="border p-2 rounded w-full"
          />

          <!-- Range field -->
          <input
            v-if="field.form.type === 'range'"
            type="range"
            :id="key"
            v-model="formData[key]"
            :min="field.form.range.min"
            :max="field.form.range.max"
            class="w-full"
          />

          <!-- Select field -->
          <select
            v-if="field.form.type === 'select'"
            :id="key"
            v-model="formData[key]"
            class="border p-2 rounded w-full"
          >
            <option
              v-for="option in field.form.options"
              :key="option"
              :value="option"
            >
              {{ option }}
            </option>
          </select>

          <!-- Checkbox field -->
          <div v-if="field.form.type === 'checkbox'" class="flex items-center">
            <input
              type="checkbox"
              :id="key"
              v-model="formData[key]"
              class="mr-2"
            />
            <label :for="key">{{ field.form.label }}</label>
          </div>

          <!-- Tags field (array) -->
          <div v-if="field.form.type === 'tags'">
            <input
              v-for="(tag, index) in formData[key]"
              :key="index"
              v-model="formData[key][index]"
              placeholder="Введите тег"
              class="border p-2 rounded w-full mb-1"
            />
            <button
              type="button"
              @click="addTag(key)"
              class="text-blue-600 underline mt-1"
            >
              Добавить тег
            </button>
          </div>
        </div>

        <div class="flex justify-end mt-4">
          <button type="button" @click="closeModal" class="mr-4">Отмена</button>
          <button
            type="submit"
            class="bg-blue-500 text-white px-4 py-2 rounded"
          >
            Сохранить
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed } from "vue";

// Props
const props = defineProps({
  show: Boolean,
  title: String,
  settings: Object,
  initialData: Object,
});

// Emits
const emit = defineEmits(["close", "save"]);

// Local state for form data
const formData = ref({});

// Initialize formData based on initialData or default values
watch(
  () => props.settings,
  () => {
    formData.value = Object.keys(props.settings).reduce((acc, key) => {
      acc[key] =
        props.initialData[key] || props.settings[key]?.form?.default || "";
      return acc;
    }, {});
  },
  { immediate: true }
);

// Computed property for form fields
const fields = computed(() => props.settings);

// Close modal
const closeModal = () => {
  emit("close");
};

// Add a new tag in the tags field
const addTag = (key) => {
  formData.value[key] = formData.value[key] || [];
  formData.value[key].push("");
};

// Handle form submission
const onSubmit = () => {
  emit("save", formData.value);
  closeModal();
};
</script>

<style scoped>
/* Optional styling for the modal */
.fixed {
  z-index: 50;
}
</style>

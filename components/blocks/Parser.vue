<script setup>
const props = defineProps({
  settings: {
    type: Object,
    required: true,
  },
});

const models = ref({});
for (const [key, { form }] of Object.entries(props.settings)) {
  if (form?.type === "range")
    models.value[key] = [form.range.min, form.range.max];
  else models.value[key] = form?.default;
}
</script>

<template>
  <div class="flex flex-wrap">
    <div
      v-for="({ form, question, format }, key) in props.settings"
      :key="key"
      class="p-3 m-1 border-1 border-dashed"
    >
      <div>{{ form?.title }}:</div>

      <template v-if="form?.type">
        <div v-if="form.type === 'input'">
          <input v-model="models[key]" :placeholder="form.placeholder" />
        </div>

        <div v-if="form.type === 'checkbox'">
          <label>
            <input
              type="checkbox"
              v-model="models[key]"
              :value="form.default"
            />
            {{ form.label }}
          </label>
        </div>

        <div v-if="form.type === 'radio'">
          <label v-for="option in form.options" :key="option">
            <input type="radio" v-model="models[key]" :value="option" />
            {{ option }}
          </label>
        </div>

        <div v-if="form.type === 'range'" class="flex">
          <div>
            <input
              type="range"
              v-model.number="models[key][0]"
              :min="form.range.min"
              :max="form.range.max"
            />
            <div class="text-center">от {{ models[key][0] }}</div>
          </div>
          <div>
            <input
              type="range"
              v-model.number="models[key][1]"
              :min="form.range.min"
              :max="form.range.max"
            />
            <div class="text-center">до {{ models[key][1] }}</div>
          </div>
        </div>

        <div v-if="form.type === 'select'">
          <select multiple v-model="models[key]">
            <option
              v-for="option in form.options"
              :key="option"
              :value="option"
            >
              {{ option }}
            </option>
          </select>
        </div>

        <p v-if="question">Вопрос к АИ: {{ question }}</p>
        <p v-if="format">Формат: {{ format }}</p>
      </template>
    </div>
  </div>
</template>

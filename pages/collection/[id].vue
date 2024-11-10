<script setup>
const { data } = await useFetch(
  `http://localhost/api/summary/collection?id=${useRoute().params.id}`
);
console.log("data", data.value.data, useRoute().params.id);

const settings = data.value.data.settings;

const models = ref({});

for (const [key, { form }] of Object.entries(settings)) {
  if (form?.type === "range")
    models.value[key] = [form.range.min, form.range.max];
  else models.value[key] = form?.default;
}

//console.log("models", models.value);

const sendData = async () => {
  try {
    const response = await fetch("http://localhost/api/summary/analyze", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ models: models.value, settings }), // Обернули в объект с ключом data
    });
    const data = await response.json();
    console.log("Ответ от сервера:", data);
  } catch (error) {
    console.error("Ошибка при отправке данных:", error);
  }
};
</script>

<template>
  <div class="flex">
    <div class="flex">
      <div
        v-for="({ form }, key) in settings"
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
        </template>
      </div>
    </div>

    <div class="flex">
      <div>
        <button @click="sendData">Отправить данные</button>
        <pre>{{ models }}</pre>
      </div>
    </div>
  </div>
</template>

<!--
PowerBi для HR
Цель HR нанимать не лучших из потока подавших свою резюме на вакансию, а лучших из всей базы данных.
Фишка чат GPT, что он может пересказывать текст своими слова, делая почти человескую оценку текста
-->

<script setup>
const queryTemplate = {
  name: {
    key: "name",
    type: "string",
    query: "Верни ФИО кандидата",
    form: {
      type: "input",
      placeholder: "%ФИО%",
      title: "ФИО",
      default: "",
      searchable: {
        split_by: " ",
      },
    },
  },

  //   contacts1: {
  //     email: "Email кандидата",
  //     type: "String",
  //   },

  age: {
    key: "age",
    type: "integer",
    query: "Верни возраст кандидата",
    form: {
      type: "range",
      placeholder: "Возраст",
      title: "Возраст",
      range: {
        min: 5,
        max: 100,
      },
    },
  },
  city: {
    key: "city",
    city: "Город, где живет кандидат на русском языке",
    type: "string",
    form: {
      type: "input",
      title: "Город",
      default: "",
      placeholder: "%город%",
      searchable: true,
    },
    dictionary: true,
  },

  spheres: {
    key: "spheres",
    form: {
      type: "select",
      title: "Сфера проектов",
      options: ["Медицина", "GameDev", "Социальные сети", "Ecomerce", "Другие"],
      //default: [],
    },
    question:
      "Возьми весь опыт кандидата за 100%, разложи весь опыт кандидата по сферам %array% в % соотношении количеством времени, которое кандидат в них проработал, верни все это в формате %format%",
    format: " 'название сферы' => '% времени из общего опыта, который приходится на эту сферу' ",
    visualize: true,
  },

  management: {
    key: "management",
    type: "boolean",
    form: {
      title: "Опыт управления командами",
      type: "checkbox",
      label: "Есть",
      default: false,
    },
  },

  tech_skills: {
    key: "tech_skills",
    type: "array",
    form: {
      type: "tags",
      title: "Профессиональные скилы",
      default: "",
    },
    question: "Выведи все скилы кандидата в %format%",
    format: " 'Название скила' => 'Уровень скила от 1 до 5' ",
  },
};

const models = ref({});

for (const [key, { form }] of Object.entries(queryTemplate)) {
  if (form?.type === "range") models.value[key] = [form.range.min, form.range.max];
  else models.value[key] = form?.default;
}

//console.log("models", models.value);

const sendData = async () => {
  try {
    const response = await fetch("http://localhost:3000/backend", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ data: { models: models.value, query: queryTemplate } }), // Обернули в объект с ключом data
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
    <div v-for="({ form }, key) in queryTemplate" :key="key" class="p-3 m-1 border-1 border-dashed">
      <div>{{ form?.title }}:</div>

      <template v-if="form?.type">
        <div v-if="form.type === 'input'">
          <input v-model="models[key]" :placeholder="form.placeholder" />
        </div>

        <div v-if="form.type === 'checkbox'">
          <label> <input type="checkbox" v-model="models[key]" :value="form.default" /> {{ form.label }} </label>
        </div>

        <div v-if="form.type === 'radio'">
          <label v-for="option in form.options" :key="option">
            <input type="radio" v-model="models[key]" :value="option" /> {{ option }}
          </label>
        </div>

        <div v-if="form.type === 'range'" class="flex">
          <div>
            <input type="range" v-model.number="models[key][0]" :min="form.range.min" :max="form.range.max" />
            <div class="text-center">от {{ models[key][0] }}</div>
          </div>
          <div>
            <input type="range" v-model.number="models[key][1]" :min="form.range.min" :max="form.range.max" />
            <div class="text-center">до {{ models[key][1] }}</div>
          </div>
        </div>

        <div v-if="form.type === 'select'">
          <select multiple v-model="models[key]">
            <option v-for="option in form.options" :key="option" :value="option">{{ option }}</option>
          </select>
        </div>
      </template>
    </div>
  </div>

  <button @click="sendData">Отправить данные</button>
  <pre>{{ models }}</pre>
</template>

<!--
PowerBi для HR
Цель HR нанимать не лучших из потока подавших свою резюме на вакансию, а лучших из всей базы данных.
Фишка чат GPT, что он может пересказывать текст своими слова, делая почти человескую оценку текста
-->

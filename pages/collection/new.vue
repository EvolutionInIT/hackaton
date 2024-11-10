<script setup>
import ParserBlocks from "~/components/blocks/Parser.vue";

const saveCollection = async () => {
  const response = await fetch("http://localhost/api/summary/collection/save", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ settings, title: title.value }), // Обернули в объект с ключом data
  });
  const data = await response.json();
  //console.log("Ответ от сервера:", data, data.data.id);
  if (data.data.id) {
    setTimeout(() => {
      navigateTo({ name: "collection-id", params: { id: data.data.id } });
    }, 500);
  }
};

const title = ref("Новое название коллекции");

const settings = {
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
    format:
      " 'название сферы' => '% времени из общего опыта, который приходится на эту сферу' ",
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

//:to="{ name: 'collection-new' }"
</script>
<template>
  <div class="justify-center mb-4">
    <div class="mb-4">
      <label for="collection-title" class="block text-gray-700"
        >Название коллекции:</label
      >
      <input
        v-model="title"
        type="text"
        placeholder="Введите название коллекции"
        class="mt-1 block w-full p-2 border border-gray-300 rounded"
      />
    </div>

    <button
      @click="saveCollection"
      class="inline-block px-6 py-2 text-white bg-blue-500 rounded hover:bg-blue-600 transition duration-300"
    >
      Сохранить коллекцию
    </button>
  </div>

  <div class="flex">
    <div class="h-screen w-1/2 m-2 p-4 border-r border-gray-300 border-dashed">
      <div>Выбранные параметры</div>
    </div>

    <div class="h-screen w-1/2 m-2 p-4 border-r border-gray-300 border-dashed">
      <div>Доступные параметры</div>
      <ParserBlocks :settings="settings" />

      <div class="flex justify-center mb-4">
        <button
          class="inline-block px-6 py-2 text-white bg-blue-500 rounded hover:bg-blue-600 transition duration-300"
        >
          Добавить новый параметр
        </button>
      </div>
    </div>
  </div>
</template>

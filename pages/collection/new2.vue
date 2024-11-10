<script setup>
import { ref } from "vue";
import draggable from "vuedraggable";
import ParserBlocks from "~/components/blocks/Parser.vue";
//import ModalForm from "~/components/blocks/ModalForm.vue";

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

const tasksColumn1 = ref([]);
const tasksColumn2 = ref([]);

let i = 0;
Object.keys(settings).forEach((key) => {
  tasksColumn2.value.push({ [key]: settings[key], id: i++, key: key });
});

console.log(tasksColumn2.value);

const onDragEnd = () => {
  // Логика после перетаскивания, если необходимо
};

const saveCollection = async () => {
  let settingsFinal = {};
  const taskIds = tasksColumn1.value.map((task) => {
    console.log(task);
    settingsFinal = { ...settingsFinal, [task.key]: task[task.key] };
    return task.id;
  });
  console.log("ID задач из колонки 1:", taskIds, settingsFinal);

  const response = await fetch("http://localhost/api/summary/collection/save", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ settings: settingsFinal, title: title.value }), // Обернули в объект с ключом data
  });
  const data = await response.json();
  if (data.data.id) {
    setTimeout(() => {
      navigateTo({ name: "collection-id", params: { id: data.data.id } });
    }, 500);
  }
};

const debugAnalyze = ref("");
const testCollection = async () => {
  debugAnalyze.value = "";
  const response = await fetch("http://localhost/api/summary/analyze/test", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ settings }), // Обернули в объект с ключом data
  });
  const data = await response.json();
  debugAnalyze.value = data.data;
};

const title = ref("Новое название коллекции");

const clearElement = (elem) => {
  return { [elem.key]: elem[elem.key] };
};

const isModalOpen = ref(true);

// Function to open modal
const openModal = () => {
  isModalOpen.value = true;
};
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

    <div class="flex">
      <div>
        <button
          @click="saveCollection"
          class="px-6 py-2 text-white bg-blue-500 rounded hover:bg-blue-600 transition duration-300"
        >
          Сохранить коллекцию
        </button>
      </div>
      <div class="ml-auto">
        <button
          @click="testCollection"
          class="px-6 py-2 text-white bg-blue-500 rounded hover:bg-blue-600 transition duration-300"
        >
          Протестировать коллекцию!
        </button>

        <pre>
        {{ debugAnalyze }}
        </pre>
      </div>
    </div>
  </div>

  <div class="flex h-screen">
    <div class="w-1/2 p-4 border-r border-gray-300">
      <h2 class="text-lg font-bold">Выбранные параметры</h2>
      <draggable
        class="space-y-4 min-h-screen border-1 border-dashed"
        ghost-class="ghost"
        :force-fallback="true"
        v-model="tasksColumn1"
        item-key="id"
        group="tasks"
        @end="onDragEnd"
      >
        <template #item="{ element }">
          <ParserBlocks :settings="clearElement(element)" />
        </template>
      </draggable>
    </div>

    <div class="w-1/2 p-4">
      <h2 class="text-lg font-bold">
        Каталог общих и персанализированных параметров для профессий
      </h2>
      <draggable
        class="space-y-4 min-h-screen border-1 border-dashed"
        ghost-class="ghost"
        :force-fallback="true"
        v-model="tasksColumn2"
        item-key="id"
        group="tasks"
        @end="onDragEnd"
      >
        <template #item="{ element }">
          <ParserBlocks :settings="clearElement(element)" />
        </template>
      </draggable>
      <div class="flex justify-center mt-1">
        <button
          class="px-6 py-2 mt-1 text-white bg-blue-500 rounded hover:bg-blue-600 transition duration-300"
        >
          Добавить новый параметр
        </button>
      </div>

      <!-- <ModalForm
        :show="isModalOpen"
        title="Редактирование настроек"
        :settings="settings"
        :initialData="formData"
        @close="isModalOpen = false"
        @save="handleSave"
      /> -->
    </div>
  </div>
</template>

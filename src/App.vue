<script setup>
import "./main.css";
import { ref, watch, computed, onMounted } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return (a.createAt = b.createAt);
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

const removeTodo = (todo) => {
  todos.value = todos.value.filter((val) => val !== todo);
};
</script>
<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Opa! Salve
        <input
          type="text"
          placeholder="Seu nome aqui"
          id="name"
          v-model="name"
        />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE TODO</h3>
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>O que fazer?</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="ex. Dar banho no gato"
          v-model="input_content"
        />
        <h4>Selecione uma Categoria</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category-1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Profissional</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              id="category-2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Pessoal</div>
          </label>
        </div>

        <input type="submit" value="Adicionar" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todos_asc"
          :key="todo.cretaedAt"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Excluir</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

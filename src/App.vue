<template>
  <main class="app">
    <section class="create-todo">
      <h3>CREATE TODOS</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list</h4>
        <input
          type="text"
          placeholder=" Make a ....."
          v-model="input_content"
        />
        <p class="text_err">{{ err_content }}</p>
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="learning"
              v-model="input_category"
            />
            <span class="bubble learning"></span>
            <div>Learning</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="sport"
              v-model="input_category"
            />
            <span class="bubble sport"></span>
            <div>Sport</div>
          </label>
        </div>
        <p class="text_err">{{ err_category }}</p>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div
          v-for="(todo, index) in todo_asc"
          :class="`todo-item ${todo.done && 'done'}`"
          :key="index"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="handleRemoveTodo(todo)">
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
import { ref, watch, onMounted, computed } from "vue";

let todos = ref([]);
let name = ref("");

const input_content = ref("");
const input_category = ref(null);
const err_content = ref("");
const err_category = ref("");

const todo_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createAt - a.createAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "") {
    err_content.value = "ban can nhap todo";
  }
  if (input_category.value === null) {
    err_category.value = "ban can chon 1 trong 2 category";
  }
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createAt: new Date().getTime(),
  });

  input_category.value = null;
  input_content.value = "";
  err_content.value = "";
  err_category.value = "";
};

const handleRemoveTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<script setup lang="ts">
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([
  {
    content: "",
    priority: null,
    done: false,
    createdAt: new Date().getTime(),
  },
]);

const name = ref("");

const input_content = ref("");
const input_priority = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() == "" || input_content.value == null) return;

  todos.value.push({
    content: input_content.value,
    priority: input_priority.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = "";
  input_priority.value = null;
};

watch(
  todos,
  (newValue) => {
    localStorage.setItem("todos", JSON.stringify(newValue));
  },
  { deep: true }
);

watch(name, (newValue) => {
  localStorage.setItem("name", newValue);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || "[]");
});

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title">
        What's Up, <input type="text" placeholder="Name here" v-model="name" />
      </h1>
    </section>

    <section class="create-todo">
      <h3>CREATE TO DO</h3>
      <form @submit.prevent="addTodo">
        <h3>What's in your todo list?</h3>
        <input
          type="text"
          placeholder="e.g. make a video "
          v-model="input_content"
        />

        <h4>Pick a priority</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="priority"
              id="priority"
              value="low"
              v-model="input_priority"
            />
            <span class="bubble low"></span>
            <div>Low</div>
          </label>

          <label>
            <input
              type="radio"
              name="priority"
              id="priority"
              value="medium"
              v-model="input_priority"
            />
            <span class="bubble medium"></span>
            <div>Medium</div>
          </label>

          <label>
            <input
              type="radio"
              name="priority"
              id="priority"
              value="high"
              v-model="input_priority"
            />
            <span class="bubble high"></span>
            <div>High</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" class="todo-item">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <spam :class="`bubble ${todo.priority}`"></spam>
          </label>

          <div :class="`todo-content ${todo.done}`">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

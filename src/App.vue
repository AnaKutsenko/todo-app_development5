<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]); // Array for storing the to-do list
const name = ref(""); // Variable to store username

const input_content = ref(""); // Variable for containing the new task
const input_category = ref(null); // Variable for the category of the new task

// Computed property for the sorted to-do list
const todos_asc = computed(() =>
  todos.value.slice().sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

// Watch name changes and save them to localStorage
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

// Watch changes to the to-do list and save them in localStorage
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

// Function for adding a new case
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

// Function for deleting a case
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo); // Remove the case from the list
};

onMounted(() => {
  name.value = localStorage.getItem("name") || ""; // Load the username
  todos.value = JSON.parse(localStorage.getItem("todos")) || []; // Load the to-do list
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,
        <input type="text" id="name" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <!-- Form for creating a new task -->
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="e.g. make a video"
          v-model="input_content"
        />

        <h4>Pick a category</h4>
        <div class="options">
          <!-- Selecting a category for a new task -->
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todos_asc"
          :key="todo.createdAt"
          :class="['todo-item', { done: todo.done }]"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span
              :class="`bubble ${
                todo.category == 'business' ? 'business' : 'personal'
              }`"
            ></span>
          </label>

          <div class="todo-content">
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

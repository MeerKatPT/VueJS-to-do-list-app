<script setup>
//ref to hand our state
//onMounted hook to display what we need
//computed to order things by date
//watch - check any changes on our references
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);

// give back an ascending list by date
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  // if the fields are empty or have no value just returns the function
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  //push to the array and get the time, to then be able to set it ascending
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
};

//watching changes in the "todos" and updates the local storage with the new value
watch(
  todos,
  (newTodo) => {
    localStorage.setItem("todos", JSON.stringify(newTodo));
  },
  { deep: true }
);

// watching the name and if newName changes, sets localstore to that new name too.
watch(name, (newName) => {
  localStorage.setItem("name", newName);
});

//To save the name, pull it from onMounted
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || []);
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,<input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          placeholder="E.g. Make a new project with PHP"
          v-model="input_content"
        />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
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
      <div class="list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        ></div>
        <label>
          <input type="checkbox" v-model="todo.done" />
          <span :class="`bubble ${todo.category}`"></span>
        </label>
      </div>
    </section>
  </main>
</template>

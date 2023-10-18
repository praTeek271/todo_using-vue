<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up,
        <input
          type="text"
          id="name"
          placeholder="Name here"
          v-model="name"
          style="font-family: Poppins, sans-serif;
          font-style: italic;
          -webkit-text-stroke-width: 0.5px;
          -webkit-text-stroke-color: var(--grey);
          color: #95949417;
		  text-transform:uppercase;"
        />
      </h2>
    </section>

    <section class="create-todo">
      <form id="new-todo-form" @submit.prevent="addTodo">
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

      <div v-for="category in categories" :key="category.id" :class="category.name" style="margin: 29px 0 47px 0;">

        <h4 style="box-shadow: -5px -5px 20px 5px #00000063;padding: 10px 0 10px 23px;border-radius: 9px;">{{ category.name }}</h4>

        <div v-for="todo in getTodosByCategory(category.name)" :key="todo.id" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category == 'business' ? 'business' : 'personal'}`"></span>
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

<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');
const input_content = ref('');
const input_category = ref(null);

const categories = ref([{ id: 1, name: 'business' }, { id: 2, name: 'personal' }]);

const todos_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt));

const capitalizedName = computed(() => {
  return name.value.charAt(0).toUpperCase() + name.value.slice(1);
});

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = ''; // Clear the input field
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

const getTodosByCategory = (category) => {
  return todos.value.filter((todo) => todo.category === category);
};

onMounted(() => {
  name.value = localStorage.getItem('name') || prompt('Enter your name to create your personalized To-Do list:');
  localStorage.setItem('name', name.value);

  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<style scoped>
  /* Add your CSS styles here */
</style>

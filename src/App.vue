<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.created_at - a.created_at;
}))

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    created_at: Date.now()
  });

  input_content.value = '';
  input_category.value = null;
};

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, { deep: true });

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo);
};

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title">
        <span>What's up, </span>
        <input v-model="name" type="text" placeholder="Name here" class="name-input" />
      </h1>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <label>What's on your todo list?</label>
        <input v-model="input_content" type="text" placeholder="e.g. make a video" class="todo-input" />

        <h4>Pick a category</h4>
        <div class="options">
          <label :class="{ selected: input_category === 'business' }">
            <input type="radio" name="category" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label :class="{ selected: input_category === 'personal' }">
            <input type="radio" name="category" value="personal" v-model="input_category">
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
        <div v-for="todo in todos_asc" :key="todo.created_at" class="todo-item">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.done ? 'done' : ''} ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" :class="{ done: todo.done }" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>  
  </main>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: system-ui, -apple-system, Segoe UI, Roboto, sans-serif;
}

body {
  background: #f3f4f6;
  display: flex;
  justify-content: center;
}

/* APP CONTAINER */
.app {
  width: 420px;
  min-height: 100vh;
  padding: 25px;
}

/* GREETING */
.greeting {
  margin-bottom: 20px;
}

.title {
  font-size: 22px;
  font-weight: 600;
  color: white;
}

.name-input {
  border: none;
  outline: none;
  font-weight: 600;
  font-size: 22px;
  color: white;
  background: transparent;
}

/* CREATE TODO CARD */
.create-todo {
  background: #ffffff;
  padding: 20px;
  border-radius: 14px;
  margin-bottom: 20px;
}

.create-todo h3 {
  color: black;
  font-size: 14px;
  font-weight: 700;
  margin-bottom: 15px;
}

.create-todo label {
  color: black;
  font-size: 14px;
}

/* INPUT */
.todo-input {
  width: 100%;
  padding: 12px;
  margin-top: 8px;
  margin-bottom: 18px;
  border-radius: 10px;
  border: none;
  background: #e5e7eb;
  color: black;
  font-size: 14px;
}

.todo-input::placeholder {
  color: #6b7280;
}

/* CATEGORY OPTIONS */
.options {
  display: flex;
  gap: 12px;
  margin-bottom: 18px;
}

.options label {
  flex: 1;
  background: #e5e7eb;
  padding: 16px;
  border-radius: 10px;
  cursor: pointer;
  text-align: center;
  border: 2px solid transparent;
  transition: 0.2s;
  color: black;
}

.options label.selected {
  border: 2px solid #ec4899;
  background: #fff;
}

.options input {
  display: none;
}

/* CATEGORY DOTS */
.bubble {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  display: inline-block;
  margin-bottom: 6px;
}

.business {
  background: #3b82f6;
}

.personal {
  background: #ec4899;
}

/* ADD BUTTON */
.create-todo input[type="submit"] {
  width: 100%;
  padding: 12px;
  background: #ec4899;
  border: none;
  border-radius: 10px;
  color: white;
  font-weight: 600;
  cursor: pointer;
}

.create-todo input[type="submit"]:hover {
  background: #db2777;
}

/* TODO LIST */
.todo-list h3 {
  color: #6b7280;
  font-size: 14px;
  margin-bottom: 10px;
}

/* LIST */
.list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

/* TODO ITEM */
.todo-item {
  background: #ffffff;
  padding: 12px;
  border-radius: 10px;

  display: flex;
  align-items: center;
  justify-content: space-between;
}

/* LEFT SIDE */
.todo-item label {
  display: flex;
  align-items: center;
  gap: 10px;
}

/* CHECKBOX */
.todo-item input[type="checkbox"] {
  width: 18px;
  height: 18px;
  cursor: pointer;
}

/* CONTENT */
.todo-content {
  flex: 1;
  margin-left: 10px;
}

.todo-content input {
  width: 100%;
  border: none;
  outline: none;
  font-size: 15px;
  color: black;
  background: transparent;
}

.todo-content input.done {
  text-decoration: line-through;
  color: #6b7280;
}

/* DELETE BUTTON */
.delete {
  background: #ef4444;
  border: none;
  padding: 8px 14px;
  color: white;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
}

.delete:hover {
  background: #dc2626;
}
</style>

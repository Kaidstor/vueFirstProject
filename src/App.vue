<script setup>
  import {ref, onMounted, computed, watch} from "vue";

  const todos = ref([]);
  const name = ref('');

  const input_content = ref('');
  const input_category = ref(null);

  // eslint-disable-next-line vue/no-side-effects-in-computed-properties
  const todos_asc = computed(() => todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  }))

  const addTodo = () => {
    if (input_content.value.trim() === '' || input_category.value === null) {
      return
    }

    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime()
    })
    console.log('addTodo');
  }

  const removeTodo = todo => {
    todos.value = todos.value.filter(t => t !== todo)
  }

  watch(todos, newVal => {
    localStorage.todos = JSON.stringify(newVal)
  }, {deep: true})
  watch(name, newVal => {
    localStorage.setItem('name', newVal)
  })
  onMounted(() => {
    name.value = localStorage.name || '';
    todos.value = JSON.parse(localStorage.todos) || []
  })
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Привет, <input type="text" placeholder="ваше имя" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>Ваши задачи:</h4>
        <input
            type="text"
            placeholder="Задачка пишется тут"
            v-model="input_content" />
        <h4>Выберите категорию: </h4>
        <div class="options">
          <label>
            <input
                type="radio"
                name="category"
                value="Работа"
                v-model="input_category"/>
            <span class="bubble business"></span>
            <div>Работа</div>
          </label>
          <label>
            <input
                type="radio"
                name="category"
                value="Личные"
                v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Личные</div>
          </label>
        </div>
        <input type="submit" value="Добавить задачу">
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO СПИСОК</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>


<script>
export default {
  data() {
    return {
      todos: []
    }
  },
  methods: {
    addTodo(e) {
      const title = e.target.value.trim();
      if(!title) return;
      this.todos.push({id: Date.now(), title, completed: false});
      e.target.value = "";
    },
    removeTodo(todo) {
      this.todos = this.todos.filter(t => t.id !== todo.id);
    }
  }
}
</script>

<template>
  <section class="todoapp">
    <header class="header">
      <h1>todos</h1>
      <input type="text" class="new-todo" @keyup.enter="addTodo" autofocus placeholder="What needs to be done?">
    </header>
    <section class="main">
      <input type="text" class="toggle-all">
      <label for="toggle-all">Mark all as completed</label>
      <ul class="todo-list">
        <li class="todo" v-for="todo in todos">
          <div class="view">
            <input type="text" class="toggle">
            <label for="toggle">{{ todo.title }}</label>
            <button class="destroy" @click="removeTodo(todo)"></button>
          </div>
          <input type="text" class="edit">
        </li>
      </ul>
    </section>
    <footer class="footer" v-if="todos.length">
      <span class="todo-count">
        <strong></strong>
        <span></span>
      </span>
      <ul class="filters">
        <li><a href="#/all" :class="{ selected: visibility === 'all' }">All</a></li>
        <li><a href="#/active" :class="{ selected: visibility === 'active'}" >Active</a></li>
        <li><a href="#/completed" :class="{ selected: visibility === 'completed' }">Completed</a></li>
      </ul>
      <button class="clear-completed">Clear completed</button>
    </footer>
  </section>
</template>

<style>
@import "https://unpkg.com/todomvc-app-css@2.4.1/index.css";
</style>

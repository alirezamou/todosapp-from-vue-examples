<script>
const STORAGE_KEY = "todos_key";
const filters = {
  all: todos => todos,
  active: todos => todos.filter(todo => !todo.completed),
  completed: todos => todos.filter(todo => todo.completed)
};
export default {
  data() {
    return {
      todos: JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]"),
      visibility: 'all',
      editedTodo: null
    }
  },
  watch: {
    todos: {
      handler(todos) {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
      },
      deep: true
    }
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },
    filteredTodos() {
      return filters[this.visibility](this.todos);
    }
  },
  mounted() {
    window.addEventListener('hashchange', this.onHashChange);
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
    },
    toggleAll(e) {
      this.todos.forEach(todo => todo.completed = e.target.checked)
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    },
    onHashChange() {
      const filter = location.hash.replace("#", "");
      this.visibility = filter;
    },
    editTodo(todo) {
      this.editTitleBefore = todo.title;
      this.editedTodo = todo;
    },
    doneEdit(todo) {
      if(!this.editedTodo) return;
      this.editedTodo = null;
      if(todo.title.trim() === "")
        this.removeTodo(todo);
    },
    cancelEdit(todo) {
      todo.title = this.editTitleBefore;
      this.editedTodo = null;
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
      <input 
        type="checkbox" 
        id="toggle-all" 
        class="toggle-all" 
        @change="toggleAll"
        :checked="remaining === 0"
        >
      <label for="toggle-all">Mark all as completed</label>
      <ul class="todo-list">
        <li class="todo" v-for="todo in filteredTodos" :class="{completed: todo.completed, editing: (todo === editedTodo) }">
          <div class="view">
            <input type="checkbox" class="toggle" v-model="todo.completed">
            <label for="toggle" @dblclick="editTodo(todo)">{{ todo.title }}</label>
            <button class="destroy" @click="removeTodo(todo)"></button>
          </div>
          <input 
            type="text" 
            class="edit" 
            v-model="todo.title" 
            v-if="todo === editedTodo" 
            @keyup.enter="doneEdit(todo)" 
            @keyup.escape="cancelEdit(todo)"
            @blur="cancelEdit(todo)"
            @vue:mounted="({ el }) => el.focus()">
        </li>
      </ul>
    </section>
    <footer class="footer" v-if="todos.length">
      <span class="todo-count">
        <strong>{{ remaining }}</strong>
        <span>{{ remaining > 1 ? ' Items' : ' Item' }} left</span>
      </span>
      <ul class="filters">
        <li><a href="#all" :class="{ selected: visibility === 'all' }">All</a></li>
        <li><a href="#active" :class="{ selected: visibility === 'active'}" >Active</a></li>
        <li><a href="#completed" :class="{ selected: visibility === 'completed' }">Completed</a></li>
      </ul>
      <button class="clear-completed" @click="clearCompleted" v-if="todos.length > remaining">Clear completed</button>
    </footer>
  </section>
</template>

<style>
@import "https://unpkg.com/todomvc-app-css@2.4.1/index.css";
</style>

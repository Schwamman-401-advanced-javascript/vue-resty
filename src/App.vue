<template>
  <div id="app">
    <h1>TODOS:</h1>
    <input type="text" class="todo-input" placeholder="Add new item" v-on:keyup.enter="addTodo">
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        <label>
          <input type="checkbox" class="done-checkbox" v-model="todo.done">
          <span>&nbsp;</span>
          <del v-if="todo.done">{{ todo.text }}</del>
          <span v-else>{{ todo.text }}</span>
          <div class="space"></div>
          <button class="delete-btn" v-on:click="removeTodo(todo.id)">X</button>
        </label>
      </li>
    </ul>
  </div>
</template>

<script>

export default {
  name: 'app',
  data: () => {
    return { todos: [{text: 'todo 1', done: false, id: Date.now()}]
           }
  },
  methods: {
    addTodo({target}){
      this.todos.push({text: target.value, done: false, id: Date.now()})
      target.value = ''
    },
    removeTodo(id) {
      this.todos = this.todos.filter(todo => todo.id !== id)
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

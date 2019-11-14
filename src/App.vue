<template>
  <div id="app">
    <h2>There are {{numItems}} items to complete!</h2>
    <form @submit.prevent="addTodo">
      <label>
        <span>To Do Item</span>
        <input
          type="text"
          placeholder="Add To Do List Item"
          v-model="item.text"
        />
      </label>
      <label>
        <span>Difficulty Rating</span>
        <input type="range" min="1" max="5" v-model="item.difficulty" defaultValue="3" />
      </label>
      <label>
        <span>Assigned To</span>
        <input type="text" v-model="item.assignee" placeholder="Assigned To" />
      </label>
      <label>
        <span>Due</span>
        <input type="date" v-model="item.due" />
      </label>
      <button type="submit">Add Item</button>
    </form>
    <ul>
      <li v-for="todo in todoList" :key="todo.id">
        <label>
          <input type="checkbox" class="done-checkbox" v-on:click="setItemToDone(todo._id)">
          <span>&nbsp;</span>
          <del v-if="todo.complete">{{ todo.text }}</del>
          <span v-else>{{ todo.text }}</span>
          <div class="space"></div>
          <button class="delete-btn" v-on:click="removeItem(todo._id)">X</button>
        </label>
      </li>
    </ul>
  </div>
</template>

<script>
const superagent = require('superagent');

export default {
  name: 'app',
  data: () => {
    return { todoList: [],
             numItems: 0,
             item: { 
                      text: undefined,
                      due: undefined,
                      difficulty: 3,
                      complete: false,
                      assignee: undefined
                   }
           }
  },
  methods: {
    addTodo({target}){
      superagent
        .post('https://api-js401.herokuapp.com/api/v1/todo/')
        .send(this.item)
          .then(() => { superagent.get('https://api-js401.herokuapp.com/api/v1/todo/')
                          .then(response => {this.todoList = response.body.results})
                      });
      target.value = '';
      
    },
    removeItem(id) {
      superagent
        .delete(`https://api-js401.herokuapp.com/api/v1/todo/${id}`)
          .then(() => { superagent.get('https://api-js401.herokuapp.com/api/v1/todo/')
                          .then(response => {this.todoList = response.body.results})
                      });
    },
    setItemToDone(id) {
      let updatedItem = this.todoList.find(item => item._id === id);
      if(updatedItem._id) {
        updatedItem = {...updatedItem, complete: !updatedItem.complete};
        superagent
          .put(`https://api-js401.herokuapp.com/api/v1/todo/${id}`)
          .send(updatedItem)
            .then(() => { superagent.get('https://api-js401.herokuapp.com/api/v1/todo/')
                          .then(response => {this.todoList = response.body.results})
                      });
      }
    }
  },
  mounted() {
    superagent.get('https://api-js401.herokuapp.com/api/v1/todo/')
      .then(response => {this.todoList = response.body.results})
  },
  updated() {
    superagent.get('https://api-js401.herokuapp.com/api/v1/todo/')
      .then(response => {this.todoList = response.body.results})
      .then(() => {this.numItems = this.todoList.filter( item => !item.complete ).length})
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

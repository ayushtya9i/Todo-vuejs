<template>
  <div id="app">
    <div class="wrapper">
      <h1 class="text--heading__normal">Vue.js Todo List</h1>
    <div class="input-container">
      <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new todo">
      <button @click="addTodo">Add</button>
    </div>
    <ul>
      <li class="todo-list-item" v-for="(todo, index) in todos"  :key="index">
      
        <div class="todo-list-item--content"> 
          <input type="checkbox" v-model="todo.completed" @change="updateTodo(todo)">
          <span :class="{ 'completed': todo.completed }">{{ todo.text }}</span>
      <small>Created at: {{ todo.createdAt }}</small>
      </div>
        <button class="error" @click="removeTodo(todo.id)">Remove</button>
      </li>
      <p v-if="error" class="error">{{ error }}</p>
    </ul>
    <h2 class="text--heading__green">Completed Tasks</h2>
    <ul>
      <li class="todo-list-item__completed" v-for="(todo, index) in completedTodos" :key="index">
        <span>{{ todo.text }}</span>
      </li>
    </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    
    return {
      newTodo: '',
      todos: [],
      error: '',
    };
  },
  computed: {
    completedTodos() {
      return this.todos.filter(todo => todo.completed);
    }
  },
  mounted() {
    this.loadTodos();
  },
  methods: {
    async addTodo() {
      // to check if the input is empty or not
      if (this.newTodo.trim() === '') {
        this.error = 'Task cannot be empty!';
        setTimeout(() => {
          this.error = '';
        }, 2500);
        return;
      }
      const todo = {
        text: this.newTodo,
        completed: false,
        createdAt: new Date().toLocaleString()
      };
      try {
        // adds todo 
        const response = await axios.post('http://localhost:3001/todos', todo);
        this.todos.push(response.data);
        this.error = '';
         this.saveTodos();
      } catch (error) {
        console.error('Error adding todo:', error);
      }
      this.newTodo = '';
    },
    async updateTodo(todo) {
      try {
        // updates todo
        await axios.put(`http://localhost:3001/todos/${todo.id}`, todo);
         this.saveTodos();
      } catch (error) {
        console.error('Error updating todo:', error);
      }
    },
    async removeTodo(id) {
      try {
        // deletes todo
        await axios.delete(`http://localhost:3001/todos/${id}`);
        this.todos = this.todos.filter(todo => todo.id !== id);
        this.saveTodos();
      } catch (error) {
        console.error('Error deleting todo:', error);
      }
    },
    // function to save and load todos to/from the local storage
    async loadTodos() {
      try {
        const savedTodos = localStorage.getItem('todos');
        if (savedTodos) {
          this.todos = JSON.parse(savedTodos);
        }
      } catch (error) {
        console.error('Error loading todos:', error);
      }
    },
    saveTodos() {
      localStorage.setItem('todos', JSON.stringify(this.todos));
    }
  }
 
};
</script>
  
<style scoped>

#app {
  display:flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;
  padding: 20px;
  color:black;
}
.text--heading__normal{
   background: #121FCF;
background: -webkit-linear-gradient(to right, #121FCF 0%, #CF1512 100%);
background: -moz-linear-gradient(to right, #121FCF 0%, #CF1512 100%);
background: linear-gradient(to right, #121FCF 0%, #CF1512 100%);
background-clip: text;
-webkit-background-clip: text;
-webkit-text-fill-color: transparent;

}
.text--heading__green{
background: #1DCF29;
background: -webkit-linear-gradient(to right, #1DCF29 0%, #3B1DCF 100%);
background: -moz-linear-gradient(to right, #1DCF29 0%, #3B1DCF 100%);
background: linear-gradient(to right, #1DCF29 0%, #3B1DCF 100%);
background-clip: text;
-webkit-background-clip: text;
-webkit-text-fill-color: transparent;

}
.wrapper{
  max-width: 600px;
  padding:20px;
  box-shadow: 17px 17px 0px 0px #EBEBEB ;
  border:#0056b3 1px solid;
}
.input-container {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  gap:2.5em;
}

.todo-list-item{
  box-shadow: 17px 17px 0px 0px #EBEBEB ;
  padding:15px;
 background: #FF5F6D;  /* fallback for old browsers */
background: -webkit-linear-gradient(to right, #FFC371, #FF5F6D);  /* Chrome 10-25, Safari 5.1-6 */
background: linear-gradient(to right, #FFC371, #FF5F6D); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */

  border-radius: 12px;
}
.todo-list-item__completed{
  box-shadow: 17px 17px 0px 0px #EBEBEB ;
  padding:15px;
 background: #75da47;  /* fallback for old browsers */
background: -webkit-linear-gradient(to right, #FFC371, #75e661);  /* Chrome 10-25, Safari 5.1-6 */
background: linear-gradient(to right, #FFC371, #a9ff8f); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */

  border-radius: 12px;
}
.todo-list-item--content{
  display: flex;
  gap:1em;
  align-items: center;
}
.error {
  color: red;
  margin-top: 5px;
}


.input-container input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.input-container button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.input-container button:hover {
  background-color: #0056b3;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin-bottom: 10px;
}

.completed {
  text-decoration: line-through;
}
</style>

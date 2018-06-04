<template>
  <div class="container">
    <h1>The List</h1>
    <input class="create" type="text" v-model="todo" @keyup.enter="createTodo">
    <ul>
      <li 
        v-for="todo in todos" :key="todo"
        v-if="todo.status == mode"
        :class="{active: todo.status==false,completed: todo.status==true}">
        <input type="checkbox" v-model="todo.status">
            {{ todo.title }} 
        <button @click="deleteTodo(todo)"></button>
      </li>
      <li 
        v-for="todo in todos" :key="todo"
        v-if="mode===2"
        :class="{active: todo.status==false,completed: todo.status==true}">
        <input class="iscomplete" type="checkbox" v-model="todo.status" @click="toggleTodo(todo)">
            {{ todo.title }} 
        <button class="delete" @click="deleteTodo(todo)"></button>
      </li>
    </ul>
    <button @click="mode = 2">all</button>
    <button @click="mode = false">active</button>
    <button @click="mode = true">completed</button>
  </div>
</template>


<script>
export default {
  data: function() {
    return {
      todo: "",
      mode: 2,
      todos: []
    };
  },
  watch: {
    todos: {
      handler: function(todos) {
        this.$http
          .put("https://the-list-3212e.firebaseio.com/data.json", todos)
          .then(response => console.log(response), err => console.log(err));
      },
      deep: true
    }
  },
  methods: {
    createTodo: function() {
      var newTodo = this.todo;
      this.todos.push({
        title: newTodo,
        status: false
      });
      this.todo = "";
    },
    deleteTodo: function(to) {
      this.todos.splice(this.todos.indexOf(to), 1);
    },
    toggleTodo: function(to) {
      var status = this.todos[this.todos.indexOf(to)].status;
      status == false ? (status = true) : (status = false);
      this.todos[this.todos.indexOf(to)].status = status;
    }
  },
  created() {
    this.$http
      .get("https://the-list-3212e.firebaseio.com/data.json")
      .then(res => {
        return res.json();
      })
      .then(data => {
        for (let i = 0; i < data.length; i++) {
          let status = data[i].status;
          let title = data[i].title;
          this.todos.push({
            title,
            status
          });
        }
        console.log(data.length);
      });
  }
};
</script>

<style>
.container {
  text-align: center;
}
h1 {
  font-size: 5em;
  font-stretch: ultra-expanded;
}
.create {
  width: 60%;
  height: 15vh;
  font-size: 3em;
  text-align: center;
}
ul {
  margin: 0;
  padding: 0;
}
li {
  width: 60%;
  height: 12vh;
  list-style: none;
  font-size: 3em;
  border: 1px solid grey;
  margin: 0px auto;
  padding: 0px;
}
.iscomplete {
  float: left;
  height: 20px;
  width: 20px;
}
.delete {
  float: right;
  height: 20px;
  width: 20px;
}
.completed {
  text-decoration-line: line-through;
}

.active {
  text-decoration: none;
}
</style>


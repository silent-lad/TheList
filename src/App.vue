<template>
  <div class="container">
    <!-- <input type="text" v-model="pass" @keyup.enter="passwordCheck(pass)" v-show="!admin"> -->
    <div class="protect" v-show="admin">
      <h1>The List</h1>
      <input class="create" type="text" v-model="todo" @keyup.enter="createTodo" placeholder="What to do NEXT?">
      <ul>
        <li v-for="(todo,key) in todos" :key="key" v-if="todo.status == mode" :class="{active: todo.status==false,completed: todo.status==true}" @dblclick="toggleTodo(todo)">
          <input class="iscomplete check" type="checkbox" v-model="todo.status">
          <!-- <span style="float: left;">{{++key}}.</span> -->
          <span class="item">{{ todo.title }}</span>

          <button class="delete" @click="deleteTodo(todo)"></button>
        </li>
        <li v-for="(todo,key) in todos" :key="key" v-if="mode===2" :class="{active: todo.status==false,completed: todo.status==true}" @dblclick="toggleTodo(todo)">
          <!-- <input class="iscomplete check" type="checkbox" v-model="todo.status" @click="toggleTodo(todo)"> -->
          <!-- <span style="float: left;">{{++key}}.</span> -->
          <span class="item">{{ todo.title }}</span>
          <button class="delete" @click="deleteTodo(todo)"></button>
        </li>
      </ul>
      <div class="buttonPallete">
        <button class="btn-special" @click="mode = 2">all</button>
        <button class="btn-special" @click="mode = false">active</button>
        <button class="btn-special" @click="mode = true">completed</button>
      </div>
    </div>

  </div>
</template>


<script>
export default {
  data: function() {
    return {
      todo: "",
      mode: 2,
      todos: [],
      // password: "dinopanda",
      pass: "",
      admin: true
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
    passwordCheck: function(pass) {
      if (pass == this.password) {
        this.admin = true;
      }
    },
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
html,
body {
  background-color: black;
  color: white;
}
.container {
  text-align: center;
}

.delete {
  background-color: black;
  border: 2px solid white;
  /* padding: 15px 32px; */
  margin-top: 2px;
}
h1 {
  font-size: 5em;
  font-stretch: ultra-expanded;
}
input[type="text"] {
  background-color: black;
  width: 60%;
  height: 50%;
  font-size: 3em;
  text-align: center;
  color: white;
  outline: none;
  border-top: 0;
  border-right: 0;
  border-bottom: 5px solid white;
  border-left: 0;
  margin-bottom: 50px;
}
input[type="text"]:focus {
  outline: none;
}

.btn-special {
  background-color: grey;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  /* text-decoration: none; */
  display: inline-block;
  font-size: 16px;
  margin: 10px;
  box-shadow: 2px 2px 4px white;
  transition: 0.3s all;
}
.btn-special:focus {
  outline: none;
  /* transform: translate(2px, 2px); */
  /* box-shadow: none; */
  background-color: #333;
}
.btn-special:hover {
  transform: translate(-2px, -2px);
}

ul {
  margin: 0;
  padding: 0;
}
li {
  overflow-y: auto;
  width: 60%;
  list-style: none;
  font-size: 3em;
  border: 1px solid grey;
  box-shadow: 2px 2px 2p #999;
  margin: 0px auto;
  padding: 10px;
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


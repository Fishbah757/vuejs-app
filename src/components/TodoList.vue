<template>
  <div>
    <div class='create-border ui centered card'>
      <h1>{{ msg }}</h1>
      <div class='content'>
        <input placeholder="Title" type="text" v-model="newTodoTitle" />
        <input placeholder="Description" type="text" v-model="newTodoDesc" />
        <datepicker v-model="newTodoDate"></datepicker>
        <button class='ui basic blue button' @click="addTodo">Create</button>
      </div>
    </div>
    <div class="filters">
      <button class="filter-btn on-focus" @click="setFilter" value="all">All</button>
      <button class="filter-btn" @click="setFilter" value="done">Completed</button>
      <button class="filter-btn" @click="setFilter" value="pending">In progress</button>
    </div>
    <ul class="todolist">
      <Todo @delete-todo="removeTodo(todo)" v-for="todo in filteredTodos" v-bind:todo="todo" :key="todo.id"></Todo>
    </ul>
  </div>
</template>

<script>
import datepicker from 'vuejs-datepicker'
import Todo from '../components/Todo'
import swal from 'sweetalert'

// Creating a local storage for todos

var STORAGE_KEY = 'my-todos'
var todoStorage = {
  fetch: function () {
    var todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    todos.forEach(function (todo, index) {
      todo.id = index
    })
    todoStorage.uid = todos.length
    return todos
  },
  save: function (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  }
}

// Describing a TodoList component

export default {
  name: 'TodoList',
  components: {
    datepicker,
    Todo
  },
  data () {
    return {
      msg: 'Your todos',
      newTodoTitle: '',
      newTodoDate: '',
      newTodoDesc: '',
      todos: todoStorage.fetch(),
      todoFilter: 'all'
    }
  },
  computed: {
    filteredTodos: function () {
      switch (this.todoFilter) {
        case 'done': return this.todos.filter(todo => todo.completed)
        case 'pending': return this.todos.filter(todo => !todo.completed)
        default: return this.todos
      }
    }
  },
  watch: {
    todos: {
      handler: function (todos) {
        todoStorage.save(todos) // handling and saving to localStorage array changes in Todos
      },
      deep: true
    }
  },
  methods: {
    // method for make a string from dateObj which is reciving from datepicker component
    parseDate: function (dateObj) {
      return dateObj.getDate() + '.' + (dateObj.getMonth() + 1) + '.' + dateObj.getFullYear()
    },
    setFilter: function (btn) {
      var el = btn.target
      var val = el.value
      var dropFocus = document.getElementsByClassName('on-focus')
      if (dropFocus.length) {
        dropFocus[0].classList.remove('on-focus')
      }

      this.todoFilter = val

      el.classList.add('on-focus')
    },
    addTodo: function () {
      var toTitle = this.newTodoTitle && this.newTodoTitle.trim()
      var toDate = this.newTodoDate
      var toDesc = this.newTodoDesc && this.newTodoDesc.trim()
      if (!toTitle) {
        alert('Type the title')
        return
      }

      toDesc = !toDesc ? '' : toDesc
      toDate = !toDate ? '' : this.parseDate(toDate)
      this.todos.unshift({
        id: todoStorage.uid++,
        title: toTitle,
        date: toDate,
        desc: toDesc,
        completed: false
      })

      this.newTodoTitle = ''
      this.newTodoDate = ''
      this.newTodoDesc = ''

      swal('Created!', 'Your To-Do has been created.', 'success')
    },
    removeTodo: function (todo) {
      this.todos.splice(this.todos.indexOf(todo), 1)
      swal('Deleted!', 'Your To-Do has been deleted.', 'success')
    }
  }
}
</script>

<!-- "scoped" attribute to limit CSS to this component only -->
<style scoped>
.create-border { border: 1px black solid; }

.content > .button { margin: 10px 0!important; }

.filters {
  display: flex;
  flex-direction: row;
  width: 300px;
  margin: 0 auto;
}

.filter-btn {
  height: 40px;
  width: 100%;
  padding: 5px;
  color: rgb(0, 153, 241);
  background-color: rgb(255, 255, 255);
  border: 1px rgb(2, 160, 240) solid;
  border-radius: 5px;
  margin: 10px 5px;
}

.on-focus {
  background-color: rgb(0, 153, 241);
  color: #fff;
  text-shadow: 1px 3px 2px black;
}

h1 { margin-top: 10px; }

input { margin-top: 10px; }

.vdp-datepicker {
  padding: 10px 0;
  margin: 0 calc(21% + 1px);
}

.todolist { padding-left: 0; }

.m-top { margin-top: 15px; }
</style>
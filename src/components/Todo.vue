<template>
  <div @edit="handleEdit(todo)" v-bind:class="validDate" class='todoitem ui centered card'>
    <div class='content'>
        <div class='header'>
            {{ todo.title }}
        </div>
        <div class='meta'>
            {{ todo.desc }}
        </div>
        <div class='meta'>
            {{ todo.date }}
        </div>
        <div class='extra content'>
          <span @click="removeTodo(todo)" class='right floated trash icon'>
            <i class='trash icon'></i>
          </span>
          <span @click="openForm(todo)" class='right floated edit icon'>
            <i class='edit icon'></i>
          </span>
        </div>
    </div>
    <div class='ui bottom attached green basic button' @click="completeTodo" v-show="todo.completed">
        Completed
    </div>
    <div class='ui bottom attached red basic button' @click="completeTodo" v-show="!todo.completed">
        Pending
    </div>
    <Form v-bind:todo="todo" @edit="editCommit" @cancel="closeForm" v-show="isEditing"></Form>
  </div>
</template>

<script>
import Form from './Form'
import swal from 'sweetalert'

export default {
  props: ['todo'],
  data () {
    return {
      isEditing: false
    }
  },
  components: {
    Form
  },
  computed: {
    // set own style to pending, belated or completed todo
    validDate: function () {
      if (!this.todo.date && !this.todo.completed) return 'blue-border'
      return ((new Date().getTime() - this.reParseDate(this.todo.date)) > 0) ? (this.todo.completed ? 'complete-border' : 'fail-border') : this.todo.completed ? 'complete-border' : 'blue-border'
    }
  },
  methods: {
    removeTodo: function (todo) {
      this.$emit('delete-todo', todo)
    },
    // method make a Date object from string special for placeholder in datepicker
    reParseDate: function (str) {
      var DateArr = str.split('.')
      return new Date(DateArr[2], DateArr[1] - 1, DateArr[0]).getTime()
    },
    openForm: function () {
      this.isEditing = true
    },
    closeForm: function () {
      this.isEditing = false
    },
    editCommit: function (params) {
      this.todo.title = params.toTitle
      this.todo.date = params.toDate
      this.todo.desc = params.toDesc
    },
    completeTodo: function () {
      this.todo.completed ? swal('Set pending status!', 'To-Do pending!', 'success') : swal('Set completed status!', 'To-Do completed!', 'success')
      this.todo.completed = !this.todo.completed
    }
  }
}
</script>

<style>
.blue-border { border: 1px rgb(0, 153, 241) solid!important; }

.fail-border { border: 1px red solid!important; }

.complete-border { border: 1px rgb(212, 212, 212) solid!important; }

.blue-border, .complete-border, .fail-border { border-bottom: none!important; }

.icon {
  margin-bottom: -10px !important;
  margin-top: 10px !important;
}

.icon:hover { cursor: pointer; }

.todoitem {
  text-align: left;
  padding: 10px 0;
}
</style>
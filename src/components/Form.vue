<template>
    <div id="openModal" class="modalDialog">
        <div class='ui centered card'>
          <div class='content'>
            <button @click="editCancel" class="close ui right blue button">X</button>
            <h2>Edit</h2>
            <input type="text" v-model="newTodoTitle" />
            <input type="text" v-model="newTodoDesc" />
            <datapicker v-bind:placeholder='this.todo.date' v-model="newTodoDate" class="center"></datapicker>
            <button class='ui basic blue button' @click="editDone">Submit</button>
          </div>
        </div>
    </div>
</template>

<script>
import datapicker from 'vuejs-datepicker'
import swal from 'sweetalert'

export default {
  name: 'Form',
  props: ['todo'],
  data () {
    return {
      newTodoTitle: this.todo.title,
      newTodoDate: this.todo.date,
      newTodoDesc: this.todo.desc
    }
  },
  components: {
    datapicker
  },
  methods: {
    // method for make a string from dateObj which is reciving from datepicker component
    parseDate: function (dateObj) {
      return dateObj.getDate() + '.' + (dateObj.getMonth() + 1) + '.' + dateObj.getFullYear()
    },
    editDone: function () {
      this.$emit('cancel')
      var toTitle = this.newTodoTitle && this.newTodoTitle.trim()
      var toDate = this.newTodoDate
      var toDesc = this.newTodoDesc && this.newTodoDesc.trim()
      if (!toTitle) {
        alert('Fill both fields')
        return
      }
      toDate = toDate === this.todo.date ? this.todo.date : this.parseDate(this.newTodoDate)
      this.$emit('edit', {toDesc, toTitle, toDate})
      swal('Changed', 'To-Do was changed!', 'success')
    },
    editCancel: function () {
      this.$emit('cancel')
      swal('Change was canceled', 'To-Do has not changed!', 'error')
    }
  }
}
</script>

<style>
.modalDialog input { margin-top: 10px; }

.center { margin-top: -10px!important; }

.card { top: 20%; }

.content > .button { margin: 10px 0!important; }

.vdp-datepicker {
    padding: 10px 0;
    margin: 0 calc(21% + 1px);
}

.todolist { padding-left: 0; }

.modalDialog {
  text-align: center;
	position: fixed;
	font-family: Arial, Helvetica, sans-serif;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	background: rgba(0,0,0,0.5);
	z-index: 99999;
	-webkit-transition: opacity 400ms ease-in;
	-moz-transition: opacity 400ms ease-in;
	transition: opacity 400ms ease-in;
	display: block;
	pointer-events: auto;
}

.modalDialog > div {
	width: 400px;
	position: relative;
	margin: 10% auto;
	padding: 5px 20px 13px 20px;
	border-radius: 10px;
	background: #fff;
	background: -moz-linear-gradient(#fff, #999);
	background: -webkit-linear-gradient(#fff, #999);
	background: -o-linear-gradient(#fff, #999);
}

.close {
	background: #606061;
	color: #FFFFFF;
	line-height: 20px;
	position: absolute;
	right: -15px;
	text-align: center;
	top: -20px;
  padding-left: 15px!important;
	width: 20px;
	text-decoration: none;
	font-weight: bold;
	-webkit-border-radius: 12px;
	-moz-border-radius: 12px;
	border-radius: 12px;
	-moz-box-shadow: 1px 1px 3px #000;
	-webkit-box-shadow: 1px 1px 3px #000;
	box-shadow: 1px 1px 3px #000;
}

.close:hover { background: #00d9ff; }
</style>
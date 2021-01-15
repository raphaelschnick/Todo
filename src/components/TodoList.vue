<template>
    <div class="container">
        <input type="text" class="todo-input" placeholder="Todo" v-model="newTodo" @keyup.enter="addTodo">
        <div v-for="(todo, index) in todos" :key="todo.id" class="todo-item">
            <div class="todo-item-left">
                <input type="checkbox" v-model="todo.completed">
                <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" 
                :class="{ completed: todo.completed }">
                    {{ todo.title}}
                </div>
                    <div class="edit">
                        <input v-if="todo.editing" class="todo-item-edit" type="text" 
                        v-model="todo.title" @blur="doneEdit(todo)"
                        @keyup.esc="cancelEdit(todo)"
                        @keyup.enter="doneEdit(todo)"
                        v-focus>
                        <span class="focus-border">
                        <i></i>
                        </span>
                    </div>
            </div>
            <div class="edit-item" @click="editTodo(todo)">
               &#9998;
            </div>
            <div class="remove-item" @click="removeTodo(index)">
                &times;
            </div>
        </div>

        <div class="extra-container">
            <div><label><input type="checkbox" 
            :checked="!anyRemaining" @change="checkAllTodos"> 
            Alle erledigen </label></div>
            <transition name="fade">
            <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
            </transition>
            <div>{{ remaining }} zu erledigen </div>
        </div>
    </div>
</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      todos: [
        {
          'id': 1,
          'title': 'Real World Vue schauen',
          'completed': false,
          'editing': false,
        },
        {
          'id': 2,
          'title': 'Einkaufen',
          'completed': false,
          'editing': false,
        },
      ]
    }
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining() {
      return this.remaining != 0
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0
    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
        editing: false,
      })
      this.newTodo = ''
      this.idForTodo++
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title
      todo.editing = true
    },
    doneEdit(todo) {
      if (todo.title.trim() == '') {
        todo.title = this.beforeEditCache
      }
      todo.editing = false
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache
      todo.editing = false
    },
    removeTodo(index) {
      this.todos.splice(index, 1)
    },
    checkAllTodos() {
      this.todos.forEach((todo) => todo.completed = event.target.checked)
    },
    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}

</script>

<style>
button {
  background-color: #3CBC8D;
  border: none;
  color: black;
  border-radius: 5px;
  padding: 14px 15px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  cursor: pointer;
}

.todo-input {
  width: 100%;
  padding: 12px 20px;
  margin: 8px 0;
  box-sizing: border-box;
  border: none;
  border-radius: 5px;
  background-color: #3CBC8D;
  color: black;
  transition-property: all;
  transition-duration: 0.3s;
}

.todo-input:focus {
    transition: ease-in-out 0.3s;
    background-color: white;
}

.todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
}

.remove-item {
    cursor: pointer;
    margin-left: 14px;
}

.remove-item:hover {
    color: red;
}

.edit {
    position: relative;
}
.edit-item {
    cursor: pointer;
    margin-left: auto;
}

.edit-item:hover {
    color: #3CBC8D;
}

.todo-item-left {
    display: flex;
    align-items: center;;
}

.todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
}

.todo-item-edit {
    font-size: 24px;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
}

.completed {
    text-decoration: line-through;
    color: grey;
}

.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-bottom: 14px;
}

.fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
}

.fade-enter, .fade-leave-to {
    opacity: 0;
}

.todo-item-edit {border: 1px solid #ccc; padding: 7px 14px 9px; transition: 0.4s;}
.todo-item-edit ~ .focus-border:before,
.todo-item-edit ~ .focus-border:after{content: ""; position: absolute; top: 0; left: 0; width: 0; height: 2px; background-color: #3399FF; transition: 0.3s;}
.todo-item-edit ~ .focus-border:after{top: auto; bottom: 0; left: auto; right: 0;}
.todo-item-edit ~ .focus-border i:before,
.todo-item-edit ~ .focus-border i:after{content: ""; position: absolute; top: 0; left: 0; width: 2px; height: 0; background-color: #3399FF; transition: 0.4s;}
.todo-item-edit ~ .focus-border i:after{left: auto; right: 0; top: auto; bottom: 0;}
.todo-item-edit:focus ~ .focus-border:before,
.todo-item-edit:focus ~ .focus-border:after{width: 100%; transition: 0.3s;}
.todo-item-edit:focus ~ .focus-border i:before,
.todo-item-edit:focus ~ .focus-border i:after{height: 100%; transition: 0.4s;}


</style>

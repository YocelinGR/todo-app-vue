<template>
  <div>
    <input type = "text" class = "todo-input" placeholder = "Nueva tarea :D " v-model= "newTodo" @keyup.enter = "addTodo" >
    <transition-group name = "fade" enter-active-class = "animated fadeInUp" leave-active-class = "animated fadeOutDown">
    <div v-for = "(todo,index) in todosFiltered" :key="todo.id" class= "todo-item">
      <div class = "todo-item-left">
        <input type = "checkbox" v-model = "todo.completed">
        <div v-if = "!todo.editing" @dblclick = "editTodo(todo)" class = "todo-item-label" :class = "{ completed: todo.completed }">{{todo.title}}</div>
        <input v-else class = "todo-item-edit" type = "text" v-model = "todo.title" @blur = "doneEdit(todo)" @keyup.enter = "doneEdit(todo)" @keyup.esc = "cancelEdit(todo)">
      </div>
      <div class = "remote-item" @click = "removeTodo(index)">
        &times;
      </div>
    </div>
    </transition-group>
    <div class= "extra-container">
      <div><label><input type="checkbox" :checked= "!anyRemaining" @change = "checkAllTodos">Marcar todas</label></div>
      <div>{{remaining }} Pendientes</div>
    </div>
    <div class = "extra-container">
      <div>
        <button :class = "{ active:filter == 'all' }" @click = "filter = 'all'">Todos</button>
        <button :class = "{ active: filter == 'active'}" @click = "filter = 'active'">Activos</button>
        <button :class= "{ active: filter == 'completed'}" @click = "filter = 'completed'">Completados</button>
      </div>
      <div>
      <transition name= "fade">
        <button v-if="showClearCompletedButton" @click= "clearCompleted">Limpiar Completos</button>
      </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'to-do-list',
  data () {
    return {
      newTodo: '',
      idForTodo: 4,
      beforeEditCache: '',
      filter: 'all',
      todos : [
        {
          'id': 1,
          'title': 'Learn vue js',
          'completed': false,
          'editing': false,
        },
        {
          'id': 2,
          'title': 'Finish LMS',
          'completed': false,
          'editing': false,
        },
        {
          'id': 3,
          'title': 'Get a happy year´s ends with Platzi',
          'completed': false,
          'editing': false,
        },
      ]
    }
  },
  computed: {
    remaining(){
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining(){
      return this.remaining != 0
    },
    todosFiltered(){
      if(this.filter == 'all'){
        return this.todos
      } else if(this.filter == 'active'){
        return this.todos.filter(todo => !todo.completed)
      } else if(this.filter == 'completed'){
        return this.todos.filter(todo => todo.completed)
      }
      return this.todos
    },
    showClearCompletedButton(){
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
    addTodo(){
      if (this.newTodo.trim().length == 0){
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
    cancelEdit(todo) {
      todo.title = this.beforeEditCache
      todo.editing = false
    },
    checkAllTodos(){
      this.todos.forEach((todos) => todos.completed = event.target.checked)
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title
      todo.editing = true
    },
    doneEdit(todo) {
      if (todo.title.trim() == ''){
        todo.title = this.beforeEditCache
      }
      todo.editing = false
    },
    removeTodo(index) {
        this.todos.splice(index, 1)
    },
    clearCompleted(){
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
}
</script>

<style lang = "scss">
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css");
  .todo-list {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;

    &:focus {
      outline: 0;
    }
  }
  .todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content:space-between;
  }
  .remove-item {
    cursor: pointer;
    margin-left: 14px;
    &:hover{
      color: black;
    }
  }
  .todo-item-left {
    display: flex;
    align-items: center;
  }
  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }
  .todo-item-edit {
    font-size: 22px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
  }
  .completed {
    text-decoration: line-through;
    color: gray;
  }
  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }

  button {
    font-size: 14px;
    background-color: white;
    appearance: none;
  }

  button:hover {
      background: lightgreen;
    }
  
  button:focus {
      outline: none;
    }

  .active {
    background: lightgreen;
  }
  .fade-enter-active, .fade-leave-active {
    transition: opacity 2s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>

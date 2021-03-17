<template>
  <div >
    <input type="text" class="todo-input" placeholder="Enter a task to do" v-model="newTodo" @keyup.enter="addTodo">
    <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
    <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed">
        <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class="{ completed : todo.completed}">
          {{todo.title}}</div>
        <input v-else class="todo-item-edit" type="text" v-model="todo.title" @blur="editTodo(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
      </div>
      <div class="remove-item" @click="removeTodo(index)">
        &times;
      </div>
    </div>
    </transition-group>

    <div class="extra-container">
      <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos">All Done</label></div>
      <div>{{ remaining}} tasks left</div>
    </div>

    <!--Adding filters-->
    <div class="extra-container">
      <div>
        <button :class="{ active: filter == 'all'}" @click="filter = 'all'">ALL</button>
        <button :class="{ active: filter == 'active'}" @click="filter = 'active'">ACTIVE</button>
        <button :class="{ active: filter == 'completed'}" @click="filter = 'completed'">SHOW COMPLETED</button>
      </div>
      <div>
        <transition name="fade">
       <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
       </transition>
      </div>

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
      filter: 'all',
      todos: [
        {
          'id': 1,
          'title': 'Learn Vue',
          'completed': false,
          'editing':false,
        },
        {
          'id': 2,
          'title': 'Create a Project',
          'completed': false,
          'editing':false,
        },
      ]
      }
    },
    computed: {
      remaining() {
        return this.todos.filter(todo => !todo.completed).length
      },
      anyRemaining() {
        return this.remaining !=0
      },
      todosFiltered() {
        if (this.filter == 'all') {
          return this.todos
        } else if (this.filter == 'active') {
          return this.todos.filter(todo => !todo.completed)
        } else if (this.filter == 'completed') {
          return this.todos.filter(todo => todo.completed)
        }

        return this.todos
      },
      showClearCompletedButton() {
        return this.todos.filter(todo => todo.completed).length > 0
      }
    },
    directives: {
      focus: {
        inserted: function(el) {
          el.focus()
        }
      }
    },
    methods: {
      addTodo(){
        if(this.newTodo.trim().length ==0){
          return
        }
        this.todos.push({
          id: this.idForTodo,
          title: this.newTodo,
          completed: false,
        })

        this.newTodo =''
        this.idForTodo++

        },
        editTodo(todo) {
          this.beforeEditCache = todo.title
          todo.editing = true
        },
        doneEdit(todo) {
          if(todo.title.trim().length == 0){
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
    },
}
</script>

<!-- Add "global" attribute to limit CSS to this component only -->
<style>
  @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.2/animate.min.css");

  *{
    background-color: rgb(163, 99, 16);
  }

  .todo-input {
    width: 600px;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
    color: blue;

    /*&:focus {
      outline: 0
    }*/
}

.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  animation-duration: 0.3s;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
  /* &:hover {
    color: black;
  }*/
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
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100px;
  padding: 10px;
  border: 1px solid #ccc;
  font-family: 'Avenir', Arial, Helvetica, sans-serif;

  /*&:focus {
    outline:none;
  }*/
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
  border-top: 1px solid lightgray;
  padding-top: 14px;
  margin-bottom: 14px;
}

button {
  font-size: 20px;
  background-color: white;
  appearance: none;

  /*&:hover {
    background: lightgreen;
  }*/

 /*&:focus {
    outline: none;
  }*/
}

.active {
  background: green;
}

.fade-center-active, .fade-leave-active{
  transition: opacity .2s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
 </style>

<!--</style>-->

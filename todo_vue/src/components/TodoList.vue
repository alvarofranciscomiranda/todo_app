<script setup>
  import { ref, computed, watchEffect } from 'vue'

  // const filters = {
  //   all: (todos) => todos,
  //   active: (todos) => todos.filter((todo) => !todo.completed),
  //   completed: (todos) => todos.filter((todo) => todo.completed)
  // }

  let beforeEditCache = ref()
  let newTodo = ref()
  let filter = ref('all')
  const idForTodo = ref(3)
  const todos = ref([
        {
          id: 1,
          title: 'Finish Vue Screenshot',
          completed: false,
          editing: false,
        },
        {
          id: 2,
          title: 'Take over world',
          completed: false,
          editing: false,
        }
    ]
  )

  function addTodo(e){
    const value = e.target.value.trim()
    if (value){
      todos.value.push({
        id: idForTodo,
        title: newTodo,
        completed: false,
      })
      e.target.value = ''
      newTodo = ''
    }
  }

  function removeTodo(todo) {
    todos.value.splice(todos.value.indexOf(todo), 1)
  }

  function editTodo(todo){
    beforeEditCache = todo.title
    todo.editing = true
  }

  function doneEdit(todo){
    if(todo.title.trim() == 0){
      removeTodo(todo)
    }
    todo.editing = false
  }

  function cancelEdit(todo){
    todo.title = beforeEditCache
    todo.editing = false
  }

  function checkAllTodos(e){
    todos.value.forEach((todo) => (todo.completed = e.target.checked))
  }

  const remaining = computed(() => todos.value.filter(todo => !todo.completed).length)
  const anyRemaining = computed(() => remaining.value > 0 ? remaining.value : 0)
  const todosFiltered = computed(() => {
    if (filter.value === 'all') {
      return todos.value
    } else if (filter.value === 'active') {
      return todos.value.filter(todo => !todo.completed)
    } else if (filter.value === 'completed') {
      return todos.value.filter(todo => todo.completed)
    }
    return todos.value
  })
</script>

<template>
  <div>
    <input type="text" class="todo-input" placeholder="What needs to be done" v-model="newTodo" @keyup.enter="addTodo">

    <div v-for="todo in todosFiltered" :key="todo.id" class="todo-item">
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed">
        <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class="{completed : todo.completed}">{{todo.title}}</div>
        <input v-else class="todo-item-edit" type="text" v-model="todo.title" @vue:mounted="({ el }) => el.focus()"
               @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)">
      </div>
      <div class="remove-item" @click="removeTodo(todo)">
        &times;
      </div>
    </div>

    <div class="extra-container">
      <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check All</label></div>
      <div>{{ remaining }} items left</div>
    </div>

    <div class="extra-container">
      <div>
        <button :class="{ active: filter === 'all' }" @click="filter = 'all'">All</button>
        <button :class="{ active: filter === 'active' }" @click="filter = 'active'">Active</button>
        <button :class="{ active: filter === 'completed' }" @click="filter = 'completed'">Completed</button>
      </div>

<!--      <div>-->
<!--        <transition name="fade">-->
<!--        <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>-->
<!--        </transition>-->
<!--      </div>-->

    </div>
  </div>
</template>

<style lang="scss">
  .todo-input{
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;

    &:focus {
      outline: 0;
    }
  }

  .todo-item{
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 0.3s;
  }

  .remove-item{
    cursor: pointer;
    margin-left: 14px;
    &:hover{
      color: red;
    }
  }

    .todo-item-left { // later
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
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc; //override defaults
    font-family: 'Avenir', Helvetica, Arial, sans-serif;

    &:focus {
      outline: none;
    }
  }

  .completed{
    text-decoration: line-through;
    color: grey;
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

    &:hover {
      background: lightgreen;
    }

    &:focus {
      outline: none;
    }
  }

  .active {
    background: lightgreen;
  }

</style>

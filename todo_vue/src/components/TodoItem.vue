<script setup>
  import {defineProps, defineEmits, ref } from 'vue';

  defineProps({
    todo: Object
  })
  defineEmits(['removeTodo'])

  // Create reactive variable for beforeEditCache
  let beforeEditCache = ref()

  function editTodo(todo){
    beforeEditCache = todo.title
    todo.editing = true
  }

  function doneEdit(todo){
    if(todo.title.trim().length === 0){
      return('removeTodo',todo);
    }
    todo.editing = false
  }

  function cancelEdit(todo){
    todo.title = beforeEditCache
    todo.editing = false
  }

</script>

<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input type="checkbox" v-model="todo.completed">
      <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class="{completed : todo.completed}">{{todo.title}}</div>
      <input v-else class="todo-item-edit" type="text" v-model="todo.title" @vue:mounted="({ el }) => el.focus()"
             @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)">
    </div>
    <div class="remove-item" @click="$emit('removeTodo',todo)">
      &times;
    </div>
  </div>
</template>

<style >


</style>

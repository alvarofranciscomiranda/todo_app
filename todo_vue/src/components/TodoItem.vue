<script setup>
import {defineProps, defineEmits, ref, getCurrentInstance, toRefs} from 'vue';
  const { emit } = getCurrentInstance()

  const props = defineProps({
    todo: Object
  })
  defineEmits(['removeTodo'])

  const { todo} = toRefs(props);

  // Create reactive variable for beforeEditCache
  let beforeEditCache = ref()

  function editTodo(todo){
    beforeEditCache = todo.title
    todo.editing = true
  }

  function doneEdit(todo){
    if(todo.title.trim().length === 0){
      emit('removeTodo',todo);
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
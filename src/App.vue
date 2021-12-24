<template>
  <img alt="Vue logo" src="./assets/logo.png" />
  <HelloWorld msg="Hello Vue 3.0 + Vite" />
  <todo-list> </todo-list>
  <virtual-list></virtual-list>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import TodoList from './components/TodoList.vue'
import VirtualList from './components/VirtualList.vue'

export default {
  name: 'App',
  components: {
    HelloWorld,
    TodoList,
    VirtualList
  }
}
</script>

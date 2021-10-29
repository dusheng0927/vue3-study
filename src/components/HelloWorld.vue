<template>
  <h1>{{ msg }}</h1>
  <button @click="count++">count is: {{ count }}</button>
  <p>double: {{ doubleCount }}</p>
</template>

<script>
import { ref, computed } from 'vue'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  setup () {
    const count = ref(1)
    const doubleCount = computed(() => count.value * 2)
    return {
      count, doubleCount
    }
  }
  
}
</script>

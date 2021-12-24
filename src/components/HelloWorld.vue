<template>
  <h1>{{ msg }}</h1>
  <button @click="count++">count is: {{ count }}</button>
  <p>double: {{ doubleCount }}</p>
  <p>
    <span>{{ message }}</span> <button @click="changeMessage">change message</button>
  </p>
  <p>
    <custom-input v-model="inputText"></custom-input>
  </p>
  <p>{{ inputText }}</p>

  
</template>

<script>
import { ref, computed, nextTick } from 'vue'


export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  components: {
    'custom-input': {
      props: ['modelValue'],
      emits: ['update:modelValue'],
      template: `<input type="text" :value="modelValue" @input="$emit('update:modelValue', $event.target.value)" />`
    },
  },
  setup () {
    const count = ref(1)
    const doubleCount = computed(() => count.value * 2)
    const inputText = ref('text')

    const message = ref('hello!')
    const changeMessage = async () => {
      count.value = count.value + 1
      message.value += count.value
      await nextTick()
      console.log('after nextTick Now DOM is updated')
    }
    
    return {
      count, doubleCount, changeMessage, message, inputText
    }
  }
  
}
</script>

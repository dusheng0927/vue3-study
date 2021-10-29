<script>
import { defineComponent } from 'vue'

export default defineComponent({
  setup() {
    function useDebounceRef(value, delay=200) {
      let timer = null
      return customRef((track, trigger) => {
        return {
          get() {
            track()
            return value
          },
          set (newVal) {
            clearTimeout(timer)
            timer = setTimeout(() => {
              value = newVal
              trigger()
            }, delay)
          }
        }
      })
    }
    return { useDebounceRef }
  },
})
</script>


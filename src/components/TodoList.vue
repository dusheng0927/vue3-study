<template>
  <input class="add-todo" autocomplete="off" autofocus type="text" v-model="input" @keyup.enter="addTodo" @blur="addTodo" />
  <ul class="list-todos">
    <li class="list-item" v-for="item in filteredList" :key="item.id" :class="{editing: curEdit === item, completed: item.completed}">
      <div class="view" >
        <input class="list-item-checkbox" type="checkbox" v-model="item.completed">
        <label class="list-item-text" :class="{'done-item': item.completed }" @dblclick="editTodo(item)">{{ item.text }}</label>
        <button class="delete-btn" @click="deleteTodo(item)">x</button>
      </div>
      <input class="edit" type="text" v-model="item.text" @keyup.enter="saveEdit(item)" @blur="saveEdit(item)" @keyup.esc="cancelEdit(item)" />
    </li>
  </ul>
  <div class="info-list">
    <span :class="{activeBtn: type === 'all'}" @click="type='all'"> All </span>
    <span :class="{activeBtn: type === 'active'}" @click="type='active'"> Active </span>
    <span :class="{activeBtn: type === 'completed'}" @click="type='completed'"> Completed </span>
  </div>
</template>

<script>
import { computed, ref, watchEffect  } from 'vue'

const useAdd = todos => {
  const input = ref('')
  const addTodo = () => {
    const text = input.value?.trim()
    if (text.length === 0) return
    todos.value.unshift({
      text,
      id: todos.value.length + 1,
      completed: false,
    })
    input.value = ''
  }
  return { input, addTodo }
}

const useDelete = todos => {
  const deleteTodo = todo => {
    const index = todos.value.indexOf(todo)
    todos.value.splice(index, 1)
  }
  return { deleteTodo }
}

const useEdit = (remove) => {
  let initText = ''
  let curEdit = ref(null) // 当前编辑项对象

  const editTodo = todo => {
    initText = todo.text
    curEdit.value = todo
  }

  const saveEdit = (todo) => {
    todo.text = todo.text.trim()
    if (!todo.text.length) {
      remove(todo)
    } else {
      curEdit.value = null
    }
  }

  const cancelEdit = (todo) => {
    curEdit.value = null
    todo.text = initText
  }

  return { curEdit, editTodo, saveEdit, cancelEdit }
}

const useFilter = () => {
  const filter = {
    all: list => list,
    active: list => list.filter(item => !item.completed),
    completed: list => list.filter(item => item.completed)
  }
  return { filter }
}

// const useLocalstorage = () => {
//   const key='TODOKEYS'
//   const list = ref(localStorage.getItem(key) || [])
//   watchEffect(() => {
//     localStorage.setItem(key, list.value)
//   })
//   return list
// }

export default {
  name: 'TodoList',
  setup() {
    const todos = ref([])
    const totalNum = computed(() => todos.value.length)
    const { input, addTodo } = useAdd(todos)
    const { deleteTodo } = useDelete(todos)
    const { curEdit, editTodo, saveEdit, cancelEdit } = useEdit(deleteTodo)
    const { filter } = useFilter(todos)

    const type = ref('all')
    const filteredList = computed(() => filter[type.value](todos.value))
    
    return { todos, input, addTodo, deleteTodo, curEdit, editTodo,
      saveEdit, cancelEdit, totalNum, type,
      filteredList
    }
  }
}
</script>

<style scoped>
* {
  box-sizing: border-box;
  padding:0;
  margin:0;
}
.add-todo {
  line-height: 25px;
  padding-left: 5px;
}
.list-todos {
  width: 460px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin: 20px auto;
  min-height: 200px;
  list-style-type: none;
}
.list-item {
  line-height: 30px;
  text-align: left;
  padding: 10px;
  line-height: 30px;
  border-bottom: 1px solid #ccc;
}
.list-item-checkbox {
  width: 30px;
}
.list-item-text {
  flex: 1;
  font-size: 14px;
}
.delete-btn {
  justify-content: flex-end;
  padding: 2px 5px;
}
.edit, .editing .view {
  display: none;
}
.view, .editing .edit {
  display: flex;
  align-items: center;
}
.edit {
  margin-left: 11px;
  margin-top: 5px;
}
.done-item {
  text-decoration: line-through;
  color:#bbb8b8;
}
.info-list {
  display: flex;
  flex-direction: row;
  width: 480px;
  margin: 0 auto;
}
.info-list span {
  margin: 10px;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 5px;
}
.activeBtn {
  background: #0075ff;
  color: #fff;
}
</style>

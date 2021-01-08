<template>
  <AddTodo @add-todo="addTodo" />
  <TodoItem
    v-for="todo in todos"
    :key="todo.id"
    :todoProps="todo"
    @item-completed="markComplete"
    @delete-item="deleteTodo"
  />
</template>

<script>
import { ref } from 'vue'
import axios from 'axios'
// import { v4 as uuidv4 } from 'uuid'

import TodoItem from './TodoItem'
import AddTodo from './AddTodo'

export default {
  name: 'Todos',
  components: { TodoItem, AddTodo },
  setup() {
    const todos = ref([])

    const getAllTodos = async () => {
      try {
        const res = await axios.get(
          'https://jsonplaceholder.typicode.com/todos?_limit=5'
        )
        todos.value = res.data
        console.log(todos.value)
      } catch (error) {
        console.log(error)
      }
    }

    getAllTodos()

    const markComplete = id => {
      todos.value = todos.value.map(todo => {
        if (todo.id === id) todo.completed = !todo.completed
        return todo
      })
    }

    /* const deleteTodo = id => {
      todos.value = todos.value.filter(todo => todo.id !== id)
    } */

    const deleteTodo = async id => {
      try {
        await axios.delete(`https://jsonplaceholder.typicode.com/todos/${id}`)
        todos.value = todos.value.filter(todo => todo.id !== id)
      } catch (error) {
        console.log(error)
      }
    }

    /* const addTodo = newTodo => {
      todos.value.push(newTodo)
      // console.log(todos.value)
    } */

    const addTodo = async newTodo => {
      try {
        const { title, completed } = newTodo
        const res = await axios.post(
          'https://jsonplaceholder.typicode.com/todos',
          {
            title,
            completed
          }
        )

        todos.value.push(res.data)
        // console.log(todos.value)
      } catch (error) {
        console.log(error)
      }
    }

    return {
      todos,
      markComplete,
      deleteTodo,
      addTodo
    }
  }
}
</script>

<style></style>

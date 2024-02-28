<script setup>
import {ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const input_title = ref('')
const input_content = ref('')
const input_priority = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_title.value.trim() === '' || input_priority.value === null){
    return
  }

  todos.value.push({
    title: input_title.value,
    content:input_content.value,
    priority: input_priority.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_title.value = ''
  input_content.value = ''
  input_priority.value = null
}

const removeTodo = todo =>{
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true})


watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})


onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title">
      Vítej v ToDoo <input type="text" placeholder="Tvoje jméno" v-model="name"/>
      </h1>
      <h3 class="navAdd"><a id="navAdd" href="#task">Pridat ukol</a></h3>
    </section>

    
    <!-- alt + 96 -->
    <section class="todo-list">
      <h3>Tvoje úkoly</h3>
      <div class="list">
          <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
            <label>
              <input type="checkbox" v-model="todo.done"/>
              <span :class="`bubble ${todo.priority}`"></span>
            </label>
            <div class="todo-content">
              <input type="text" class="description" style="font-weight: 600;" v-model="todo.title"/>
              <input type="text" class="description" v-model="todo.content"/>
            </div>
            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Smazat</button>
            </div>
          </div>
      </div>
      <div v-if="todos.length == 0">
        <h2 class="noTodos">Nemáš žádné úkoly. Měl bys nějaký přidat!</h2>
      </div>
    </section>

    <section class="create-todo">
      <h3>Přidej další úkol</h3>
      <h4>Co bych tak chtěl dnes udělat?</h4>
      <form @submit.prevent="addTodo">

        <input 
        type="text" 
        style="margin-bottom: 0.5rem;"
        placeholder="např. Vytvořit To Do aplikaci" 
        v-model="input_title"/>
        <h4>Přidej svému úkolu popis!</h4>
        <input 
        type="text"
        placeholder="ukol = nazev, popis, priorita, drag&drop"
        v-model="input_content"/>

        <h3>Vyber prioritu úkolu</h3>

        <div class="options">
          <label>
            <input 
            type="radio" 
            name="priorita" 
            value="lowPrio" 
            v-model="input_priority" />
            <span class="bubble lowPrio"></span>
            <div>Nízká</div>
          </label>
          <label>
            <input 
            type="radio" 
            name="priorita"  
            value="midPrio" 
            v-model="input_priority" />
            <span class="bubble midPrio"></span>
            <div>Střední</div>
          </label>
          <label>
            <input 
            type="radio" 
            name="priorita" 
            value="highPrio" 
            v-model="input_priority" />
            <span class="bubble highPrio"></span>
            <div>Vysoká</div>
          </label>

        </div>

        <input type="submit" value="Přidat úkol" id="task"/>
      </form>
    </section>
  </main>

</template>


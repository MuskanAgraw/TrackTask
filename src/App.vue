<template>
    <main class="app">
        
        <section class="greeting">
            <h2 class="title">
                What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
            </h2>
        </section>

        <section class="create-todo">
            <h3>CREATE A TODO</h3>

            <form id="new-todo-form" @submit.prevent="addTodo">
                <h4>What's on your todo list?</h4>
                <input 
                    type="text" 
                    name="content" 
                    id="content" 
                    placeholder="e.g. make a video"
                    v-model="input_content" />
                
                <h4>Pick a category</h4>
                <div class="options">
                    <label v-for="category in categories" :key="category">
                        <input 
                            type="radio" 
                            name="category" 
                            :id="'category-' + category" 
                            :value="category"
                            v-model="input_category" />
                        <span :class="'bubble ' + category"></span>
                        <div>{{ category }}</div>
                    </label>
                </div>

                <input type="submit" value="Add todo" />
            </form>
        </section>

        <section class="todo-list">
            <h3>TODO LIST</h3>
            <div class="list" id="todo-list">

                <div v-for="(todo, index) in todos_asc" :key="todo.id" :class="`todo-item ${todo.done && 'done'}`">
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span :class="`bubble ${todo.category}`"></span>
                        <span>{{ index + 1 }}. </span> <!-- Add numbering -->
                    </label>

                    <div class="todo-content">
                        <input type="text" v-model="todo.content" />
                    </div>

                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo)">Delete</button>
                    </div>
                </div>

            </div>
        </section>

    </main>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const categories = ['business', 'personal', 'work', 'home']; // Define your category options

const todos_asc = computed(() => todos.value.sort((a,b) =>{
    return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
}, {
    deep: true
})

const addTodo = () => {
    if (input_content.value.trim() === '' || input_category.value === null) {
        return
    }

    todos.value.push({
        content: input_content.value,
        category: input_category.value,
        done: false,
        editable: false,
        createdAt: new Date().getTime()
    })
    input_content.value = ''; // Clear input after adding todo
}

const removeTodo = (todo) => {
    todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<style scoped>
.bubble {
    width: 20px;
    height: 20px;
    border-radius: 50%;
    border: 2px solid #ccc;
    margin-right: 0.5rem;
    display: inline-block;
}

.business { background-color: #ff9800; }
.personal { background-color: #03a9f4; }
.work { background-color: #4caf50; }
.home { background-color: #f44336; }

.todo-item {
    display: flex;
    align-items: center;
    background-color: #fff;
    padding: 1rem;
    border-radius: 0.5rem;
    margin-bottom: 0.5rem;
    border: 1px solid #ccc;
}

.todo-item.done .todo-content input {
    text-decoration: line-through;
    color: #888;
}
</style>

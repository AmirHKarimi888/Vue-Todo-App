<template>
    <div class="addTodos">
        <div class="mt-20 mb-20 text-3xl">
            <p class="text-center">Vue Todo App</p>
        </div>
        <div
            class="manage mx-auto mt-20 w-[300px] text-center shadow-xl bg-gray-100 border border-gray-400 grid justify-center items-center p-10">
            <div>
                <input v-model="todoText" type="text" class="rounded-lg p-2 border border-gray-500"
                    placeholder="What to do">
            </div>
            <div>
                <button type="button" @click="addTodo"
                    class="text-white mt-5 bg-gradient-to-br from-pink-500 to-orange-400 hover:bg-gradient-to-bl focus:ring-4 focus:outline-none focus:ring-pink-200 dark:focus:ring-pink-800 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2">Add
                    Todo</button>
            </div>
        </div>

        <div class="show my-20">
            <ul class="mx-auto w-[300px] grid grid-cols-1 gap-5">
                <li v-for="todo in todos" :key="todo.id" @click="toggleDone(todo.id)"
                    class="bg-gray-100 shadow-xl cursor-pointer grid grid-cols-2 justify-center items-center" :class="todo.done ? 'border-4 border-green-500' : 'border-4 border-gray-400'">
                    <div class="text-center font-bold" :class="todo.done ? 'text-green-500' : 'text-gray-900'">
                        {{ todo.title }}
                    </div>
                    <div class="mt-5 text-center">
                        <button @click.self="deleteTodo(todo.id)" class="mb-4 text-red-500">
                            <i class="fa fa-trash"></i>
                        </button>
                    </div>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import axios from "axios"

export default {
    data() {
        return {
            url: "https://64c10bfafa35860bae9fd682.mockapi.io/amirhk888",
            todoText: "",
            todos: [],
            globalTodoId: 0
        }
    },
    mounted() {
        axios.get(this.url + "/todos")
            .then((response) => this.todos = response.data)
    },
    methods: {
        addTodo() {
            const newTodo = {
                id: this.todos.length + 1,
                title: this.todoText,
                done: false
            }

            axios.post(this.url + "/todos", newTodo)
                .then(() => {
                    this.todos.push(newTodo);
                })
        },
        toggleDone(id) {
            const selectedTodo = this.todos.filter((todo) => {
                if(todo.id == id) {
                    return todo;
                }
            })

            axios.put(this.url + "/todos/" + id, {
                ...selectedTodo[0],
                done: !selectedTodo[0].done
            })
            .then(() => {
                selectedTodo[0].done = !selectedTodo[0].done
            })
        },
        deleteTodo(id) {
            axios.delete(this.url + "/todos/" + id)
                .then(() => {
                    this.todos = this.todos.filter((todo) => {
                        if (todo.id != id) {
                            return todo;
                        }
                    })
                })
        }
    }
}
</script>
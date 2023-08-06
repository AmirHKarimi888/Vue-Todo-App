<template>
    <div class="addTodos">
        <div class="mt-20 mb-20 text-3xl">
            <p class="text-center">Vue Todo App</p>
        </div>
        <div
            class="manage mx-auto mt-20 w-[300px] shadow-xl bg-gray-100 border border-gray-300 grid justify-center items-center p-10">
            <div>
                <input v-model="todoText" type="text" class="rounded-lg p-2 border border-gray-500"
                    placeholder="What to do">
            </div>
            <div>
                <button v-if="globalEditStatus" @click="editTodo" type="button"
                    class="text-white mt-5 bg-gradient-to-br from-green-400 to-blue-600 hover:bg-gradient-to-bl focus:ring-4 focus:outline-none focus:ring-green-200 dark:focus:ring-green-800 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2">Edit
                    Todo</button>
                <button v-else type="button" @click="addTodo"
                    class="text-white mt-5 bg-gradient-to-br from-pink-500 to-orange-400 hover:bg-gradient-to-bl focus:ring-4 focus:outline-none focus:ring-pink-200 dark:focus:ring-pink-800 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2">Add
                    Todo</button>
            </div>
        </div>

        <div class="show">
            <ul class="mt-20 mb-20 mx-auto w-[60%] grid sm:grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-5">
                <li v-for="todo in todos" :key="todo.id"
                    class="bg-gray-100 border border-gray-300 shadow-xl grid justify-center contents-center p-5">
                    <div class="text-center">
                        {{ todo.text }}
                    </div>
                    <div class="mt-5 text-center">
                        <button @click="deleteTodo(todo.id)" type="button"
                            class="text-white bg-gradient-to-r from-red-400 via-red-500 to-red-600 hover:bg-gradient-to-br focus:ring-4 focus:outline-none focus:ring-red-300 dark:focus:ring-red-800 shadow-lg shadow-red-500/50 dark:shadow-lg dark:shadow-red-800/80 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2">Delete
                            Todo</button>
                        <button v-if="todo.editStatus" @click="preEditTodo(todo.id)" type="button"
                            class="text-white bg-gradient-to-r from-green-400 via-green-500 to-green-600 hover:bg-gradient-to-br focus:ring-4 focus:outline-none focus:ring-green-300 dark:focus:ring-green-800 shadow-lg shadow-green-500/50 dark:shadow-lg dark:shadow-green-800/80 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2">Edit
                            Todo</button>
                        <button v-else @click="preEditTodo(todo.id)" type="button"
                            class="preEditBtn text-white bg-gradient-to-r from-blue-500 via-blue-600 to-blue-700 hover:bg-gradient-to-br focus:ring-4 focus:outline-none focus:ring-blue-300 dark:focus:ring-blue-800 shadow-lg shadow-blue-500/50 dark:shadow-lg dark:shadow-blue-800/80 font-medium rounded-lg text-sm px-5 py-2.5 text-center mr-2 mb-2 disabled:opacity-75"
                            :id="todo.id">Edit
                            Todo</button>
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
            globalEditStatus: false,
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
                text: this.todoText,
                editStatus: false
            }

            axios.post(this.url + "/todos", newTodo)
                .then(() => {
                    this.todos.push(newTodo);
                })
        },

        preEditTodo(id) {
            if (this.todos[id - 1].editStatus == false) {
                this.todos[id - 1].editStatus = true;
                this.globalEditStatus = true;
                this.globalTodoId = id;
                this.todoText = this.todos[id - 1].text;

                document.querySelectorAll(".preEditBtn").forEach((x) => {
                    if (x.id != id) {
                        let IID = x.id
                        document.getElementById(IID).setAttribute("disabled", "");
                    }
                })
            } else if (this.todos[id - 1].editStatus == true) {
                this.todos[id - 1].editStatus = false;
                this.globalEditStatus = false;
                this.globalTodoId = 0;
                this.todoText = "";

                document.querySelectorAll(".preEditBtn").forEach((x) => {

                    let IID = x.id
                    document.getElementById(IID).removeAttribute("disabled", "");
                })
            }
        },

        editTodo() {
            const newTodo = {
                id: this.globalTodoId,
                text: this.todoText,
                editStatus: false
            }

            axios.delete(this.url + "/todos/" + this.globalTodoId)
                .then(() => {
                    axios.post(this.url + "/todos", newTodo);
                })
                .then(() => {
                    this.todos[this.globalTodoId - 1].text = this.todoText;
                })
                .then(() => {
                    this.todos[this.globalTodoId - 1].editStatus = false;
                    this.globalEditStatus = false;
                    this.globalTodoId = 0;
                    this.todoText = "";

                    document.querySelectorAll(".preEditBtn").forEach((x) => {

                        let IID = x.id
                        document.getElementById(IID).removeAttribute("disabled", "");
                    })
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
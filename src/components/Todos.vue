<template>
    <section class="todoapp">
        <header class="header">
            <h1>Todos</h1>
            <input type="text" class="new-todo" placeholder="Ajouter une tâche" v-model="newTodo" @keyup.enter="addTodo">
        </header>
        <div class="main">
            <input type="checkbox" class="toggle-all" v-model="allDone">
            <ul class="todo-list">
                <li class="todo" v-for="todo in filteredTodos" :key="todo.id" :class="{completed: todo.completed, editing: todo === editing}">
                    <div class="view">
                        <input type="checkbox" v-model="todo.completed" class="toggle">
                        <label @dblclick="editTodo(todo)">{{ todo.name }}</label>
                        <button class="destroy" @click.prevent="deleteTodo(todo)"></button>
                    </div>
                    <input type="text" class="edit" v-model="todo.name" v-focus="todo === editing" @keyup.enter="doneEdit" @blur="doneEdit" @keyup.esc="cancelEdit">
                </li>
            </ul>
        </div>
        <footer class="footer" v-show="hasTodos">
            <span class="todo-count">
                <strong>{{ remaining }}</strong> tâches à faire
            </span>
            <ul class="filters">
                <li><a href="#" :class="{selected: filter === 'all' }" @click.prevent="filter = 'all'">Toutes</a></li>
                <li><a href="#" :class="{selected: filter === 'todo' }" @click.prevent="filter = 'todo'">A faire</a></li>
                <li><a href="#" :class="{selected: filter === 'done' }" @click.prevent="filter = 'done'">Faites</a></li>
            </ul>
            <button class="clear-completed" v-show="done" @click.prevent="deleteCompleted">Supprimer les tâches finies</button>
        </footer>
    </section>
</template>

<script>

import Vue from 'vue';

export default {
    data () {
        return {
            todos: [],
            newTodo: '',
            filter: 'all',
            editing: null
        }
    },
    methods: {
        addTodo () {
            this.todos.push({
                name: this.newTodo,
                completed: false
            })
            this.newTodo = ''
        },
        editTodo (todo) {
            this.editing = todo
            this.oldTodo = todo.name
        },
        doneEdit () {
            this.editing = null
        },
        cancelEdit () {
            this.editing.name = this.oldTodo
            this.doneEdit();
        },
        deleteTodo (todo) {
            this.todos = this.todos.filter(i => i !== todo);
        },
        deleteCompleted () {
            this.todos = this.todos.filter(todo => !todo.completed)
        }
    },
    computed: {
        allDone: {
            get () {
                return this.remaining === 0
            },
            set (value) {
                this.todos.forEach(todo => {
                    todo.completed = value
                })
            }
        },
        hasTodos () {
            return this.todos.length > 0
        },
        remaining () {
            return this.todos.filter(todo => !todo.completed).length
        },
        done () {
            return this.todos.filter(todo => todo.completed).length
        },
        filteredTodos () {
            if (this.filter === 'todo') {
                return this.todos.filter(todo => !todo.completed)
            } else if (this.filter === 'done') {
                return this.todos.filter(todo => todo.completed)
            }
            return this.todos;
        }
    },
    directives: {
        focus (el, value) {
            if (value) {
                Vue.nextTick(_ => {
                    el.focus()
                })
            }
        }
    }
}
</script>

<style src="./todos.css"></style>

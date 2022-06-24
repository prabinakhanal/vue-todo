<template>
<h1>todos </h1>
<div>
    <input type="text" class="todo-input" placeholder="What needs to be done?" v-model="newTodo" @keyup.enter="addTodo">
    <div v-for="(todo,index) in todosFiltered" :key="todo.id" class="todo-item">
        <div class="todo-items">
            <input type="checkbox" v-model="todo.completed">
            <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class="{completed: todo.completed}">{{todo.title}}</div>
            <input v-else class="todo-edit" type="text" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)"  v-focus>
        </div>
        <div class="remove-item" @click="removeTodo(index)">
            &times;
        </div>
    </div>

    <div class="itemsleft">
        <div>
            <input type="checkbox" :checked="!anyExisting" @change="checkAllTodos">
            <button class="left">{{existing}} items left</button>
            <button :class="{active:filter=='all'}" @click="filter ='all'">All</button>
            <button :class="{active:filter=='active'}" @click="filter ='active'">Active</button>
            <button :class="{active:filter=='completed'}" @click="filter ='completed'">Completed</button>
        </div>
        <div>
            <button v-if="show" @click="clearCompleted">Clear Completed</button>
        </div>

    </div>

</div>
</template>

<script>
export default {
    name: 'todo-list',
    data() {
        return {
            newTodo: '',
            idForTodo: 1,
            beforeEditCache: '',
            filter: 'all',
            editing: false,
            todos: [

            ]

        }
    },

    computed: {
        existing() {
            return this.todos.filter((todo) => !todo.completed).length;
        },
        anyExisting() {
            return this.existing != 0
        },
        todosFiltered() {
            if (this.filter == 'all') {
                return this.todos
            } else if (this.filter == 'active') {
                return this.todos.filter(todo => !todo.completed)
            } else if (this.filter == 'completed') {
                return this.todos.filter(todo => todo.completed)
            }
            return this.todos
        },
        show() {
            return this.todos.filter(todo => todo.completed).length > 0
        }

    },

    directives: {
        focus: {
            inserted: function (el) {
                el.focus()
            }
        }
    },
    methods: {
        addTodo() {

            if (this.newTodo.trim().length == 0) {
                return
            }
            this.todos.push({
                id: this.idForTodo,
                title: this.newTodo,
                completed: false

            })

            this.newTodo = ''
            this.idForTodo++
        },
        editTodo(todo) {
            this.beforeEditCache = todo.title
            todo.editing = true
        },
        doneEdit(todo) {
            if (todo.title.trim() == '') {
                todo.title = this.beforeEditCache
            }
            todo.editing = false
        },
        cancelEdit(todo) {
            todo.editing = false,
                todo.title = this.beforeEditCache;
        },
        removeTodo(index) {
            this.todos.splice(index, 1)
        },
        checkAllTodos() {
            this.todos.forEach((todo) => todo.completed =
                event.target.checked)
        },
        clearCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed)
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style>
html,
body {
    margin: 0;
    padding: 0;
}

body {
    font: 14px 'Helvetica Neue', Helvetica, Arial, sans-serif;
    line-height: 1.4em;
    background: #f5f5f5;
    color: #4d4d4d;
    min-width: 230px;
    max-width: 550px;
    margin: 0 auto;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    font-weight: 300;
}

h1 {

    top: -155px;
    width: 100%;
    font-size: 100px;
    font-weight: 100;
    text-align: center;
    color: rgba(175, 47, 47, 0.15);
    -webkit-text-rendering: optimizeLegibility;
    -moz-text-rendering: optimizeLegibility;
    text-rendering: optimizeLegibility;
}

.todo-input {
    padding: 16px 16px 16px 60px;
    border: none;
    background: white;
    box-shadow: 5px 5px 5px #E0E0E0;
    width: 100%;
    height: 50px;
    outline: 0px;
    font-size: 24px;
    color: gray;
    font-style: italic;
}

.todo-item {
    margin-top: 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-left: 25px;

}

.remove-item {
    cursor: pointer;
    position: relative;
    top: 0;
    right: 10px;
    bottom: 0;
    width: 40px;
    height: 40px;
    margin: auto 0;
    font-size: 30px;
    color: #cc9a9a;
    margin-top: 20px;
    transition: color 0.2s ease-out;
}

.remove-item:hover {
    color: #af5b5e;
}

.todo-items {
    display: flex;
    align-items: center;
}

.todo-item-label {
    padding: 10px;

    margin-left: 12px;

}

.todo-edit {
    margin-left: 12px;
    width: 100%;
    font-size: 24px;
    padding: 10px;
    color: #2c3e50;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    outline: none;

}

.completed {
    text-decoration: line-through;
    color: grey;
}

.itemsleft {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-bottom: 14px;

}

.left {
    border: 0px;
    outline: 0px;
    background: transparent;
}

.itemsleft button {
    margin-left: 10px;

}

.active {
    border-color: rgba(175, 47, 47, 0.2);
    border-radius: 5px;
}
</style>

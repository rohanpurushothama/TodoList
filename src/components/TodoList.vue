<template>
  <div class="container">
    <input type="text" class="user-input" placeholder="Please enter the task" v-model="newTodo" @keyup.enter="addTodo"/>
    <todo-item
      v-for="(todo, index) in todos" :key="todo.id" :todo="todo" :index="index" :checkAll="!remainingZero">
    </todo-item>

    <div class="extra-container">
    <todo-check-all :remainingZero='remainingZero'></todo-check-all>
     <todo-item-remaining :remaining='remaining'></todo-item-remaining>
    </div>

    <div class="filters-button">
      <todo-filter></todo-filter>
        <todo-clear-completed :showClearCompleted='showClearCompleted'></todo-clear-completed>
    </div>
  </div>
</template>

<script>
import {todoEventBus} from '@/event-bus'

import TodoItem from "./TodoItem";
import TodoItemRemaining from './TodoItemRemaining'
import TodoCheckAll from './TodoCheckAll'
import TodoFilter from './TodoFilter'
import TodoClearCompleted from './TodoClearCompleted'

export default {
    
  name: "todo-list",
  components: {
    TodoItem,
    TodoItemRemaining,
    TodoCheckAll,
    TodoFilter,
    TodoClearCompleted,
  },
  data() {
    return {
      newTodo: "",
      idForTodo: 0,
      filter: 'all',
      todos: [],
    };
  },
  created(){
      todoEventBus.$on('deleteTodo',(index) => this.removeTodo(index))
      todoEventBus.$on('finishedEdit', (data) => this.finishedEdit(data))
      todoEventBus.$on('checkAllChanged', (checked) => this.checkAllTodo(checked))
      todoEventBus.$on('filterChanged', (filter) => this.filter = filter)
      todoEventBus.$on('clearCompletedTodo', () => this.clearCompleted())
  }, 
    beforeDestroy() {
    todoEventBus.$off('removedTodo')
    todoEventBus.$off('finishedEdit')
    todoEventBus.$off('checkAllChanged')
    todoEventBus.$off('filterChanged')
    todoEventBus.$off('clearCompletedTodos')
    },
  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completion).length;
    },
    remainingZero() {
      return this.remaining != 0;
    },
        todosFiltered() {
      if (this.filter == 'all') {
        return this.todos
      } else if (this.filter == 'pending') {
        return this.todos.filter(todo => !todo.completion)
      } else if (this.filter == 'completed') {
        return this.todos.filter(todo => todo.completion)
      }
      return this.todos
    },
    showClearCompleted() {
      return this.todos.filter((todo) => todo.completion).length > 0;
    },
  },

  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        return;
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completion: false,
        editing: false,
      });
      (this.newTodo = " "), 
      this.idForTodo++;
    },
    removeTodo(index) {
      this.todos.splice(index, 1)
    },
    checkAllTodo() {
      this.todos.forEach((todo) => (todo.completion = event.target.checked))
    },
    clearCompleted() {
      this.todos = this.todos.filter((todo) => !todo.completion)
    },
    finishedEdit(){
        this.todos.splice('data.index', 1, 'data.todo')
    }
  },
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped lang="scss">
* {
  box-sizing: border-box;
}
.container {
  margin: auto;
  border: 1000px 600px;
  padding: 10px 20px;
}

.user-input {
  margin-right: auto;
  margin-left: auto;
  background: #7cd7f3;
  display: flex;
  border: 1px;
  border-color: rgb(3, 84, 121);
  width: 500px;
  height: 40px;
  border-radius: 5px;
  &:focus {
    outline: 0px;
  }
}


.completion {
  text-decoration: line-through;
  color: green;
}
</style>

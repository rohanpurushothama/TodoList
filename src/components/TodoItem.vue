<template>
  <div class="todo-item">
    <div class="todo-item-shown">
      <input type="checkbox" v-model="todo.completion" @change="doneEdit" />
      <div v-if="!editing" @dblclick="editTodo" class="todo-item-label" :class="{completion : completion}">
        {{title}}
      </div>
      <input v-else class="todo-item-edit" type="text" v-model="title" @blur="doneEdit" @keyup.enter="doneEdit" v-focus @keyup.esc="cancelEdit" />

      <div class="remove-item" @click="removeTodo(index)">
        &times;
      </div>
    </div>
  </div>
</template>

<script>

import { todoEventBus } from '@/event-bus';

export default {
    name: 'todo-item',
    props: {
        todo: {
            type: Object,
            required: true
        },
        index: {
            type: Object,
            required: true
        },
        checkAll: {
            type: Boolean,
            required: true
        }
    },
    data() {
        return{
            'id' : this.todo.id,
            'title': this.todo.title,
            'completion': this.todo.completion,
            'editing': this.todo.editing,
            'this.beforeEditCache': '',
        }
    },
    watch: {
        checkAll(){
        if(this.checkAll){
            this.completion = true
        } else {
            this.completion = this.todo.completion
        }
        }
    },
    directives: {
    focus: {
      inserted: function (el) {
        el.focus();
      },
    },
  },

    methods: {
    removeTodo(index){
        todoEventBus.$emit('deleteTodo', index)
    },
           editTodo(){
           this.beforeEditCache = this.title
           this.editing = true
       },
       doneEdit(){
           if(this.title.trim() == ''){
               this.title = this.beforeEditCache
           }
           this.editing = false
           todoEventBus.$emit('finishedEdit', {
               'index': this.index,
               'todo': {
                   'id': this.id,
                   'title': this.title,
                   'completion': this.completion,
                   'editing': this.editing,
               }
           })
       },
    cancelEdit(){
        this.title = this.beforeEditCache
        this.editing = false
    },
    }
}
</script>

<style coped lang="scss">
.todo-item-shown {
  margin: auto;
  display: flex;
  width: 500px;
  text-align: center;
  justify-content: space-between;
  padding: 5px;
}

</style>

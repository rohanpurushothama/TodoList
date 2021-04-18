<template>
          <div>
        <button v-if="showClearCompleted" @click="clearCompleted">
          Clear Completed
        </button>
      </div>
</template>

<script>
import { todoEventBus } from '@/event-bus';

export default {
    name: 'todo-clear-completed',
    props: {
        showClearCompleted: {
            type: Boolean,
            required: true,
        }
    },
    methods: {
        clearCompleted(){
            todoEventBus.$emit('clearCompletedTodo')
        }
    }
}
</script>
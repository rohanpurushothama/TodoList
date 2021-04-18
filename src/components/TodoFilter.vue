<template>
     <div>
        <button :class="{ active: filter == 'all' }" @click="changeFilter('all')">All</button>
        <button :class="{ active: filter == 'pending' }" @click="changeFilter('pending')">Pending</button>
        <button :class="{ active: filter == 'completed' }" @click="changeFilter('completion')">Completed</button>
      </div>
</template>

<script>
import {todoEventBus} from '@/event-bus'
export default {
    name: 'todo-filter',
    data(){
        return {
            'filter': 'all',
        }
    },
    methods: {
        changeFilter(filter){
            this.filter = filter
            todoEventBus.$emit('filterChanged', filter)
        }
    }
}
</script>
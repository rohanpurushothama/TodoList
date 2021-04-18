  
<template>
  <div><label><input type="checkbox" :checked="!remainingZero" @change="allChecked"> Check All</label></div>
</template>

<script>
import { todoEventBus } from '@/event-bus';

export default {
  name: 'todo-check-all',
  props: {
    anyRemaining: {
      type: Boolean,
      required: true,
    }
  },
  methods: {
    allChecked() {
      todoEventBus.$emit('checkAllChanged', this.remainingZero)
    }
  }
}
</script>

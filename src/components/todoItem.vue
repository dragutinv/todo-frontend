<template>
    <b-form-checkbox size="lg" v-model="isDone">
        <span class="todo-message" v-bind:class="{ doneTask: isDone }">{{ message }}</span>
        <span v-for="category in categories" v-bind:key="category.id" class="todo-category">{{ category}}</span>
    </b-form-checkbox>
</template>

<script>
import axios from 'axios'

export default {
  name: 'todoItem',
  data () {
    return {
      itemId: this.id,
      isDone: this.done
    }
  },
  props: ['id', 'message', 'categories', 'done'],
  watch: {
    isDone: function (value) {
      var self = this
      axios.get('/setStatusForItem?itemId=' + self.itemId + '&isActive=' + !value)
        .catch(function (error) {
          console.log(error)
        })
    }
  }
}
</script>

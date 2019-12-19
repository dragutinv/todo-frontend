<template>
    <b-form-checkbox size="lg" v-model="done">
        {{ message }}
        <span v-for="category in categories" v-bind:key="category.id" class="todo-category">{{ category}}</span>
    </b-form-checkbox>
</template>

<script>
export default {
  name: 'todoItem',
  props: ['message', 'categories', 'done']
}
</script>

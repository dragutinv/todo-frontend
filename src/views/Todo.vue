<template>
  <div class="home">
  <h3>TODO list</h3>
  <div v-for="item in items" v-bind:key="item.id">
    {{ item.message }}
    <ul>
      <li v-for='item in item.categories' v-bind:key="item.id">
          {{item.category}}
      </li>
    </ul>
  </div>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: 'todo',
  data () {
    return {
      items: [
        { message: 'Do this', categories: [ { category: 'cat1' } ] },
        { message: 'Do that', categories: [ { category: 'cat2' } ] }
      ]
    }
  },
  created () {
  }
}
</script>

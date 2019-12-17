<template>
  <div class="home">
    <h3>TODO list</h3>
      <div class='todo-add'>
        <input type="text" id="todo-new" placeholder="What should I remember to do" class="todo-text-input" />
        <vue-tags-input
                    v-model="category"
                    :tags="categories"
                    :autocomplete-items="autocompleteCategories"
                    placeholder="How I would categorise this"
                    @tags-changed="update"
                  />
      </div>
      <div v-for="item in activeTodoItems" v-bind:key="item.id" class="todo-list">
          <div class="todo-text"><input type="checkbox"/>{{ item.message }}</div>
          <div v-for="category in item.categories" v-bind:key="category.id" class="todo-categories">{{ category}}</div>
      </div>
      <div v-for="item in inactiveTodoItems" v-bind:key="item.id" class="todo-list">
          <div class="todo-text"><input type="checkbox"/>{{ item.message }}</div>
          <div v-for="category in item.categories" v-bind:key="category.id" class="todo-categories">{{ category}}</div>
      </div>
  </div>
</template>

<script>
// @ is an alias to /src
import VueTagsInput from '@johmun/vue-tags-input'
import axios from 'axios'

export default {
  name: 'todo',
  components: {
    VueTagsInput
  },
  data () {
    return {
      category: '',
      autocompleteCategories: [],
      items: [
        { message: 'Do this', categories: [ 'cat1', 'cat2' ], done: false },
        { message: 'Do that', categories: [ 'cat2' ], done: true }
      ],
      autocompleteTimeout: null
    }
  },
  watch: {
    'tag': 'initCategories'
  },
  computed: {
    activeTodoItems: function () {
      return this.items.filter(i => i.done === false)
    },
    inactiveTodoItems: function () {
      return this.items.filter(i => i.done === true)
    }
  },
  methods: {
    update (newTags) {
      this.autocompleteCategories = []
      this.tags = newTags
    },
    initCategories () {
      if (this.tag.length < 2) return
      const url = `https://itunes.apple.com/search?term=
        ${this.tag}&entity=allArtist&attribute=allArtistTerm&limit=6`

      clearTimeout(this.autocompleteTimeout)
      this.autocompleteTimeout = setTimeout(() => {
        axios.get(url).then(response => {
          this.autocompleteCategories = response.data.results.map(a => {
            return { text: a.artistName }
          })
        }).catch(() => console.warn('Oh. Something went wrong'))
      }, 600)
    }
  }
}
</script>

<template>
  <div class="home">
  <h3>TODO list</h3>
    <div v-for="item in activeTodoItems" v-bind:key="item.id">
      {{ item.message }}
      <div>
        <vue-tags-input
          v-model="category"
          :tags="categories"
          :autocomplete-items="autocompleteCategories"
          @tags-changed="update"
        />
      </div>
    </div>
    <div v-for="item in inactiveTodoItems" v-bind:key="item.id">
        <del>{{ item.message }}</del>
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
        { message: 'Do this', categories: [ { category: 'cat1' } ], done: false },
        { message: 'Do that', categories: [ { category: 'cat2' } ], done: true }
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

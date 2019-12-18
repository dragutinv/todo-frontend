<template>
  <div class="home">
      <b-container>
        <b-row class="text-center">
          <b-col>
           <h3>TODO list</h3>
          </b-col>
        </b-row>
        <b-row style="margin-top:2rem">
          <b-col>
            <b-input-group>
              <b-form-input id="input-large" size="lg" placeholder="What should I remember to do"></b-form-input>
              <b-button variant="outline-primary" size="lg">Save</b-button>
            </b-input-group>
          </b-col>
         </b-row>
         <b-row>
          <b-col>
            <vue-tags-input
                        v-model="category"
                        :autocomplete-items="autocompleteCategories"
                        placeholder="How I would categorise this"
                        @tags-changed="update"
                      />
          </b-col>
         </b-row>
         <b-row style="margin-top:2rem">
          <b-col>
            <div v-for="item in activeTodoItems" v-bind:key="item.id"  class="todo-item">
             <b-form-checkbox size="lg">
              {{ item.message }}
              {{ item.done }}
              <span v-for="category in item.categories" v-bind:key="category.id" class="todo-category">{{ category}}</span>
             </b-form-checkbox>
            </div>
          </b-col>
         </b-row>
         <b-row>
          <b-col>
            <todoItem></todoItem>
            <div v-for="item in inactiveTodoItems" v-bind:key="item.id" class="todo-item">
              <b-form-checkbox size="lg">
                {{ item.message }}
                <span v-for="category in item.categories" v-bind:key="category.id" class="todo-category">{{ category}}</span>
               </b-form-checkbox>
            </div>
          </b-col>
         </b-row>
      </b-container>
     </div><!-- /.home -->
</template>

<script>
// @ is an alias to /src
import VueTagsInput from '@johmun/vue-tags-input'
import axios from 'axios'
import todoItem from '../components/todoItem'

// Define a new component called button-counter

export default {
  name: 'todo',
  components: {
    VueTagsInput, todoItem
  },
  data () {
    return {
      checked: '',
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

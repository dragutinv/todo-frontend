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
                    <b-form-group
                            :invalid-feedback="invalidFeedback"
                            :valid-feedback="validFeedback"
                            :state="state"
                    >
                        <b-input-group>
                        <b-form-input id="input-large" size="lg" v-model="newTodo" :state="state"
                                      placeholder="What should I remember to do" trim></b-form-input>
                        <b-button variant="outline-primary" size="lg" v-on:click="saveTodo" :disabled="!state">Save</b-button>
                        </b-input-group>
                    </b-form-group>

                </b-col>
            </b-row>
            <b-row>
                <b-col>
                    <vue-tags-input
                            v-model="newTodoCategory"
                            :tags="tags"
                            :autocomplete-items="autocompleteCategories"
                            placeholder="How I would categorise this"
                            @tags-changed="newTags => tags = newTags"
                    />
                </b-col>
            </b-row>
            <b-row style="margin-top:2rem">
                <b-col>
                    <todoItem v-for="item in activeTodoItems" v-bind:key="item.id" v-bind:message="item.message"
                              v-bind:categories="item.categories" v-bind:done="item.done"></todoItem>
                </b-col>
            </b-row>
            <b-row>
                <b-col>
                    <todoItem v-for="item in inactiveTodoItems" v-bind:key="item.id" v-bind:message="item.message"
                              v-bind:categories="item.categories" v-bind:done="item.done"></todoItem>
                </b-col>
            </b-row>
        </b-container>
    </div><!-- /.home -->
</template>

<script>
import VueTagsInput from '@johmun/vue-tags-input'
import axios from 'axios'
import todoItem from '@/components/todoItem'

export default {
  name: 'todo',
  components: {
    VueTagsInput, todoItem
  },
  data () {
    return {
      newTodo: '',
      tags: [],
      newTodoCategory: '',
      autocompleteCategories: [],
      items: [
        { id: '1', message: 'Do this', categories: [ 'cat1', 'cat2' ], done: false },
        { id: '2', message: 'Do that', categories: [ 'cat2' ], done: true }
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
    },
    state () {
      if (this.newTodo.length === 0) return false
      if (this.isNewTodoItemAlreadyInTheList()) return false
      return true
    },
    invalidFeedback () {
      if (this.newTodo.length === 0) {
        return 'Did you miss to write your todo task?'
      }
      if (this.isNewTodoItemAlreadyInTheList()) {
        return 'This todo task is already in your list'
      }
      return 'aaa'
    },
    validFeedback () {
      return ''
    }
  },
  methods: {
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
    },
    saveTodo: function () {
      var associatedCategories = []
      this.tags.forEach(function (item, index) {
        associatedCategories.push(item.text)
      })
      this.items.unshift({ message: this.newTodo, done: false, categories: associatedCategories })
    },
    isNewTodoItemAlreadyInTheList () {
      var isAlreadyInTodoList = false
      this.items.forEach(function (item, index) {
        if (item.message.toLowerCase().trim() === this.newTodo.toLowerCase().trim()) {
          isAlreadyInTodoList = true
        }
      }, this)
      return isAlreadyInTodoList
    }
  }
}
</script>

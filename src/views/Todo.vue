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
                            <b-button variant="outline-primary" size="lg" v-on:click="saveTodo" :disabled="!state">
                                Save
                            </b-button>
                        </b-input-group>
                    </b-form-group>

                </b-col>
            </b-row>
            <b-row>
                <b-col>
                    <vue-tags-input
                            v-model="newTodoCategory"
                            :tags="tags"
                            :autocomplete-items="getAutocompleteCategories"
                            placeholder="How I would categorise this"
                            @tags-changed="newTags => tags = newTags"
                    />
                </b-col>
            </b-row>
            <b-row style="margin-top:3rem">
                <b-col>
                    <label>Filter based on category</label>
                    <vue-tags-input
                            v-model="filterTodoCategory"
                            :tags="tagsForFiltering"
                            :autocomplete-items="getAutocompleteCategories"
                            :add-only-from-autocomplete="true"
                            placeholder="Please enter categories"
                            @tags-changed="newTags => tagsForFiltering = newTags"
                    />
                </b-col>
            </b-row>
            <b-row style="margin-top:1rem">
                <b-col>
                    <todoItem v-for="item in activeTodoItems" v-bind:key="item.id" v-bind:id="item.id" v-bind:message="item.message"
                              v-bind:categories="item.categories" v-bind:done="item.done"></todoItem>
                </b-col>
            </b-row>
            <b-row>
                <b-col>
                    <todoItem v-for="item in inactiveTodoItems" v-bind:key="item.id" v-bind:id="item.id" v-bind:message="item.message"
                              v-bind:categories="item.categories" v-bind:done="item.done"></todoItem>
                </b-col>
            </b-row>
        </b-container>
    </div><!-- /.home -->
</template>

<script>
import VueTagsInput from '@johmun/vue-tags-input'
import todoItem from '@/components/todoItem'
import axios from 'axios'

axios.defaults.baseURL = 'http://localhost:8090/api'
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded'

export default {
  name: 'todo',
  components: {
    VueTagsInput, todoItem
  },
  data () {
    return {
      newTodo: '',
      tags: [],
      tagsForFiltering: [],
      newTodoCategory: '',
      filterTodoCategory: '',
      items: [],
      serverCategories: []
    }
  },
  created: function () {
    this.getTodoItemsFromServer()
    this.getCategoriesFromServer()
  },
  computed: {
    activeTodoItems: function () {
      return this.getFilteredItems(false)
    },
    inactiveTodoItems: function () {
      return this.getFilteredItems(true)
    },
    state () {
      if (this.newTodo.length === 0) {
        return false
      }
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
      return ''
    },
    validFeedback () {
      if (this.newTodo.length > 10) {
        return "Don't forget to add categories"
      }
      return ''
    },
    getAutocompleteCategories () {
      var existingCategories = this.getUniqueExistingCategories()
      var categories = Array.from(existingCategories)
      var categoriesForFiltering = []

      categories.forEach(function (item, index) {
        categoriesForFiltering.push({ text: item })
      })

      return categoriesForFiltering
    }
  },
  methods: {
    saveTodo: function () {
      var associatedCategories = []
      this.tags.forEach(function (item, index) {
        associatedCategories.push(item.text)
      })
      var newTodoItem = { message: this.newTodo, done: false, categories: associatedCategories }
      this.items.unshift(newTodoItem)
      this.saveTodoItemToServer(newTodoItem)
      this.newTodo = ''
      this.tags = []
    },
    isNewTodoItemAlreadyInTheList () {
      var isAlreadyInTodoList = false
      this.items.forEach(function (item, index) {
        if (item.message.toLowerCase().trim() === this.newTodo.toLowerCase().trim()) {
          isAlreadyInTodoList = true
        }
      }, this)
      return isAlreadyInTodoList
    },
    getUniqueExistingCategories () {
      var existingCategories = new Set()
      this.items.forEach(function (item, index) {
        item.categories.forEach(function (category, index) { existingCategories.add(category) }, this)
      }, this)

      this.serverCategories.forEach(function (category, index) {
        existingCategories.add(category)
      }, this)
      return existingCategories
    },
    getCategoriesForFiltering () {
      var categoriesForFilter = []
      this.tagsForFiltering.forEach(function (item, index) {
        categoriesForFilter.push(item.text)
      })
      return categoriesForFilter
    },
    intersect (a, b) {
      return [...new Set(a)].filter(x => new Set(b).has(x))
    },
    getFilteredItems (isDone) {
      var filterBasedOnCategories = this.getCategoriesForFiltering()
      if (filterBasedOnCategories.length > 0) {
        return this.items.filter(i => i.done === isDone && this.intersect(i.categories, filterBasedOnCategories).length > 0)
      }
      return this.items.filter(i => i.done === isDone)
    },
    getTodoItemsFromServer () {
      var self = this
      axios.get('/allTodoItems')
        .then(function (response) {
          self.items = response.data
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    saveTodoItemToServer (newTodoItem) {
      axios.post('/saveItem', newTodoItem
      ).catch(function (error) {
        console.log(error)
      })
    },
    getCategoriesFromServer () {
      var self = this
      axios.get('/categories')
        .then(function (response) {
          self.serverCategories = response.data
        })
        .catch(function (error) {
          console.log(error)
        })
    }
  }
}
</script>

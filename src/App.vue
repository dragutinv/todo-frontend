<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

.home {
  margin-top: 5%;
}

.vue-tags-input .ti-input {
  border-radius: 0.3rem;
}

.todo-category {
  background-color: #5C6BC0;
  color: #fff;
  border-radius: 0.3rem;
  padding: 3px 5px;
  margin: 2px;
  font-size: .65em;
}

.todo-message {
  margin-right: 1rem;
}

.doneTask {
  text-decoration: line-through;
}

</style>

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
  text-align: center;
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

/* TODO - ADD NEW - start */

.todo-add {
  margin-bottom: 20px;
  padding: 10px 0;
  width: 40%;

}
.todo-text-input {
  padding: 10px;
  font-size: 1.3em;
  width: 100%;
  margin-bottom: 5px;
}

/********************/

/* TODO - SHOW LIST - start */

.todo-list {
  margin-bottom: 10px;
}

.todo-text {
  text-align: left;
  padding: 4px 0;
}

.todo-categories {

}

/********************/
</style>

<template>
  <div id="app">
    <div v-if="isLoggedIn">
    <div class="hero is-success">
      <div class="is-fullwidth">
        <button @click="logout" class="button is-normal is-dark">LOGOUT</button>
        <h1 class="app-title title is-1">Vue Todos</h1>
        <span class="app-title subtitle is-5">{{moment(new Date()).format('MM/DD/YYYY')}}</span>
      </div>
    <!-- We need some navigation links to switch between views -->
      <nav class="tabs is-boxed is-fullwidth">
        <ul div class="hero-foot">
          <!--
          The RouterLink component comes with VueRouter.
          The 'to' prop is the URL route.
          -->
          <li><router-link to="/">All</router-link></li>
          <li><router-link to="/active">Active</router-link></li>
          <li><router-link to="/completed">Completed</router-link></li>
        </ul>
      </nav>
    </div>
    <section class="hero is-light">
      <add-task-form @taskAdded="addTask" />
      <!--
      The RouterView is a placeholder that VueRouter uses to know
      where to insert the designated component for a given URL.
      In this case we are passing our taskList as a prop to each route's
      component and we are listening for user events.  This way we are still
      only mainting one master list in the App.vue component.
      -->
      <router-view
      :tasks="taskList"
      @toggleDone="toggleDone"
      @removeTask="removeTask"
      />
      </section>
  </div>
  <login-page v-else @saveApiTokens="saveApiTokens" />
  </div>
</template>

<script>
/* eslint-disable */
import moment from 'moment'
import axios from 'axios'
import AddTaskForm from '@/components/AddTaskForm'
import LoginPage from '@/pages/LoginPage'

export default {
  name: 'App',
  components: { LoginPage, AddTaskForm },
  data () {
    return {
      baseURL: 'https://vue-todos.robertmckenney.ca/api',
      api: {
        accessToken: null,
        expiresAt: null,
        },
      taskList: [
        // { id: 1234, title: 'Learn Vue', isComplete: true, priority: 'medium' },
        // { id: 1235, title: 'Learn Vue Router', isComplete: false, priority: 'medium' },
        // { id: 1236, title: 'Learn Vuex', isComplete: false, priority: 'medium' },
        // { id: 1237, title: 'Learn Vue DevTools', isComplete: true, priority: 'medium' }
      ]
    }
  },
  created () {
    axios
      .get(`${this.baseURL}/priorities`)
      .then(response => {
        this.priorityOptions = response.data.data.length 
          ? response.data.data
          : []
      })
      .catch(error => { console.error(error) })
      this.loadInitialData()
  },
  methods: {
    refreshTasks () {
    axios
      .get('/todos', this.axiosOptions)
      .then(response => { this.taskList = response.data.data })
      .catch(error => { this.handleError(error) })
  },
    addTask (task) {
    axios
      .post('/todos', task, this.axiosOptions)
      .then(response => { this.taskList.push(response.data.data) })
      .catch(error => { this.handleError(error) })
  },
    toggleDone (task) {
    task.isComplete = !task.isComplete
    axios
      .put(`/todos/${task.id}`, task, this.axiosOptions)
      .catch(error => { this.handleError(error) })
  },
    removeTask (task) {
    axios
      .delete(`/todos/${task.id}`, this.axiosOptions)
      .then(response => {
        const taskIndex = this.taskList.findIndex(t => t.id === task.id)
        this.taskList.splice(taskIndex, 1)
      })
      .catch(error => { this.handleError(error) })
  },
    saveTaskList () {
      localStorage.setItem('taskList', JSON.stringify(this.taskList))
    },
    saveApiTokens (apiTokens) {
      this.api.accessToken = apiTokens.access_token
      this.api.expiresAt = apiTokens.expires_at
      localStorage.setItem('todoApiTokens', JSON.stringify(apiTokens))
      this.refreshTasks()
    },
    loadInitialData () {
    const apiTokens = JSON.parse(localStorage.getItem('todoApiTokens'))
    if (apiTokens) {
    this.api.accessToken = apiTokens.access_token
    this.api.expiresAt = apiTokens.expires_at
    if (this.isLoggedIn) this.refreshTasks()
    }
  },
    logout () {
  this.api.accessToken = null
  this.api.expiresAt = null
  localStorage.removeItem('todoApiTokens')
  }
  },
  computed: {
    isLoggedIn () {
      return this.api.accessToken && moment(this.api.expiresAt).isAfter()
    },
    axiosOptions () {
    return {
      baseURL: this.baseURL,
      headers: { 'Authorization': `Bearer ${this.api.accessToken}` }
      }
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  width: 100%;
  max-width: 550px;
  margin: 60px auto;
}
ul {
  list-style-type: none;
}
nav ul {
  display: flex;
  justify-content: flex-start;
}
nav ul li {
  margin-right: 0.05em;
}
section {
  margin-bottom: 0.1em;
}
.app-title {
margin-top: 0.75rem;
margin-left: 0.75rem;
}
.is-dark {
  float: right;
}
</style>
<template>
  <div id="app">
    <div v-if="isLoggedIn">
    <div class="hero is-success">
      <div class="is-fullwidth">
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
import moment from 'moment'
import axios from 'axios'
import AddTaskForm from '@/components/AddTaskForm'
import LoginPage from '@/pages/LoginPage'

export default {
  name: 'App',
  components: { LoginPage, AddTaskForm },
  data () {
    return {
      isLoggedIn: false,
      taskList: [
        // { id: 1234, title: 'Learn Vue', isComplete: true, priority: 'medium' },
        // { id: 1235, title: 'Learn Vue Router', isComplete: false, priority: 'medium' },
        // { id: 1236, title: 'Learn Vuex', isComplete: false, priority: 'medium' },
        // { id: 1237, title: 'Learn Vue DevTools', isComplete: true, priority: 'medium' }
      ]
    }
  },
  created () {
    localStorage.clear()
    this.taskList = JSON.parse(localStorage.getItem('taskList')) || []
    console.log(this.taskList)
  },
  methods: {
    addTask (task) {
      this.taskList.push(task)
      this.saveTaskList()
    },
    toggleDone (task) {
      task.isComplete = !task.isComplete
      this.saveTaskList()
    },
    removeTask (task) {
      const taskIndex = this.taskList.indexOf(task)
      this.taskList.splice(taskIndex, 1)
      this.saveTaskList()
    },
    saveTaskList () {
      localStorage.setItem('taskList', JSON.stringify(this.taskList))
    },
    saveApiTokens (apiTokens) {
      this.api.accessToken = apiTokens.access_token
      this.api.expiresAt = apiTokens.expires_at
      localStorage.setItem('todoApiTokens', JSON.stringify(apiTokens))
      this.refreshTasks()
    }
  },
  computed: {
    isLoggedIn () {
      return this.api.accessToken && moment(this.api.expiresAt).isAfter()
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
</style>
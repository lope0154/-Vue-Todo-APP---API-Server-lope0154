<template>
  <div class="form_Area">

    <!-- Due dates, Priority, Category dropdown -->
      <div class="dropdown">
      <div class="dropdown-trigger columns">
        <div class="column is-two-fifths">
          <label>Due date
            <input type="date" v-model="newTask.dueDate" class="button">
          </label>
        </div>
        <div class="column">
          <label>Priority
            <label class="select dropdown-trigger">
            <select v-model="newTask.priority" class="button">
              <option value="1">high</option>
              <option value="2">medium</option>
              <option value="3">low</option>
            </select>
            </label>
          </label>
        </div>
        <div class="column">
          <label>Category
            <label class="select dropdown-trigger">
            <select v-model="newTask.category" class="button">
              <option value="0"> </option>
              <option value="1">home</option>
              <option value="2">school</option>
              <option value="3">work</option>
            </select>
            </label>
          </label>
        </div>
      </div>
      </div>

    <!-- Task title & addTask button -->
    <div>
      <section>
        <div class="control">
        <input class="input" type="text" placeholder="Task Title" v-model.trim="newTask.title" @keyup.enter="addTask">
        </div>
        <button @click="addTask" class="addButton button is-warning is-pulled-right">Add Task</button>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      newTask: {}
    }
  },
  created () {
    this.newTask = this.getNewTask()
  },
  methods: {
    addTask () {
      // this.taskList.push(this.newTask)
      this.$emit('taskAdded', this.newTask)
      this.newTask = this.getNewTask()
    },
    getNewTask () {
      const now = new Date()
      const today = now.toISOString().substr(0, now.toISOString().indexOf('T'))
      console.log(now)
      return {
        id: null,
        category: ' ',
        dueDate: today,
        isComplete: false,
        priority: 'medium',
        title: ''
      }
    }
  }
}
</script>

<style>
.form_Area  {
  padding: 0.75rem;
}
.control {
  margin-top: 1rem;
}
.addButton  {
  margin-top: 0.6rem;
}
</style>

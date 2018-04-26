<template>
  <div>
    <!-- Task-list order buttons -->
    <div class="box">
    <h2 class="app-title title is-3">{{listTitle}}</h2>
      <div class="toolbar, dropdown">
        <div class="column is-two-fifths">
          <label>Order Date</label>
          <button @click="sortAscending = !sortAscending"  class="button is-medium is-outlined">Ascending</button>
        </div>
        <div class="column">
          <label for="priority-filter" class="dropdown-trigger">Show priority</label>
          <select name="priority-filter" id="priority-filter" v-model="selectedPriority" class="select button is-medium">
            <option value="">
              all
            </option>
            <option
              v-for="option in priorityOptions"
              :value="option"
              :key="option">{{option}}
            </option>
          </select>
        </div>
        <div class="column">
          <label for="category-filter" class="dropdown-trigger">Show category</label>
          <select name="category-filter" id="category-filter" v-model="selectedCategory" class="select button is-medium">
            <option value="">
              all
            </option>
            <option
              v-for="option in categoryOptions"
              :value="option"
              :key="option">{{option}}
            </option>
          </select>
        </div>
      </div>
    </div>

    <!-- Task-list Items -->
    <div class="box">
      <ul class="task-list">
        <li v-for="task in sortedTasks" :key="task.id" class="task-item">
          <div class="column">
            <span class="far"
             :class="{
             ' fa-circle': ! task.isComplete,
             ' fa-check-circle': task.isComplete}"
             @click="toggleDone(task)"></span>
          </div>
          <div class="column is-8">
            <span class="title is-5">{{ task.title }}</span>
          <section>
            <span class="title is-6">{{ task.category }}</span>
            <span>{{ task.description }}</span>
          </section>
          <section>
            <span class="">{{ task.dueDate }}</span>
          </section>
          </div>
          <div class="column">
            <span class="tag is-medium">{{ task.priority.name }}</span>
          </div>
          <div class="column is-warning">
            <span class="far fa-times-circle" @click="removeTask(task)"></span>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
import axios from 'axios'

export default {
  // We are not actually managing the task list in this component.
  // We are only displaying the list. The parent component (App.vue) is
  // passing down a reference to the list in the 'tasks' prop.
  props: ['tasks', 'listTitle'],
  data () {
    return {
      priorityOptions: ['low', 'medium', 'high'],
      categoryOptions: [' ', 'home', 'school', 'work'],
      selectedPriority: '',
      selectedCategory: '',
      sortAscending: true
    }
  },
  computed: {
    filteredPriorityTasks () {
      // If the selectedFilter is 'all', the value is set to an empty string
      // which will evaluate to false. In that case, we will reuturn the
      // entire task list. Otherwise we use the array.filter() method to return
      // only the tasks with the selected priority.
      return (!this.selectedPriority)
        ? this.tasks
        : this.tasks.filter(task => task.priority === this.selectedPriority)
    },
    filteredCategoryTasks () {
      return (!this.selectedCategory)
        ? this.tasks
        : this.tasks.filter(task => task.category === this.selectedCategory)
    },
    filteredTasks () {
      const combined = [
        ...this.filteredPriorityTasks,
        ...this.filteredCategoryTasks
      ]
      const merged = [...new Set(combined)]
      return merged.filter(task => 
        this.filteredPriorityTasks.includes(task) &&
        this.filteredCategoryTasks.includes(task)
      )
    },
    sortedTasks () {
      return [...this.filteredTasks].sort((a, b) =>{
        const dateOne = new Date(a.dueDate)
        const dateTwo = new Date(b.dueDate)
        return this.sortAscending ? (dateOne - dateTwo) : (dateTwo - dateOne)
      })
    }
  },
  methods: {
    // Since this component does not own the task list data, we need to
    // notify the parent component that the user wants to toggle the
    // completion status of the task.  We do that by emitting a custom event,
    // and passing up the affected task with the event. We will listen for
    // this event in the parent component and call the appropriate method to
    // make the change to the data.
    toggleDone (task) {
      this.$emit('toggleDone', task)
    },
    removeTask (task) {
      this.$emit('removeTask', task)
    }
  }
}
</script>

<style>
/* We will add styles here later. */
.task-item {
  display: grid;
  grid-template-columns: auto 1fr auto auto;
  align-items: center;
}
.toolbar {
  margin-bottom: 1rem;
}
section {
  padding-bottom: 0.25em;
}
</style>
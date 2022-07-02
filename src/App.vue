<script>
export default {
  data() {
    return {
      items: [
        {
          task: "Water plants in the garden",
          priority: 3
        },
        {
          task: "Play football",
          priority: 4
        },
        {
          task: "Read a book",
          priority: 1
        }
      ],
      selected: '',
      query: '',
      task: '',
      priority: '',
      allowDuplicates: false
    }
  },
  computed: {
    filteredItems() {
      if (this.query !== '')
        this.selected = '' // cannot risk a hidden selected item
      return this.items.filter((n) =>
          n.task.toLowerCase().includes(this.query.toLowerCase())
      )
    }
  },
  watch: {
    selected(item) {
      if (item !== '' && (!Object.hasOwn(item, "task") || !Object.hasOwn(item, "priority"))) {
        item = JSON.parse(item)
      }
      this.task = item.task
      this.priority = item.priority
    }
  },
  methods: {
    create() {
      if (this.hasValidInput()) {
        const item = {task: `${this.task}`, priority: parseInt(`${this.priority}`)}
        if (!this.items.map((x) => JSON.stringify(x)).includes(JSON.stringify(item)) || this.allowDuplicates) {
          this.items.push(item)
          this.task = this.priority = ''
        }
      }
    },
    update() {
      if (this.hasValidInput() && this.selected) {
        const i = this.items.map((x) => JSON.stringify(x)).indexOf(this.selected)
        let string = `{"task": "${this.task}", "priority": ${this.priority}}`
        // console.log(JSON.parse(string)) // debugging only
        // console.log(i) // debugging only
        this.items[i] = JSON.parse(string)
        this.selected = JSON.stringify(this.items[i])
      }
    },
    del() {
      if (this.selected) {
        const i = this.items.map((x) => JSON.stringify(x)).indexOf(this.selected)
        this.items.splice(i, 1)
        this.selected = ''
        this.task = ''
        this.priority = ''
      }
    },
    hasValidInput() {
      return this.task.trim() && this.priority > 0
    }
  }
}
</script>

<template>
  <div><input v-model="query" placeholder="Filter task"></div>

  <select size="5" v-model="selected" title="Task List">
    <option v-for="item in filteredItems">{{ JSON.stringify(item) }}</option>
  </select>

  <div class="inputs">
    <label>Task: <input v-model="task"></label>
    <label>Priority: <input v-model="priority"></label>
    <label><input v-model="allowDuplicates" type="checkbox">Allow duplicates while creating</label>
  </div>

  <div class="buttons">
    <button @click="create">Create</button>
    <button @click="update">Update</button>
    <button @click="del">Delete</button>
  </div>
</template>

<style>
* {
  font-size: inherit;
  color: coral;
  border-color: crimson;
  background: #292929;
}

.inputs {
  display: grid;
}

input {
  display: block;
  border-radius: 10px;
  margin-bottom: 10px;
  width: auto;
}

input[type='checkbox'] {
  min-height: 1.5em;
  min-width: 1.5em;
}

select {
  float: left;
  margin: 0 1em 1em 0;
  max-width: 80%;
  min-width: 50%;
  width: fit-content;
  overflow-x: auto;
}

option:hover {
  color: red;
  background-color: #404040;
}

option:checked {
  color: black;
  background-color: red;
}

.buttons {
  display: grid;
}

button {
  min-height: 2.5em;
}

</style>

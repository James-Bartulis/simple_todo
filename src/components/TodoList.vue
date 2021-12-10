<script>
export default {
  name: "TodoList",
  data() {
    return {
      name: '',
      list: [
        {name: 'Home', type: 'Home', done: false},
        {name: 'Work', type: 'Work', done: true},
        {name: 'Hobby', type: 'Hobby', done: false},
        {name: 'Urgent', type: 'Urgent', done: true},
      ],
      type: 'Home',
      filterBy: undefined

    }
  },
  computed: {
    filteredList() {
      if(this.filterBy == undefined) return this.list;
      return this.list.filter(task => task.done == this.filterBy);
    }
  },
  methods: {
    addTask() {
      this.name = this.name.trim();
      if(this.name == '') return;

      this.list.push({
        name: this.name,
        type: this.type,
        done: false
      });

      this.name = '';
    },
    deleteTask(todo) {
      this.list.splice(this.list.indexOf(todo), 1);
    },
    setFilter(value) {
      this.filterBy = value;
    },
    toggleTask(todo) {
      todo.done = !todo.done;
      zoom.target.blur();
    }
  }
}
</script>

<template>
  <div class="Container">
    <h1><u>TodoList</u></h1><br>
    <div class="newTask">
      <input
        v-model="name"
        @keyup.enter="addTask()"
        placeholder="New Task"
        type="text">
      <button @click="addTask()">Add Task</button>
      <select v-model="type" :class="type">
        <option class="Home">Home</option>
        <option class="Work">Work</option>
        <option class="Hobby">Hobby</option>
        <option class="Urgent">Urgent</option>
      </select>
    </div>
    <ul>
      <li v-for="todo in filteredList" :class="todo.type">
        <input
          v-model="todo.name"
          @click="toggleTask(todo, $zoom)"
          :class="{done : todo.done}"
          type="text">
        <button @click="deleteTask(todo)">x</button>
      </li>
    </ul>
    <div class="footer">
      <div :class="{selected : filterBy == undefined}" @click="setFilter()">All</div>
      <div :class="{selected : filterBy == false}" @click="setFilter(false)">Todo</div>
      <div :class="{selected : filterBy == true}" @click="setFilter(true)">Completed</div>
    </div>
  </div>
</template>

<style lang="sass" scoped>
$blue: #021f4b
$yellow: #cb8a2c
$green: #01342d
$red: #470119

$textDark: #333
$textLight: #DDD
$removeButton: rgba(200, 200, 200, 0.5)
$selected: rgba(100, 100, 100, 0.5)

$todoBackground: #f4e5d2
* 
  cursor: default
  user-select: none
.Container
  background-color: $todoBackground
  margin: auto
  width: fit-content
  padding: 10px
  border-radius: 5px
  & > *
    width: 300px
    font-size: 1.5em
    font-family: Verdana, Geneva, Tahoma, sans-serif
    margin: 0
    padding: 0
  & > .newTask
    display: flex
    justify-content: space-between
    & > *
      outline: none
      border: none
      padding: 5px
    & > input, & > button
      flex-grow: 1
      border-bottom: 2px solid $textDark
      background-color: inherit
    & > input
      cursor: text
    & > select
      color: $textLight
      border-top-right-radius: 5px
      border-bottom-right-radius: 5px
  & > ul
    list-style: none
    & > li
      display: flex
      margin-top: 5px
      padding: 5px 10px
      border-radius: 5px
      & > *
        padding: 5px
        border: none
        outline: none
        font-size: 0.9em
        background-color: inherit
      & > input
        flex-grow: 1
        color: $textLight
      & > button
        color: $removeButton
        font-weight: bold
  & > .footer
    display: flex
    justify-content: space-between
    margin-top: 5px
    & > div
      font-size: 0.7em
      border: 1px solid $textDark
      border-radius: 5px
      padding: 5px

.done
  text-decoration: line-through 3px solid $textLight
.Home
  background-color: $blue
.Work
  background-color: $yellow
.Hobby
  background-color: $green
.Urgent
  background-color: $red
.selected
  background-color: $selected
</style>
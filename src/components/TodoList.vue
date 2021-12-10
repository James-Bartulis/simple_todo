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
      filterBy: undefined,
      editCache: '',
      editedTask: undefined,
      click: undefined,
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
    editTask(todo, event){
      this.editCache = todo.name;
      this.editedTask = todo;
      event.target.focus();
    },
    cancelEdit(todo, event){
      if(this.editedTask != todo) return;
      todo.name = this.editCache;
      this.editedTask = undefined;
      event.target.blur();
    },
    doneEdit(todo, event){
      if(this.editedTask != todo) return;
      this.editedTask = undefined;
      todo.name = todo.name.trim();
      if(todo.name == '')
        this.deleteTask(todo);
      event.target.blur();
    },
    deleteTask(todo) {
      this.list.splice(this.list.indexOf(todo), 1);
    },
    setFilter(value) {
      this.filterBy = value;
    },
    toggleTask(todo) {
      todo.done = !todo.done;
    },
    clickHandler(event) {
      event.preventDefault();
      return new Promise ((resolve) => {
        if (this.click) {
          clearTimeout(this.click)
          resolve(2) // return 2 for double click
        }
        this.click = setTimeout(() => {
         this.click = undefined
         resolve(1) // return 1 for single click
        }, 200)
      });
    },
    clickAction(todo, event, clickType) {
      if(clickType == 1) // single click
        this.toggleTask(todo);
      else // double click
        this.editTask(todo, event);
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
          @mousedown="clickHandler($event).then(clickType => clickAction(todo, $event, clickType))"
          @blur="doneEdit(todo, $event)"
          @keyup.enter="doneEdit(todo, $event)"
          @keyup.esc="cancelEdit(todo, $event)"
          :class="{done : todo.done && editedTask != todo}"
          tabindex="-1"
          type="text">
        <button @click="deleteTask(todo)">x</button>
      </li>
      <!-- when filteredList empty -->
      <li v-if="filteredList.length == 0">
        <p v-if="filterBy">There are no completed tasks.</p>
        <p v-else>There are no tasks, relax! :D</p>
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
    width: 350px
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
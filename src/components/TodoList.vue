<script>
export default {
  name: "TodoList",
  data() {
    return {
      name: '',
      list: [],
      type: 'Home',
      filterBy: undefined,
      filterType: false,
      editCache: '',
      editedTask: undefined,
      click: undefined,
      darkMode: false,
    }
  },
  computed: {
    filteredList() {
      if(this.filterBy == undefined && this.filterType == false) return this.list;
      let l = this.list;
      if(this.filterType) l = this.filterByType(l);
      if(this.filterBy != undefined) l = this.filterByDone(l);
      return l;
    },
    totalLength() {
      return this.list.length;
    },
    todoLength() {
      return this.list.reduce((prev, cur) => {
        return !cur.done + prev;
      }, 0);
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
    toggleFilterType() {
      this.filterType = !this.filterType;
    },
    filterByType(l) {
        return l.filter(task => task.type == this.type);
    },
    filterByDone(l) {
        return l.filter(task => task.done == this.filterBy);
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
    },
    toggleDarkMode() {
      this.darkMode = !this.darkMode;
      document.body.classList.toggle('dark');
      localStorage.setItem("darkMode", JSON.stringify(this.darkMode));
    }
  },
  mounted() {
    this.darkMode = JSON.parse(localStorage.getItem("darkMode"));
    if(this.darkMode)
      document.body.classList.toggle('dark');
    this.list = JSON.parse(localStorage.getItem("list")) || [
        {name: 'Home', type: 'Home', done: false},
        {name: 'Work', type: 'Work', done: true},
        {name: 'Hobby', type: 'Hobby', done: false},
        {name: 'Urgent', type: 'Urgent', done: true},
      ];
  },
  watch: {
    list: {
      handler(newValue, oldValue) {
        localStorage.setItem("list", JSON.stringify(newValue));
      },
      deep: true
    }
  }
}
</script>

<template>
  <div class="Container">
    <div class="header">
      <h1><u>TodoList</u></h1>
      <label class="switch">
        <input @click="toggleDarkMode()" class="checkbox" type="checkbox">
        <span class="slider"></span>
      </label>
    </div><br>

    <div class="newTask">
      <input
        v-model="name"
        @keyup.enter="addTask()"
        placeholder="New Task"
        type="text">
      <button @click="addTask()">Add Task</button>
      <button @click="toggleFilterType()">
        Filter
      </button>
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
        <button v-if="todo.done" @click="deleteTask(todo)">x</button>
      </li>
      <!-- when filteredList empty -->
      <li v-if="filteredList.length == 0">
        <p v-if="filterBy">There are no completed tasks.</p>
        <p v-else>There are no tasks, relax! :D</p>
      </li>
    </ul>

    <div class="footer">
      <div :class="{selected : filterBy == undefined}" @click="setFilter()">All: {{ totalLength }}</div>
      <div :class="{selected : filterBy == false}" @click="setFilter(false)">Todo: {{ todoLength }}</div>
      <div :class="{selected : filterBy == true}" @click="setFilter(true)">Completed: {{ totalLength - todoLength }}</div>
    </div>
  </div>
</template>

<style lang="sass">
$blue: #021f4b
$yellow: #cb8a2c
$green: #01342d
$red: #470119

$textDark: #333
$textLight: #DDD
$removeButton: rgba(200, 200, 200, 0.5)
$selected: rgba(100, 100, 100, 0.5)

$todoBackground: #f4e5d2
$bodyBackground: #DDD

:root
  --blue: #{$blue}
  --yellow: #{$yellow}
  --green: #{$green}
  --red: #{$red}

  --textdark: #{$textDark}
  --textlight: #{$textLight}
  --textinput: #{$textLight}
  --removebutton: #{$removeButton}
  --selected: #{$selected}

  --todobackground: #{$todoBackground}
  --bodybackground: #{$bodyBackground}

$darktodobackground: #333
$darkbodybackground: #111

.dark
  --blue: #{$blue}
  --yellow: #{$yellow}
  --green: #{$green}
  --red: #{$red}

  --textdark: #{$textLight}
  --textlight: #{$textDark}
  --removebutton: #{$removeButton}
  --selected: #{$selected}

  --todobackground: #{$darktodobackground}
  --bodybackground: #{$darkbodybackground}

* 
  cursor: default
  user-select: none
  transition: .4s linear background-color, color
.Container
  background-color: var(--todobackground)
  margin: auto
  width: fit-content
  padding: 10px
  border-radius: 5px
  resize: horizontal
  overflow: auto
  min-width: 350px
  & > .header
    display: flex
    align-items: center
    justify-content: space-between
    height: 28px
    color: var(--textdark)
    font-size: 0.75em
    & > .switch
      position: relative
      width: 45px
      height: 24px
      & > .slider
        position: absolute
        cursor: pointer
        top: 0
        left: 0
        right: 0
        bottom: 0
        background-color: var(--bodybackground)
        border-radius: 5px
        transition: .4s
        &:before
          border-radius: 5px
          background-color: var(--selected)
          position: absolute
          content: ""
          height: 18px
          width: 18px
          left: 3px
          bottom: 3px
          transition: .4s
      & > .checkbox:checked + .slider:before
        transform: translateX(18px)
  & > *
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
      border-bottom: 2px solid var(--textdark)
      background-color: inherit
      color: var(--textdark)
    & > input
      cursor: text
    & > select
      color: var(--textinput)
      border-top-right-radius: 5px
      border-bottom-right-radius: 5px
  & > ul
    list-style: none
    & > li
      display: flex
      margin-top: 5px
      padding: 5px 10px
      border-radius: 5px
      transition: none
      & > *
        transition: none
        padding: 5px
        border: none
        outline: none
        font-size: 0.9em
        background-color: inherit
      & > input
        flex-grow: 1
        color: var(--textinput)
      & > button
        color: var(--removebutton)
        font-weight: bold
      & > p
        color: var(--textdark)
  & > .footer
    display: flex
    justify-content: space-between
    margin-top: 5px
    & > div
      font-size: 0.7em
      color: var(--textdark)
      border-radius: 5px
      padding: 5px
      transition: none
      &:hover
        background-color: var(--selected)

.done
  text-decoration: line-through 3px solid var(--textinput)
.Home
  background-color: var(--blue)
.Work
  background-color: var(--yellow)
.Hobby
  background-color: var(--green)
.Urgent
  background-color: var(--red)
.selected
  background-color: var(--selected)
</style>
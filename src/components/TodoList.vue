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
      type: 'Home'

    }
  },
  computed: {
    
  },
  methods: {

  }
}
</script>

<template>
  <div class="Container">
    <h1><u>TodoList</u></h1><br>
    <div class="newTodo">
      <input
        v-model="name"
        type="text">
      <button>Add Task</button>
      <select v-model="type">
        <option>Home</option>
        <option>Work</option>
        <option>Hobby</option>
        <option>Urgent</option>
      </select>
    </div>
    <ul>
      <li v-for="todo in list">
        <input
          v-model="todo.name"
          type="text">
        <div class="removeTask">x</div>
      </li>
    </ul>
    <div class="footer"></div>
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
  & > .newTodo
    display: flex
    justify-content: space-between
    & > input
      cursor: text
    & > input, & > button
      flex-grow: 1
  & > ul
    list-style: none
    & > li
      display: flex
      margin-top: 5px
      & > input
        flex-grow: 1

</style>
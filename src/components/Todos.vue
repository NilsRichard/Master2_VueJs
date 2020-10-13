<template>
  <div style="height: 100%">
    <div id="myDIV" class="header">
      <h2 style="margin: 5px">
        Todo List <span class="w3-small">(click on an item to tick it)</span>
      </h2>
      <input
        type="text"
        v-model="newTodoDesc"
        placeholder="Describe the task..."
        v-on:keyup.enter="addTodo()"
      />
      <span v-on:click="addTodo()" class="btn">Add</span>
      <span v-on:click="selectAll()" class="btn">Tick All</span>
      <span v-on:click="removeDoneTasks()" class="btn">Clear done</span>
      <span
        v-on:click="displayAll()"
        v-bind:class="{ 'w3-blue': displayMode == 0 }"
        class="btn"
        >Display all</span
      >
      <span
        v-on:click="displayTodo()"
        v-bind:class="{ 'w3-blue': displayMode == 1 }"
        class="btn"
        >Display todo</span
      >
      <span
        v-on:click="displayDone()"
        v-bind:class="{ 'w3-blue': displayMode == 2 }"
        class="btn"
        >Display done</span
      >
    </div>

    <ul id="myUL">
      <li
        v-for="todo in getFilteredList().slice().reverse()"
        :key="todo.id"
        v-bind:class="{ checked: todo.done }"
      >
        <div class="w3-container">
          <span style="min-width: 400px" v-if="!todo.editing">
            {{ todo.description }}
          </span>
          <input
            class="w3-input"
            style="width: 400px"
            v-if="todo.editing"
            v-model="todo.description"
            type="text"
          />
          <button
            class="w3-right w3-button w3-red w3-round w3-margin-left"
            v-on:click="removeTodo(todo.id)"
          >
            &times;
          </button>
          <button
            class="w3-right w3-button w3-green w3-round w3-margin-left"
            v-on:click="toggleDone(todo.id)"
          >
            Check
          </button>
          <button
            class="w3-right w3-button w3-yellow w3-round w3-margin-left"
            v-on:click="edit(todo)"
          >
            Edit
          </button>
        </div>
        <!-- <span class="edit" v-on:click="edit(todo.id)">Edit</span> -->
      </li>
    </ul>

    <footer v-if="todos.length > 0" class="w3-teal w3-container">
      <p>{{ nbTaskLeft() }} tâches restantes.</p>
    </footer>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

let currentId = 0; // Autoincremented values for the tasks ids

class Todo {
  public description: string; // name, secription of the task
  public done = false; // task checked or not
  public editing = false; // user currently editing the task
  public id = currentId++;

  constructor(description: string) {
    this.description = description;
  }
}

/**
 * Possible modes to display the tasks
 */
enum DisplayMode {
  All,
  Todo,
  Done,
}

@Component
export default class Todos extends Vue {
  @Prop() private msg!: string;

  private todos: Todo[] = [];
  private filteredTodos: Todo[] = [];

  private displayMode: DisplayMode = DisplayMode.All;

  private newTodoDesc = "";

  /**
   * Filters the list depending on the mode
   */
  getFilteredList() {
    switch (this.displayMode) {
      case DisplayMode.All:
        return this.todos;
      case DisplayMode.Done:
        return this.todos.filter((e) => {
          return e.done;
        });
      case DisplayMode.Todo:
        return this.todos.filter((e) => {
          return !e.done;
        });
      default:
        return [];
    }
  }

  displayAll() {
    this.displayMode = DisplayMode.All;
  }

  displayDone() {
    this.displayMode = DisplayMode.Done;
  }

  displayTodo() {
    this.displayMode = DisplayMode.Todo;
  }

  /**
   * Displays the input to edit the task
   */
  edit(todo: Todo) {
    if (todo.editing) {
      todo.editing = false;
    } else {
      todo.editing = true;
    }
  }

  /**
   * Toggle a tasks to the opposite boolean value
   */
  toggleDone(id: number) {
    this.todos.forEach((element) => {
      if (element.id === id) element.done = !element.done;
    });
  }

  /**
   * Removes a todo from the list
   */
  removeTodo(id: number) {
    this.todos = this.todos.filter(function (value: Todo) {
      return value.id !== id;
    });
  }

  /**
   * Remoes all the done tasks
   */
  removeDoneTasks() {
    this.todos = this.todos.filter(function (value: Todo) {
      return !value.done;
    });
    this.filteredTodos = this.todos;
  }

  /**
   * Returns the number of undone tasks
   */
  nbTaskLeft() {
    let nb = 0;
    this.todos.forEach((e: Todo) => {
      if (!e.done) nb++;
    });
    return nb;
  }

  /**
   * Adds a new Task with thedescription given in the newTodoDesc input
   */
  addTodo() {
    const value = this.newTodoDesc.toString();
    this.todos.push(new Todo(value));
    this.newTodoDesc = "";
    this.filteredTodos = this.todos;
  }

/**
 * Sets all tasks to done
 */
  selectAll() {
    this.todos.forEach((element: Todo) => {
      element.done = true;
    });
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
footer {
  position: relative;
  bottom: 0;
  width: 100%;
  height: 50px;
}

body {
  margin: 0;
  min-width: 250px;
}

/* Include the padding and border in an element's total width and height */
* {
  box-sizing: border-box;
}

/* Remove margins and padding from the list */
ul {
  margin: 0;
  padding: 0;
}

/* Style the list items */
ul li {
  position: relative;
  padding: 12px 8px 12px 40px;
  list-style-type: none;
  background: #eee;
  font-size: 18px;
  transition: 0.2s;

  /* make the list items unselectable */
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

/* Set all odd list items to a different color (zebra-stripes) */
ul li:nth-child(odd) {
  background: #f9f9f9;
}

/* Darker background-color on hover */
ul li:hover {
  background: #ddd;
}

/* When clicked on, add a background color and strike out text */
ul li.checked {
  background: #888;
  color: #fff;
  text-decoration: line-through;
}

/* Add a "checked" mark when clicked on */
ul li.checked::before {
  content: "";
  position: absolute;
  border-color: #fff;
  border-style: solid;
  border-width: 0 2px 2px 0;
  top: 10px;
  left: 16px;
  transform: rotate(45deg);
  height: 15px;
  width: 7px;
}

/* Style the close button */
.close {
  position: absolute;
  right: 0;
  top: 0;
  padding: 12px 16px 12px 16px;
}

.close:hover {
  background-color: red;
  color: white;
}

/* Style the edit button */
.edit {
  position: absolute;
  right: 40px;
  top: 0;
  padding: 12px 16px 12px 16px;
}

.edit:hover {
  background-color: gray;
  color: white;
}

/* Style the header */
.header {
  background-color: teal;
  padding: 30px 40px;
  color: white;
  text-align: center;
}

/* Clear floats after the header */
.header:after {
  content: "";
  display: table;
  clear: both;
}

/* Style the input */
input {
  margin: 0;
  border: none;
  border-radius: 0;
  width: 30%;
  padding: 10px;
  float: left;
  font-size: 16px;
}

/* Style the "Add" button */
.btn {
  padding: 10px;
  margin-left: 5px;
  width: 10%;
  background: #d9d9d9;
  color: #555;
  float: left;
  text-align: center;
  font-size: 16px;
  cursor: pointer;
  transition: 0.3s;
  border-radius: 0;
}

.btn:hover {
  background-color: #bbb;
}
</style>

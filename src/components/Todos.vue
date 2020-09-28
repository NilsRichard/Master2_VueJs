<template>
  <div style="height: 100%">
    <div class="w3-container w3-teal">
      <h2>Todos</h2>
    </div>

    <div class="w3-container">
      <label class="w3-text-teal"><b>Description</b></label>
      <input
        class="w3-input w3-border w3-light-grey"
        placeholder="Describe the task"
        v-model="newTodoDesc"
        type="text"
      />
      <br />
      <button v-on:click="addTodo()" class="w3-btn w3-blue-grey">
        Add task
      </button>
    </div>
    <br />

    <div class="w3-margin">
      <table class="w3-table w3-border w3-card">
        <tr class="w3-teal">
          <th>Description</th>
          <th>
            Done
            <button v-on:click="selectAll()" class="w3-small">
              Select all
            </button>
          </th>
        </tr>
        <tr
          v-bind:class="{ 'w3-opacity': todo.done }"
          v-for="todo in todos"
          :key="todo.id"
        >
          <td>
            <del v-if="todo.done">
              {{ todo.description }}
            </del>
            <div v-else>{{ todo.description }}</div>
          </td>

          <td>
            <input v-model="todo.done" type="checkbox" />
          </td>
        </tr>
      </table>
    </div>

    <footer class="w3-teal w3-container">
      <p>
        {{ nbTaskLeft() }} tâches restantes.
      </p>
    </footer>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

let currentId = 0;

class Todo {
  public description: string;
  public done = false;
  public id = currentId++;

  constructor(description: string) {
    this.description = description;
  }
}

@Component
export default class Todos extends Vue {
  @Prop() private msg!: string;

  private todos: Todo[] = [new Todo("eat")];

  private newTodoDesc = "";

  nbTaskLeft() {
    let nb = 0;
    this.todos.forEach((e: Todo) => {
      if (!e.done) nb++;
    });
    return nb;
  }

  addTodo() {
    const value = this.newTodoDesc.toString();
    this.todos.push(new Todo(value));
  }

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
</style>

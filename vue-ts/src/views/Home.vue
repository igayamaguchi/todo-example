<template>
  <div class="home">
    <form @submit.prevent="add">
      <label>
        <input type="text" v-model="newTodo" />
      </label>
      <button type="button">add</button>
    </form>
    <ul>
      <li v-for="todo in todoList" :key="todo.id">{{ todo.value }}</li>
    </ul>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component({})
export default class Home extends Vue {
  newTodo: string = '';
  todoList: Todo[] = [];

  // @Prop({ required: true }) f: string;

  add(): void {
    const newId = getNewId(this.todoList);
    const todo = new Todo(newId, this.newTodo);
    this.todoList.push(todo);
    this.newTodo = '';
  }
}

function getNewId(list: Todo[]) {
  const max = list.reduce((prev, current) => {
    return prev > current.id ? prev : current.id;
  }, 0);
  return max + 1;
}

class Todo {
  id: number;
  value: string;
  order: number;

  constructor(id: number, value: string, order: number = 99) {
    this.id = id;
    this.value = value;
    this.order = order;
  }
}
</script>

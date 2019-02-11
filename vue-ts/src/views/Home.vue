<template>
  <div class="home">
    <form @submit.prevent="add">
      <label>
        <input type="text" v-model="newTodo" />
      </label>
      <button type="button">add</button>
    </form>
    <h3>Todo</h3>
    <ul>
      <li v-for="todo in todoList" :key="todo.id">
        <button @click.prevent="doing(todo)" type="button">Complete</button>
        <div>{{ todo.value }}</div>
        <button @click.prevent="removeTodo(todo)" type="button">Remove</button>
      </li>
    </ul>
    <h3>Doing</h3>
    <ul>
      <li v-for="todo in doingList" :key="todo.id">
        <button @click.prevent="complete(todo)" type="button">Complete</button>
        <div>{{ todo.value }}</div>
        <button @click.prevent="removeDoing(todo)" type="button">Remove</button>
      </li>
    </ul>
    <h3>Complete</h3>
    <ul>
      <li v-for="todo in completeList" :key="todo.id">
        <div>{{ todo.value }}</div>
        <button @click.prevent="removeComplete(todo)" type="button">
          Remove
        </button>
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component({})
export default class Home extends Vue {
  newTodo: string = '';
  todoList: Todo[] = [];
  doingList: Todo[] = [];
  completeList: Todo[] = [];

  // @Prop({ required: true }) f: string;

  add(): void {
    const newId = getNewId(this.todoList);
    const todo = new Todo(newId, this.newTodo);
    this.todoList.push(todo);
    this.newTodo = '';
  }

  removeTodo(todo: Todo): void {
    const index = this.todoList.indexOf(todo);
    this.todoList.splice(index, 1);
  }

  removeDoing(todo: Todo): void {
    const index = this.doingList.indexOf(todo);
    this.doingList.splice(index, 1);
  }

  removeComplete(todo: Todo): void {
    const index = this.completeList.indexOf(todo);
    this.completeList.splice(index, 1);
  }

  doing(todo: Todo): void {
    const doing = todo.next();
    this.doingList.push(doing);
    this.removeTodo(todo);
  }

  complete(todo: Todo): void {
    const complete = todo.next();
    this.completeList.push(complete);
    this.removeDoing(todo);
  }
}

function getNewId(list: Todo[]) {
  const max = list.reduce((prev, current) => {
    return prev > current.id ? prev : current.id;
  }, 0);
  return max + 1;
}

abstract class AbstractTodo {
  id: number;
  value: string;
  order: number;

  constructor(id: number, value: string, order: number = 99) {
    this.id = id;
    this.value = value;
    this.order = order;
  }

  abstract next(): AbstractTodo;
}

class Todo extends AbstractTodo {
  next(): AbstractTodo {
    return new Doing(this.id, this.value, this.order);
  }
}

class Doing extends AbstractTodo {
  next(): AbstractTodo {
    return new Complete(this.id, this.value, this.order);
  }
}

class Complete extends AbstractTodo {
  next(): AbstractTodo {
    return new Doing(this.id, this.value, this.order);
  }
}
</script>

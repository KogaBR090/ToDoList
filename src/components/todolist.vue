<script setup>
import { ref, onMounted, watch, computed } from "vue";




// variáveis reativas
const todos = ref([]);

const inputTodo = ref("");
const dateTodo = ref("");

const checkbox = ref(false);

//ordenar a lista por ordem de criação, mesmo recarregando
const todos_list = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (
    inputTodo.value.trim() === "" ||
    (dateTodo.value === "" && inputTodo.value === null) ||
    dateTodo.value === null
  ) {
    window.alert("Por favor, preencha todos os campos.");
    return;
  }
  todos.value.push({
    name: inputTodo.value,
    dueDate: dateTodo.value,
    done: false,
    createdAt: new Date().getTime(),
  });
};

const deleteTodos = () => {
  todos.value = [];
  localStorage.clear("todos");
};

watch(todos, (newValue) => {
  localStorage.setItem("todos,", JSON.stringify(newValue));
});

watch(inputTodo, (newValue) => {
  localStorage.setItem("inputTodo", newValue);
});

onMounted(() => {
  // Recupera o valor do input e data do localStorage quando o componente é montado
  inputTodo.value = localStorage.getItem("inputTodo") || "";
  dateTodo.value = localStorage.getItem("dateTodo") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

watch(inputTodo, (newValue) => {
  localStorage.setItem("inputTodo", newValue);
});

watch(dateTodo, (newValue) => {
  localStorage.setItem("dateTodo", newValue);
});
</script>


<template>
  <div class="todoContainer">
    <form id="new-todo-form" @submit.prevent="addTodo" class="app">
      <input
        id="inputTodo"
        type="text"
        v-model="inputTodo"
        placeholder="Adicione uma nova tarefa"
      />
      <input id="dateTodo" type="date" v-model="dateTodo" required />
      <button type="submit">Adicionar</button>
    </form>

    <button type="button" @click="deleteTodos()">Limpar</button>

    <div class="list" id="todo-list">
      <h1>Lista de Tarefas</h1>
      <div
        v-for="todo in todos_list"
        :key="todo.index"
        :class="`todo-item ${todo.done && 'done'}`"
      >
        <label>
          <input type="checkbox" v-model="todo.done" />
          <input type="date" v-model="todo.dueDate">
          <input type="text" v-model="todo.name" />
        </label>
      </div>
    </div>
  </div>
</template>

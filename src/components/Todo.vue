<script setup>
import { ref, computed } from "vue";
import TodoItem from "./TodoItem.vue";

const todos = ref([
  {
    title: "吃饭",
    completed: true
  }
]); // 所有备忘列表
const newTodo = ref(""); // 输入框输入的内容
const editedTodo = ref({}); // 当前编辑的todo

// 添加备忘
let id = 1;
function addTodo() {
  console.log("添加", newTodo.value);
  if (!newTodo.value) {
    return;
  }
  todos.value.unshift({
    id: id++,
    title: newTodo.value,
    completed: false,
  });
  newTodo.value = "";
  console.log("添加成功", todos.value);
}



// 未完成数量
const remaining = computed(() => {
  return todos.value.filter((todo) => !todo.completed).length;
});
// 未完成计算单位
const pluralize = computed(() => {
  return (remaining.value === 1 || remaining.value === 0)
    ? "item" : "items";
});
// 清除已完成
function clearCompleted() {
  todos.value = todos.value.filter((todo) => !todo.completed);
}


</script>

<template>
  <!-- “添加新备忘”输入框 -->
  <input
    class="new-todo"
    placeholder="What needs to be done?"
    type="text"
    v-model="newTodo"
    @keyup.enter="addTodo"
  />

  <!-- 所有备忘列表 -->
  <ul class="todo-list">
    <li
      v-for="todo in todos"
      class="todo"
      :key="todo.id"
      :class="{ completed: todo.completed, editing: todo.id == editedTodo.id }"
    >
      <!-- 使用 props 和 emit 进行父组件和子组件之间的通信 -->
      <TodoItem
        :title="todo.title"
        @update:title="(title) => todo.title = title"
        :completed="todo.completed"
        @update:completed="(completed) => todo.completed = completed"
        @delete="() => todos.value.splice(todos.value.indexOf(todo), 1)"
        
      ></TodoItem>
      
    </li>
  </ul>

  <!-- 相关信息与快速操作 -->
  <footer class="footer" v-show="todos.length">
    <span class="todo-count">
      <!-- remaining 计算剩余的未完成的数量，pluralize 用来过滤单位是否要负数 -->
      <strong>{{ remaining }}</strong> {{ pluralize }} left
    </span>
    <!-- 当有已完成的备忘时，一键移除已完成按钮出现 -->
    <button
      class="clear-completed"
      @click="clearCompleted"
      v-show="todos.length > remaining"
    >
      Clear completed
    </button>
  </footer>
</template>

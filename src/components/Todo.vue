<script setup>
import { ref, computed } from "vue";
import Velocity from "velocity-animate";
import TodoItem from "./TodoItem.vue";

const todos = ref([
  {
    id: 0,
    title: "吃饭",
    completed: true,
  },
  {
    id: 1,
    title: "睡觉",
    completed: true,
  },
  {
    id: 2,
    title: "打原神启动",
    completed: true,
  },
]); // 所有备忘列表
const newTodo = ref(""); // 输入框输入的内容
const remaining = computed(() => {
  return todos.value.filter((todo) => !todo.completed).length;
}); // 未完成数量
const pluralize = computed(() => {
  return remaining.value === 1 || remaining.value === 0 ? "item" : "items";
}); // 未完成计算单位
const matchTodos = computed(() => {
  return todos.value.filter((todo) => {
    return todo.title.toLowerCase()
      .indexOf(newTodo.value.toLowerCase()) > -1;
  });
}); // 与当前输入匹配的todo

/* CRUD */
// 添加备忘
let id = 3;
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
  console.log("匹配结果", matchTodos.value);
}
// 删除备忘
function removeTodo(todo) {
  for (let i = 0; i < todos.value.length; i++) {
    console.log(todos.value[i], todo.id, todo.id == todos.value[i].id);
  }
  todos.value = todos.value.filter((t) => t.id != todo.id);
  console.log("删除", todo.title, todos.value, matchTodos);
}
// 清除已完成
function clearCompleted() {
  todos.value = todos.value.filter((todo) => !todo.completed);
}

/* 动画效果 */
// 进入中
function beforeEnter(el) {
  el.style.opacity = 0;
  el.style.height = 0;
}
function enter(el, done) {
  // 设置延时
  var delay = el.dataset.index * 150;
  setTimeout(function () {
    // 更新元素样式
    Velocity(el, { opacity: 1, height: "58px" }, { complete: done });
  }, delay);
}
// 离开时
function leave(el, done) {
  // 设置延时
  var delay = el.dataset.index * 150;
  setTimeout(function () {
    // 更新元素样式
    Velocity(el, { opacity: 0, height: 0 }, { complete: done });
  }, delay);
}
</script>

<template>
  <!-- “添加新备忘”输入框 -->
  <input
    class="new-todo"
    placeholder="What needs to be done?"
    type="text"
    v-model="newTodo"
    @input="console.log('input change', newTodo, matchTodos)"
    @keyup.enter="addTodo"
  />

  <!-- 添加 before-enter、enter 和 leave 的钩子 -->
  <transition-group
    name="staggered-fade"
    tag="ul"
    v-bind:css="false"
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
    class="todo-list"
  >
    <!-- v-for 遍历匹配中的备忘 computedTodos，key 绑定备忘 id，class 绑定样式 -->
    <li
      v-for="todo in matchTodos"
      class="todo"
      :key="todo.id"
      :class="{ completed: todo.completed }"
    >
      <!-- <h1>{{ todo.id }}</h1> -->
      <TodoItem
        :title="todo.title"
        @update:title="(title) => (todo.title = title)"
        :completed="todo.completed"
        @update:completed="(completed) => (todo.completed = completed)"
        @delete="removeTodo(todo)"
      ></TodoItem>
    </li>
  </transition-group>

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

<!-- <style>
@import "https://unpkg.com/todomvc-app-css@2.1.0/index.css";
</style> -->

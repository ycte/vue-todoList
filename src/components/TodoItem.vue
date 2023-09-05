<script setup>
import { ref } from "vue";
const props = defineProps({
  title: String,
  completed: Boolean
});
const emit = defineEmits(["delete", "update:title", "update:completed"]);
const editingState = ref({
  isEditing: false,
  editingTitle: ""
})
const checked = ref(props.completed);
console.log(props);
console.log(editingState.value.editingTitle);

// 编辑备忘
function editTodo() {  
  editingState.value.editingTitle = props.title;
  console.log("编辑备忘", editingState.value.editingTitle);
  editingState.value.isEditing = true;
}
// 保存编辑
function doneEdit() {
  // todos.value[todos.value.indexOf(todo)] = todo;
  // todos.value = todos.value.map((x) => {
  //   return todo.id === x.id ? { ...todo } : x;
  // });
  
  if (editingState.value.editingTitle) {
    emit("update:title", editingState.value.editingTitle);
    console.log("编辑完成", editingState.value.editingTitle);
    editingState.value.isEditing = false;
    editingState.value.editingTitle = "";
  }
}
// 取消编辑
function cancelEdit() {
  editingState.value.isEditing = false;
  editingState.value.editingTitle = "";
}

// 更新完成情况
function updateCompleted(completed) {
  emit("update:completed", completed);
}

// 删除备忘
function deleteTodo() {
  emit("delete");
}


</script>

<template>
  <div class="view">
    <input class="toggle" type="checkbox" v-model="checked"
      @change="updateCompleted($event.target.checked)"
    />
    <label @dblclick="editTodo()">{{ props.title }}</label>
    <button
      class="destroy"
      @click="deleteTodo()"
    ></button>
  </div>
  <input
    class="edit"
    type="text"
    v-model="editingState.editingTitle"
    @blur="doneEdit()"
    @keyup.enter="doneEdit()"
    @keyup.esc="cancelEdit()"
  />
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Swal from 'sweetalert2';

defineProps({
  msg: String,
})

const tasks = ref([]);
const value = ref('');
let required = ref(false);
let duplicate = ref(false);

onMounted(() => {
  const getTasks = JSON.parse(localStorage.getItem('tasklist'));
  if (getTasks?.length > 0) {
    tasks.value = getTasks
  }
})




const onAdd = () => {
  let inputVal = value.value ?? "";
  if (inputVal.trim() !== "") {
    let isDuplicate = tasks.value.some(task => task.value.toLowerCase() === inputVal.toLowerCase());
    if (isDuplicate) {
      duplicate.value = true;
    } else {
      let data = {
        value: inputVal,
        completed: false
      }
      tasks.value.push(data);
      localStorage.setItem('tasklist', JSON.stringify(tasks.value));
      value.value = '';
      duplicate.value = false;
    }
  } else {
    required.value = true;
  }
}

const onCheckClick = (event, task) => {
  if (event.target.checked) {
    task.completed = true;
    localStorage.setItem('tasklist', JSON.stringify(tasks.value));
  } else {
    task.completed = false;
    localStorage.setItem('tasklist', JSON.stringify(tasks.value));
  }
}

const onDelete = (val) => {
  Swal.fire({
    title: "Are you sure?",
    text: "Want to delete task!",
    icon: "warning",
    showCancelButton: true,
    confirmButtonColor: "#3085d6",
    cancelButtonColor: "#d33",
    confirmButtonText: "Yes"
  }).then((result) => {
    if (result.isConfirmed) {
      handleDelete(val)
    }
  });
}

const handleDelete = (val) => {
  const index = tasks.value.findIndex((task) => task.value === val);
  if (index > -1) {
    tasks.value.splice(index, 1);
    localStorage.setItem('tasklist', JSON.stringify(tasks.value));
  }
}

</script>

<template>
  <div class="card">
    <h1>{{ msg }}</h1>
    <div class="inputCont">
      <div style="width: 80%;">
        <input type="text" class="form-control" v-model="value" placeholder="Task name">
        <span class="text-danger" v-if="required">This field is required.</span>
        <span class="text-danger" v-if="duplicate">This task already exists!</span>
      </div>
      <div style="width: 20%;">
        <button type="button" class="addBtn float-right" @click="onAdd">Add +</button>
      </div>
    </div>
    <div class="card">
      <h2>Task list</h2>
      <ul class="ul-list custom-scroll">
        <li v-for="task in tasks">
          <label :class="{ completed: task.completed }">
            <input type="checkbox" v-model="task.completed" @change="onCheckClick($event, task)">
            <span>{{ task.value }}</span>
          </label>
          <button class="btn-remove float-right" @click="onDelete(task.value)">Remove</button>
        </li>
        <li v-if="tasks?.length == 0">
          <spa>No tasks to display.</spa>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}

.addBtn {
  margin-left: 10px;
}

.ul-list {
  list-style: none;
  padding: 10px;
  text-align: left;
  max-height: 290px;
  overflow-x: auto;
}

.inputCont {
  text-align: left;
  display: flex;
  margin-bottom: 10px;
  font-size: 14px;
  padding: 10px;
}

.text-danger {
  color: red;
}

li {
  border-radius: 5px;
  text-align: left;
  padding: 11px;
  margin-bottom: 5px;
  min-height: 35px;
  height: auto;
  background-color: #cccccc26;
  box-shadow: 0 2px 0px 0 rgba(0, 0, 0, 0.2), 0 2px 10px 0 rgba(0, 0, 0, 0.19);
  display: flex;
  align-items: center;
  justify-content: space-between;
}

li:hover {
  background-color: #ffffff;
  cursor: pointer;
  transform: scale(1.01);
  transition: transform 0.3s ease;
}

.float-right {
  float: right;
}

.btn-remove {
  color: #dc3545 !important;
  background-color: #dc35451f;
  padding: 2px 12px;
  border-radius: 6px;
  font-size: 12px;
  cursor: pointer;
}

.btn-remove:hover {
  border-color: #dc3545;
}

h2 {
  margin-top: 0px;
}

.completed {
  text-decoration: line-through;
}

.custom-scroll::-webkit-scrollbar-track {
  background-color: #f1f1f1;
}

/* Handle */
.custom-scroll::-webkit-scrollbar-thumb {
  background-color: #9da1a594;
}

.custom-scroll::-webkit-scrollbar {
  width: 5px;
}
</style>

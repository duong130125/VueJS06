<template>
  <div class="todo-container">
    <form id="todo-form" @submit="handleAdd">
      <input
        v-model="valueInput"
        type="text"
        id="todo-input"
        placeholder="Enter your job"
        required
      />
      <button type="submit" class="add-btn">Add Job</button>
    </form>

    <ul id="todo-list">
      <li v-for="job in listJobs" :key="job.id" class="todo-item">
        <input
          @click="handleClick(job.id)"
          type="checkbox"
          v-model="job.status"
        />
        <h4 :class="{ completed: job.status }">
          {{ job.name }}
        </h4>
        <button @click="handleDelete(job.id)" class="delete-btn">Delete</button>
      </li>
    </ul>

    <div class="progress">
      <span>
        Số công việc hoàn thành:
        <strong>{{ completedJobs }}/{{ listJobs.length }}</strong> công việc
      </span>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
const valueInput = ref("");

const data = [
  { id: 1, name: "Code", status: false },
  { id: 2, name: "Review code", status: true },
  { id: 3, name: "Test application", status: false },
];

if (!localStorage.getItem("data")) {
  localStorage.setItem("data", JSON.stringify(data));
}

const listJobs = ref(JSON.parse(localStorage.getItem("data")));
const handleAdd = (event) => {
  event.preventDefault();
  if (valueInput.value.trim() === "") return;

  const newJob = {
    id: listJobs.value.length
      ? listJobs.value[listJobs.value.length - 1].id + 1
      : 1,
    name: valueInput.value,
    status: false,
  };

  listJobs.value.push(newJob);
  valueInput.value = "";
  localStorage.setItem("data", JSON.stringify(listJobs.value));
};

const handleDelete = (id) => {
  listJobs.value = listJobs.value.filter((job) => job.id !== id);
  localStorage.setItem("data", JSON.stringify(listJobs.value));
};

const handleClick = (id) => {
  const job = listJobs.value.find((job) => job.id === id);
  if (job) {
    job.status = !job.status;
    localStorage.setItem("data", JSON.stringify(listJobs.value));
  }
};

const completedJobs = computed(() => {
  return listJobs.value.filter((job) => job.status).length;
});
</script>

<style scoped>
.todo-container {
  font-family: Arial, sans-serif;
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 5px;
  background-color: #fff;
}

#todo-form {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

#todo-input {
  flex-grow: 1;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 5px;
}

.add-btn {
  background-color: #4caf50;
  color: white;
  padding: 10px 15px;
  font-size: 16px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-left: 10px;
}

#todo-list {
  list-style-type: none;
  padding: 0;
}

.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: #f9f9f9;
  padding: 10px;
  margin-bottom: 5px;
  border-radius: 5px;
  border: 1px solid #ddd;
}

.todo-item input {
  margin-right: 10px;
}

.todo-item h4 {
  flex-grow: 1;
  margin: 0;
}

.todo-item h4.completed {
  text-decoration: line-through;
  color: #888;
}

.delete-btn {
  background-color: #f44336;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
}

.progress {
  background-color: #eee;
  padding: 10px;
  text-align: center;
  border-radius: 5px;
  margin-top: 20px;
}

.progress strong {
  color: #333;
}
</style>

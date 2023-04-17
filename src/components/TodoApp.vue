<template>
  <link
    rel="stylesheet"
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css"
  />

  <div style="height: 96">
    <table
      class="table-auto border-collapse w-full h-96 shadow-lg overflow-y-scroll"
    >
      <thead class="bg-gray-800 text-white">
        <tr>
          <th class="border border-gray-400 p-2 text-left">Task</th>
          <th class="border border-gray-400 p-2 text-left">Status</th>
          <th class="border border-gray-400 p-2 text-left">Actions</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="task in tasksList" :key="task._id" class="hover:bg-gray-100">
          <td class="border border-gray-400 p-2">{{ task.taskName }}</td>
          <td class="border border-gray-400 p-2">
            {{ task.completed ? "Finished" : "Pending" }}
          </td>

          <td class="border border-gray-400 p-1">
            <button
              class="bg-red-600 hover:bg-red-700 text-white font-bold py-1 px-2 rounded focus:outline-none focus:shadow-outline"
              @click="deleteTask(task._id)"
            >
              <i class="far fa-trash-alt"></i> Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <br />

    <div>
      <input
        type="text"
        v-model="newTaskName"
        class="border rounded-lg px-4 py-2 mr-2"
        placeholder="Enter task name"
      />
      <button
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        @click="addTask"
      >
        Add Task
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import axios from "axios";

const tasks = ref([]);
const API_URL = "http://localhost:5000";
const newTaskName = ref("");

axios
  .get(`${API_URL}/task`)
  .then((response) => {
    tasks.value.push(...response.data.tasks);
  })
  .catch((error) => {
    console.log(error);
  });

const tasksList = computed(() => {
  return tasks.value;
});

function addTask() {
  axios
    .post(`${API_URL}/task`, {
      taskName: newTaskName.value,
      completed: false,
    })
    .then(() => {
      axios
        .get(`${API_URL}/task`)
        .then((response) => {
          tasks.value = [];
          newTaskName.value = "";
          tasks.value.push(...response.data.tasks);
        })
        .catch((error) => {
          console.log(error);
        });
    })
    .catch((error) => {
      console.log(error);
    });
}

function deleteTask(id) {
  axios
    .delete(`${API_URL}/task/${id}`)
    .then((response) => {
      tasks.value = tasks.value.filter((task) => task._id !== id);
      console.log(response.data);
    })
    .catch((error) => {
      console.log(error);
    });
}
</script>

<script>
export default {
  name: "MyComponent",
  setup() {
    return { tasksList, newTaskName, addTask, deleteTask };
  },
};
</script>
<script setup>
import { onMounted, ref } from 'vue';
import axios from 'axios';
import { PencilAltIcon } from '@heroicons/vue/outline'
import { TrashIcon } from '@heroicons/vue/outline'

// Users
const users = ref([])
const fetchUsers = () => {
  const data = axios.get("http://localhost:3000/users").then((res) => users.value = res.data);
}  

onMounted(() => {
  fetchUsers()  
})

// Create User
const firstname = ref("");
const lastname = ref("");
const createUser = () => {
  try {
    axios.post("http://localhost:3000/users", {
      firstname: firstname.value,
      lastname: lastname.value
    }).then(() => {
      alert("Foydalanuvchi qo'shildi.")
    }).finally(() => {
      fetchUsers();
    })
    firstname.value = "";
    lastname.value = "";
  } catch (e) {
    console.log(e);
  }
}

// Update User
const isEdit = ref(false)
const id = ref(null)
const edit = (user) => {
  isEdit.value = true
  id.value = user.id
  firstname.value = user.firstname
  lastname.value = user.lastname
}

const updateUser = () => {
  try {
    axios.put('http://localhost:3000/users/' + id.value, {
      firstname: firstname.value,
      lastname: lastname.value
    }).then(() => {
      alert("Foydalanuvchi yangilandi.")
    }).finally(() => {
      fetchUsers();
    })
    firstname.value = "";
    lastname.value = "";
    id.value = null
    isEdit.value = false
  } catch (e) {
    console.log(e);
  }
}

const deleteUser = (id) => {
  try {
    if (window.confirm("Haqiqatda o'chirmoqchimisiz?")) {
      axios.delete("http://localhost:3000/users/" + id).then(() => {
        alert("Foydalanuvchi o'chirildi.")
      }).finally(() => {
        fetchUsers()
      })
    }
  } catch (e) {
    console.log(e);
  }
}
</script>

<template>
  <div class="w-full min-h-screen px-6 text-center">
    <div class="mt-5 mb-7">
      <h1 class="mb-3 text-2xl font-bold">Foydalanuvchi yaratish.</h1>
      <form method="POST e.preventDefault()">
        <div class="flex flex-col items-center space-y-2">
          <input
            class="px-3 py-2 mt-1 bg-white border rounded-md shadow-sm w-80 border-slate-500 focus:outline-none focus:border-sky-500 focus:ring-sky-500 sm:text-sm focus:ring-1"
            v-model="firstname" type="text" placeholder="Ismingizni kiriting">
          <input
            class="px-3 py-2 mt-1 bg-white border rounded-md shadow-sm w-80 border-slate-500 focus:outline-none focus:border-sky-500 focus:ring-sky-500 sm:text-sm focus:ring-1"
            v-model="lastname" type="text" placeholder="Familiyangizni kiriting">
        </div>
        <button class="px-6 py-2 mt-4 text-white transition rounded bg-sky-500 hover:bg-sky-700" @click="createUser"
          v-if="!isEdit">Qo'shish</button>
        <button class="px-6 py-2 mt-4 text-white transition rounded bg-sky-500 hover:bg-sky-700" @click="updateUser"
          v-else>Yangilash</button>
      </form>
    </div>
    <div class="flex flex-col justify-center">
      <h1 class="mb-3 text-lg font-bold">Barcha foydalanuvchilar oling.</h1>
      <table class="border border-collapse table-auto border-slate-400">
        <thead>
          <tr class="border border-slate-400">
            <th>№</th>
            <th>Ism</th>
            <th>Familiya</th>
            <th>O'zgartirish</th>
            <th>O'chirish</th>
          </tr>
        </thead>
        <tbody>
          <tr class="border border-slate-400" v-for="(user, index) in users" :key="index">
            <td>{{ index + 1 }}</td>
            <td>{{ user.firstname }}</td>
            <td>{{ user.lastname }}</td>
            <td class="flex justify-center text-blue-500" @click="edit(user)">
              <PencilAltIcon class=" w-7 h-7" />
            </td>
            <td @click="deleteUser(user.id)">
              <TrashIcon class="text-red-600 w-7 h-7" />
            </td>
          </tr>

        </tbody>
      </table>

    </div>
  </div>
</template>

<style>
</style>

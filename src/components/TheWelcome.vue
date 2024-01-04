<script setup lang="ts">
import { signal, computed } from '@/state/angular-signal';
import Nested from "@/components/Nested.vue";

const listaAlunos = signal([]);

const form = {
  name: '',
  age: 18
}

// only grown ups
const filteredLista = computed(() => {
  return listaAlunos().filter(a => a.age > 18)
})

function signalUser() {
  const aluno = {
    name: form.name,
    age: form.age
  }
  if (!listaAlunos().some((a: any) => (aluno.name.toLowerCase() == a.name.toLowerCase()))) {
    listaAlunos.update(listaUsers => ([...listaUsers, aluno]));
  }
}

</script>

<template>
  <div class="w-full max-w-xs mx-auto">
    <form class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
      <div class="mb-4">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="name">
          Name
        </label>
        <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" v-model="form.name" type="text" id="name" required/>
      </div>
      <div class="mb-6">
        <label class="block text-gray-700 text-sm font-bold mb-2" for="age">
          Age
        </label>
        <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" v-model.number="form.age" type="number" id="age" min="0" required/>
      </div>
      <div class="flex items-center justify-between">
        <button type="button" @click="signalUser()" class="text-blue-500 hover:text-blue-600 underline cursor-pointer">
          Action
        </button>
      </div>
    </form>
    <br/>
    <p>Lista de Aderentes:</p>
    <table class="w-full whitespace-no-wrap">
      <thead>
      <tr class="text-xs font-semibold tracking-wide text-left text-gray-500 uppercase border-b border-gray-600">
        <th class="px-4 py-3">Name</th>
        <th class="px-4 py-3">Age</th>
        <th class="px-4 py-3">Actions</th>
      </tr>
      </thead>
      <tbody class="bg-white divide-y">
      <tr v-for="(user, index) in listaAlunos()" :key="index" class="text-gray-700">
        <td class="px-4 py-3">
          <div class="flex items-center text-sm">
            <div>
              <p class="font-semibold">{{ user.name }}</p>
            </div>
          </div>
        </td>
        <td class="px-4 py-3 text-sm">{{ user['age'] }}</td>
        <td class="px-4 py-3 text-sm">
          <button class="text-blue-500 hover:text-blue-600 underline cursor-pointer">
            Action
          </button>
        </td>
      </tr>
      </tbody>
    </table>
    <br/>
    <nested :message="'Lista de Adultos:'" :lista="filteredLista()"></nested>
  </div>

</template>


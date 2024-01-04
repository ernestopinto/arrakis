<script setup lang="ts">

const props = defineProps(['message', 'lista'])

</script>

<template>
  <p>{{props.message}}</p>
  <table class="w-full whitespace-no-wrap">
    <thead>
    <tr class="text-xs font-semibold tracking-wide text-left text-gray-500 uppercase border-b border-gray-600">
      <th class="px-4 py-3">Name</th>
      <th class="px-4 py-3">Age</th>
      <th class="px-4 py-3">Actions</th>
    </tr>
    </thead>
    <tbody class="bg-white divide-y">
    <tr v-for="(user, index) in props.lista" :key="index" class="text-gray-700">
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
</template>

<style scoped>

</style>

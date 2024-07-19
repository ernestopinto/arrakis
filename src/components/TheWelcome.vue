<script setup lang="ts">
import { signal, computed } from '@/state/angular-signal';
import Nested from "@/components/Nested.vue";
import {provide, ref} from "vue";

let open = ref(false);

// operations
const operations = [
  {
    op: 1,
    desc: 'delete'
  },
  {
    op: 2,
    desc: 'edit'
  }
]

// in real life u would have a id after the update...
const form = {
  code:'',
  name: '',
  age: 18
}

let fEdit = {
  code:'',
  name: '',
  age: 18
}

const listaAlunos = signal([]);
const errorsList = signal([]);

const displayErrorName = computed(() => {
  return errorsList().find((e: {field: string, message: string}) => (e.field == 'name'))?.message;
});

const displayErrorAge = computed(() => {
  return errorsList().find((e: {field: string, message: string}) => (e.field == 'age'))?.message;
});

// only grown ups
let filteredLista = computed(() => {
  return listaAlunos().filter(a => a.age > 18)
})

const getTokenFor = (letters: number) => Math.random().toString(36).substr(2, letters);

function signalUser() {
  let fValid = true;
  errorsList.set([]);

  if (!form.name || form.name == ''){
    errorsList.update(errors => ([...errors, {field: 'name', message: 'Name is required!'}]));
    fValid = false;
  }

  if (form.age <= 5){
    errorsList.update(errors => ([...errors, {field: 'age', message: 'That age is impossible!'}]));
    fValid = false;
  }

  // console.log(errorsList());

  if (!fValid){
    return;
  }

  if (errorsList().some(error => (error.field == 'name'))){
    // splice
    errorsList().splice(errorsList().indexOf(errorsList().find(e => e.field == 'name')), 1);
  }

  if (errorsList().some(error => (error.field == 'age'))){
    // splice
    errorsList().splice(errorsList().indexOf(errorsList().find(e => e.field == 'age')), 1);
  }

  const aluno = {
    code: getTokenFor(5),
    name: form.name,
    age: form.age
  }
  //
  if (!listaAlunos().some((a: any) => (aluno.name.toLowerCase() == a.name.toLowerCase()))) {
    listaAlunos.update(listaUsers => ([...listaUsers, aluno]));
  }

}

///

const getRowFromNested = (data) => alert(`Getting data from nested element: ${JSON.stringify(data.channel)}`);

function getListFromSignalAction(u: any, operation: number) {
  switch (operation){
    case (operations[0].op): {
      //
      listaAlunos.update(l => (l.filter((us: any) => (us.code != u.code))));
      console.log('delete done!');
      console.log(listaAlunos());
      //
      break
    }
    default: {
      // loads form value to pop up
      fEdit = {...u};
      open.value = true;
      //
      // ...
      //
    }
  }
}


function  submitEdiForm(fData: any){
  if (!isValid(fData)){
    alert('Something is wrong with your data!');
    return;
  }
  listaAlunos.update((l: any) => {
    const value = l.find((u: any) => u.code == fData.code);
    value.name = fData.name;
    value.age = fData.age;
    //
    return l;
  });
  // updates the below list!!
  filteredLista = computed(() => {
    return listaAlunos().filter(a => a.age > 18)
  })
  open.value = false;
  console.log('Form submited! -> ', fData);
}

const isValid = (obj: any): boolean => {
  if (obj.age < 6) {
    return false;
  }

  return !(!obj.name || obj.name.trim().length < 4);

};

</script>

<template>

  <div class="w-full mx-auto">
    <div class="m-form">
      <form class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4">
        <div class="mb-4">
          <label class="block text-gray-700 text-sm font-bold mb-2" for="name">
            Name
          </label>
          <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" v-model="form.name" type="text" id="name" required/>
          <p v-if="displayErrorName() && displayErrorName() != ''">
            {{displayErrorName()}}
          </p>
        </div>
        <div class="mb-6">
          <label class="block text-gray-700 text-sm font-bold mb-2" for="age">
            Age
          </label>
          <input class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline" v-model.number="form.age" type="number" id="age" min="0" required/>
          <p v-if="displayErrorAge() && displayErrorAge() != ''">
            {{displayErrorAge()}}
          </p>
        </div>
        <div class="flex items-center justify-between">
          <button type="button" @click="signalUser()" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
            Click me
          </button>
        </div>
      </form>
    </div>
    <br/>
    <p>Lista de Aderentes:</p>
    <table class="w-full whitespace-no-wrap">
      <thead>
      <tr class="text-xs font-semibold tracking-wide text-left text-gray-500 uppercase border-b border-gray-600">
        <th class="px-4 py-3">Name</th>
        <th class="px-4 py-3">Age</th>
        <th class="px-4 py-3">Delete</th>
        <th class="px-4 py-3">Edit</th>
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
          <button @click="getListFromSignalAction(user, operations[0].op)" class="text-blue-500 hover:text-blue-600 underline cursor-pointer">
            Action Delete
          </button>
        </td>
        <td class="px-4 py-3 text-sm">
          <button @click="getListFromSignalAction(user, operations[1].op)" class="text-blue-500 hover:text-blue-600 underline cursor-pointer">
            Action Edit
          </button>
        </td>
      </tr>
      </tbody>
    </table>
    <br/>
    <div style="border-bottom-style: dashed; padding: 12px; border-bottom-width: thin; background-color: azure;">
      <nested :message="'Lista de Aderentes Adultos (nested component):'" :lista="filteredLista()" :validOperations="operations" @rowUpdating="getRowFromNested"></nested>
    </div>
    <!-- code --->
    <div class="code-demo">
    <pre class="font-mono text-sm bg-gray-800 text-white p-2 rounded">
      <code>
        // Some snippets behind the scenes
        // ...
        const listaAlunos = signal([]);
        const errorsList = signal([]);
        // Updating - Filtered list
        listaAlunos.update(l => (l.filter((us: any) => (us.code != u.code))));
        // ...
        // Adding the error to the Array of errors
        if (!form.name || form.name == ''){
          errorsList.update(errors => ([...errors, {field: 'name', message: 'Name is required!'}]));
          fValid = false;
        }
        // Computing the error to be displayed on the html (under the form elements)
        const displayErrorName = computed(() => {
          return errorsList().find((e: {field: string, message: string}) => (e.field == 'name'))?.message;
        });
        //
        // ...
        // updating the list after the row editing fData is loaded from the Form
        listaAlunos.update((l: any) => {
          const value = l.find((u: any) => u.code == fData.code);
          value.name = fData.name;
          value.age = fData.age;
          //
          return l;
        });
        // IMPORTANT!! -> updates the below list!!
        filteredLista = computed(() => {
          return listaAlunos().filter(a => a.age > 18)
        });
      </code>
    </pre>
    </div>
  </div>

  <!--Pop Up-->
  <div v-if="open" class="fixed inset-0 flex items-center justify-center z-50">
    <div class="bg-white p-8 rounded shadow-lg">
      <button @click="open = false" class="float-right">Close</button>
      <h1 class="mb-4 text-xl">Form</h1>
      <form @submit.prevent="submitEdiForm(fEdit)">
        <div class="mb-4">
          <label for="name" class="block mb-2">Name</label>
          <input v-model="fEdit.name" type="text" id="name" class="block w-full p-2 border rounded" required>
        </div>
        <div class="mb-4">
          <label for="age" class="block mb-2">Age</label>
          <input v-model="fEdit.age" type="number" id="age" class="block w-full p-2 border rounded" required>
        </div>
        <button type="submit" class="px-4 py-2 text-white bg-blue-500 rounded">Submit</button>
      </form>
    </div>
  </div>

  <!--      -->
</template>


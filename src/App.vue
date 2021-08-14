<template>
  <div class="container mx-auto w-full">
    <form @submit.prevent="submitData">
      <label for="ticketId">Enter your Ticket ID</label>
      <input
        class=" rounded-sm border-2 border-gray-300 block py-2 px-2 text-gray-400"
        placeholder="Ticket ID e.g. WSBA-1234"
        name="ticketId"
        v-model="data.id"
      />

      <button
        class="bg-green-400 hover:bg-green-500 text-white font-bold py-2 px-4 my-3 rounded"
        type="submit">
        Check
      </button>
    </form>

    <!-- Not Found message -->
    <div class="container mx-auto my-5" v-if="notFound">
      <p class="font-light text-gray-500">Ticket with the ID was not found</p>
    </div>

    <!-- Ticket DATA -->
    <div
      class="container mx-auto my-5 grid grid-cols-2 gap-2"
      v-if="workItem.id && !notFound && !isLoading"
    >
      <div
        v-for="(value, name) in workItem"
        v-bind:key="name"
        class="bg-gray-200 pl-2 py-2 rounded-md hover:bg-gray-300"
      >
        <p class="font-medium text-gray-500">{{ name.toUpperCase() }}</p>
        <p class="font-light text-gray-500">{{ value ? value : "n/a" }}</p>
      </div>
    </div>

    <div class="loader mx-auto" v-if="isLoading"></div>
  </div>
</template>

<script>
import { reactive, ref } from "vue";
import axios from "axios";

export default {
  setup() {
    // Create object variable for the form inputs
    var data = reactive({
      id: null,
    });

    var notFound = ref(false);

    var isLoading = ref(false)

    const apiUrl =
      "https://prod-15.westeurope.logic.azure.com:443/workflows/3d2695d1848a400fa51cd37dc10569c5/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=b-nzaMmrI_8ma9-nI6cHDRdL3t4g1tAz5G65e7bqOfc";

    let workItem = reactive({
      id: null,
      sbu: null,
      title: null,
      state: null,
      deadline: null,
      priority: null,
      brand: null,
      primaryContact: null,
      secondaryContact: null,
      sprint: null,
    });

    async function submitData() {
      this.notFound = false;
      this.isLoading = true
      var ticket = {
        id: parseInt(data.id.replace('WSBA-', ''))
      }

      await axios
        .post(apiUrl, ticket, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((response, ) => {
          console.log(response);
          if (response.status == 200) {
            this.workItem.id = response.data.id;
            this.workItem.sbu = response.data.sbu;
            this.workItem.title = response.data.title;
            this.workItem.state = response.data.state;
            this.workItem.deadline = response.data.deadline;
            this.workItem.priority = response.data.priority;
            this.workItem.primaryContact = response.data.primaryContact;
            this.workItem.secondaryContact = response.data.secondaryContact;
            this.workItem.brand = response.data.brand;
            this.workItem.sprint = response.data.sprint;
          } else {
            this.notFound = true;
          }
          this.isLoading = false;
        })
        .catch((err) => {
          console.log(err);
        });
    }

    return { submitData, data, workItem, notFound, isLoading };
  },
};
</script>

<style>
/* TBD */
label {
  display: block;
}

.loader {
  border: 8px solid #f3f3f3;
  border-radius: 50%;
  border-top: 8px solid rgb(46, 194, 46);
  width: 50px;
  height: 50px;
  -webkit-animation: spin 2s linear infinite; /* Safari */
  animation: spin 2s linear infinite;
}

/* Safari */
@-webkit-keyframes spin {
  0% { -webkit-transform: rotate(0deg); }
  100% { -webkit-transform: rotate(360deg); }
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>

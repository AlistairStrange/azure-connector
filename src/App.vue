<template>
  <div class="container mx-auto px-4">
    <form @submit.prevent="submitData">
      <label for="ticketId" class="mb-2 mx-2">Enter your Ticket ID</label>
      <div class="flex">
        <input
          class="w-3/12 rounded-sm border-2 border-gray-300 block px-2 mx-2 text-gray-400"
          placeholder="Enter your Ticket ID - e.g. WSBA-1234"
          name="ticketId"
          v-model="data.id"
        />

        <button
          class="text-white font-bold py-2 px-4 my-3"
          type="submit">
          Check
        </button>
      </div>
    </form>

    <!-- Not Found message -->
    <not-found v-if="notFound"></not-found>

    <!-- Loading wheel -->
    <loading-wheel v-if="isLoading"></loading-wheel>

    <!-- Ticket DATA -->
    <search-results :data=workItem v-if="workItem.id && !notFound && !isLoading"></search-results>

  </div>
</template>

<script>
import { reactive, ref } from "vue";
import axios from "axios";
import LoadingWheel from "./components/LoadingWheel.vue";
import SearchResults from "./components/SearchResults.vue";
import NotFound from "./components/NotFound.vue";

export default {
  components: { LoadingWheel, SearchResults, NotFound },
  setup() {
    // Create object variable for the form inputs
    var data = reactive({
      id: null,
    });

    // Toggle the error message
    var notFound = ref(false);
    // Toggle the loading animation
    var isLoading = ref(false);

    // Logic apps url hook
    const apiUrl = "https://prod-15.westeurope.logic.azure.com:443/workflows/3d2695d1848a400fa51cd37dc10569c5/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=b-nzaMmrI_8ma9-nI6cHDRdL3t4g1tAz5G65e7bqOfc";

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

    // MAKING the HTTP request to LogicApps
    async function submitData() {
      // Mantain control variables before every new search
      this.notFound = false;
      this.isLoading = true

      // Consolidate user's input
      var ticket = {
        id: adjustInput()
      }

      // Call the LogicApps hook
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

    function adjustInput() {
      // put the input to UpperCase
      var ticketNumber = data.id.toUpperCase();
      // remove the ticket prefix - add more rules if needed e.g. WSBB
      ticketNumber = parseInt(ticketNumber.replace('WSBA-', ''));

      return ticketNumber;
    }

    return { submitData, data, workItem, notFound, isLoading };
  },
};
</script>

<style scoped>
  /* TBD */
  label {
    display: block;
  }

  button {
    /* Henkel Red used for OneWeb*/
    background-color: rgb(225, 0, 15) !important; 
  }

  button:hover { 
    background-color: #84010a !important;
  }
</style>

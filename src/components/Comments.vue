<template>
  <div class="w-6/12 container my-5 px-4">
    <form @submit.prevent="submitComment">
      <!--Email  -->
        <label for="email" class="mb-2 mx-2">From</label>
        <input
          class="rounded-sm border-2 border-gray-300 block px-2 mx-2 text-gray-400"
          placeholder="Enter your email address"
          name="email"
          type="email"
          v-model="comment.from"
        />

        <!-- Text -->
        <label for="comment" class="mb-2 mx-2">Text</label>
        <textarea
          class="w-full rounded-sm border-2 border-gray-300 block px-2 mx-2 text-gray-400"
          name="comment"
          v-model="comment.text"
          placeholder="Enter your comment"
          rows="5"
        />

        <button class="text-white font-bold py-2 px-4 my-3" type="submit">
          Submit
        </button>
    </form>

    <loading-wheel v-if="isLoading"></loading-wheel>
    <p v-if="success">Success</p>
    <p v-if="failure">Failure</p>
  </div>
</template>

<script>
import { reactive, ref } from "vue";
import axios from "axios";
import LoadingWheel from "./LoadingWheel.vue";

export default {
  components: { LoadingWheel },
  props: {
    id: Number,
  },
  setup(props) {
    var comment = reactive({
      email: null,
      text: null,
      id: props.id,
    });

    var success = ref(false);
    var failure = ref(false);
    var isLoading = ref(false);
    const apiUrl =
      "https://prod-240.westeurope.logic.azure.com:443/workflows/1f4be62774a24ff49d688af3dc78dcaf/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=AFvlS67qtIoUDeTCJBs94t1ywQDNojG7BoKPfiCm-dQ";

    async function submitComment() {
      if (comment.email && comment.text) {
        this.isLoading = true;

        await axios
          .post(apiUrl, comment, {
            headers: {
              "Content-Type": "application/json",
            },
          })
          .then((response) => {
            console.log(response);
            if (response.status == 200) {
              this.success = true;
              // Clean input
              comment.text = null;
            } else {
              this.failure = true;
            }
            this.isLoading = false;
          })
          .catch((err) => {
            console.log(err);
          });
      }
    }

    return {
      comment,
      submitComment,
      isLoading,
      success,
      failure,
    };
  },
};
</script>

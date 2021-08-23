<template>
  <div class="w-full container my-2 px-4 bg-gray-200 py-2 rounded-md float-right">
    <p class="text-gray-400 hover:text-gray-500 float-right cursor-pointer" @click="hideComments">
      Close x
    </p>
    <form @submit.prevent="submitComment" id="comments-section">
      <!--Email  -->
      <label for="email" class="mb-2 mx-2 font-medium text-gray-500">From</label>
      <input
        class="w-8/12 rounded-md bg-gray-100 block px-2 py-2 mx-2 mb-2 text-gray-400"
        placeholder="Enter your email address"
        name="email"
        type="email"
        v-model="comment.email"
      />

      <!-- Text -->
      <label for="comment" class="mb-2 mx-2 font-medium text-gray-500">Text</label>
      <textarea
        class="w-11/12 rounded-md bg-gray-100 block px-2 mx-2 text-gray-400"
        name="comment"
        v-model="comment.text"
        placeholder="Enter your comment"
        rows="5"
      />

      <button class="text-white font-bold py-2 px-4 mx-2 my-4 rounded-md" v-if="!isLoading" type="submit">
        Submit
      </button>
    </form>

    <loading-wheel v-if="isLoading"></loading-wheel>
    <p v-if="success" class="text-gray-500 font-light mx-2">
      Comment was successfully posted
    </p>
    <p v-if="failure" class="text-gray-500 font-light mx-2">
      Ooops, something went wrong
    </p>
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
  emits: ["hide-comments"],
  setup(props, { emit }) {
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

    function hideComments() {
      emit("hide-comments");
    }

    async function submitComment() {
      if (comment.email.length >= 5 && comment.text.length > 0) {
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
      hideComments,
      isLoading,
      success,
      failure,
    };
  },
};
</script>

<template>
  <div class="bg-gray-100 cursor-pointer pl-2 py-2 rounded-md hover:bg-gray-200" @click="copy(content)">
    <!-- Name -->
    <p class="font-medium text-gray-500">
        {{ mail }}
    </p>

    <!-- Message -->
    <span class="font-light text-gray-500" v-html="message">
    </span>
  </div>
</template>

<script>
import {ref} from "vue";


export default {
    props: { 
        mail: String,
        message: String,
    },
  setup(props) {
      var mail = ref(props.mail);
      var message = ref(props.message);

      return {
          mail,
          message
      }
  },
};
</script>

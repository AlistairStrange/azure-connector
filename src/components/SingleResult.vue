<template>
  <div class="bg-gray-100 cursor-pointer pl-2 py-2 rounded-md hover:bg-gray-200" @click="copy(content)">
    <!-- Copied message -->
    <span class="text-gray-400 font-light float-right mr-3 animate-bounce" v-if="isCopied">Copied</span>

    <!-- Label -->
    <p class="font-medium text-gray-500">{{ name.toUpperCase() }}</p>

    <!-- Value -->
    <p class="font-light text-gray-500">
      {{ content }}
    </p>
  </div>
</template>

<script>
import { ref, watchEffect } from "vue";
import useClipboard from "vue-clipboard3";

export default {
  props: {
    label: String,
    value: String,
  },
  setup(props) {
    const { toClipboard } = useClipboard();

    var name = ref(props.label);
    var content = ref(props.value);
    var isCopied = ref(false);

    // Copy to clipboard
    async function copy(val) {
      try {
        await toClipboard(val);

        // Trigger info message
        isCopied.value = true;
      } catch (e) {
        console.error(e);
      }
    }

    // Hide Copied info message after 3 seconds
    watchEffect(() => {
      if (isCopied.value) {
        setTimeout(() => (isCopied.value = false), 2000);
      }
    });

    return {
      name,
      content,
      isCopied,
      copy,
    };
  },
};
</script>

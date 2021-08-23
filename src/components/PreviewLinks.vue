<template>
  <div class="col-span-full bg-gray-100 pl-2 py-2 rounded-md hover:bg-gray-200 gap-2">
    <DocumentDuplicateIcon class="h-5 w-5 cursor-pointer text-gray-400 font-light float-right mr-3" @click="copy(previewLinks)" />
    <!-- Copied message -->
    <span class="text-gray-400 font-light float-right mr-3 animate-bounce" v-if="isCopied">Copied</span>

    <p class="font-medium text-gray-500">PREVIEW LINKS</p>
    <span class="font-light text-gray-500" v-html="previewLinks"> </span>
  </div>
</template>

<script>
import { ref, watch } from "vue";
import useClipboard from "vue-clipboard3";
import { DocumentDuplicateIcon } from "@heroicons/vue/solid";

export default {
  props: {
    links: String,
  },
  components: { DocumentDuplicateIcon },
  setup(props) {
    const { toClipboard } = useClipboard();

    var previewLinks = ref(props.links);
    var isCopied = ref(false);

    // Copy to clipboard
    async function copy(val) {
      try {
        // First replace html elements, then format each link on separate line
        await toClipboard(val.replace(/(<([^>]+)>)/gi, "").replaceAll('.html', '.html \n\n'));

        // Trigger info message
        isCopied.value = true;
      } catch (e) {
        console.error(e);
      }
    }

    // Hide Copied info message after 3 seconds
    watch(() => {
      if (isCopied.value) {
        setTimeout(() => (isCopied.value = false), 2000);
      }
    });

    return {
      previewLinks,
      copy,
      isCopied,
    };
  },
};
</script>

<style scoped>
ul {
  list-style: disclosure-closed;
  list-style-position: inside;
}
</style>

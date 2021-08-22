<template>
  <div class="container mx-auto mt-5 grid grid-cols-2 gap-2">
    <single-result
      v-for="(value, name) in ticketData"
      v-bind:key="name"
      :label="name"
      :value="value ? value : 'n/a'"
    ></single-result>

    <!-- Preview links full col width -->
    <preview-links :links="previewLinks ? previewLinks : 'n/a'"></preview-links>
  </div>

  <p
    class="font-medium text-gray-400 hover:text-gray-500 float-right cursor-pointer"
    @click="triggerComments"
    v-if="!showComments"
  >
    + Add comment
  </p>

  <!-- Comment Section -->
  <add-comment @hide-comments="triggerComments" v-if="showComments" :id="ticketData.id"></add-comment>
</template>

<script>
import { reactive, ref } from "vue";
import AddComment from "./AddComment.vue";
import SingleResult from "./SingleResult.vue";
import PreviewLinks from "./PreviewLinks.vue";

export default {
  components: { AddComment, SingleResult, PreviewLinks },
  props: {
    data: Object,
  },

  setup(props) {
    const ticketData = reactive({
      id: props.data.id,
      sbu: props.data.sbu,
      title: props.data.title,
      state: props.data.state,
      deadline: props.data.deadline,
      priority: props.data.priority,
      sprint: props.data.sprint,
      brand: props.data.brand,
      primaryContact: props.data.primaryContact,
      secondaryContact: props.data.secondaryContact,
    });

    const previewLinks = ref(props.data.previews);

    var showComments = ref(false);

    function triggerComments() {
      showComments.value = !showComments.value;
    }

    return {
      ticketData,
      previewLinks,
      showComments,
      triggerComments,
    };
  },
};
</script>

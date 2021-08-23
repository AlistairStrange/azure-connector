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

  <add-comment-button @click="triggerComments" v-if="!showComments"> </add-comment-button>

  <!-- Comment Section -->
  <add-comment @hide-comments="triggerComments" v-if="showComments" :id="ticketData.id"></add-comment>
</template>

<script>
import { reactive, ref } from "vue";
import AddComment from "./AddComment.vue";
import SingleResult from "./SingleResult.vue";
import PreviewLinks from "./PreviewLinks.vue";
import AddCommentButton from "./AddCommentButton.vue";

export default {
  components: { AddComment, SingleResult, PreviewLinks, AddCommentButton },
  props: {
    data: Object,
  },

  setup(props) {
    const ticketData = reactive({
      id: 'WSBA-' + props.data.id,
      sbu: props.data.sbu,
      title: props.data.title,
      state: props.data.state,
      deadline: props.data.deadline.split("T")[0], // remove Time info from timestamp
      priority: props.data.priority,
      sprint: props.data.sprint,
      brand: props.data.brand,
      primaryContact: props.data.primaryContact.replaceAll(";", "; "), //properly format multiple mail adresses in a single field
      secondaryContact: props.data.secondaryContact ? props.data.secondaryContact.replaceAll(";", "; ") : props.data.secondaryContact,
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

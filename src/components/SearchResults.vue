<template>
  <div class="container mx-auto mt-5 grid grid-cols-2 gap-2">
    <div
      v-for="(value, name) in ticketData"
      v-bind:key="name"
      class="bg-gray-200 pl-2 py-2 rounded-md hover:bg-gray-300"
    >
      <p class="font-medium text-gray-500">{{ name.toUpperCase() }}</p>
      <p class="font-light text-gray-500">
        {{ value ? value : "n/a" }}
      </p>
    </div>

    <!-- Preview links full col width -->
    <div
      class="col-span-full bg-gray-200 pl-2 py-2 rounded-md hover:bg-gray-300 gap-2"
    >
      <p class="font-medium text-gray-500">PREVIEW LINKS</p>
      <span
        class="font-light text-gray-500"
        v-html="previewLinks ? previewLinks : 'n/a'"
      >
      </span>
    </div>
  </div>
  
  <p class="font-medium text-gray-500 float-right cursor-pointer" @click="triggerComments" v-if=!showComments>+ Add comment</p>
  <!-- Comment Section -->
  <comments @hide-comments="triggerComments" v-if=showComments :id=ticketData.id></comments>
</template>

<script>
import { reactive, ref } from "vue";
import Comments from "./Comments.vue";

export default {
  components: { Comments},
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

    function triggerComments(){
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

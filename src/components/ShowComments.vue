<template>
  <!-- Just display all comments - already filtered -->
  <div class="container">
    <h2 class="font-medium text-gray-500 text-xl my-2">Discussion</h2>
    <!-- Looping through comments array -->
    <div v-for="(value, index) in comments" v-bind:key="index" class="bg-white pl-2 py-2 my-2 rounded-md">
      <p v-if="value.createdBy.uniqueName" class="font-medium text-gray-500">
        {{ value.createdBy.uniqueName }}
      </p>

      <span v-if="value.text" class="font-light text-gray-500" v-html="value.text"> </span>
    </div>

    <span v-if="comments.length == 0" class="font-light text-gray-500">
      No comments available.
    </span>
  </div>
</template>

<script>
import axios from "axios";
import { ref, onMounted } from "vue";

// import SingleComment from "./SingleComment.vue";

export default {
  props: {
    id: Number,
  },
  // components: { SingleComment },
  setup(props) {
    onMounted(() => {
      getComments();
    });

    // Logic Apps hook for fetching all comments
    const apiUrl =
      "https://prod-49.westeurope.logic.azure.com:443/workflows/027ad0da0ef54f7cb25a6ed376ace107/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=E_RZgM1w7gP4NGxmG9m7rIHOXlF8CCRIdQ5Xn5jWxJY";
    // Reply token to be set headers
    const replyToken = "?reply?";
    const replyMail = "webstudio-reply@henkel.com";
    const externalCommentToBeRemoved = "<strong>Posted by:</strong> ";
    // Get ticket ID from props
    var ticket = {
      id: props.id,
    };
    // Array or Object of all comments
    var comments = ref([]);

    // Fetch all comments for specific Ticket ID stored in ticket.id
    async function getComments() {
      await axios
        .post(apiUrl, ticket, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((response) => {
          console.log("Complete response:");
          console.log(response);
          if (response.status == 200 && response.data.data.totalCount > 0) {
            filterComments(response.data.data.comments);
          }
        })
        .catch((error) => {
          console.log(error);
        });
    }

    // This function is called when success response received from Logic Apps
    function filterComments(data) {
      // Remove unnecessary comments and return adjust object/array
      console.log("filter function:");
      console.log(data);

      data.forEach((comment) => {
        if (isReply(comment.text)) {
          comment.text = adjustInternalComment(comment.text);
          comments.value.push(comment);
        } else if (isExternalComment(comment)) {
          // Adjust the reply name for all external comments
          comment.createdBy.uniqueName = getExternalMail(comment.text);
          // Adjusting the text by removing the last part "Posted by...."
          comment.text = comment.text.slice(0, comment.text.indexOf(externalCommentToBeRemoved));
          comments.value.push(comment);
        }
      });
    }

    // Check if comment is marked by WebStudio team as replyASw`3de4
    function isReply(data) {
      if (data.includes(replyToken)) {
        return true;
      } else {
        return false;
      }
    }
    // Check if the comment was created by business partner
    function isExternalComment(data) {
      if (data.createdOnBehalfOf && data.createdOnBehalfOf.uniqueName == replyMail) {
        return true;
      } else {
        return false;
      }
    }

    // Adjust Internal comment
    function adjustInternalComment(data) {
      return data.replace(replyToken, "");
    }

    // Get mail of business partner who posted the comment
    function getExternalMail(data) {
      // Get index of last line with external mail
      var start = data.indexOf(externalCommentToBeRemoved);
      // Get the externaal mail -> start + externalCommentToBeRemoved means we get rid of the "Posted By..." part
      var mail = data.substring(start + externalCommentToBeRemoved.length, data.length);

      return mail;
    }

    return { comments, ticket };
  },
};
</script>

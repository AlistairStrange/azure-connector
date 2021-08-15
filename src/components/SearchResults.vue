<template>
  <div class="container mx-auto my-5 grid grid-cols-2 gap-2">
    <div
      v-for="(value, name) in props.data"
      v-bind:key="name"
      class="bg-gray-200 pl-2 py-2 rounded-md hover:bg-gray-300"
    >
      <p class="font-medium text-gray-500">{{ name.toUpperCase() }}</p>
      <p class="font-light text-gray-500">{{ value ? value : "n/a" }}</p>
    </div>
  </div>
</template>

<script>
export default {
    props: {
        data: Object
    },

    setup(props) {

        return { 
            props,
        }
    },
}
</script>

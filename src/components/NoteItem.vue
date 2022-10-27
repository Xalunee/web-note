<template>
  <div
    class="note border rounded p-3 m-2"
    v-bind:style="{ 'background-color': note.color }"
  >
    <div>
      <h5>
        <strong>{{ note.title }}</strong>
      </h5>
      <div class="d-flex border-top p-2">
        <p>{{ note.content }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    note: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style scoped>
.note {
  width: 300px;
}
</style>

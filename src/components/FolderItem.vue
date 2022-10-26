<template>
  <div
    class="folder border rounded p-3 mt-2"
    v-bind:style="{ 'background-color': folder.color }"
  >
    <div>
      <h5>
        <strong>{{ folder.name }}</strong>
        <p>Заметок: {{ folder.notesCount }}</p>
      </h5>
    </div>
    <div class="folder__btns">
      <button class="btn btn-danger btn-sm" @click="$emit('remove', folder)">
        Удалить
      </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    folder: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style scoped></style>

<template>
  <div
    class="folder border rounded p-3 mt-2"
    v-bind:style="{ 'background-color': folder.color }"
  >
    <div>
      <h5 class="mb-0">
        <strong>{{ folder.name }}</strong>
      </h5>
      <p class="mb-2">Заметки: {{ folder.notesCount }}</p>
    </div>
    <div class="folder__btns d-flex justify-content-between">
      <button class="btn btn-dark btn-sm" @click="$emit('edit', folder)">
        Изменить
      </button>
      <button class="btn btn-danger btn-sm" @click="$emit('remove', folder)">
        Удалить
      </button>
      <button class="btn btn-info btn-sm" @click="$emit('showNotes', folder)">
        Выбрать папку
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

<style scoped>
@media (max-width: 811px) {
  .folder__btns {
    flex-direction: column;
    row-gap: 5px;
  }
  
  .btn-sm {
    font-size: 12px;
  }
}
</style>

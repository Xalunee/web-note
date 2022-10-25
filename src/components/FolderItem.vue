<template>
  <div class="folder border rounded p-3 mt-2">
    <div>
      <h5>
        <strong>{{ folder.name }}</strong>
      </h5>
    </div>
    <div class="folder__btns">
      <button 
        class="btn btn-outline-danger btn-sm"
        @click="$emit('remove', folder)"
      >
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

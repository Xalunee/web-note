<template>
  <div
    class="note border rounded p-3 m-2"
    v-bind:style="{ 'background-color': note.color }"
  >
    <div>
      <h5>
        <strong>{{ note.title }}</strong>
      </h5>
      <div class="d-flex border-top content">
        <p class="m-0">{{ note.content }}</p>
      </div>
      <div class="note__btns d-flex justify-content-between">
        <button class="btn btn-dark btn-sm" @click="$emit('edit', note)">
          Изменить
        </button>
        <button class="btn btn-danger btn-sm" @click="$emit('remove', note)">
          Удалить
        </button>
        <button class="btn btn-info btn-sm" @click="$emit('move', note)">
          Переместить
        </button>
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
  height: auto;
}

.content {
  padding-top: 15px;
  padding-bottom: 15px;
}
.content > * {
  text-align: left;
  width: 260px;
  word-wrap: break-word;
}

@media (max-width: 811px) {
  .note {
    width: 220px;
    height: auto;
  }

  .content {
    height: auto;
  }

  .content > * {
    width: 180px;
  }
  .note__btns {
    flex-direction: column;
    row-gap: 5px;
  }

  .btn-sm {
    font-size: 12px;
  }
}
</style>

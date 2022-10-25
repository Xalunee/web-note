<template>
  <form @submit.prevent class="text-start rounded p-2">
    <h5><strong>Создание папки</strong></h5>
    <label for="name" class="form-label">Название</label>
    <input
      v-model="folder.title"
      type="text"
      class="form-control mb-3"
      id="name"
    />
    <button
    @click="createFolder"
    type="submit"
    class="btn btn-outline-dark">
      Создать
    </button>
  </form>
</template>

<script>
export default {
  data() {
    return {
      folder: {
        title: ''
      }
    }
  },
  methods: {
    createFolder() {
      this.folder.id = Date.now();
      this.$emit('create', this.folder)
      this.folder = {
        title: ''
      }
    }
  },
};
</script>

<style scoped></style>

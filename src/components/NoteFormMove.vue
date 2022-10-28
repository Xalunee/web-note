<template>
  <form @submit.prevent class="text-start rounded p-2">
    <h5><strong>Перемещение заметки</strong></h5>
    <label for="folder" class="form-label">Папка</label>
    <my-select
      class="form-control mb-3"
      id="folder"
      v-model="note.folder"
      :options="folderOptions"
    />
    <button class="btn btn-dark" @click="moveNote">Переместить</button>
  </form>
</template>

<script>
import MySelect from "./UI/MySelect.vue";

export default {
  props: {
    folderOptions: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      note: {
        folder: "",
        options: "",
      },
    };
  },
  methods: {
    moveNote() {
      this.$emit("move", this.note);
    },
  },
  components: { MySelect },
};
</script>

<style scoped></style>

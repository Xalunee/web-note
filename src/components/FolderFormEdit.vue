<template>
  <form @submit.prevent class="text-start rounded p-2">
    <h5><strong>Изменение папки</strong></h5>
    <label for="name" class="form-label">Название</label>
    <input
      v-model="folder.name"
      type="text"
      class="form-control mb-3"
      id="name"
    />
    <label for="color" class="form-label">Цвет</label>
    <my-select
      class="form-control mb-3"
      id="color"
      v-model="folder.color"
      :options="colorOptions"
    />
    <button class="btn btn-dark" @click="editFolder">Изменить</button>
  </form>
</template>

<script>
import MySelect from "./UI/MySelect.vue";

export default {
  data() {
    return {
      folder: {
        name: "",
        color: "White",
      },
      colorOptions: [
        { value: "White", name: "White" },
        { value: "Blue", name: "Blue" },
        { value: "Orange", name: "Orange" },
        { value: "Yellow", name: "Yellow" },
        { value: "Purple", name: "Purple" },
        { value: "Pink", name: "Pink" },
        { value: "Green", name: "Green" },
        { value: "Lime", name: "Lime" },
        { value: "LightGray", name: "LightGray" },
      ],
    };
  },
  methods: {
    editFolder() {
      this.$emit("edit", this.folder);
      this.folder = {
        name: "",
      };
    },
  },
  components: { MySelect },
};
</script>

<style scoped></style>

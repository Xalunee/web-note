<template>
  <form @submit.prevent class="text-start rounded p-2">
    <h5><strong>Создание заметки</strong></h5>
    <label for="title" class="form-label">Название</label>
    <input
      v-model="note.title"
      type="text"
      class="form-control mb-3"
      id="title"
    />
    <label
      for="content"
      class="form-label"
    >
      Описание
    </label>
    <textarea
      v-model="note.content"
      maxlength="256"
      class="form-control textarea mb-3"
      id="content"
    >
    </textarea>
    <label for="color" class="form-label">Цвет</label>
    <my-select
      class="form-control mb-3"
      id="color"
      v-model="note.color"
      :options="colorOptions"
    />
    <button @click="createNote" type="submit" class="btn btn-outline-dark">
      Создать
    </button>
  </form>
</template>

<script>
import MySelect from "./UI/MySelect.vue";

export default {
  data() {
    return {
      note: {
        title: "",
        content: "",
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
    createNote() {
      this.$emit("create", this.note);
      this.note = {
        title: "",
        content: "",
      };
    },
  },
  components: { MySelect },
};
</script>

<style scoped>
.textarea {
  height: 200px;
  width: 300px;
  resize: none;
}
</style>

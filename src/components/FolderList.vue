<template>
  <div>
    <my-dialog v-model:show="dialogEditVisible">
      <folder-form-edit @create="editFolder" />
    </my-dialog>
    <folder-item
    v-for="folder in folders"
    :folder="folder"
    :key="folder.id"
    @remove="$emit('remove', folder)"
    @edit="$emit('edit', folder)"
    />
  </div>
</template>

<script>
import FolderItem from "./FolderItem.vue";

export default {
  props: {
    folders: {
      type: Array,
      required: true,
    },
  },
  components: { FolderItem },
};
</script>

<style scoped></style>

<template>
  <div>
    <folder-item
    v-for="folder in folders"
    :folder="folder"
    :key="folder.id"
    @remove="$emit('remove', folder)"
    @edit="$emit('edit', folder)"
    @showNotes="$emit('showNotes', folder)"
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

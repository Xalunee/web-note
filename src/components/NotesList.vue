<template>
  <div>
    <note-item
      v-for="note in notes"
      :note="note"
      :key="note.id"
      @remove="$emit('remove', note)"
      @edit="$emit('edit', note)"
      @move="$emit('move', note)"
    />
  </div>
</template>

<script>
import NoteItem from "./NoteItem.vue";

export default {
  props: {
    notes: {
      type: Array,
      required: true,
    },
  },
  components: { NoteItem },
};
</script>

<style scoped></style>

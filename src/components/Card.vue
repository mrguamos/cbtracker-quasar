<template>
  <q-card
    style="
      color: white;
      background: radial-gradient(circle, #35a2ff 0%, #014a88 100%);
    "
  >
    <q-card-section>
      <div class="text-h6">{{ label }}</div>
      <div class="text-subtitle2">${{ content }}</div>
    </q-card-section>
  </q-card>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
export default defineComponent({
  name: 'Card',
  props: {
    label: String,
    content: Number,
  },
});
</script>

<style></style>

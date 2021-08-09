<template>
  <q-page padding>
    <Suspense>
      <template #default>
        <index-wrapper />
      </template>
      <template #fallback>
        <div>Loading please wait ...</div>
      </template>
    </Suspense>
  </q-page>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import IndexWrapper from 'src/components/IndexWrapper.vue';

export default defineComponent({
  components: { IndexWrapper },
  name: 'Index',
});
</script>

<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="bg-primary text-white">
      <q-toolbar>
        <q-toolbar-title>
          <q-avatar>
            <img src="https://cdn.quasar.dev/logo-v2/svg/logo-mono-white.svg" />
          </q-avatar>
          CryptoBlades Tracker
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <div>
        <router-view />
      </div>
    </q-page-container>

    <q-footer elevated class="bg-primary text-white">
      <q-toolbar>
        <q-toolbar-title>
          <div class="row justify-center">
            <div>
              Powered by
              <a href="https://github.com/mrguamos/cbtracker" target="_blank"
                >mrguamos</a
              >
            </div>
          </div>
        </q-toolbar-title>
      </q-toolbar>
    </q-footer>
  </q-layout>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'MainLayout',
});
</script>

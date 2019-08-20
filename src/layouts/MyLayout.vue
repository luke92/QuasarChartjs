<template>
  <q-layout view="hHh lpR fFf">

    <q-header elevated class="bg-primary text-white">
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          @click="leftDrawerOpen = !leftDrawerOpen"
          aria-label="Menu"
        >
          <q-icon name="menu" />
        </q-btn>

        <q-toolbar-title>
          HUA Reports
        </q-toolbar-title>

        <div>HUA Reports v1.0</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      bordered
      content-class="bg-grey-2"
    >
      <q-list>
        <q-item-label header>Aplicaciones</q-item-label>
        <q-item clickable tag="a" target="" to="labcoreVistaResultados">
          <q-item-section avatar>
            <q-icon name="img:statics/labcore.png" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Resultados Laboratorio</q-item-label>
            <q-item-label caption>LabCore LIS</q-item-label>
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { openURL } from 'quasar';

export default {
  name: 'MyLayout',
  data() {
    return {
      leftDrawerOpen: this.$q.platform.is.desktop,
    };
  },
  created() {
    console.log(this.$q.localStorage.getItem('API_URL'));
  },
  methods: {
    openURL,
  },
};
</script>

<style>
</style>

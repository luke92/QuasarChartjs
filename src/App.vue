<template>
  <div id="q-app">
    <router-view />
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  created() {
    axios.get('appsettings.json').then((response) => {
      this.$q.localStorage.set('API_URL', response.data.API_URL);
    });
  },
};
</script>

<style>
</style>

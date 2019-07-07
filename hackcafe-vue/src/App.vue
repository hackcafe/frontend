<template>
  <v-app dark>
    <global-toolbar></global-toolbar>

    <v-content>
      <MainPage/>
    </v-content>
  </v-app>
</template>

<script>
import MainPage from './components/MainPage';
import GlobalToolbar from './components/GlobalToolBar';

export default {
  name: 'App',
  components: {
    MainPage,
    GlobalToolbar,
  }
}
</script>

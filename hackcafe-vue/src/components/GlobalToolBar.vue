<template>
  <v-toolbar app dark color="primary">
    <img :src="logoSrc" height='50px'>
    <v-toolbar-title class="headline">
      <span class="font-weight-bold">HackCafe</span>
    </v-toolbar-title>

    <v-spacer></v-spacer>

    <v-toolbar-items>
      <v-btn large icon href="https://github.com/hackcafe" target="_blank">
        <github-circle-icon class="icon-2x"/>
      </v-btn>
    </v-toolbar-items>
  </v-toolbar>
</template>

<style>
.material-design-icon.icon-2x {
  height: 2em;
  width: 2em;
}
.material-design-icon.icon-2x > .material-design-icon__svg {
  height: 2em;
  width: 2em;
}
</style>

<script>
import GithubCircleIcon from 'vue-material-design-icons/GithubCircle';

export default {
  name: 'GlobalToolbar',
  components: {
    GithubCircleIcon
  },
  data: () => ({
    logoSrc: require('@/assets/HackCafeLogo.png')
  })
};
</script>
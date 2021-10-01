<template>
  <v-app>
    <AppBar />
    <v-main>
      <router-view />
    </v-main>
  </v-app>
</template>

<script>
import AppBar from "./components/AppBar.vue";
import Home from "./views/Home.vue";

export default {
  name: "App",

  data: () => ({
    //
  }),
  components: {
    AppBar,
    Home
  }
};
</script>

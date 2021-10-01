<template>
  <div>
    <v-app-bar dark app color="primary">
      <v-app-bar-nav-icon @click="menu =! menu"></v-app-bar-nav-icon>
      <v-toolbar-title>Music App</v-toolbar-title>
      <template v-slot:extension>
        <v-tabs center-active centered>
          <v-tabs-slider color="yellow"></v-tabs-slider>
          <v-tab v-for="(item, index) in enlaces" 
            :key="index" 
            :to="{ name: item.nombre }" 
            exact>
            {{ item.nombre }}
          </v-tab>
        </v-tabs>
      </template>
    </v-app-bar>

    <v-navigation-drawer 
      app
      temporary 
      v-model="menu">
      <v-list-item>
        <v-list-item-title class="title">Music App</v-list-item-title>
      </v-list-item>
      <v-divider></v-divider>
      <v-list nav dense shaped>
        <v-list-item
          v-for="(item, index) in enlaces" :key="index"
          link :to="{ name: item.nombre }" 
          exact
        >
          {{ item.nombre }}
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
  </div>
</template>

<script>
export default {
  name: "AppBar",
  data() {
    return {
      menu: false,
      enlaces: [
        { nombre: 'Home' },
        { nombre: 'Search' },
      ]
    };
  },
};
</script>
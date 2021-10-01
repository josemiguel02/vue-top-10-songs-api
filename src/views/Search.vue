<template>
  <v-container>
    <v-row justify="space-between">
      <v-col xs="12" sm="6" md="4" class="mt-10">
        <!-- Alerts -->
        <div>
          <v-alert
            :type="alert.type"
            v-model="alert.active"
            dismissible
            transition="scale-transition">
            {{alert.title}}
          </v-alert>
        </div>

        <h1 class="display-1">Top 10 Songs</h1>

        <!-- Search -->
        <v-text-field
          class="mt-4"
          color="yellow darken-1"
          :loading="false"
          label="Ingrese una cancion o un artista"
          v-model="search"
          @keyup.enter="getHits(search)"
        ></v-text-field>

        <!-- Button -->
        <v-btn
          color="yellow darken-1"
          rounded
          class="ma-2 white--text"
          @click="getHits(search)"
        >
          Buscar
          <v-icon>mdi-account-music</v-icon>
        </v-btn>
      </v-col>

      <!-- Info Artista -->
      <v-col xs="12" sm="6" md="4"
        offset-md="3"
        class="mt-10"
        v-if="data.artist.length">
        <v-card class="pa-4 rounded-xl">
          <div
            v-for="(item, i) in data.artist"
            :key="i"
            class="d-flex flex-no-wrap justify-space-between">
            <v-avatar size="150">
              <v-img :src="item.result.primary_artist.image_url">
                <div class="fill-height bottom-gradient"></div>
              </v-img>
            </v-avatar>
            <div class="my-4">
              <v-card-title>
                <a :href="item.result.primary_artist.url" target="_blank" class="text-decoration-none">
                  <p class="indigo--text text--accent-2">{{ item.result.primary_artist.name }}</p>
                </a>
              </v-card-title>
              <v-card-subtitle
                v-if="item.result.primary_artist.is_verified === true"
              >
                <v-icon color="success">mdi-check-circle</v-icon>
                Is verified
              </v-card-subtitle>
              <v-card-subtitle v-else>
                <v-icon color="red">mdi-alpha-x-circle</v-icon>
                Is not verified
              </v-card-subtitle>
            </div>
          </div>
        </v-card>
      </v-col>
    </v-row>

    <!-- Progress Bar -->
    <v-row justify="space-around">
      <v-col cols="6" md="3">
        <v-dialog v-model="loading.estado" hide-overlay persistent width="300">
          <v-card class="pa-4" color="teal">
            <div class="mb-2 font-weight-medium text-center white--text">
              {{loading.titulo}}
            </div>
            <v-progress-linear
              color="white"
              indeterminate
              rounded
              height="7"
            >
            </v-progress-linear>
          </v-card>
        </v-dialog>
      </v-col>
    </v-row>

    <!-- Content Hits -->
    <v-row class="" align-content="center">
      <v-col v-for="(item, i) in data.hits" :key="i">
        <v-card :color="item.color" dark class="pa-1 rounded-xl">
          <div class="d-flex flex-no-wrap justify-space-between">
            <div>
              <v-card-title class="text-h5">{{ item.titulo }}</v-card-title>
              <v-card-subtitle>{{ item.nombre }}</v-card-subtitle>
              <v-card-actions>
                <v-btn
                  :href="item.enlace"
                  target="_blank"
                  class="ml-2 mt-3"
                  fab
                  icon
                  height="50px"
                  right
                  width="50px"
                >
                  <v-icon>mdi-play</v-icon>
                </v-btn>
              </v-card-actions>
            </div>
            <v-avatar class="ma-3" size="125" tile>
              <v-img :src="item.imagen"></v-img>
            </v-avatar>
          </div>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
/* Axios */
import axios from "axios";
import { mapState, mapMutations } from 'vuex';

export default {
  name: "About",
  data() {
    return {
      search: undefined,
      data: {
        artist: [],
        hits: []
      },
      alert: {
        active: false,
        type: null,
        title: null
      }
    };
  },
  methods: {
    ...mapMutations(['mostarLoading', 'ocultarLoading']),
    async getHits(artista) {
      //Cleaning Data
      this.data.artist = [];
      this.data.hits = [];
      
      const settings = {
        method: "GET",
        url: `https://genius.p.rapidapi.com/search?q=${artista}`,
        headers: {
          "x-rapidapi-key":
            "59f2c929b0msh75c51c91c2fcfa3p1c0b51jsn6337f5ea9ac6",
          "x-rapidapi-host": "genius.p.rapidapi.com",
        },
      };

      try {
        this.mostarLoading('Obteniendo Informacion');
        let datos = await axios.request(settings);
        let hits = datos.data.response.hits;

        if (hits.length) {
          this.showAlerts('success', 'Se ha encontrado resultados')
          this.data.artist.push(hits[0]);
          hits.forEach((item) => {
            let result = item.result;
            this.data.hits.push({
              titulo: result.title,
              nombre: result.primary_artist.name,
              imagen: result.song_art_image_url,
              color: result.song_art_secondary_color,
              enlace: result.url,
            });
          });
        } else {
          this.showAlerts('error', 'No se ha encontrado resultados')
        }
      } catch (error) {
        console.log(error);
      } finally {
        this.ocultarLoading();
      }
    },

    showAlerts(type, title) {
      this.alert.type = type;
      this.alert.title = title;
      this.alert.active = true;

      let timer = this.showAlerts.timer;
      if (timer) {
        clearTimeout(timer);
      }
      this.showAlerts.timer = setTimeout(() => {
        this.alert.active = false;
      }, 3000);
    }
  },

  computed: {
    ...mapState(['loading'])
  },
};
</script>
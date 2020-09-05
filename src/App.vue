<template>
  <v-app>
    <v-main>
      {{location}}
      <div style="width: 100% ;height: 100%;z-index:1222">
        <GeolocationSelector v-model="location" />
      </div>
    </v-main>
  </v-app>
</template>

<script>
import GeolocationSelector from "./components/GeolocationSelector";

export default {
  name: "App",

  components: {
    GeolocationSelector
  },

  data: () => ({
    dialog: false,
    location: {}
  })
};
</script>

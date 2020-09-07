<template>
  <v-app>
    <v-main>
      <v-container fluid grid-list-md>
        <v-layout wrap>
          <v-flex md12>
            <v-card outlined>
              <v-list-item v-if="!location.position">
                <v-list-item-avatar size="60">
                  <v-icon dark
                    large
                    class="
                      primary white--text`
                    "
                    >mdi-map-marker</v-icon
                  >
                </v-list-item-avatar>
                <v-list-item-content>
                  <v-list-item-title class="title"
                    >No Location selected</v-list-item-title
                  >
                </v-list-item-content>
              </v-list-item>
              <v-list-item v-else>
                <v-list-item-avatar size="60">
                  <v-icon
                    large
                    class="
                      success white--text
                    "
                    >mdi-map-marker-check</v-icon
                  >
                </v-list-item-avatar>
                <v-list-item-content>
                  <v-list-item-title class="title text-wrap">{{
                    location.address
                  }}</v-list-item-title>
                  <v-list-item-subtitle>
                    Latitude: {{ location.position.lat }}
                  </v-list-item-subtitle>
                  <v-list-item-subtitle>
                    Longitude: {{ location.position.lng }}
                  </v-list-item-subtitle>
                </v-list-item-content>
                <v-list-item-action>
                  <v-btn color="error" @click="reset">Reset</v-btn>
                </v-list-item-action>
              </v-list-item>
            </v-card>
          </v-flex>
          <v-flex xs12 md4>
            <v-list dense>
              <v-subheader>You can choose a geolocation by:</v-subheader>
              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-circle-medium</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>
                    Double tapping(clicking) on a location on the map
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-circle-medium</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>
                    Dragging and placing the marker
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-icon>
                  <v-icon>mdi-circle-medium</v-icon>
                </v-list-item-icon>
                <v-list-item-content>
                  <v-list-item-title>
                    Using the Geosearch tool on the top left
                  </v-list-item-title>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-flex>
          <v-flex xs12 md8>
            <v-sheet>
              <div style="width:100%;height: 500px">
                <GeolocationSelector v-model="location" :key="key" />
              </div>
            </v-sheet>
          </v-flex>
        </v-layout>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import GeolocationSelector from "./components/GeolocationSelectorMap";

export default {
  name: "App",

  components: {
    GeolocationSelector
  },

  data: () => ({
    dialog: false,
    location: {},
    key: 1
  }),
  methods: {
    reset() {
      this.key += 1;
      this.location = {};
    }
  }
};
</script>

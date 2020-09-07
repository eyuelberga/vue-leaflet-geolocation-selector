<template>
  <l-map
    ref="map"
    @dblclick="onMapClick"
    :zoom="zoom"
    :center="[
      position.lat || userLocation.lat || defaultLocation.lat,
      position.lng || userLocation.lng || defaultLocation.lng
    ]"
  >
    <l-tile-layer
      :url="tileProvider.url"
      :attribution="tileProvider.attribution"
    />
    <l-geosearch :options="geoSearchOptions"></l-geosearch>
    <l-marker
      v-if="position.lat && position.lng"
      visible
      draggable
      :icon="icon"
      :lat-lng.sync="position"
      @dragstart="dragging = true"
      @dragend="dragging = false"
    >
      <l-tooltip :content="tooltipContent" :options="{ permanent: true }" />
    </l-marker>
  </l-map>
</template>
<script>
import { LMap, LMarker, LTileLayer, LTooltip } from "vue2-leaflet";
import { OpenStreetMapProvider } from "leaflet-geosearch";
import LGeosearch from "vue2-leaflet-geosearch";
import { icon } from "leaflet";
export default {
  name: "LocationInput",
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LTooltip,
    LGeosearch
  },
  props: {
    value: {
      type: Object,
      required: true
    },
    defaultLocation: {
      type: Object,
      default: () => ({
        lat: 8.9806,
        lng: 38.7578
      })
    }
  },
  data() {
    return {
      loading: false,
      geoSearchOptions: {
        provider: new OpenStreetMapProvider(),
        showMarker: false,
        autoClose: true
      },
      userLocation: {},
      icon: icon({
        iconRetinaUrl: require("leaflet/dist/images/marker-icon-2x.png"),
        iconUrl: require("leaflet/dist/images/marker-icon.png"),
        shadowUrl: require("leaflet/dist/images/marker-shadow.png")
      }),
      position: {},
      address: "",
      tileProvider: {
        attribution:
          '&copy; <a target="_blank" href="http://osm.org/copyright">OpenStreetMap</a> contributors',
        url: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
      },
      zoom: 18,
      dragging: false
    };
  },
  mounted() {
    this.getUserPosition();
    this.$refs.map.mapObject.on("geosearch/showlocation", this.onSearch);
  },

  watch: {
    position: {
      deep: true,
      async handler(value) {
        this.address = await this.getAddress();
        this.$emit("input", { position: value, address: this.address });
      }
    }
  },
  computed: {
    tooltipContent() {
      if (this.dragging) return "...";
      if (this.loading) return "Loading...";
      return `<strong>${this.address.replace(
        ",",
        "<br/>"
      )}</strong> <hr/><strong>lat:</strong> ${
        this.position.lat
      }<br/> <strong>lng:</strong> ${this.position.lng}`;
    }
  },
  methods: {
    async getAddress() {
      this.loading = true;
      let address = "Unresolved address";
      try {
        const { lat, lng } = this.position;
        const result = await fetch(
          `https://nominatim.openstreetmap.org/reverse?format=jsonv2&lat=${lat}&lon=${lng}`
        );
        if (result.status === 200) {
          const body = await result.json();
          address = body.display_name;
        }
      } catch (e) {
        console.error("Reverse Geocode Error->", e);
      }
      this.loading = false;
      return address;
    },
    async onMapClick(value) {
      // place the marker on the clicked spot
      this.position = value.latlng;
    },
    onSearch(value) {
      const loc = value.location;
      this.position = { lat: loc.y, lng: loc.x };
    },
    async getUserPosition() {
      if (navigator.geolocation) {
        // get GPS position
        navigator.geolocation.getCurrentPosition(pos => {
          // set the user location
          this.userLocation = {
            lat: pos.coords.latitude,
            lng: pos.coords.longitude
          };
        });
      }
    }
  }
};
</script>

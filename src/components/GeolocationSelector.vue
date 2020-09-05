<template
  ><div style="width: 100%;height: 100%">
    <l-map
      ref="map"
      @dblclick="onMapClick"
      :zoom="zoom"
      :center="[marker.position.lat, marker.position.lng]"
    >
      <l-tile-layer
        :url="tileProvider.url"
        :attribution="tileProvider.attribution"
      />
      <l-geosearch :options="geoSearchOptions"></l-geosearch>
      <l-marker
        visible
        draggable
        :lat-lng.sync="marker.position"
        @update:latLng="onPositionChange"
        @dragstart="dragging = true"
        @dragend="dragging = false"
      >
        <l-tooltip :content="tooltipContent" :options="{ permanent: true }" />
      </l-marker>
    </l-map>
  </div>
</template>
<script>
import { LMap, LMarker, LTileLayer, LTooltip } from "vue2-leaflet";
import { OpenStreetMapProvider } from "leaflet-geosearch";
import LGeosearch from "vue2-leaflet-geosearch";
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
      default: () => ({ lat: 8.9806, lng: 38.7578 })
    }
  },
  data() {
    return {
      geoSearchOptions: {
        provider: new OpenStreetMapProvider(),
        showMarker: false,
        autoClose: true,
        position: "topleft"
      },
      marker: {
        position: { ...this.defaultLocation }
      },
      address:null,
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
    console.log(this.marker)
  },
  computed: {
    tooltipContent() {
      return this.dragging
        ? `Place the marker on your location choice`
        : `<strong>${this.address ||
            "Loading..."}</strong> <hr/><strong>lat:</strong> ${
            this.marker.position.lat
          }<br/> <strong>lng:</strong> ${this.marker.position.lng}`;
    }
  },
  methods: {
    async onPositionChange(value) {
      this.address = await this.getAddress();
      this.$emit("input", value);
    },
    async getAddress() {
      const { lat, lng } = this.marker.position;
      const result = await fetch(
        `https://nominatim.openstreetmap.org/reverse?format=jsonv2&lat=${lat}&lon=${lng}`
      );
      const body = await result.json();
      console.log(body);
      if (result.status === 400) {
        return "Address could not be retrieved";
      }
      return body.name || body.display_name;
    },
    async onMapClick(value) {
      // place the marker on the clicked spot
      this.marker.position = value.latlng;
    },
    onSearch(value) {
      const loc = value.location;
      this.marker.position = { lat: loc.y, lng: loc.x };
    },
    async getUserPosition() {
      if (navigator.geolocation) {
        // get position
        navigator.geolocation.getCurrentPosition(async pos => {
          this.marker.position = {
            lat: pos.coords.latitude,
            lng: pos.coords.longitude
          };
        });
      }
    }
  }
};
</script>

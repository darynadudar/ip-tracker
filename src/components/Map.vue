<script>

import "leaflet/dist/leaflet.css";
import {LMap, LMarker, LTileLayer} from "@vue-leaflet/vue-leaflet";

export default {
    name: 'Map',
    components: {
        LMap,
        LTileLayer,
        LMarker,
    },
    props: {
        lat: {
            type: Number,
            default: 50.45466
        },
        lng: {
            type: Number,
            default: 30.5238
        },
        zoom: Number,
    },
}
</script>

<template>
    <div class="map-container">
        <div class="map" style="width: 100%; height: calc(100vh - 255px); position: relative; outline-style: none;">
            <l-map ref="map" :zoom="zoom" :center="[lat, lng]">
                <l-tile-layer
                    url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png"
                    layer-type="base"
                    name="OpenStreetMap"
                ></l-tile-layer>

                <l-marker :lat-lng="[this.lat, this.lng]" draggable></l-marker>
            </l-map>
        </div>
    </div>
</template>

<style lang="scss" scoped>

.map-container {
    position: relative;
    z-index: 1;
    width: 100%;
}

.leaflet-control-container {
    display: none;
}

</style>

<style lang="scss">

.leaflet-container {
    .leaflet-control-container {
        display: none;
    }

    .leaflet-marker-icon {
        content: url("@/assets/images/icon-location.svg");
        margin-left: -17px !important;
        width: unset !important;
    }
}

</style>

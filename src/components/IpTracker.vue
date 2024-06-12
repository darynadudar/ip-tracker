<script>
import axios from 'axios';
import Map from "@/components/Map.vue";

export default {
    name: 'IpTracker',
    components: {
        Map,
    },
    data() {
        return {
            pageTitle: 'IP Address Tracker',
            requestUrl: 'https://geo.ipify.org/api/v2/country,city',
            apiKey: 'at_OAuI07SYhjn3TtNJZXQhapw7pZoUW',
            searchQuery: '',
            searchData: {
                "ip": "176.37.199.165",
                "location": {
                    "country": "UA",
                    "region": "Misto Kyiv",
                    "city": "Kyiv",
                    "lat": 50.45466,
                    "lng": 30.5238,
                    "postalCode": "",
                    "timezone": "+03:00",
                    "geonameId": 703448
                },
                "as": {
                    "asn": 39608,
                    "name": "LANETUA-AS",
                    "route": "176.37.128.0/17",
                    "domain": "lanet.ua",
                    "type": "Cable/DSL/ISP"
                },
                "isp": "Lanet Network"
            },
            zoom: 3,
            isButtonDisabled: true,
        }
    },
    watch: {
        searchQuery(newQuery) {
            this.isButtonDisabled = !newQuery;
        }
    },
    methods: {
        sendRequest() {
            if (!this.searchQuery) return;

            this.isButtonDisabled = true;

            const self = this;

            axios.get(this.requestUrl + `?apiKey=${this.apiKey}&ipAddress=${this.searchQuery}`)
                .then(function (response) {
                    self.searchData = response.data;
                    self.zoom = 7;
                })
                .catch(function (error) {
                    console.log(error);
                })
                .finally(function () {
                    self.isButtonDisabled = false;
                });
        },
    }
}
</script>

<template>
    <main>
        <div class="top">
            <h1>{{ pageTitle }}</h1>

            <form class="search-form">
                <input v-model="searchQuery"
                       name="searchQuery"
                       id="searchQuery"
                       placeholder="Search for any IP address or domain">
                <button type="button" @click="sendRequest()" :disabled="isButtonDisabled">
                    <svg xmlns="http://www.w3.org/2000/svg" width="11" height="14"><path fill="none" stroke="#FFF" stroke-width="3" d="M2 1l6 6-6 6"/></svg>
                </button>
            </form>

            <div class="search-data-wrapper">
                <div class="search-data">
                    <div class="search-data__item">
                        <div class="search-data__item-title">Ip address</div>
                        <div>{{ searchData.ip }}</div>
                    </div>
                    <div class="search-data__item">
                        <div class="search-data__item-title">Location</div>
                        <div>{{ searchData.location.country + ', ' + searchData.location.region }}</div>
                    </div>
                    <div class="search-data__item">
                        <div class="search-data__item-title">Timezone</div>
                        <div>{{ 'UTC ' + searchData.location.timezone }}</div>
                    </div>
                    <div class="search-data__item">
                        <div class="search-data__item-title">ISP</div>
                        <div>{{ searchData.isp }}</div>
                    </div>
                </div>
            </div>
        </div>

        <Map :lat="searchData.location.lat" :lng="searchData.location.lng" :zoom="zoom"/>
    </main>
</template>

<style lang="scss">

main {
    display: flex;
    align-items: center;
    flex-direction: column;
}

.top {
    position: relative;
    z-index: 2;

    display: flex;
    flex-direction: column;
    width: 100%;
    align-items: center;

    background-image: url("@/assets/images/pattern-bg.png");
    background-repeat: no-repeat;
    background-size: cover;
}

.search-data-wrapper {
    position: relative;
    margin-bottom: 65px;
}

h1 {
    color: #FFF;
}

.search-form {
    display: flex;
    align-items: center;
    position: relative;
    margin-bottom: 48px;

    input,
    button {
        height: 58px;
        padding: 0 24px;
    }

    input {
        min-width: 400px;
        border-radius: 15px 0 0 15px;
        border: none;
        outline: none;

        &::placeholder {
            opacity: 0.5;
        }
    }

    button {
        display: inline-flex;
        align-items: center;
        background-color: #000;
        border: none;
        border-radius:  0 15px 15px 0;
        transition: background-color 300ms linear;

        &:not([disabled]) {
            &:hover {
                background-color: #3F3F3F;
                cursor: pointer;
            }
        }
    }
}

.search-data {
    position: absolute;
    top: 0;
    left: 50%;
    transform: translateX(-50%);

    display: flex;
    padding: 37px 32px;
    max-width: 1110px;
    background: #FFF;
    box-shadow: 0 50px 50px -25px rgba(0, 0, 0, 0.10);
    border-radius: 15px;

    &__item {
        position: relative;
        padding: 0 32px;
        width: 100%;
        min-width: 200px;
        font-size: 26px;
        line-height: 30px;
        font-weight: 500;

        &:not(:last-child) {

        }

        &:after {
            position: absolute;
            top: 0;
            right: 0;
            content: "";
            height: 100%;
            width: 1px;
            color: #000;
            opacity: 0.15;
        }
    }

    &__item-title {
        margin-bottom: 12px;
        opacity: 0.5;
        font-size: 12px;
        line-height: initial;
        font-weight: 700;
        text-transform: uppercase;
        letter-spacing: 1.75px;
    }
}

.map-container {
    position: relative;
    z-index: -1;
    width: 100%;
}

.leaflet-control-container {
    display: none;
}
</style>

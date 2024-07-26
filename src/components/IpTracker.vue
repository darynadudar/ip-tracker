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
                "ip": "",
                "location": {},
                "isp": ""
            },
            zoom: 3,
            isButtonDisabled: true,
        }
    },
    beforeMount() {
        this.sendRequest();
    },
    watch: {
        searchQuery(newQuery) {
            this.isButtonDisabled = !newQuery;
        }
    },
    methods: {
        sendRequest() {
            this.isButtonDisabled = true;

            const self = this;

            axios.get(this.requestUrl + `?apiKey=${this.apiKey}${this.searchQuery.length ? '&ipAddress=' + this.searchQuery : ''}`)
                .then(function (response) {
                    self.searchData = response.data;
                    self.zoom = 7;
                })
                .catch(function (error) {
                    alert("Sorry, we can't find this IP :( \nPlease check the data");
                    console.log(error);
                })
                .finally(function () {
                    self.isButtonDisabled = false;
                });
        }
    }
}
</script>

<template>
    <div>
        <div class="top">
            <h1>{{ pageTitle }}</h1>

            <form class="search-form">
                <input v-model="searchQuery"
                       name="searchQuery"
                       id="searchQuery"
                       placeholder="Search for any IP address or domain">
                <button type="button" @click="sendRequest()" :disabled="isButtonDisabled">
                    <svg xmlns="http://www.w3.org/2000/svg" width="11" height="14">
                        <path fill="none" stroke="#FFF" stroke-width="3" d="M2 1l6 6-6 6"/>
                    </svg>
                </button>
            </form>

            <div class="search-data-wrapper">
                <div class="search-data">
                    <dl class="search-data__item">
                        <dt class="search-data__item-title">Ip address</dt>
                        <dd>{{ searchData.ip }}</dd>
                    </dl>
                    <dl class="search-data__item">
                        <dt class="search-data__item-title">Location</dt>
                        <dd>{{ searchData.location.country + ', ' + searchData.location.region }}</dd>
                    </dl>
                    <dl class="search-data__item">
                        <dt class="search-data__item-title">Timezone</dt>
                        <dd>{{ 'UTC ' + searchData.location.timezone }}</dd>
                    </dl>
                    <dl class="search-data__item">
                        <dt class="search-data__item-title">ISP</dt>
                        <dd>{{ searchData.isp }}</dd>
                    </dl>
                </div>
            </div>
        </div>

        <Map :lat="searchData.location.lat" :lng="searchData.location.lng" :zoom="zoom"/>
    </div>
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
        border-radius: 0 15px 15px 0;
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
    flex-direction: column;
    gap: 16px;
    padding: 20px;
    max-width: 1110px;
    background: #FFF;
    box-shadow: 0 50px 50px -25px rgba(0, 0, 0, 0.10);
    border-radius: 15px;

    &__item {
        position: relative;
        margin: 0 32px 0 0;
        padding-right: 32px;
        width: 100%;
        min-width: 200px;
        font-size: 18px;
        line-height: 30px;
        font-weight: 500;
    }

    &__item-title {
        margin-bottom: 8px;
        opacity: 0.5;
        font-size: 10px;
        line-height: initial;
        font-weight: 700;
        text-transform: uppercase;
        letter-spacing: 1.75px;
    }

    dd {
        margin: 0;
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

@media (min-width: 768px) {
    .search-form {
        input {
            min-width: 400px;
        }
    }

    .search-data {
        font-size: 26px;
        line-height: 30px;

        &__item-title {
            margin-bottom: 12px;
            font-size: 12px;
        }
    }
}

@media (min-width: 1024px) {
    .search-data {
        padding: 37px 32px;
        flex-direction: row;
        gap: 0;

        &__item {
            &:after {
                position: absolute;
                top: 0;
                right: 0;
                content: "";
                height: 100%;
                width: 1px;
                background-color: #000;
                opacity: 0.15;
            }

            &:last-child {
                margin: 0;
                padding: 0;

                &:after {
                    content: none;
                }
            }
        }
    }
}

</style>

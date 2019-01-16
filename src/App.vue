<template>
    <div id="app">
        <section class="md-layout md-alignment-center md-gutter">

            <div class="md-layout-item md-size-25 ">
            <md-field class="">
                <label for="searchByName">Search By cat's name</label>
                <md-input v-model="searchByName" id="searchByName" placeholder="Ex: Maine Coon" class="search-box"></md-input>
            </md-field>
            </div>
            <div class="md-layout-item md-size-25 md-gutter">
                <md-field class="">
                    <md-menu md-size="medium">
                        <md-button md-menu-trigger>Search By Country</md-button>
                        <md-menu-content >
                            <md-menu-item value="" @click="handlerFlagClick('')">All</md-menu-item>
                            <md-menu-item :value="country"
                                          :key="index"
                                          v-for="(country, index) in countries"
                                          @click="handlerFlagClick(country)"
                                          >
                                <span :class="`flag-${country.toLowerCase()}`" class="md-icon flag normal-flag"></span>
                                <span class="md-list-item-text">{{ country }}</span>
                            </md-menu-item>
                        </md-menu-content>
                    </md-menu>
                </md-field>
            </div>
        </section>
        <div class="md-layout md-alignment-center">
            <Cat v-for="(cat, index) in filterCats" class="md-layout-item md-small-size-50 md-medium-size-33 md-large-size-20"
                 :key="index"
                 :cat="cat"
                 :onCatClick="handlerCatClick(cat.id)"
            />
        </div>
        <Modal v-if="isModalVisible" :cat="cats[clickedCatIndex]" @close="closeModal"/>
    </div>
</template>

<script>
    import Cat from './components/Cat.vue'
    import Modal from './components/Modal.vue';

    import axios from 'axios';

    import Vue from 'vue';
    import VueMaterial from 'vue-material';

    import 'vue-material/dist/vue-material.min.css';

    Vue.use(VueMaterial);

    export default {
        name: 'app',
        components: {
            Cat, Modal,
        },
        data() {
            return {
                searchByName: '',
                searchByFlag: '',
                cats: [],
                countries: [],
                clickedCatIndex: 0,
                isModalVisible: false,
            }
        },
        mounted() {
            this.loadList();
        },
        computed: {
            filterCats: function () {
                return this.cats
                    .filter(this.byName)
                    .filter(this.byFlag)
            },
        },
        methods: {
            byName(cat) {
                return cat.name.match(new RegExp(this.searchByName, "i"));
            },
            byFlag(cat) {
                return cat.country_code.match(this.searchByFlag);
            },
            handlerFlagClick(flag) {
                this.searchByFlag = flag;

            },
            handlerCatClick(catId) {
                return () => {
                    this.clickedCatIndex = this.cats.findIndex(cat => cat.id === catId);
                    this.showModal();
                }
            },
            showModal() {
                this.isModalVisible = true;
            },
            closeModal() {
                this.isModalVisible = false;
            },
            async loadList() {
                try {
                    const config = {
                        headers: {'x-api-key': 'ea49e96d-cde8-4501-bf3a-cb71a4cbf868'}
                    };
                    let response = await axios.get('https://api.thecatapi.com/v1/breeds', config);

                    // the response is an Array, so just use the first item as the Image
                    const cats = response.data;
                    const countries = Array.from(new Set(response.data.map(country => {
                        // Fix issue with bad country SP
                        return country.country_code === "SP" ? country.country_code = "SG" : country.country_code
                    } )));
                    this.countries = countries;
                    Promise.all(cats.map(cat => {
                        return this.loadInformations(cat);
                    })).then(catsInformation => {
                        catsInformation.forEach((catInfo = {}, index) => {
                            const extra = {desc: "", url: "", tid:"", ...catInfo};
                            cats[index].extra = extra;
                        });
                        this.cats = cats;
                    });
                } catch (err) {
                    // eslint-disable-next-line
                    console.error(err);
                }
            },
            async loadInformations(cat) {
                try {
                    let urlSplitted = cat.wikipedia_url !== null ? cat.wikipedia_url.split('/') : [];
                    let title = urlSplitted[urlSplitted.length - 1];

                    let responseMedia = await axios.get(`https://en.wikipedia.org/api/rest_v1/page/media/${title}`);
                    let extra = {};

                    if (responseMedia.data.items[0]) {
                        extra.url = responseMedia.data.items[0].thumbnail !== undefined ? responseMedia.data.items[0].thumbnail.source : '';
                        extra.desc = responseMedia.data.items[0].description !== undefined ? responseMedia.data.items[0].description.text : '';
                    } else {
                        extra.url = 'default.png';

                    }
                    return extra;
                } catch (err) {
                    // eslint-disable-next-line
                    console.error("Error loadInformations()", err);
                }
            }
        }
    };
</script>

<style lang="scss">
    .md-subhead.ellipsis {
        min-height: 2.5rem;
    }
</style>

<style lang="scss" scoped>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
    }
    .section-search {
        display: inline-flex;
    }

    .flag {
        margin-left: 1rem;
        margin-right: 12rem;
        text-indent: 4rem;
    }

    @import "~vue-material/dist/theme/engine"; // Import the theme engine

    @include md-register-theme("default", (
            primary: md-get-palette-color(blue, A200), // The primary color of your application
            accent: md-get-palette-color(red, A200) // The accent or secondary color
    ));

    @import "~vue-material/dist/theme/all"; // Apply the theme
    .md-layout-item {
        max-height: 550px;

        &:after {
            width: 100%;
            height: 100%;
            display: block;
            background: primary;
            content: " ";
        }
    }

    input {
        outline: none;
    }

    input[type=search] {
        -webkit-appearance: textfield;
        -webkit-box-sizing: content-box;
        font-family: inherit;
        font-size: 100%;
    }

    input::-webkit-search-decoration,
    input::-webkit-search-cancel-button {
        display: none;
    }

    input[type=search] {
        background: #ededed url(https://static.tumblr.com/ftv85bp/MIXmud4tx/search-icon.png) no-repeat 9px center;
        border: solid 1px #ccc;
        padding: 9px 10px 9px 32px;
        width: 65px;

        height: 20px;
        margin: 10px 20px 0 0;

        -webkit-border-radius: 10em;
        -moz-border-radius: 10em;
        border-radius: 10em;

        -webkit-transition: all .5s;
        -moz-transition: all .5s;
        transition: all .5s;
    }

    input[type=search]:focus {
        width: 130px;
        background-color: #fff;
        border-color: primary;

        -webkit-box-shadow: 0 0 5px rgba(109, 207, 246, .5);
        -moz-box-shadow: 0 0 5px rgba(109, 207, 246, .5);
        box-shadow: 0 0 5px rgba(109, 207, 246, .5);
    }

    input:-moz-placeholder {
        color: #999;
    }

    input::-webkit-input-placeholder {
        color: #999;
    }

</style>

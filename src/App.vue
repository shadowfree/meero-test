<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Cats msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Cats from './components/Cats.vue'
import axios from 'axios';

export default {
    name: 'app',
    components: {
        Cats
    },
    data() {
        return {
            cats: []
        }
    },
    mounted() {
        this.loadList();
    },
    methods: {
        async loadList() {
            try {
                axios.defaults.headers.common['x-api-key'] = "ea49e96d-cde8-4501-bf3a-cb71a4cbf868";
                let response = await axios.get('https://api.thecatapi.com/v1/breeds');

                this.image = response.data[0]; // the response is an Array, so just use the first item as the Image
                /* eslint no-console: "log" */
                console.log("-- Image from TheCatAPI.com");
                console.log("id:", this.image.id);
                console.log("url:", this.image.url);
            } catch (err) {
                console.log(err);
            }
        }
    }
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

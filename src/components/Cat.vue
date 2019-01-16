<template>
    <md-card>
        <md-card-media-cover md-solid>
            <md-card-media md-ratio="4:3">
                <img :src="cat.extra.url" alt="cat.name"/>

            </md-card-media>

            <md-card-area>
                <md-card-header>
                    <span class="md-title">{{ cat.name }}</span>
                    <span class="md-subhead ellipsis">{{ cat.extra.desc }}</span>
                </md-card-header>

                <md-card-actions>
                    <md-button @click="onCatClick">Show Popup</md-button>
                </md-card-actions>
            </md-card-area>
        </md-card-media-cover>
    </md-card>


</template>

<script>

    export default {
        components: {},
        name: 'Cat',
        props: ['cat', 'onCatClick']
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
    .md-card {
        width: 240px;
        margin: 5px;
        display: inline-block;
        vertical-align: top;
    }

    .md-card-area {
        height: 65%;
    }

    .ellipsis {
        overflow: hidden;
        display: -webkit-box;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;

    }
</style>

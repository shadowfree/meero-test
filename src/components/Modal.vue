<template>
    <div class="modal-backdrop">
        <div class="modal">
            <header class="modal-header">
                <div>{{cat.name}}</div>
                <button
                        type="button"
                        class="btn-close"
                        @click="close"
                >x
                </button>
            </header>
            <section class="modal-body">
                <md-card-media md-medium>
                    <img :src="cat.extra.url"/>
                </md-card-media>
                <p>{{ cat.extra.desc }}</p>
            </section>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'modal',
        props: ['cat'],
        methods: {
            close() {
                this.$emit('close');
            },
        },
    };
</script>

<style>
    .modal-backdrop {
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        z-index: 10;
        background-color: rgba(0, 0, 0, 0.3);
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .modal {
        background: #FFFFFF;
        box-shadow: 2px 2px 20px 1px;
        overflow-x: auto;
        display: flex;
        flex-direction: column;

    }

    .modal-header {
        display: flex;
        border-bottom: 1px solid #eeeeee;
        color: #000000;
        justify-content: space-between;
    }

    .modal-header div {
        padding-top: 20px;
        padding-left: 20px;
        font-size: 22px;
    }

    .modal-body {
        position: relative;
        padding: 20px 10px;
    }

    .btn-close {
        border: none;
        font-size: 20px;
        padding: 20px;
        cursor: pointer;
        font-weight: bold;
        color: #000000;
        background: transparent;
    }
    .modal-body {
        max-width: 20rem;
        margin: 1rem;
        display: inline-block;
        vertical-align: top;
    }
</style>

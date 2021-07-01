<template>
    <div id="div-card">
        <!--<img src="../assets/img-alimentos.jpeg" width="100%">-->
        <img :src="getImgUrl(imagen)" v-bind:alt="imagen" width="100%">        
        <h3 class="nombre-producto"> {{ nombreProducto }} </h3>
    </div>
</template>

<script>
export default {
    name: 'Card',
    props: ['nombre', 'imagen'],
    data: function() {
        return {
            nombreProducto: this.nombre
        }
    },
    methods: {
        getImgUrl(pic) {
            return require('../assets/'+ this.imagen)
        },         
    },

}
</script>


<style scoped>
.nombre-producto {
    text-align: center;
    margin-top: 0em;
}
</style>
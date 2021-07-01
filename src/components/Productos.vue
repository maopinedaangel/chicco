<template>
    <div id="div-productos">
        <h1 class="subtitulo">Categorías</h1>
        <div class="lista-categorias">
            <Card v-for="c in listaCategorias" :key="c.id" :nombre="c.nombre" :imagen="c.imagen" />
        </div>
        <h1 class="subtitulo">Productos</h1>        
        <div class="lista-items">
            <Card v-for="p in listaProductos" :key="p.id" :nombre="p.nombre" :imagen="p.imagen"/>  
        </div>
    </div>
</template>


<script>
import axios from 'axios';
import Card from '../components/Card'
export default {
    name: 'Productos',
    components: {
        Card
    },
    data: function() {
        return {
            listaCategorias: [],
            listaProductos: []
        }
    },
    methods: {
      
    },
    beforeCreate: function() {
        axios.
        get("http://localhost:8000/categorias")
        .then(respuesta => {
            this.listaCategorias = respuesta.data
        })
        .catch(error => {
            alert("No se encontraron datos.")
        })
        axios.
        get("http://localhost:8000/productos")
        .then(respuesta => {
            this.listaProductos = respuesta.data
        })
        .catch(error => {
            alert("No se encontraron datos.")
        })        
    }
}
</script>


<style scoped>
.subtitulo {
    text-align: center;
}
.lista-categorias {
    margin-top: 2%;
    margin-bottom: 2%;
    width: 90%;
    margin-left: auto;
    margin-right: auto;    
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    column-gap: 3%;
    row-gap: 2em;

}
.lista-items {
    margin-top: 2%;
    margin-bottom: 2%;    
    width: 85%;
    margin-left: auto;
    margin-right: auto;    
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    column-gap: 3%;
    row-gap: 2em;

}
</style>
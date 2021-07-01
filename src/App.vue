<template>
  <div id="app">
    <h1 class="titulo">tabaquería</h1>
    <header>
      <MenuBar />

    </header>
    <main>     
      <div id="div-main"> 
        <Slide />            
        <Productos /> 
      </div>  
      <Pedido />                   
    </main>
    <footer>
    </footer>
  </div>
</template>

<script>
import Slide from './components/Slide'
import MenuBar from './components/MenuBar'
import Productos from './components/Productos'
import Pedido from './components/Pedido'

export default {
  name: 'App',
  components: {
    Slide,
    MenuBar,
    Productos,
    Pedido
  }

}
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
header {
  margin-top: 0%;
  width: 100%;
  position: fixed;
  top: 0;
  z-index: 2;
}
MenuBar {
  margin-top: 0%;
}
main {
  position: relative;
  margin-top: 6%;
}

.titulo {
  /*font-family: Comic Sans, "Helvetica Neue", Helvetica, sans-serif;*/
  font-size: 60px;
  font-family: cursive,"Helvetica Neue", Helvetica, sans-serif;
  font-style: italic;
  margin-top: 1.5em;
  margin-bottom: 0.6em;
}
</style>

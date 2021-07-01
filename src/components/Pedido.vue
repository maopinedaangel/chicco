<template>
    <div id="div-pedido">
        <h3 class="titulo-pedido">Nuevo Pedido</h3>
        <label id="nombre-usuario">Usuario: {{ usuario.nombre }} {{ usuario.apellido}}</label><br><br>              
        <form v-on:submit.prevent="consultarCliente(cedula)" id="form-cliente">
            <label id="label-cliente" for="input-cliente">Cédula cliente:</label>
            <input type="text" id="input-cliente" v-model="cedula" name="input-cliente"><br><br>
            <button type="submit" id="boton-enviarcliente">Enviar</button>            
        </form><br>
        <label v-if="clienteActivo" id="nombre-cliente">Cliente: {{ miCliente.nombre }} {{ miCliente.apellido}}</label>
        <br><br><br>

        <form v-on:submit.prevent="agregarProducto(idProducto)" id="form-producto">
            <label id="label-producto" for="input-producto">Agregar producto:</label>
            <!--<input list="productos" id="input-productos" name="input-productos">-->
            <select id="productos" v-model="idProducto">
                <option v-for="p in bdProductos" v-bind:key="p.id" :value="p.id">{{ p.nombre }}</option>
            </select><br><br>
            <label id="label-cant" for="input-producto">Cantidad:</label>            
            <input type="number" id="input-cant" v-model="cantProducto" name="input-cant"><br><br>
            <label id="label-comentario" for="input-comentario">Observaciones:</label>            
            <input type="text" id="input-comentario" v-model="comentario" name="input-comentario"><br><br>            
            <button type="submit" id="boton-enviarproducto">Enviar</button>              
        </form> 
        <!--<label>El producto seleccionado es el número {{ idProducto }}.</label>-->  
        <br><br>      

        <h3 v-if="clienteActivo" class="titulo-pedido">Lista de Productos</h3>
        <table v-if="clienteActivo" class="tabla-productos">
            <thead>
                <tr>
                    <th>Código</th>
                    <th>Producto</th>    
                    <th>Cantidad</th>   
                    <th>Valor</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="item in listaPedido" v-bind:key="item.index">
                    <td>{{ item.id_producto }}</td>
                    <td>{{ item.nombre }}</td>
                    <td>{{ item.cantidad }}</td>
                    <td>{{ item.valor }}</td>
                </tr>
                <tr>
                    <td>TOTAL</td>
                    <td></td>
                    <td></td>
                    <td>{{ pedido.valorPedido }}</td>   
                </tr>
            </tbody>
        </table>
        <br><br>

        <form v-on:submit.prevent="enviarPedido" id="form-pedido">
            <label id="label-formapago" for="input-formapago">Forma de pago: </label>
            <select id="formaspago" v-model="pedido.formaPago">
            <!--<select id="formaspago" v-model="miVenta.pedido_in.forma_pago"> -->               
                <option value="Efectivo">Efectivo</option>  
                <option value="debito">Tarjeta débito</option>                                
                <option value="credito">Tarjeta de crédito</option> 
            </select><br><br>
            <label id="label-valorpagado" for="input-valorpagado">Valor pagado: </label>
            <input id="input-valorpagado" type="number" v-model="pedido.valorPagado" name="input-valorpagado"><br><br>             
            <!--<input id="input-valorpagado" type="number" v-model="miVenta.pedido_in.valor_pagado" name="input-valorpagado">  -->      
            <label id="label-cambio" name="label-cambio">Cambio: {{ this.cambio }}</label><br><br>   
            <button type="submit" id="boton-enviarpedido">Enviar</button>            
        </form>
        <br><br>
        <textarea rows="20" cols="40" v-model="factura"></textarea>
        <br><br>
        <button type="submit" v-on:click="guardarCompra" id="boton-guardarCompra">Guardar</button> 
        <div id="contacto">
            <div class="div-link">
                <img class="logo" src="../assets/wa.png">
                <a id="link-wa" class="link-img" target="_blank" :href="linkWa">Enviar por Whatsapp</a>  
            </div>
            <div class="div-link">
                <img class="logo" src="../assets/mail.png">
                <a id="link-mail" class="link-img" :href="email">Enviar por correo electrónico</a>  
            </div> 
        </div>
                       
    </div>
</template>


<script>
import axios from 'axios'
export default {
    name: 'Pedido',

    data: function() {
        return {
            bdProductos: [],            
            cedula: "",
            usuario: {
                cedula: 71264076,
                nombre: "Mauricio",
                apellido: "Pineda Angel"
            },
            pedido: {
                valorPedido: 0,
                formaPago: "",
                valorPagado: "",
                idPedido: ""
            },
            miCliente: "",
            clienteActivo: false,            
            listaPedido: [],
            cantProducto: 1,
            idProducto: "",
            comentario: "",
            //valorPedido: 0,
            miVenta: {
                pedido_in: {
                    cc_cliente: "",
                    valor: 0,
                    forma_pago: "",
                    valor_pagado: "",
                    estado: "",
                    turno: "",
                    cc_usuario: ""
                },
                compras: []
            },
            factura: "",
            linkWa: "",
            email: "",
        }
    },

    computed: {
        cambio: function() {
            return this.pedido.valorPagado - this.pedido.valorPedido
        }
    },

    methods: {

        consultarCliente: function(cc) {
            var numeroCedula = parseInt(cc)
            axios.
            get("http://localhost:8000/cliente", {params: { cc: numeroCedula}})
            .then(result => {
                this.miCliente = result.data
                this.clienteActivo = true
            })
            .catch(error => {
                alert(error.response.data.detail)
            })
        },

        agregarProducto: function(idProducto) {
            //alert(idProducto)
            var idItem = idProducto
            axios.
            get("http://localhost:8000/producto", {params: { prod: idItem}})
            .then(result => {
                this.miProducto = result.data
                var nombreItem = result.data.nombre
                //alert(nombreItem)
                while (nombreItem.length > 20) {
                    nombreItem = nombreItem.slice(0, -1)
                }
                //alert(nombreItem)
                var cantidadItem = this.cantProducto
                var comentarioItem = this.comentario
                var valorItem = cantidadItem*result.data.precio
                var item = { id_producto: idItem, nombre: nombreItem, cantidad: cantidadItem, valor: valorItem, comentario: comentarioItem}
                this.listaPedido.push(item)
                this.pedido.valorPedido += valorItem
                this.cantProducto = 1
                this.comentario = ""
            })
            .catch(error => {
                alert(error.response.data.detail)
            })            

        },

        enviarPedido: function() {

            this.miVenta.pedido_in.cc_cliente = this.miCliente.cedula 
            this.miVenta.pedido_in.valor = this.pedido.valorPedido 
            this.miVenta.pedido_in.forma_pago = this.pedido.formaPago
            this.miVenta.pedido_in.valor_pagado = this.pedido.valorPagado                                        
            this.miVenta.pedido_in.estado = "Pendiente"
            this.miVenta.pedido_in.turno = 1
            this.miVenta.pedido_in.cc_usuario = this.usuario.cedula
            //alert(this.listaPedido.length)
            this.miVenta.compras = this.listaPedido.slice()

            axios.
            post("http://localhost:8000/pedido/nuevo", this.miVenta)
            .then(respuesta => {
                //alert(respuesta.data.mensaje);
                this.pedido.idPedido = respuesta.data.id
                //alert("id Pedido: " + this.pedido.idPedido)
                this.factura = respuesta.data.factura              
            })
            .catch(error => {
                alert(error.response.data.detail)
            }); 

        },

        guardarCompra: function() {

            for (var i in this.listaPedido) {
                var item = this.listaPedido[i]
                //alert("item:" + item)
                //alert("id pedido: " + this.pedido.idPedido)
                var nuevaCompra = { id_pedido: this.pedido.idPedido, id_producto: item.id_producto, cantidad: item.cantidad, comentario: item.comentario }
                axios.
                post("http://localhost:8000/compra/nueva", nuevaCompra)
                .then(respuesta => {
                    //alert(respuesta.data.mensaje);
                    alert("Se preparó la compra")
                    this.prepararMensaje();               
                })
                .catch(error => {
                    alert(error.response.data.detail)
                });                   
            } 
        },


        prepararMensaje: function() {
            alert("Factura preparada, cliente " + this.miCliente.telefono)
            /*var linkWa = "https://wa.me/573017453410/?text=" + this.factura  */  
            this.linkWa = "https://wa.me/" + "57" + this.miCliente.telefono.toString() + "/?text=" + this.factura            
            /*document.getElementById("link-wa").setAttribute("href", linkWa)*/
            var asunto = "Tu compra en Tabaqueria"
            var cuerpo = this.factura
            this.email = "ingmauriciopineda@gmail.com"
            var correo = "mailto:" + this.email + "?subject=" + asunto + "&body=" + cuerpo            
            /*var correo = "mailto:ingmauriciopineda@gmail.com?" + "subject=" + asunto + "&body=" + cuerpo*/
            /*document.getElementById("link-mail").setAttribute("href", correo)*/
        }
    },

    beforeCreate: function() {
        axios.
        get("http://localhost:8000/productos")
        .then(result => {
            this.bdProductos = result.data
        })
        .catch(error => {
            //alert(error.response.data.detail)
        }) 
        //alert(this.usuario.nombre + " " + this.usuario.apellido)       
    },
   
    
}
</script>


<style scoped>

#div-pedido {
    width: 30%;
    padding: 2%;
    margin-left: auto;
    margin-right: auto;
    border-color:black;
    border-width: 1px;
    border-style: solid;
}

.tabla-productos {
  width: 90%;
  margin-left: auto;
  margin-right: auto;
  border-collapse: collapse;
}

.tabla-productos th,
.tabla-productos td {
  margin:auto;
  padding: 1em;
}

.tabla-productos th {
  color: #fff;
  font-weight: 300;
  background-color: #78909C;
}

.tabla-productos tr:nth-child(odd) td {
  background-color: #fff;
}

.tabla-productos tr:nth-child(even) td {
  background-color: #eceff1;
}

.tabla-productos td:nth-child(4) {
  text-align: right;
}

.tabla-productos td:nth-child(2) {
  text-align:left;
}

.tabla-productos td:nth-child(1),
.tabla-productos td:nth-child(3) {
  text-align: center;
}

.tabla-productos td:nth-child(2) {
  min-width: 35%;
}

.logo {
    width: 50px;
    height: 50px;
    margin: 1rem;
}
/*
#contacto {
    display: flex;
    justify-content: center;
    align-items: center;
    padding:1rem;
}
*/
</style>
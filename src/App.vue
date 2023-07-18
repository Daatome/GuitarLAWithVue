<script setup>

import {ref, reactive, onMounted, watch} from 'vue'
import {db} from './data/guitarras'
import Guitarra from './components/Guitarra.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'


//with ref
const guitarras= ref([])
const carrito=ref([])
const mainGuitarra= ref({})

/*
const state= reactive({
    guitarras:[]
})
*/
//this method is excecuted when the dom is ready 
onMounted(()=>{
    //for reactive (objects oriented)
    //state.guitarras=db,
    guitarras.value= db
    mainGuitarra.value=db[3]
    const localCarrito= localStorage.getItem('carrito')
    if(localCarrito){
        carrito.value= JSON.parse(localCarrito)
    }
   

})

watch(carrito,()=>{
    guardarCarrito()
},{
    deep:true
})

const agregarCarrito=( guitarra)=>{
    
    if(carrito.value.includes(guitarra,0)){
        const index= carrito.value.indexOf(guitarra,0)
        carrito.value[index].cantidad++
    }else{
        guitarra.cantidad=1
        carrito.value.push(guitarra)
    }
    Swal.fire({
    title: 'Agregado!',
    text: `${guitarra.nombre} agregada`,
    icon: 'success',
    confirmButtonText: 'Cool!'
    })

}

const aumentarCantidad= (guitarra)=>{
    const index= carrito.value.indexOf(guitarra,0)
    carrito.value[index].cantidad++

}
const disminuirCantidad= (guitarra)=>{
    const index= carrito.value.indexOf(guitarra,0)
    if(carrito.value[index].cantidad===1){
        eliminarProducto(guitarra)
    }else{
        carrito.value[index].cantidad--
    }

}
const eliminarProducto= (guitarra)=>{
    const index= carrito.value.indexOf(guitarra,0)
    carrito.value.splice(index,1)
    Swal.fire({
    title: 'Eliminado!',
    text: `${guitarra.nombre} eliminada`,
    icon: 'warning',
    confirmButtonText: 'Cool!'
    })

}

const vaciarCarrito= ()=>{
    carrito.value=[]
    Swal.fire({
    title: 'Eliminado!',
    text: 'Carrito Vacio',
    icon: 'info',
    confirmButtonText: 'Oh no mis guitarras!'
    })

}

const guardarCarrito= ()=>{
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}







</script>

<template>


    <Header
    v-bind:carrito="carrito"
    v-bind:mainGuitarra="mainGuitarra"
    @aumentar-cantidad="aumentarCantidad"
    @disminuir-cantidad="disminuirCantidad"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
    @agregar-carrito="agregarCarrito"
    />


  

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra 
            v-for="guitarra in guitarras"
            :key="guitarra.id"
            v-bind:guitarra="guitarra"
            @agregar-carrito="agregarCarrito"/>

        </div>
    </main>

    <Footer/>

</template>


<script setup>
import { ref, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarra from "./components/Guitarra.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";

// con REF
const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

// watch sirve como un useEffect que escucha cambios
// que es lo que revisarás en cada rato
watch(
  carrito,
  () => {
    guardarLocalStorage();
  },
  // lo va revisar de una forma más estricta
  {
    deep: true,
  }
);

onMounted(() => {
  guitarras.value = db;
  guitarra.value = db[3];
  const carritoStorage = localStorage.getItem("carrito");
  if (carritoStorage) {
    carrito.value = JSON.parse(carritoStorage);
  }
});

const guardarLocalStorage = () => {
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const agregarCarrito = (guitarra) => {
  const index = carrito.value.findIndex((prod) => prod.id === guitarra.id);
  if (index >= 0) {
    carrito.value[index].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
};

const decrementarCantidad = (guitarra) => {
  const index = carrito.value.findIndex((prod) => prod.id === guitarra.id);
  if (index >= 0) {
    if (carrito.value[index].cantidad === 1) {
      carrito.value.splice(index, 1);
    } else {
      carrito.value[index].cantidad--;
    }
  }
};

const incrementarCantidad = (guitarra) => {
  const index = carrito.value.findIndex((prod) => prod.id === guitarra.id);
  if (index >= 0) {
    carrito.value[index].cantidad++;
  }
};

const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter((prod) => prod.id !== id);
  guardarLocalStorage();
};

const limpiarCarrito = () => {
  carrito.value = [];
};
</script>

<template>
  <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @decrementar-cantidad="decrementarCantidad"
    @incrementar-cantidad="incrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-producto="eliminarProducto"
    @limpiar-carrito="limpiarCarrito"
  />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra
        v-for="guitarra in guitarras"
        :key="guitarra.id"
        :guitarra="guitarra"
        @agregar-carrito="agregarCarrito"
      />
      <!-- FIN GUITARRA -->
    </div>
  </main>
  <Footer />
</template>

<style scoped>
h1 {
  text-transform: uppercase;
  color: red;
}
</style>

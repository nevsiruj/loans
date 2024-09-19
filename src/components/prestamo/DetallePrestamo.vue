<template>
  <div class="p-6 max-w-full mx-auto">
    <h1 class="text-2xl font-bold mb-4">Detalles del Préstamo</h1>

    <div class="mb-4">
      <p><strong>Cliente:</strong> {{ prestamo.clienteNombre }}</p>
      <p><strong>Fecha de Inicio:</strong> {{ prestamo.fechaInicio }}</p>
      <p><strong>Monto:</strong> {{ prestamo.monto }}</p>
      <p><strong>Plazo:</strong> {{ prestamo.plazo }} meses</p>
      <p><strong>Interés:</strong> {{ prestamo.interes }}%</p>
      <p><strong>Garantía:</strong> {{ prestamo.garantia }}</p>
    </div>

    <h2 class="text-xl font-bold mb-2">Pagos Realizados</h2>
    <ul>
      <li v-for="pago in pagos" :key="pago.id" class="mb-2">
        Fecha: {{ pago.fecha }} - Monto: {{ pago.monto }}
      </li>
    </ul>

    <h2 class="text-xl font-bold mb-2">Agregar Pago</h2>
    <form @submit.prevent="addPago" class="mb-6">
      <input
        type="date"
        v-model="nuevoPago.fecha"
        required
        class="border p-2 rounded"
      />
      <input
        type="number"
        v-model.number="nuevoPago.monto"
        placeholder="Monto del pago"
        required
        class="border p-2 rounded ml-2"
      />
      <button
        type="submit"
        class="ml-2 bg-indigo-600 text-white py-2 px-4 rounded"
      >
        Agregar Pago
      </button>
    </form>

    <h2 class="text-xl font-bold mb-2">Próximos Vencimientos</h2>
    <ul>
      <li
        v-for="vencimiento in proximosVencimientos"
        :key="vencimiento.id"
        class="mb-2"
      >
        Fecha: {{ vencimiento.fecha }} - Monto: {{ vencimiento.monto }}
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const props = defineProps({
  prestamoId: {
    type: String,
    required: true,
  },
});

const prestamo = ref({});
const pagos = ref([]);
const nuevoPago = ref({ fecha: '', monto: 0 });
const proximosVencimientos = ref([]);

const fetchPrestamoDetails = async () => {
  const response = await fetch(`YOUR_API_URL/prestamos/${props.prestamoId}`);
  const data = await response.json();
  prestamo.value = data;
};

const fetchPagos = async () => {
  const response = await fetch(
    `YOUR_API_URL/pagos?prestamoId=${props.prestamoId}`
  );
  const data = await response.json();
  pagos.value = data;
};

const fetchProximosVencimientos = async () => {
  const response = await fetch(
    `YOUR_API_URL/vencimientos?prestamoId=${props.prestamoId}`
  );
  const data = await response.json();
  proximosVencimientos.value = data;
};

const addPago = async () => {
  const response = await fetch(`YOUR_API_URL/pagos`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      prestamoId: props.prestamoId,
      fecha: nuevoPago.value.fecha,
      monto: nuevoPago.value.monto,
    }),
  });

  if (response.ok) {
    fetchPagos();
    nuevoPago.value = { fecha: '', monto: 0 };
  } else {
    console.error('Error al agregar pago');
  }
};

onMounted(() => {
  fetchPrestamoDetails();
  fetchPagos();
  fetchProximosVencimientos();
});
</script>

<style scoped>
/* Estilos adicionales si es necesario */
</style>

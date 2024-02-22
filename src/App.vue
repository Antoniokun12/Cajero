<template>
  <div class="containerr">
    <div v-if="mostrarBloque1">
      <h3>{{ alerta }}</h3>
      <label for="saldo">Ingrese un Saldo inicial</label>
      <input type="number" v-model.trim="saldo" class="form" />
      <label for="usuario">Ingrese un usuario</label>
      <input type="text" v-model.trim="usuario" class="form" />
      <label for="contraseña">Ingrese una contraseña</label>
      <input type="text" v-model.trim="contraseña" class="form" />
      <button @click="agregar()">Agregar</button>
    </div>
    <div v-else>
      <h3>{{ alerta }}</h3>
      <h1>Saldo</h1>
      <h2>{{ formatSaldo }}</h2>
      <label for="operacion">Seleccione una operación:</label>
      <select v-model="tipoOperacion">
        <option value="retiro">Retiro</option>
        <option value="ingreso">Ingreso</option>
      </select>
      <label for="cantidad">Cantidad:</label>
      <input type="number" v-model.trim="cantidad" class="form" />
      <label for="usuario">Usuario:</label>
      <input type="text" v-model.trim="usuarioOperacion" class="form" />
      <label for="contraseña">Contraseña:</label>
      <input type="text" v-model.trim="contraseñaOperacion" class="form" />
      <button @click="realizarOperacion">Realizar Operación</button>
      <div v-if="tipoOperacion === 'retiro' && billetes.length > 0">
        <h3>Billetes entregados:</h3>
        <ul>
          <li v-for="billete in billetes" :key="billete.denominacion">
            {{ billete.cantidad }} billetes de {{ billete.denominacion }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watchEffect } from "vue";
const mostrarBloque1 = ref(true);
const registros = ref([]);
const alerta = ref("");
const usuario = ref("");
const contraseña = ref("");
const saldo = ref("");
const tipoOperacion = ref("retiro");
const cantidad = ref("");
const usuarioOperacion = ref("");
const contraseñaOperacion = ref("");
const formatSaldo = ref("");
const billetes = ref([]);

watchEffect(() => {
  formatSaldo.value = saldo.value.toLocaleString("es-CO", {
    style: "currency",
    currency: "COP",
  });
});

const ocultarAlerta = () => {
  setTimeout(() => {
    alerta.value = "";
  }, 3000);
};

const agregar = () => {
  if (usuario.value === "") {
    alerta.value = "Ingrese un usuario";
    ocultarAlerta();
  } else if (contraseña.value === "") {
    alerta.value = "Ingrese una contraseña";
    ocultarAlerta();
  } else {
    ocultarAlerta();
    registros.value.push({
      usuario: usuario.value,
      contraseña: contraseña.value,
    });
    console.log(registros.value);
    saldo.value = parseFloat(saldo.value);
    mostrarBloque1.value = false;
  }
};

const realizarOperacion = () => {
  const usuarioValido = registros.value.some(
    (registro) =>
      registro.usuario === usuarioOperacion.value &&
      registro.contraseña === contraseñaOperacion.value
  );

  if (cantidad.value === "") {
    alerta.value = "Ingrese una cantidad";
    ocultarAlerta();
  } else if (usuarioOperacion.value === "") {
    alerta.value = "Ingrese el usuario";
    ocultarAlerta();
  } else if (contraseñaOperacion.value === "") {
    alerta.value = "Ingrese la contraseña";
    ocultarAlerta();
  } else {
    if (!usuarioValido) {
      alerta.value = "Usuario o contraseña incorrectos";
      ocultarAlerta();
      return;
    }

    if (tipoOperacion.value === "retiro") {
      if (cantidad.value > saldo.value) {
        alerta.value = "No hay suficiente saldo para el retiro";
        ocultarAlerta();
      } else {
        saldo.value -= cantidad.value;
        const denominaciones = [100000, 50000, 20000, 10000, 5000, 2000];
        billetes.value = [];

        for (const denominacion of denominaciones) {
          const cantidadBilletes = Math.floor(cantidad.value / denominacion);
          if (cantidadBilletes > 0) {
            billetes.value.push({
              denominacion,
              cantidad: cantidadBilletes,
            });
            cantidad.value %= denominacion;
          }
        }
        alerta.value = "Retiro exitoso";
        ocultarAlerta();
        limpiar();
      }
    } else if (tipoOperacion.value === "ingreso") {
      saldo.value += cantidad.value;
      alerta.value = "Ingreso exitoso";
      ocultarAlerta();
      limpiar();
    }
  }
};

function limpiar() {
  cantidad.value = "";
  usuarioOperacion.value = "";
  contraseñaOperacion.value = "";
}
</script>


<style>
body {
  background-color: #4d514e;
  margin: 0;
  padding: 0;
}

.containerr {
  max-width: 450px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
  background-color: #cae0e7;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.form {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
  box-sizing: border-box;
}

select {
  padding-right: 20px;
  background-color: #f5f5f5;
  border: 1px solid #ccc;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  height: 40px;
  width: 50%;
  margin-left: 25%;
  margin-right: 30%;
}
</style>








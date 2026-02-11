<template>
  <div class="container">
    <h3>Datos Estudiante</h3>
    <form action="">
      <div class="datos">
        <label for="cedula">Cédula:</label>
        <span class="error-msg" v-if="validar && !estudiante.cedula">{{
          requerido
        }}</span>
        <input
          type="number"
          id="cedula"
          v-model="estudiante.cedula"
          placeholder="Ingrese la cédula"
        />
        <label for="nombre">Nombre:</label>
        <span class="error-msg" v-if="validar && !estudiante.nombre">{{
          requerido
        }}</span>
        <input
          type="text"
          id="nombre"
          v-model="estudiante.nombre"
          placeholder="Ingrese el nombre"
        />
        <label for="apellido">Apellido:</label>
        <span class="error-msg" v-if="validar && !estudiante.apellido">{{
          requerido
        }}</span>
        <input
          type="text"
          id="apellido"
          v-model="estudiante.apellido"
          placeholder="Ingrese el apellido"
        />
        <label for="carrera">Carrera:</label>
        <span class="error-msg" v-if="validar && !estudiante.carrera">{{
          requerido
        }}</span>
        <input
          type="text"
          id="carrera"
          v-model="estudiante.carrera"
          placeholder="Ingrese la carrera"
        />

        <label for="fechaNacimiento">Fecha de Nacimiento:</label>
        <span class="error-msg" v-if="validar && !estudiante.fechaNacimiento">{{
          requerido
        }}</span>
        <input
          type="date"
          id="fechaNacimiento"
          v-model="estudiante.fechaNacimiento"
        />
        <label for="telefono">Teléfono:</label>
        <span class="error-msg" v-if="validar && !estudiante.telefono">{{
          requerido
        }}</span>
        <input
          type="text"
          id="telefono"
          v-model="estudiante.telefono"
          placeholder="Ingrese el teléfono"
        />
      </div>
      <div class="botones">
        <button
          type="submit"
          v-show="guardar"
          @click.prevent="guardarEstudiante"
        >
          Guardar
        </button>
        <button type="submit" v-show="visible" @click.prevent="actualizar">
          Actualizar
        </button>
        <button @click.prevent="cancelar">Cancelar</button>
      </div>
      <h3>{{ mensaje }}</h3>
    </form>
  </div>
</template>

<script>
import {
  guardarFachada,
  actualizarFachada,
  mostrarPorIdFachada,
} from "@/clients/EstudianteClient";

export default {
  props: {
    idBuscar: {
      type: [Number, String],
      default: "",
    },
    guardar: {
      type: Boolean,
      default: true,
    },
    visible: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      estudiante: {
        id: "",
        cedula: "",
        nombre: "",
        apellido: "",
        carrera: "",
        fechaNacimiento: "",
        telefono: "",
      },
      mensaje: "",
      requerido: "Este campo es requerido",
      validar: false,
    };
  },
  watch: {
    idBuscar: {
      handler(newVal) {
        if (newVal) {
          this.consultarPorId(newVal);
        }
      },
      immediate: true,
    },
  },
  methods: {
    async consultarPorId(id) {
      const resp = await mostrarPorIdFachada(id);
      this.estudiante = resp;
    },
    async guardarEstudiante() {
      try {
        this.validar = true;
        if (this.formularioValido) {
          this.estudiante.id = null;
          console.log("Enviando estudiante:", JSON.stringify(this.estudiante));
          await guardarFachada(this.estudiante);
          this.$emit("salir");
          this.$emit("txt", 1);
          this.limpiar();
        }
      } catch (error) {
        this.mensaje = "No se guardó";
        this.tiempo();
      }
    },
    async actualizar() {
      try {
        await actualizarFachada(this.estudiante.id, this.estudiante);
        this.$emit("salir");
        this.$emit("txt", 2);
        this.limpiar();
      } catch (error) {
        this.mensaje = "No se actualizó";
        this.tiempo();
      }
    },
    cancelar() {
      this.$emit("salir");
      this.limpiar();
    },
    limpiar() {
      this.validar = false;
      this.estudiante = {
        id: "",
        cedula: "",
        nombre: "",
        apellido: "",
        carrera: "",
        fechaNacimiento: "",
        telefono: "",
      };
    },
    tiempo() {
      setTimeout(() => {
        this.mensaje = "";
      }, 4000);
    },
  },
  computed: {
    formularioValido() {
      return (
        this.estudiante.cedula &&
        this.estudiante.nombre &&
        this.estudiante.nombre.trim() !== "" &&
        this.estudiante.apellido &&
        this.estudiante.apellido.trim() !== "" &&
        this.estudiante.carrera &&
        this.estudiante.carrera.trim() !== "" &&
        this.estudiante.fechaNacimiento &&
        this.estudiante.telefono &&
        this.estudiante.telefono.trim() !== ""
      );
    },
  },
};
</script>

<style scoped>
.container {
  max-width: 500px;
  margin: 10px auto; /* Reduced top margin as per previous validation */
  padding: 25px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.1);
  font-family: Arial, sans-serif;
}

form {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.datos {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.datos label {
  text-align: left;
  font-weight: bold;
  color: #555;
  margin-bottom: 4px;
}
.datos input {
  height: 42px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 14px;
  box-sizing: border-box;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.datos input:focus {
  border-color: #6c63ff;
  box-shadow: 0 0 6px rgba(108, 99, 255, 0.4);
  outline: none;
}
button {
  height: 35px;
  width: 100px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 12px;
  font-weight: bold;
  margin-left: 20px;
  margin-top: 25px;
}
.error-msg {
  color: #ca1306;
  font-size: 11px;
  font-weight: bold;
  margin-bottom: -8px;
  text-align: left;
}

.botones button:hover {
  transform: scale(1.05);
}

.botones button:nth-child(1) {
  background: #4caf50; /* verde para guardar */
  color: white;
}

.botones button:nth-child(2) {
  background: #4caf50; /* verde para actializar */
  color: white;
}

.botones button:nth-child(3) {
  background: #f95d52; /* rojo para cancelar */
  color: white;
}

h3 {
  text-align: center;
  margin-top: 20px;
  color: #333;
}
</style>
<template>
  <div class="container">
    <h3>Datos Curso</h3>
    <div class="formulario">
      <form action="">
      <div class="datos">
        <label for="codigoCurso">Codigo:</label>
        <span class="error-msg" v-if="validar && !curso.codigoCurso">{{
          requerido
        }}</span>
        <input
          type="text"
          id="codigoCurso"
          v-model="curso.codigoCurso"
          required
          placeholder="ingrese codigo curso"
        />
        <label for="nombre">Nombre:</label>
        <span class="error-msg" v-if="validar && !curso.nombre">{{
          requerido
        }}</span>
        <input type="text" id="nombre" v-model="curso.nombre" required placeholder="ingrese nombre curso"/>
        <label for="descripcion">Descripcion:</label>
        <span class="error-msg" v-if="validar && !curso.descripcion">{{
          requerido
        }}</span>
        <input
          type="text"
          id="descripcion"
          v-model="curso.descripcion"
          placeholder="ingrese descripcion curso"
          required
        />
        <label for="creditos">Creditos:</label>
        <span class="error-msg" v-if="validar && !curso.creditos">{{
          requerido
        }}</span>
        <input type="number" id="creditos" v-model="curso.creditos" required placeholder="ingrese creditos curso"/>
        <label for="cupos">Cupos:</label>
        <span class="error-msg" v-if="validar && !curso.cupos">{{
          requerido
        }}</span>
        <input type="number" id="cupos" v-model="curso.cupos" required placeholder="ingrese cupos curso"/>
      </div>
      <div class="botones">
        <button
          type="submit"
          v-show="guardar"
          @click.prevent="guardarCurso"
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
  </div>
</template>
<script>
import {
  mostrarPorIdFachada,
  guardarFachada,
  actualizarFachada,
} from "@/clients/CursoClient";
export default {
  props: {
    codigoBuscar: {
      type: String,
      required: true,
    },
    guardar: {
      type: Boolean,
    },
    visible: {
      type: Boolean,
    },
  },
  data() {
    return {
      curso: {
        id: "",
        codigoCurso: "",
        nombre: "",
        descripcion: "",
        creditos: "",
        cupos: "",
      },
      requerido: "Este campo es requerido",
      validar: false,
      mensaje: "",
    };
  },
  watch: {
    codigoBuscar(newValue, oldValue) {
      console.log(oldValue + " aqui llego");
      if (oldValue !== newValue) {
        (this.mensaje = ""), this.Buscar(newValue);
      }
    },
  },

  methods: {
    async Buscar(id) {
      const respuesta = await mostrarPorIdFachada(id);
      this.curso = respuesta;
    },

    async actualizar() {
      try {
        await actualizarFachada(this.curso.id, this.curso);
        this.$emit("salir", "");
        this.$emit("txt", 2);
        this.limpiar();
      } catch {
        this.mensaje = "No se actualizo";
        this.tiempo();
      }
    },

    async guardarCurso() {
      try {
        this.validar = true;
        this.curso.id = null;
        if (this.formularioValido) {
          await guardarFachada(this.curso);
          this.$emit("salir", "");
          this.$emit("txt", 1);
          this.limpiar();
        }
      } catch {
        this.mensaje = "No se guardo";
        this.tiempo();
      }
    },
    limpiar() {
      this.false = false;
      this.curso.id = "";
      this.curso.codigoCurso = "";
      this.curso.nombre = "";
      this.curso.descripcion = "";
      this.curso.creditos = "";
      this.curso.cupos = "";
    },
    cancelar() {
      this.$emit("salir", "");
      this.limpiar();
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
        this.curso.codigoCurso &&
        this.curso.codigoCurso.trim() !== "" &&
        this.curso.nombre &&
        this.curso.nombre.trim() !== "" &&
        this.curso.descripcion &&
        this.curso.descripcion.trim() !== "" &&
        this.curso.creditos !== "" &&
        this.curso.creditos > 0 &&
        this.curso.cupos !== "" &&
        this.curso.cupos >= 0
      );
    },
  },
};
</script>
<style scoped>
.container {
  max-width: 500px;
  margin: 40px auto;
  padding: 25px;
  background: linear-gradient(to right, #40e0d0, #b19cd9); /* ejemplo con gradiente */
  border-radius: 12px;
  box-shadow: 0 6px 18px rgba(0, 0, 0, 0.1);
  font-family: Arial, sans-serif;
}
.formulario {
  display: flex;
  flex-direction: column;
  gap: 15px;
  padding: 10px;
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
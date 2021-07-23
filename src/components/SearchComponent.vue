<template>
  <div class="search">
    <div class="content-search d-flex justify-content-between">
      <input
        class="py-2 px-4 w-75"
        type="text"
        placeholder="Agregar detalle"
        v-model="nuevaTarea"
        autofocus
      />
      <button class="py-2 px-4" @click="agregarTarea()">
        Agregar
      </button>
    </div>

    <label
      for=""
      class="w-100 text-danger text-start py-2"
      v-show="error != ''"
    >
      {{ error }}
    </label>
  </div>
</template>

<script>
  export default {
    name: "SearchComponent",
    created() {},
    data() {
      return {
        nuevaTarea: "",
        error: "",
      };
    },
    props: ["seAgrego"],
    watch: {
      seAgrego(value) {
        if (value == true) {
          this.nuevaTarea = "";
        }
      },
    },
    methods: {
      agregarTarea() {
        console.log(this.nuevaTarea);
        if (this.nuevaTarea != "") {
          this.$emit("nuevaTarea", this.nuevaTarea);
        } else {
          this.error = "No puedes agregar un campo vacío";
        }
      },
    },
  };
</script>

<style lang="scss" scoped>
  input {
    border: 1px solid #bdbdbd;
    box-sizing: border-box;
    border-radius: 12px;
    margin-right: 15px;
  }
  input:focus,
  input:active {
    outline: none !important;
  }
  button {
    background: #2f80ed;
    border: 0;
    border-radius: 12px;
    box-shadow: 0px 2px 6px rgba(127, 177, 243, 0.4);
    color: #fff;
  }
</style>
